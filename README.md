# Annotations

Annotations are at the heart of how `noun-and-verb` operates. These are the minimal hints from the designer/developer of the API that turn out to being sufficient to generate a vast majority of the
customized code needed to create functioning APIs to current standards of "good" APIs.


> #### Prisma doc-comments
> Two rules of prisma doc-comments:
>  1. They start with THREE `///`
>  2. Consecutive lines are collated to a single paragraph 

`noun-and-verb` exploits this to add super-powers to your prisma schema.
"annotations" are well-specified hints that placed correctly, allow `noun-and-verb` to auto-generate a vast majority of the code needed to get a functioning API to modern standards. Outside these annotations, all of the code you write or `noun-and-verb` generates are in service of one of the
other three frameworks/tools we use to enable a functioning API server - 
1. [Prisma](https://prisma.io), 
2. [graphQL](https://graphql.org/),
3. [Apollo Server](https://www.apollographql.com/)


Without further ado, the much hyped 6 `annotations`:

1. `@createOnly`: 
   - Applies to model fields
   - Typically reserved for things like primary keys
2. `@readOnly`: 
   - Applies to model fields
   - Typically for fields populated on the server
3. `@writeOnly`:
   - Applies to model fields
   - Typically for 'secrets' the user provides for use by the server
4. `@scalar`: 
   - Applies to model fields
   - Converts to [graphQL scalars](https://graphql.org/learn/schema/#scalar-types)
   - `noun-and-verb` adds support for dozens more (via [validate.js](https://validatejs.org/#validators))
   - For unknown scalars, a working scaffolding is generated
5. `@seed`:
   - Applies to models and relation-fields
   - Specified as `@seed min,max,unique-field`
   - The `min` and `max` control the number of sub-relations created. For 1:1 relationships, `min = max = 1`. Defaults to `0,10` at the field level, and `0,100` at the model level
   - The seeder uses `upsert` to create rows in the table. This requires a `unique-field` to search for existence
   - If `unique-field` is not provided, uses the first non-index string field, followed by the first non-index numeric field
   - Specifications at the relation-field level override the model specification
   - *Only models with `@seed` annotations appear in the seeding script*
   - All relationships are followed unless a loop or a `@seed 0,0` annotation is detected
6. `@mock`: 
   - Applies to model fields. 
   - Specifies the function used to generate a mock value. 
   - All functions from [`faker.js`](https://fakercloud.com/api) are made available by default
   - Many of the well-known `@scalar` from #4 are automatically mapped to appropriate 'faker' calls 
   - For fields without `@mock` annotations, simple mocker functions are generated. For the base types (strings/ints/floats/booleans), sensible default functions are generated. For unknown/unmapped scalars, a "scaffolding" function is generated within a local "mocker" module. The developer is expected to fill in the details.
   - Mock functions are generated in individual files, and only generated if not already existing.
   - The mock functions get two parameters:
     ```
     // mocker/model_name/field_name.ts
     export default function(faker: Faker, model: any): <string|boolean|number> {
       // TODO: implement mock ability. 
       // `model` provides access to all fields already mocked.
     }
     ```

## Noun-and-Verb Generator
```diff
 datasource db {
   provider = "sqlite"
   url      = "file:./dev.db"
 }
 
 generator client {
   provider = "prisma-client-js"
 }
 
+generator noun_and_verb {
+  provider = "noun_and_verb"
+}
+
 model User {
   id String @id @default(cuid())
 
   createdAt DateTime? @default(now())
 
   modifiedAt DateTime? @default(now())
 
   /// Email address of the user
   email String
 
   /// password. Ideally, not readable via the API
   password String
 
   // relations (NOTE: double slashes are local comments and NOT parsed)
   posts Post[]
 }
 
 model Post {
   id String @id @default(cuid())
 
   createdAt DateTime? @default(now())
 
   modifiedAt DateTime? @default(now())
 
   /// Title of the post. Keep is short and click-worthy
   title String
 
   /// Slug used in URI for the article. Allows modification of title post-publishing of article.
   /// While the slug value can be changes, caution advised. Modifications *might* have negative SEO impacts.
   slug String
 
   /// The meat of the article
   article String
 
   // relations (NOTE: double slashes are local comments and NOT parsed)
   User   User?   @relation(fields: [userId], references: [id])
   userId String?
 }
```

## API Annotations
```diff
 datasource db {
   provider = "sqlite"
   url      = "file:./dev.db"
 }
 
 generator client {
   provider = "prisma-client-js"
 }
 
 generator noun_and_verb {
   provider = "noun_and_verb"
 }
 
 model User {
+  /// @createOnly
+  /// @readOnly
   id String @id @default(cuid())
 
+  /// @createOnly
+  /// @readOnly
   createdAt DateTime? @default(now())
 
+  /// @readOnly
   modifiedAt DateTime? @default(now())
 
   /// Email address of the user
+  /// @scalar Email
   email String
 
   /// password. Ideally, not readable via the API
+  /// @writeOnly
   password String
 
   // relations (NOTE: double slashes are local comments and NOT parsed)
   posts Post[]
 }
 
 model Post {
+  /// @createOnly
+  /// @readOnly
   id String @id @default(cuid())
 
+  /// @createOnly
+  /// @readOnly
   createdAt DateTime? @default(now())
 
+  /// @readOnly
   modifiedAt DateTime? @default(now())
 
   /// Title of the post. Keep is short and click-worthy
   title String
 
   /// Slug used in URI for the article. Allows modification of title post-publishing of article.
   /// While the slug value can be changes, caution advised. Modifications *might* have negative SEO impacts.
   slug String
 
   /// The meat of the article
   article String
 
   // relations (NOTE: double slashes are local comments and NOT parsed)
   User   User?   @relation(fields: [userId], references: [id])
   userId String?
 }
```

## Mock data Annotations
```diff
 datasource db {
   provider = "sqlite"
   url      = "file:./dev.db"
 }
 
 generator client {
   provider = "prisma-client-js"
 }
 
 generator noun_and_verb {
   provider = "noun_and_verb"
 }
 
+/// @seed 0,100,email
 model User {
   /// @createOnly
   /// @readOnly
   id String @id @default(cuid())
 
   /// @createOnly
   /// @readOnly
   createdAt DateTime? @default(now())
 
   /// @readOnly
   modifiedAt DateTime? @default(now())
 
   /// Email address of the user
+  /// (uses faker.internet.email by default.
+  /// See [Scalar-Faker map](../scalar-faker-map.md) for other scaalars with default fakers.
   /// @scalar Email
   email String
 
   /// password. Ideally, not readable via the API
   /// @writeOnly
+  /// @mock faker.internet.password
   password String
 
   // relations (NOTE: double slashes are local comments and NOT parsed)
+  /// @seed 2,10
   posts Post[]
 }
 
 model Post {
   /// @createOnly
   /// @readOnly
   id String @id @default(cuid())
 
   /// @createOnly
   /// @readOnly
   createdAt DateTime? @default(now())
 
   /// @readOnly
   modifiedAt DateTime? @default(now())
 
   /// Title of the post. Keep is short and click-worthy
+  /// @mock faker.lorem.sentence
   title String
 
   /// Slug used in URI for the article. Allows modification of title post-publishing of article.
   /// While the slug value can be changes, caution advised. Modifications *might* have negative SEO impacts.
+  /// @mock mocker.slug
   slug String
 
   /// The meat of the article
+  /// @mock faker.lorem.paragraphs
   article String
 
   // relations (NOTE: double slashes are local comments and NOT parsed)
   User   User?   @relation(fields: [userId], references: [id])
   userId String?
 }
```

