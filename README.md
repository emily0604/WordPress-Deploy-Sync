
# Gulp WordPress Deploy & Sync

A WordPress Deployment & Sync workflow that **doesn't kill braincells**

### Table of Contents

- [Installation](#installation)
- [Deploying](#deploying)
- [Syncing](#syncing)

### Installation

#### Install **wordpress-deploy-sync**:

```bash
npm install wordpress-deploy-sync --save
````

#### Create a `config.json` file:

The configuration file holds all the relevant database, user and path information. You will have to create one of these in the root of your theme folder (a default example of the schema is below).

```json
{
  "local": {
  	"url": "http://localhost:8888",
    "db_name": "local_db_name",
    "db_user": "local_db_username",
    "db_password": "local_db_password",
    "db_host": "localhost",
    "port": 8889,
    "dump": "/applications/MAMP/library/bin/mysqldump"
  },
  "remote": {
    "theme": "your_theme_directory",
    "url": "http://domain.com",
    "db_name": "remote_db_name",
    "db_user": "remote_db_username",
    "db_password": "remote_db_password",
    "db_host": "localhost",
    "port": 3306,
    "ftp": {
      "host": "ftp.domain.com",
      "username": "ftp_username",
      "password": "ftp_password",
      "destination": "./public_html/wordpress/"
    }
  }
}
````

**Note:** Ensure that you add `config.json` to your `.gitignore` as it's likely to have private credentials you don't want exposed publically or checked into a repo.

**Note:** Don't add trailing slashes to `local.url` or `remote.url` values.

**Note:** You shouldn't have to update values like `local.port` or `local.dump` unless you've changed where [MAMP]() has been installed or is running the MySQL server.

**Note:** The `remote.ssh.desination` value should be the path to the root of your WordPress install is on your server.

#### Enable Remote MySQL Access:

For many hosting provides (like **BlueHost**) you will have to enable remote MySQL access. If you have to provide your IP address to *whitelist* you can use a service like [whatsmyip.org](http://www.whatsmyip.org/) or run the command `ifconfig` in a terminal session.

### Initialization

`wordpress-deploy-sync` expects config options to be passed to it in order for it to find and map everything correctly. Using a file like the `config.json` example above is the easiest way to go.

```javascript
var config      = require( 'config' );
var wordpress   = require( 'wordpress-deploy-sync' );
wordpress( config ).deploy();
````

#### Useage with Gulp:

`wordpress-deploy-sync` can be used on it's own or with a task runner. An example of how you can integrate it your `gulpfile.js` is below:

```javascript
var gulp        = require( 'gulp' );
var config      = require( 'config' );
var wordpress   = require( 'wordpress-deploy-sync' )( config );

gulp.task( 'deploy', function () {
  wordpress.deploy();
});

gulp.task( 'sync', function () {
  wordpress.sync();
});
````

### Deploying

Gulp WordPress Deploy & Sync will generate a `./sync` folder in your theme directory. This will store things like a backup copy of your local and remote databases as well as any content from your `wp-contents/plugins` or `wp-contents/uploads` folders. These copies are required when running a `gulp sync` which is perfect for working with a team over Github (as you will need to check in this content to stay consistent).

**Note:** You will need to be running MAMP/WAMP etc. to properly run `deploy`.

```bash
gulp deploy
````

### Syncing

When you're working with a team of people on a WordPress you'll want to check in code. Before doing this, you should run `gulp deploy` to ensure that all relevant *static assets* and your local database will be reflected in your Git repo. If you've pulled down someones work (ie. `git pull`) and they've made changes to the WordPress database, plugins or uploads, then it's a good time to run `gulp sync`.

**Note:** You will need to be running MAMP/WAMP etc. to properly run `sync`.

```bash
gulp sync
````

`gulp sync` looks in the `./sync/` folder and will compare/update your local database and `wp-contents` folder.
