# Business comes and says: We need to rewrite everything from Node to Deno...

Let's start with the riddle, who is shown in this photo? 🙂

<p align="center">
  <a href="https://en.wikipedia.org/wiki/Ryan_Dahl" rel="nofollow">
    <img src="./img/ryan-dahl.jpeg" width="482"  height=" 307" alt="Ryan dahl">
  </a>
</p>

Most likely, you have worked with his product 😉

<details>
  <summary>Answer</summary>

  [**Ryan Dahl**](https://en.wikipedia.org/wiki/Ryan_Dahl) is a software engineer and the original developer of the **`Node.js`** (~2009 year). In January 2012, Dahl announced that he turn over the reins to [NPM](https://en.wikipedia.org/wiki/Npm_(software)) creator.

  In 2018 he made a presentation ['10 things I regret about Node.js'](https://www.youtube.com/watch?v=M3BM9TB-8yA&vl=en) at the [JSConf](https://jsconf.com/) conference. Also, at this presentation, he announced [Deno](https://deno.land/) — a secure runtime for JavaScript and TypeScript.
</details>

In this article, we will take a look at `Deno` and how it compares to `Node.js` to help understand what they have in common, how they differ and is it real that `Deno` will kill `Node.js`?

## Program for today:
##  - Things that work differently than in `Node.js`
## - `Node.js` & `io.js` & `Deno`
## - Easter eggs
## - Conclusions

 ## Project

Let's imagine a situation: Business comes to us and says us to rewrite the project from [Node](https://nodejs.org) to [Deno](https://deno.land).

But why? Who knows 🙂

For now, let's imagine that there is no other way, and we need to do it. But then what? Do we really have to rewrite all lines of code in the project?

Let's check this with an example of a small project.

```
├── node_modules
├── public
├── src/
│   ├── api/
│   │   ├── /* apis */
│   │   └── api.ts
│   ├── common/
│   │   ├── enums/
│   │   │   ├── /* enums */
│   │   │   └── index.ts
│   │   ├── interfaces/
│   │   │   ├── /* interfaces */
│   │   │   └── index.ts
│   │   └── types/
│   │       ├── /* types */
│   │       └── index.ts
│   ├── helpers/
│   │   ├── /* helpers */
│   │   └── index.ts
│   ├── repositories/
│   │   ├── /* repositories */
│   │   └── repositories.ts
│   ├── services/
│   │   ├── /* services */
│   │   └── services.ts
│   └── server.ts
├── .env
├── .eslintrc.yml
├── package-lock.json
├── package-lock.json
└── tsconfig.json
```

<p align="center">
  Project structure
</p>


```json
// package.json

{
  "private": true,
  "scripts": {
    "lint:js": "eslint --ext .js,.ts src",
    "lint": "npm run lint:js",
    "start": "nodemon --exec ts-node --files -r dotenv/config ./src/server.ts"
  },
  "dependencies": {
    "axios": "0.21.1",
    "dotenv": "8.2.0",
    "koa": "2.13.1",
    "koa-bodyparser": "4.3.0",
    "koa-router": "10.0.0",
    "koa-static": "5.0.0"
  },
  "devDependencies": {
    "@types/jest": "26.0.22",
    "@types/koa": "2.13.1",
    "@types/koa-bodyparser": "4.3.0",
    "@types/koa-router": "7.4.2",
    "@types/koa-static": "4.0.1",
    "@types/node": "14.14.41",
    "@typescript-eslint/eslint-plugin": "4.22.0",
    "@typescript-eslint/parser": "4.22.0",
    "eslint": "7.24.0",
    "jest": "26.6.3",
    "nodemon": "2.0.7",
    "prettier": "2.2.1",
    "ts-jest": "26.5.5",
    "ts-node": "9.1.1",
    "typescript": "4.2.4"
  }
}
```

<p align="center">
  Dependencies
</p>

```ts
// src/server.ts

import { resolve } from 'path';
import Koa from 'koa';
import serve from 'koa-static';
import Router from 'koa-router';
import bodyParser from 'koa-bodyparser';
import { ENV } from './common/enums';
import { initRepositories } from './repositories/repositories';
import { initServices } from './services/services';
import { initApis } from './api/api';

const app = new Koa();

app.use(bodyParser());

const repositories = initRepositories();

const services = initServices({
  repositories,
});

const apiRouter = initApis({
  Router,
  services,
});

app.use(apiRouter.routes());

app.use(serve(resolve(__dirname, '../public')));

app.listen(ENV.APP.SERVER_PORT);

console.log(`Listening to connections on port — ${ENV.APP.SERVER_PORT}`);
```

<p align="center">
  Root file
</p>

Nothing special. The project is written using a node-framework - [Koa](https://koajs.com) (next generation of the [Express](https://expressjs.com) node-framework).

What it can do:

- Serve static content

  ```
  > curl "http://localhost:3000"

  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="UTF-8" />
      <meta http-equiv="X-UA-Compatible" content="IE=edge" />
      <meta name="viewport" content="width=device-width, initial-scale=1.0" />
      <title>Document</title>
    </head>
    <body>
      <h1>Hello world 😛</h1>
    </body>
  </html>
  ```

- Has a posts api (wrapper for [jsonplaceholder](https://jsonplaceholder.typicode.com) service).

  ```
  > curl "http://localhost:3000/api/v1/posts/1"

  {"userId":1,"id":1,"title":"sunt aut facere repellat provident occaecati excepturi optio reprehenderit","body":"quia et suscipit\nsuscipit recusandae consequuntur expedita et cum\nreprehenderit molestiae ut ut quas totam\nnostrum rerum est autem sunt rem eveniet architecto"}
  ```

- Has a books CRUD api (all data is stored in `json` format).


  ```
  > curl "http://localhost:3000/api/v1/books/1"

  {"id":"1","name":"DDD"}
  ```

That's all. Everything works fine!

Let's move on to the most interesting part.

## From Node to Deno

```
> rm -rf node_modules package-lock.json package.json
```

```
error: Cannot resolve module "file:///Users/vladislavzubko/Documents/projects/node-deno/src/services/services" from "file:///Users/vladislavzubko/Documents/projects/node-deno/src/server.ts".
    at file:///Users/vladislavzubko/Documents/projects/node-deno/src/server.ts:8:0
```


```
error: Uncaught PermissionDenied: Requires env access to "PORT", run again with the --allow-env flag
    SERVER_PORT: Deno.env.get('PORT'),
```

```
error: Uncaught ReferenceError: require is not defined
const contentType = require('./content-type.enum');
```

```
error: Uncaught ReferenceError: module is not defined
module.exports = {
```

```json
// .vscode/settings.json

{
  "deno.enable": true,
  "deno.lint": true,
  "deno.unstable": true
}
```

Extension

```ts
// src/dependencies.ts

import 'https://deno.land/x/dotenv/load.ts';
import * as Oak from 'https://deno.land/x/oak/mod.ts';

export { Oak };
```

```ts
// src/server.ts

import { Oak } from './dependencies.ts';
import { ENV } from './common/enums/index.ts';
import { initRepositories } from './repositories/repositories.ts';
import { initServices } from './services/services.ts';
import { initApis } from './api/api.ts';

const app = new Oak.Application();

const repositories = initRepositories();

const services = initServices({
  repositories,
});

initApis({
  Router: Oak.Router,
  services,
  app,
});

app.use(async (ctx) => {
  await Oak.send(ctx, ctx.request.url.pathname, {
    root: 'public',
    index: 'index.html',
  });
});

app.listen({
  port: ENV.APP.SERVER_PORT,
});

console.log(`Listening to connections on port — ${ENV.APP.SERVER_PORT}`);
```

```
> curl "http://localhost:3000/api/v1/posts/1"

{"userId":1,"id":1,"title":"sunt aut facere repellat provident occaecati excepturi optio reprehenderit","body":"quia et suscipit\nsuscipit recusandae consequuntur expedita et cum\nreprehenderit molestiae ut ut quas totam\nnostrum rerum est autem sunt rem eveniet architecto"}
```

```
> curl "http://localhost:3000/api/v1/books/1"

{"id":"1","name":"DDD"}
```
