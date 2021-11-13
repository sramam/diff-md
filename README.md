# Annotations
`noun-and-verb` is built on adding annotation hints to a prisma schema. These take the form

```diff

*** schema-2-anno-gql.prisma	2021-11-13 12:19:05.000000000 -0800
--- schema-3-anno-seeder.prisma	2021-11-13 12:19:05.000000000 -0800
***************
*** 1,49 ****
--- 1,55 ----
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
  
+ /// @seed 0,100,email
  model User {
    /// @createOnly
    /// @readOnly
    id       Int    @id @default(autoincrement())
    
    /// Email address of the user
    /// @scalar Email
    email    String
    
    /// password. Ideally, not readable via the API
    /// @writeOnly
+   /// @mock faker.internet.password
    password String
  
    // relations (NOTE: double slashes are local comments and NOT parsed)
+   /// @seed 2,10
    posts    Post[]
  }
  
  model Post {
    /// @createOnly
    /// @readOnly
    id      Int    @id @default(autoincrement())
    
    /// Title of the post. Keep is short and click-worthy
+   /// @mock faker.lorem.sentence
    title   String
  
    /// Slug used in URI for the article. Allows modification of title post-publishing of article.
    /// While the slug value can be changes, caution advised. Modifications *might* have negative SEO impacts.
+   /// @mock mocker.slug
    slug    String
    
    /// The meat of the article
+   /// @mock faker.lorem.paragraphs
    article String
  
    // relations (NOTE: double slashes are local comments and NOT parsed)
    User    User?  @relation(fields: [userId], references: [id])
    userId  Int?
  }
```
