# Heroku Buildpack for Metabase

Add the following to your app.json:

~~~json
"buildpacks": [
  {
    "url": "https://github.com/ably-forks/metabase-buildpack"
  }
]
~~~

## Upgrading Metabase

The value specified in `bin/version` is what will be downloaded when the slugs are built on Heroku. To bump the version simply set this file to the desired version and open a pull request.

### Testing upgrades

To test updates before merging to `main`, you'll need to spin up a test Heroku app that uses the PR. To do that you need to have the [Heroku CLI](https://cli.heroku.com) installed.

Ensure you're logged in:

```
heroku login
```

Create a new app, set the first buildpack to `heroku/jvm`

```
heroku apps:create metabase-upgrade-test --team ably --buildpack heroku/jvm
```

Add the PR branch as a buildpack, not the branch name is specified after the `#` in the buildpack URL.

```
heroku buildpacks:add https://github.com/ably-forks/metabase-buildpack#branch-name-from-pr --app metabase-upgrade-test
```

Add a PostgreSQL database to the test app:

```
heroku addons:create heroku-postgresql:essential-0 --app metabase-upgrade-test
```

(Optional) Clone the production PostgreSQL config if you want to ensure the upgrade goes smooth:

Using Heroku's [Direct database-to-database copy](https://devcenter.heroku.com/articles/heroku-postgres-backups#direct-database-to-database-copies) feature you can clone the production database to your new app to ensure smooth sailing:

```
heroku pg:copy production-app-name::DATABASE_URL DATABASE_URL --app metabase-upgrade-test
```

Next you would want to deploy a copy of Metabase to this test Heroku app, for that you need to follow the instructions further down in this README.

After you've deployed Metabase and you confirm it is working you can merge the PR and destroy the test app:

```
heroku apps:destroy -a metabase-upgrade-test
```

## Deploying updates

Clone your metabase repository from Heroku:

~~~
heroku login
heroku git:clone --app my-metabase 
cd my-metabase
~~~

### Deploying to the test app

Switch the git remote to be the test app:

```
heroku git:remote --remote metabase-upgrade-test --app metabase-upgrade-test
```

Push to the test app:

```
git push metabase-upgrade-test --force
```
### Deploying to production

Ensure your Heroku remote is correct:

```
heroku git:remote --app my-metabase
```

You also need to reset your local `main` to match Heroku if someone else deployed:

~~~
$ git fetch heroku
$ git reset --hard heroku/main
~~~

Use an empty commit to trigger a fresh build on Heroku:

~~~
$ git commit --allow-empty -m "Upgrade metabase"
$ git push heroku main
~~~
