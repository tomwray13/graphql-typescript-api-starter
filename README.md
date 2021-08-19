# GraphQL TypeScript API Starter

This is a starter repo for GraphQL TypeScript backends. If you want to learn how this starter was built from scratch, take a look at the [accompanying detailed blog post](https://tomray.dev/setup-and-deploy-graphql-server).

This starter uses the following stack:

- Express
- Apollo Server
- Prisma
- Docker
- TypeScript
- Postgres
- Heroku

## Initial set up

You can either press the Green Button **Use this template** in the top right in Github, or you can use `git clone`:

```shell
git clone https://github.com/tomwray13/graphql-typescript-api-starter.git <YOUR_DIRECTORY_NAME>
```

Then push your code to your own Github repository:

```shell
git remote rename origin upstream
git remote add origin <URL_TO_GITHUB_REPO>
git branch -M main
git push -u origin main
```

Once you have your own repository set up, install the local package dependencies:

```shell
npm install
```

You can now run your server locally and open up the playground:

```shell
npm run dev
```

See the full list of scripts in the `package.json` file.

## Local database set up

Copy the `env.example` file to create your own `env` file:

```shell
cp env.example .env
```

You can (optionally) tweak the user and password in the `env` file for the local database, but make sure you make the same edits in the `docker-compose.yml` file.

Run this command to launch your Docker local Postgres server:

```shell
docker-compose up -d
```

If you're using the Docker extension in VSCode, you can use this instead of running the previous command in the terminal.

## Using Prisma

To enable querying and mutations to the database with Prisma, you'll first need to add models in the `prisma.schema` file. For example:

```graphql
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model Board {
  id          Int       @id @default(autoincrement())
  createdAt   DateTime  @default(now())
  updatedAt   DateTime  @updatedAt
  title       String
  description String?
  path        String    @unique
}
```

Anytime you make changes to the `prisma.schema` file, you'll need to run migrations so the changes are applied to your local database.

There is a script you can use for this:

```shell
npm run migrate
```

This will prompt you to give a name for the migration.

To access the data in the database, you'll need to make use of Prisma Client.

You just need to uncomment this line in the `index.ts` file:

```ts
// import { prisma } from "./prisma/client";
```

And then use Prisma in your resolvers to access the database. For example:


```ts
...

const resolvers = {
  Query: {
    boards: () => {
      return prisma.board.findMany()
    },
    board: (parent, args: any) => {
      return prisma.board.findUnique({
        where: {
          id: Number(args.id)
        }
      })
    },
  },
};

...
```

## Deplyment to Heroku

Login to Heroku and create a new project. and connect your Github repository to the Heroku project, enabling automatic deploys.

Go to the **Resources** tab, search for "Heroku Postgres" and enable the "Hobby Dev" plan.

Then go to the **Settings** tab and add an ENV variable in the Config Vars section called `NPM_CONFIG_PRODUCTION=false`.

Finally, go back to the **Deploy** tab and press the Deploy Branch button.

More detailed step-by-step help is detailed in [the blog post](https://tomray.dev/setup-and-deploy-graphql-server).
