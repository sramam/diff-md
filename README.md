# Annotations
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

