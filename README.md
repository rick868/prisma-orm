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

### Field modifiers﻿
They are used to indicate only two things:
- Whether a field is optional, in which case we use ?
- Whether a field can contain multiple values in which case we use [].
### Field types﻿
- **String:** represents text data.
- **Int:** represents a 32-bit signed integer.
- **BigInt:** represents large integers (64 bit).
- **Float:** represents floating point numbers e.g. 32.7329
- **Decimal:** represents precise decimal values.
- **Boolean:** represents true or false values.
- **DateTime:** represents date and time.
- **Bytes:** represents binary data, useful for storing images, etc.
- **JSON:** represents a JSON object eg metadata JSON.
- **UUID:** represents a universally unique identifier e.g. id UUID @default(uuid()).
### Field Attributes﻿
- @id: marks the field as the primary key of the model.
- @default: sets a default value for the field eg createdAt DateTime @default(now())
- @unique: ensures that a field has unique values across all rows in a table.
- @updatedAt: automatically updates a field to the current timestamp whenever the record is updated eg updatedAt DateTime @updatedAt.
- @map: maps the field to a column with a different name in the database for example firstName String @map("first_name")
- @relation: defines relationships between models. Specifies foreign keys and the related models eg author User @relation(fields: [authorId], references: [id])
- @@id: it is a model attribute, and it defines a composite primary key using multiple fields eg @@id([field1, field2])
- @@unique: it is a model attribute, and it ensures a unique constraint on a combination of multiple fields. eg @@unique([username, emailAddress])
- @@map: it is a model attribute, and it maps the model to a table with a different name in the database.

Models are defined inside ```schema.prisma``` file. 

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

The data model definition is made up of:

- Models (model primitives) that define a number of fields, including relations between models
- Enums (enum primitives) (if your connector supports Enums)
- Attributes and functions that change the behavior of fields and models

The corresponding database looks like this:
![UML diagram](assets/blogging-platform.png) 
## 
### Migrations
In Prisma, migrations are a way to manage and apply changes to your database in a controlled and consistent way.

Prisma Migrate enables you to:
- Keep your database schema in sync with your Prisma schema as it evolves and
- Maintain existing data in your database
**Prisma Migrate** can be considered a hybrid database schema migration tool, meaning it has both of declarative and imperative elements:
- Declarative: The data model is described in a declarative way in the Prisma schema. Prisma Migrate generates SQL migration files from that data model.
- Imperative: All generated SQL migration files are fully customizable. Prisma Migrate hence provides the flexibility of an imperative migration tool by enabling you to modify what and how migrations are executed (and allows you to run custom SQL to e.g. make use of native database feature, perform data migrations, ...).

To run a migration, execute the following command
```
npx prisma migrate dev --name MIGRATION-NAME
```
This command creates a migration based on changes in the schema.prisma file. The ```--name``` parameter allows you to give a descriptive name to the migration.

For example:
```
npx prisma migrate dev --name initial-migration
```
### Prisma Client
The Prisma Client is an auto-generated and type-safe database client that you use to interact with your database in a Node.js or TypeScript application. 

It’s generated based on the models you define in your Prisma schema (schema.prisma) and provides a simple, intuitive, and type-safe API for CRUD operations, filtering, pagination, and more.

To generate a client, we use the command:
```
npx prisma generate
```
This command reads your schema and generates the Prisma Client inside the node_modules directory.

During migrations, @prisma/client package should be installed automatically to help you in working with prisma client.

If it is not already installed, run the command:
```
npm install @prisma/client
```


