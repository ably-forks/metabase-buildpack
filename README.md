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
heroku addons:create heroku-postgresql:mini -a metabase-upgrade-test
```

Next you would want to deploy a copy of Metabase to this test Heroku app, for that you need to follow the instructions in the README of our metabase repository.

After you've deployed Metabase and you confirm it is working you can merge the PR and destroy the test app:

```
heroku apps:destroy -a metabase-upgrade-test
```


## Empty commits for deploying updates

Clone this repository from Heroku:

~~~
$ heroku login
$ heroku git:clone -a my-metabase 
$ cd my-metabase
~~~

You might also need to reset your local `main` to match Heroku if someone else deployed:

~~~
$ git fetch heroku
$ git reset --hard heroku/main
~~~

Use an empty commit to trigger a fresh build on Heroku:

~~~
$ git commit --allow-empty -m Upgrade metabase
$ git push heroku main
~~~
