# Annotations
`noun-and-verb` is built on adding annotation hints to a prisma schema. These take the form

```/// @annotation any,necessary,parameters```

These annotations can be applied to the Prisma models and/or individual fields. 

## An example

A prisma model 

diff --git a/schema-1-nv-generator.prisma b/schema-2-anno-gql.prisma
index 7f950b3..3f6f76c 100644
--- a/schema-1-nv-generator.prisma
+++ b/schema-2-anno-gql.prisma
@@ -1,43 +1,49 @@
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
  {+/// @createOnly+}
{+  /// @readOnly+}
  id       Int    @id @default(autoincrement())
  
  /// Email address of the user
  {+/// @scalar Email+}
  email    String
  
  /// password. Ideally, not readable via the API
  {+/// @writeOnly+}
  password String

  // relations (NOTE: double slashes are local comments and NOT parsed)
  posts    Post[]
}

model Post {
  {+/// @createOnly+}
{+  /// @readOnly+}
  id      Int    @id @default(autoincrement())
  
  /// Title of the post. Keep is short and click-worthy
  title   String

  /// Slug used in URI for the article. Allows modification of title post-publishing of article.
  /// While the slug value can be changes, caution advised. Modifications *might* have negative SEO impacts.
  slug    String
  
  /// The meat of the article
  article String

  // relations (NOTE: double slashes are local comments and NOT parsed)
  User    User?  @relation(fields: [userId], references: [id])
  userId  Int?
}

