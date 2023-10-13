# NOTE: the Heroku Buildpack release artifact will no longer be maintained by Metabase, as we strongly encourage everyone to either deploy in Heroku via containers or fork this repository. Please see announcement https://www.metabase.com/releases/Metabase-0.45

Heroku Buildpack for Metabase

Add the following to your app.json:

"buildpacks": [
  {
    "url": "https://github.com/ably-forks/metabase-buildpack"
  }
]

## Updates

Check the version number in `bin/version` to the required Metabase version

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
