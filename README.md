# Annotations
`noun-and-verb` is built on adding annotation hints to a prisma schema. These take the form

```diff
--- schema-0.prisma	2021-11-13 15:52:10.000000000 -0800
+++ schema-2-anno-gql.prisma	2021-11-13 15:54:33.000000000 -0800
@@ -1,47 +1,63 @@
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
-  User   User? @relation(fields: [userId], references: [id])
+  User   User?   @relation(fields: [userId], references: [id])
   userId String?
 }
```
