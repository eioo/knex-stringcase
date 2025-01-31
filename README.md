# knex-stringcase

**Used with [npm knex](https://www.npmjs.com/package/knex) to convert database column names for use by a node application.**

By default this library assumes your database columns use snakecase `my_key` and your node application uses camelcase `myKey`, however these settings can be changed.

## Why

If the database has column names that are all in snakecase for example (a common practice), then that can be sub-ideal in your application.

With this library:

```javascript
const user = await db('users')
    .first('key', 'isVerified')
    .where({ id: params.userId, deletedAt: null });
```

Returns an object `{ key: 'xxxx', isVerified: true }` from a database where columns are named `id`, `key`, `is_verified`, and `deleted_at`. Removing your snakecase concerns.

## How

By leveraging these configuration options provided by knex `postProcessResponse` and `wrapIdentifier`.

* http://knexjs.org/#Installation-post-process-response

* http://knexjs.org/#Installation-wrap-identifier

Knex provides these options but this library acts as a helper to make the conversions simpler.

## Installation

```
npm i knex --save
npm i knex-stringcase --save
```

## Usage

```javascript
const knex = require('knex');
const knexStringcase = require('knex-stringcase');

const configFromKnexReadme = {
  client: 'mysql',
  connection: {
    host : '127.0.0.1',
    user : 'your_database_user',
    password : 'your_database_password',
    database : 'myapp_test'
  }
};

const options = knexStringcase(configFromKnexReadme);
const db = knex(options);
```

The two knex config options this library overrides are `postProcessResponse` and `wrapIdentifier`. If you provide those options they will be run after string conversion has already taken place. If you wish to run before conversion has taken place use `beforePostProcessResponse` and `beforeWrapIdentifier` instead.

### TypeScript usage

```typescript
import * as knex from 'knex';
import * as knexStringcase from 'knex';

const configFromKnexReadme = {
  client: 'mysql',
  connection: {
    host : '127.0.0.1',
    user : 'your_database_user',
    password : 'your_database_password',
    database : 'myapp_test'
  }
};

const options = knexStringcase<knex.Config>(configFromKnexReadme);
const db = knex(options);
```


## New options

#### beforePostProcessResponse (result: array|object, queryContext: object) => array|object

A function which runs before modifications made by this library if needed.

#### beforeWrapIdentifier (value: string, queryContext: object) => string

A function which runs before modifications made by this library if needed.

#### dbStringcase <default: 'snakecase'>

A function or a string which describes how keys should be modified when headed to the database. If a string is provided keys will be modified by their respective function found in [npm stringcase](https://www.npmjs.com/package/stringcase). Alternatively a function can be passed, taking the string in its current state which will give you more control to suit your needs.

This parameter may be an array describing more than one alteration in sequence. eg `['snakecase', 'uppercase']`.

#### appStringcase <default: 'camelcase'>

A function or a string which describes how keys should re-enter your application from the database. This attribute may also be be an array and operates very similarly to `dbStringcase` above.

#### ignoreStringcase (obj: object) => boolean

A function which can be used to skip conversion on objects if needed. If true the object is not converted and will be returned as is. This is useful in case you are using moment for your dates for example.

`obj => moment.isMoment(obj)`

## Contribute

Sure!
