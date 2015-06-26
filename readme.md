## Deploying to Heroku using Node, Express, Mongo and Mongoose

### To start:

1. Make sure you have an account with heroku: [https://www.heroku.com/](https://www.heroku.com/)

2. Make sure you have installed the heroku toolbelt - [https://toolbelt.heroku.com/](https://toolbelt.heroku.com/)

3. Make sure that you have initialized a git repository by running `git init` and then add the heroku remote using `heroku git:remote -a NAME_OF_YOUR_APP`

* Create a `Procfile` by running `echo "web: node app.js" > Procfile`. This file must be named with a capital P and with no extention. **It also must be in the same directory as your app.js**

3. To add an environment variable, we use the `heroku config:set NAME_OF_ENVIRONMENT_VARIABLE=VALUE` so let's add an environment variable called PORT.

```
heroku config:set PORT=80 -a NAME_OF_YOUR_APP
```

* In your `app.js` file, we need to tell heroku to run on PORT 80 instead of our usual 3000,  the `port` argument in your `app.listen` function so that it looks for a `proccess.env.PORT` environment variable first.  Example:

```
app.listen(process.env.PORT || 3000)
```

### Adding MongoLab to Heroku

Just like we need someone to host our application, we need someone to host our database server! Let's use mongolab (one of the most popular if not the most popular "MongoDB as a service"" services)

```
 heroku addons:create mongolab
```

This will create an environment variable called MONGOLAB_URI for us, which we will use when connecting to our production database. In our `models/index.js` file add the following to the `mongoose.connect` method.

```
mongoose.connect( process.env.MONGOLAB_URI || "mongodb://localhost/YOUR_DB_NAME")
```

And we are ready to integrate mongolab.

## Deploying

Now that you're potentially all setup then you just need to `git add` and `commit`.


```
git add -A
git commit -m "nice message goes here"
git push heroku master
```

If you are having an issue with your RSA key or it is prompting you to type a password, enter in terminal `ssh-keygen -t rsa` and press enter until you are brought back to your terminal prompt. When that is done, type in `heroku keys:add` (if you see more than one, do **not** add the github one) and try git push heroku master again.

Once you have done this, make sure you have scaled your dynos to use the free plan

```
heroku run ps:scale web=1 -a YOUR_APP_NAME
```

You should now be able to visit your application by entering the following in the terminal:

```
heroku open
```

Once you have done this, to start taking a look at your server logs (essential for debugging) run this command:

```
heroku logs -t 
```

To get to your mongolab dashboard you can also type

```
heroku addons:open mongolab -a NAME_OF_YOUR_APP
```

### Using a mongo shell in production

If you realllllllly want to use a mongo shell you will need your username and password to connect.

1 - head over to the mongolab console using the command above

2 - Where you see "To connect using the shell:", copy that url, it should look something like this: 

```
mongo ds043952.mongolab.com:43952/heroku_75z2z4dz -u <dbuser> -p <dbpassword>
```

3 - type in heroku config -a NAME_OF_YOUR_APP to find your db username and password, you will see something like this

```
mongodb://heroku_75z2z4dz:71rang04t4bkpjf83vvhh1c421@ds043952.mongolab.com:43952/heroku_75z2z4df`
```

Your username is what comes after the first `//` so in the case above, it is `heroku_75z2z4dz` and the password is what comes right after, between the `:` and `@`, so in this case `71rang04t4bkpjf83vvhh1c421`is the password, which means our command to run the shell looks like this:

```
mongo ds043952.mongolab.com:43952/heroku_75z2z4dz -u heroku_75z2z4dz -p 71rang04t4bkpjf83vvhh1c421
```