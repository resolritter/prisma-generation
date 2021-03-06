# Migration `20200705173714-init`

This migration has been generated by resolritter at 7/5/2020, 5:37:14 PM.
You can check out the [state of the schema](./schema.prisma) after the migration.

## Database Steps

```sql
PRAGMA foreign_keys=OFF;

CREATE TABLE "quaint"."User" (
"email" TEXT NOT NULL  ,"id" INTEGER NOT NULL  PRIMARY KEY AUTOINCREMENT,"password" TEXT NOT NULL  ,"role" TEXT NOT NULL DEFAULT 'User' )

CREATE TABLE "quaint"."Event" (
"createdAt" DATE NOT NULL DEFAULT CURRENT_TIMESTAMP ,"id" TEXT NOT NULL  ,"updatedAt" DATE NOT NULL DEFAULT CURRENT_TIMESTAMP ,
    PRIMARY KEY ("id"))

CREATE TABLE "quaint"."Document" (
"createdAt" DATE NOT NULL DEFAULT CURRENT_TIMESTAMP ,"id" INTEGER NOT NULL  PRIMARY KEY AUTOINCREMENT,"path" TEXT NOT NULL  ,"redemptionId" INTEGER NOT NULL  ,"submitterId" INTEGER   ,"updatedAt" DATE NOT NULL DEFAULT CURRENT_TIMESTAMP ,FOREIGN KEY ("redemptionId") REFERENCES "Redemption"("id") ON DELETE CASCADE ON UPDATE CASCADE,
FOREIGN KEY ("submitterId") REFERENCES "User"("id") ON DELETE SET NULL ON UPDATE CASCADE)

CREATE TABLE "quaint"."Redemption" (
"Submission" TEXT   ,"couponId" INTEGER NOT NULL  ,"createdAt" DATE NOT NULL DEFAULT CURRENT_TIMESTAMP ,"id" INTEGER NOT NULL  PRIMARY KEY AUTOINCREMENT,"userId" INTEGER   ,FOREIGN KEY ("couponId") REFERENCES "Coupon"("id") ON DELETE CASCADE ON UPDATE CASCADE,
FOREIGN KEY ("userId") REFERENCES "User"("id") ON DELETE SET NULL ON UPDATE CASCADE)

CREATE TABLE "quaint"."Coupon" (
"createdAt" DATE NOT NULL DEFAULT CURRENT_TIMESTAMP ,"eventId" TEXT   ,"id" INTEGER NOT NULL  PRIMARY KEY AUTOINCREMENT,"redeemerId" INTEGER   ,FOREIGN KEY ("redeemerId") REFERENCES "User"("id") ON DELETE SET NULL ON UPDATE CASCADE,
FOREIGN KEY ("eventId") REFERENCES "Event"("id") ON DELETE SET NULL ON UPDATE CASCADE)

CREATE UNIQUE INDEX "quaint"."User.email" ON "User"("email")

PRAGMA "quaint".foreign_key_check;

PRAGMA foreign_keys=ON;
```

## Changes

```diff
diff --git schema.prisma schema.prisma
migration ..20200705173714-init
--- datamodel.dml
+++ datamodel.dml
@@ -1,0 +1,57 @@
+datasource db {
+  provider = "sqlite"
+  url = "***"
+}
+
+generator client {
+  provider = "prisma-client-js"
+}
+
+model User {
+  id Int @default(autoincrement()) @id
+  email String  @unique
+  password String
+  coupons Redemption[]
+  // SQLite doesn't support enums
+  role      String     @default("User")
+}
+
+model Event {
+  id String @id
+  coupons Coupon[]
+  createdAt DateTime @default(now())
+  updatedAt DateTime @default(now())
+}
+
+model Document {
+  id Int @default(autoincrement()) @id
+  path String
+  createdAt DateTime @default(now())
+  updatedAt DateTime @default(now())
+
+  redemptionId Int
+  redemption Redemption @relation(fields: [redemptionId], references: [id])
+
+  submitterId  Int?
+  submitter    User?    @relation(fields: [submitterId], references: [id])
+}
+
+model Redemption {
+  id        Int     @default(autoincrement()) @id
+  // SQLite doesn't support JSON columns
+  Submission String?
+  createdAt DateTime @default(now())
+
+  document Document[]
+
+  couponId Int
+  coupon Coupon @relation(fields: [couponId], references: [id])
+}
+
+model Coupon {
+  id        Int     @default(autoincrement()) @id
+  createdAt DateTime @default(now())
+
+  redeemerId  Int?
+  redeemer    User?    @relation(fields: [redeemerId], references: [id])
+}
```


