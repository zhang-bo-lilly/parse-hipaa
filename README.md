# parse-postgres

Example of how to run [parse-server](https://github.com/parse-community/parse-server) with [postgres](https://www.postgresql.org). This also includes [parse-dashboard](https://github.com/parse-community/parse-dashboard) for viewing/modifying your data. [GraphQL](https://graphql.org) is also enabled and the playground can be accessed via parse-dashboard. To get started simply type:

```docker-compose up```

Imporant Note: On the very first run, the "parse-server"(which will show up as "parse_1" in the console) will error a few times because it can't connect to postgres (the "db") container. This is because postgres is still initializing, let it keep running and eventually you will see something like:

```db_1         | PostgreSQL init process complete; ready for start up.```

The parse-server container will then attempt to connect to the postgres container again and when it's complete you will see: 

```parse_1      | parse-server running on port 1337.
parse_1      | publicServerURL: http://localhost:1337/parse, serverURL: http://parse:1337/parse
parse_1      | GraphQL API running on http://localhost:1337/parsegraphql
parse_1      | info: Parse LiveQuery Server starts running```
```
You may also see(ignore these lines): 

```db_1         | 2020-03-18 21:59:21.550 UTC [105] ERROR:  duplicate key value violates unique constraint "pg_type_typname_nsp_index"
db_1         | 2020-03-18 21:59:21.550 UTC [105] DETAIL:  Key (typname, typnamespace)=(_SCHEMA, 2200) already exists.
db_1         | 2020-03-18 21:59:21.550 UTC [105] STATEMENT:  CREATE TABLE IF NOT EXISTS "_SCHEMA" ( "className" varChar(120), "schema" jsonb, "isParseClass" bool, PRIMARY KEY ("className") )
db_1         | 2020-03-18 21:59:21.550 UTC [106] ERROR:  duplicate key value violates unique constraint "pg_type_typname_nsp_index"
...
```

The lines above are output from parse because they attempt to configure the postgres database everytime. They will not hurt your database.

Your parse-server is binded to all of your interfaces on port 1337/parse and be can be accessed as such, e.g. `http://localhost:1337/parse`.

Parse-dashboard is binded to your localhost on port 4040 and can be accessed as such, e.g. `http://localhost:4040/`. The default login for the parse dashboard is username: "parse", password: "1234". Feel free to change the password in the [postgres-dashboard-config.json](https://github.com/netreconlab/parse-postgres/blob/master/parse-dashboard-config.json#L14). Note that ideally the password should be hashed by using something like [bcrypt-generator](https://bcrypt-generator.com) and setting "useEncryptedPasswords": false".

Note that postgres is not binded to your interfaces and is only local to the docker virtual network. This was done on purpose as the parse and parse-desktop is already exposed. 

If you want to persist the data in the database, you can uncomment the volume lines in [docker-compose](https://github.com/netreconlab/parse-postgres/blob/master/docker-compose.yml#L41)

The standard configuration can be modified to your liking by editing [index.js](https://github.com/netreconlab/parse-postgres/blob/master/index.js). Here you can add/modify things like push notifications, password resets, etc. This file as an express app and some examples provided from parse can be found [here](https://github.com/parse-community/parse-server#using-expressjs). Note that there is no need to rebuild your image when modifying files in the "index.js" file since it is volume mounted, but you will need to restart the parse container for your changes to take effect.

For verfying and cleaning your data along with other added functionality, you can add [Cloud Code](https://docs.parseplatform.org/cloudcode/guide/) to the [cloud](https://github.com/netreconlab/parse-postgres/tree/master/cloud) folder. Note that there is no need to rebuild your image when modifying files in the "cloud" folder since this is volume mounted, but you may need to restart the parse container for your changes to take effect.

The mongo version for this isn't shown as there are many examples online on how to deploy with mongo and mongo is part of the default documentation for [parse-server](https://github.com/parse-community/parse-server). The purpose of this repo is to show how to integrate with postgres and provide something out-of-the-box like [parse-server-example](https://github.com/parse-community/parse-server-example).

A parse-server BaaS such as this can be made HIPAA compliant by swapping out the "db" container for variants such as [postges]()(will post later) or [mongo](https://github.com/netreconlab/hipaa_mongodb).

**Use at your own risk. There is not promise that this is HIPAA compliant and we are not responsible for any mishandling of your data**
