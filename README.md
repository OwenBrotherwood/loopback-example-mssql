#loopback-example-mssql

**Prerequisites**:

 - Follow [Getting started with LoopBack](http://docs.strongloop.com/display/LB/Getting+started+with+LoopBack).
 - Understand [LoopBack models](http://docs.strongloop.com/display/LB/Defining+models).

**Quick start**:

```
git clone https://github.com/strongloop/loopback-example-mssql.git
cd loopback-example-mssql
npm install
```
Then run any script in `server/bin` (for example `node server/bin/discover-schema.js`),

General procedure:
  - [1. Create the application](#1-create-the-application)
  - [2. Install the connector](#2-install-the-connector)
  - [3. Configure the datasource](#3-configure-the-datasource)
  - [4. Add a model](#4-add-a-model)
  - [5. Add a script to migrate data](#5-add-a-script-to-migrate-data)
  - [6. Add a script to discover a schema](#6-add-a-script-to-discover-a-schema)
  - [7. Add a script to discover and build models](#7-add-a-script-to-discover-and-build-models)
  - [8. Conclusion](#8-conclusion)

###1. Create the application

####Application information

- Name: `loopback-example-mssql`
- Directory to contain the project: `loopback-example-mssql`

```
slc loopback loopback-example-mssql
... # follow the prompts
cd loopback-example-mssql
```

###2. Install the connector

```
npm install --save loopback-connector-mssql
```

###3. Configure the datasource

####Datasource information
- Datasource: `accountDs`
- Connector: `Microsoft SQL Server`

```
slc loopback:datasource accountDs
... # follow the prompts
```

Add the [datasource configurations](https://github.com/strongloop/loopback-example-mssql/blob/master/server/datasources.json#L9-L13) to
[`server/datasources.json`](https://github.com/strongloop/loopback-example-mssql/blob/master/server/datasources.json).

> We provide a demo server for convenience sake, but feel free to use your own database server.

###4. Add a model

####Model information
- Name: `Account`
  - Datasource: `accountDs`
  - Base class: `PersistedModel`
  - Expose via REST: `Yes`
  - Custom plural form: *Leave blank*
  - Properties
    - `email`
      - String
      - Not required
    - `createdAt`
      - Date
      - Not required
    - `lastModifiedAt`
      - Date
      - Not required

```
slc loopback:model Account
... # follow the prompts
```

###5. Add a script to migrate data

Create a directory for to store scripts.

```
mkdir server/bin
```

> `bin` is a directory name commonly used for executable files on unix and unix-like systems.

Create [`automigrate.js`](https://github.com/strongloop/loopback-example-mssql/blob/master/server/bin/automigrate.js) inside the
[`bin`](https://github.com/strongloop/loopback-example-mssql/blob/master/server/bin) directory.

> [`datasSource.automigrate()`](https://github.com/strongloop/loopback-example-mssql/blob/master/server/bin/automigrate.js) requires INSERT object, CREATE DDL, and DROP DDL rights to execute properly.

####Test the script

> #####WARNING
> [`dataSource.automigrate()`](https://github.com/strongloop/loopback-example-mssql/blob/master/server/bin/automigrate.js#L18) creates a new table in the database if it doesn't exist. If the table already exists, it will be **DESTROYED** and **ALL** existing data will be dropped. If you want to keep the existing data, use `datasource.autoupdate()` instead.

```
node server/bin/automigrate.js
```

This script creates [two models](https://github.com/strongloop/loopback-example-mssql/blob/master/server/bin/automigrate.js#L5-L14) in the
[specified data source](https://github.com/strongloop/loopback-example-mssql/blob/master/server/bin/automigrate.js#L16).

> You can view the newly inserted data using built-in [API explorer](http://docs.strongloop.com/display/LB/Use+API+Explorer). Start the application with `slc run` and browse to [`localhost:3000/explorer`][explorer] to inspect the data.

###6. Add a script to discover a schema

> *Discovery* is the process of reverse engineering a LoopBack model from an existing database schema.

Create [`discover-schema.js`](https://github.com/strongloop/loopback-example-mssql/blob/master/server/bin/discover-schema.js) inside the
[`bin` directory](https://github.com/strongloop/loopback-example-mssql/blob/master/server/bin).

####Test the script

```
node server/bin/discover-schema.js
```

You should see:

```
{
  "name": "Account",
  "options": {
    "idInjection": false,
    "mssql": {
      "schema": "dbo",
      "table": "Account"
    }
  },
  "properties": {
    "id": {
      "type": "Number",
      "required": true,
      "length": null,
      "precision": 10,
      "scale": 0,
      "id": 1,
      "mssql": {
        "columnName": "id",
        "dataType": "int",
        "dataLength": null,
        "dataPrecision": 10,
        "dataScale": 0,
        "nullable": "NO"
      }
    },
    "email": {
      "type": "String",
      "required": false,
      "length": 255,
      "precision": null,
      "scale": null,
      "mssql": {
        "columnName": "email",
        "dataType": "nvarchar",
        "dataLength": 255,
        "dataPrecision": null,
        "dataScale": null,
        "nullable": "YES"
      }
    },
    "createdat": {
      "type": "Date",
      "required": false,
      "length": null,
      "precision": null,
      "scale": null,
      "mssql": {
        "columnName": "createdAt",
        "dataType": "datetime",
        "dataLength": null,
        "dataPrecision": null,
        "dataScale": null,
        "nullable": "YES"
      }
    },
    "lastmodifiedat": {
      "type": "Date",
      "required": false,
      "length": null,
      "precision": null,
      "scale": null,
      "mssql": {
        "columnName": "lastModifiedAt",
        "dataType": "datetime",
        "dataLength": null,
        "dataPrecision": null,
        "dataScale": null,
        "nullable": "YES"
      }
    }
  }
}
```

###7. Add a script to discover and build models

Create [`discover-and-build.js`](https://github.com/strongloop/loopback-example-mssql/blob/master/server/bin/discover-and-build.js) in the
[`bin` directory](https://github.com/strongloop/loopback-example-mssql/blob/master/server/bin).

####Test the script

```
node server/bin/discover-and-build.js
```

You should see:

```
[ { id: 1,
    email: 'foo@bar.com',
    createdAt: Mon Jan 12 2015 22:38:13 GMT-0800 (PST),
    lastModifiedAt: Mon Jan 12 2015 22:38:13 GMT-0800 (PST) },
  { id: 2,
    email: 'baz@qux.com',
    createdAt: Mon Jan 12 2015 22:38:13 GMT-0800 (PST),
    lastModifiedAt: Mon Jan 12 2015 22:38:13 GMT-0800 (PST) } ]
```

> Your `createdat` and `lastmodifiedat` dates will be different.

The resulting objects are fully functional
[LoopBack models](https://github.com/strongloop/loopback-example-mssql/blob/master/server/bin/discover-and-build.js#L7) and thus contain all the
features provided by LoopBack such as
[`find()`](https://github.com/strongloop/loopback-example-mssql/blob/master/server/bin/discover-and-build.js#L10), etc.

###8. Conclusion

You've successfully implemented various Microsoft SQL Server database features provided by LoopBack.
See the [official documentation](http://docs.strongloop.com/display/LB/Defining+models) and
[loopback-connector-mssql](https://github.com/strongloop/loopback-connector-mssql) for more information.

<div class="confluence-hide">
---

- [Next tutorial][next-tutorial]
- [All tutorials][all-tutorials]

[all-tutorials]: https://github.com/strongloop/loopback-example
[explorer]: http://localhost:3000/explorer
[localhost]: http://localhost:3000
[next-tutorial]: https://github.com/strongloop/loopback-example-model-relations
</div>
