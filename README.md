# PRISMA - ORM
An ORM(Object-Relational Mapping) is a technique or a tool that allows developers to work with objects and classes in their preferred programming language, rather than writing raw SQL queries, enhancing development speed, security, and code maintainability

### Benefits of using an orm:
- **Simplified Development:**
ORMs abstract away the complexities of SQL, allowing developers to focus on application logic instead of database interactions
- **Faster Development:**
By streamlining data operations and reducing the need for writing manual SQL queries, ORMs significantly accelerate development time.
- **Improved Security:**
ORMs often include built-in mechanisms to prevent SQL injection attacks, enhancing security
- **Increased Portability:**
ORMs can abstract away database-specific details, allowing developers to switch between different database systems with minimal code changes
- **Enhanced Code Maintainability:**
ORMs provide a more structured and organized approach to database operations, making it easier to maintain and update the codebase over time.
- **Easier Database Migrations:**
ORMs can automate database schema changes and migrations, making it easier to manage database updates.

### What is Prisma?
Prisma ORM is a next-generation ORM that consists of these tools:

- [Prisma Client](https://www.prisma.io/docs/orm/prisma-client): Auto-generated and type-safe query builder for Node.js & TypeScript
- [Prisma Migrate](https://www.prisma.io/docs/orm/prisma-migrate): Declarative data modeling & migration system
- [Prisma Studio](https://github.com/prisma/studio): GUI to view and edit data in your database

Prisma Client can be used in any Node.js or TypeScript backend application (including serverless applications and microservices).

### How to use Prisma
**1. Install Prisma**

  To install prisma, run the following command
```
  npm install prisma -D
```
or
```
npm install prisma --save-dev
```
**2. Setting up**

After installing Prisma, we need to set up our Prisma project.

We run the command below:
```
 npx prisma init --datasource-provider DATABASE
```
Replace DATABASE with the database that you are uming, assuming you are using postgresql:
```
npx prisma init --datasource-provider postgresql
```
Prisma uses postgresql by default, so if your database is postgresql, you can run the command without passing ```--datasource-provider``` flag.
```
npx prisma init
```
This command does two important things:

- Creates a new directory called **prisma** that contains the file **schema.prisma**, which contains our database connection variables and models.

- Creates a **.env** file at the root of the project which is used for defining the environment variables such as the connection string.

The **.env** comes with a ```DATABASE_URL``` field template allowing you to easily configure your database connection string by only filling in important details like username, password, host, port and database name.
```
DATABASE_URL="postgresql://johndoe:randompassword@localhost:5432/mydb?schema=public"
```

```
DATABASE_URL="postgresql://postgres:1234@localhost:5432/products"
```

### Models

Models represent database tables and their relationships in a way that feels intuitive and familiar to developers.

### Key concepts of Prisma Models﻿
1. **Data Models:** Define the tables and their columns (fields) in your database.
2. **Field Types:** Specify the data types for each field (e.g., String, Int, Boolean).
3. **Relations:** Describe how models relate to each other using relation fields.
4. **Attributes:** Add additional metadata to fields and models using attributes like @id, @default, @relation.

The following schema describes a blogging platform - the data model definition is highlighted:
```
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model User {
  id      Int      @id @default(autoincrement())
  email   String   @unique
  name    String?
  role    Role     @default(USER)
  posts   Post[]
  profile Profile?
}

model Profile {
  id     Int    @id @default(autoincrement())
  bio    String
  user   User   @relation(fields: [userId], references: [id])
  userId Int    @unique
}

model Post {
  id         Int        @id @default(autoincrement())
  createdAt  DateTime   @default(now())
  updatedAt  DateTime   @updatedAt
  title      String
  published  Boolean    @default(false)
  author     User       @relation(fields: [authorId], references: [id])
  authorId   Int
  categories Category[]
}

model Category {
  id    Int    @id @default(autoincrement())
  name  String
  posts Post[]
}

enum Role {
  USER
  ADMIN
}
```
<img src="./assets/blogging platform">
