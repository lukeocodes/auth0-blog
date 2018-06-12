---
layout: post
title: "Ruby on Rails—Killer Workflow with Docker (Part 2)"
description: "Learn how to set up a killer dockerized workflow that will raise your productivity while developing Ruby on Rails applications."
longdescription: "In this series, you will learn how to set up a killer dockerized workflow that will raise your productivity while developing Ruby on Rails application. You will use tools like Docker, Docker Compose, Travis, and Heroku to set up a state-of-the-art workflow."
date: 2018-05-24 08:30
category: Technical Guide, Backend, Ruby On Rails
design:
  bg_color: "#4A4A4A"
  image: https://cdn.auth0.com/blog/ruby-workflow-docker/logo.png
author:
  name: "Vijayabharathi Balasubramanian"
  url: "https://www.pineboat.in/author/vijayabharathib/"
  mail: "yajiv.vijay@gmail.com"
  avatar: "https://cdn.auth0.com/blog/guest-authors/vijay.jpeg"
tags:
- ruby-on-rails
- docker
- ruby
- rails
- ci
- cd
- ci/cd
- continuous-integration
- continuous-delivery
- auth0
related:
- 2018-05-17-ruby-on-rails-killer-workflow-with-docker-part-1
- 2017-05-22-load-balancing-nodejs-applications-with-nginx-and-docker
- 2017-01-03-rails-5-with-auth0
---

**TL;DR:** By now, your development workflow must have a solid foundation based on [part 1](https://auth0.com/blog/ruby-on-rails-killer-workflow-with-docker-part-1). You have continuous testing and live reloading set up already. In this second and final part, you'll do three things. First, you will secure your app with [Auth0](https://auth0.com). Then, you'll establish a pipeline to automatically deploy your changes to [Heroku]. Lastly, you'll also use [Travis CI][Travis] as a quality gate to run tests before deployment.

{% include tweet_quote.html quote_text="Learn how to use @Docker to put together a modern development workflow for @rails." %}

## Identity Management with Auth0

It is better to have authentication from the start. It will force you to think about modeling records accordingly. This is where you can use the free-tier login given by [Auth0] to try it out.

### Auth0 Tenant and Client

If you have not done so yet, now is a good time to <a href="https://auth0.com/signup" data-amp-replace="CLIENT_ID" data-amp-addparams="anonId=CLIENT_ID(cid-scope-cookie-fallback-name)">sign up for a free Auth0 account</a>. After signing up, follow these instructions:

1. [Go to the Applications page in your Auth0 dashboard](https://manage.auth0.com/#/applications).
2. Click on the _Create Application_ button.
3. Input a new for the new application (e.g. "BookShelf").
4. Select _Regular Web Applications_ as the application type.
5. Click on the _Create_ button.

> This article uses tenant named *tugboat* (i.e. the domain is `tugboat.auth0.com`) and a client named *BookShelf*. 

Once the client is created, within the list of technologies shown, select **Ruby On Rails**. [Auth0] shows the [Quick Start Guide](https://auth0.com/docs/quickstart/webapp/rails/01-login) for rails by default. 

A cool feature of the guide is that the code samples show your client name and other details if you are logged in. You can reuse the code from the guide in most of the instructions used in this post, as the guide will show you code customized for your app and name you have chosen. 

It also has instructions for some of the most **commonly faced issues**. It will come in handy if you run into any such issues. The focus, for now, is on getting the authentication working and moving on to building the app.

[Auth0] reaches our application through callback URLs. So it is important to tell Auth0 client what the callback URL will be. Under the _Settings_ tab of your new application, find a text box that asks for **Allowed Callback URLs** and add this:

```bash
http://localhost:3000/auth/oauth2/callback
```

Then, hit the _Save Changes_ button on the bottom of the page. If you try to reach this URL on a browser now, you'll be getting a routing error. 

Time to fix it.

## Add Auth0 at Rails End

The following are the changes that you will perform at your back-end:

1. Add gem dependencies
2. Add middleware
3. Add a controller for authentication
4. Add secrets
5. Add Landing Pages

### Gem Dependencies

Add these two gems to your `Gemfile`. The `omniauth` one provides basic authentication capabilities while `omniauth-auth0` implements a strategy that allows authentication from multiple providers.

```Gemfile
# Gemfile
# ... leave all else intact 

gem 'omniauth', '~> 1.8.1'
gem 'omniauth-auth0', '~> 2.0.0'
# ...
```

These two lines are common for all environments. Hence, they need to be outside of any particular `:development` or `:test` group. You can indeed add these to the very end of the file.

Remember the usual drill to build the container. Stop the container if it is running and rebuild it:

```bash
docker-compose up --build
```

### Secrets Need To Stay So

The **client secret** key from Auth0 should be available for the Rails app, but that has to be a secret. One way is to add it as a plain environment variable. But secrets as environment variables are [not so safe](https://www.engineyard.com/blog/encrypted-rails-secrets-on-rails-5.1) either. 

Rails 5.2 gives an option to encrypt such secrets, in the name of **credentials**, store them in a file and commit them to version control. You can read more about it on [Engine Yard](https://www.engineyard.com/blog/rails-encrypted-credentials-on-rails-5.2). These credentials are stored in `credentials.yml.enc` while the key to decrypt the file is stored within `master.key` file. These two files are _automatically_ generated when you created a new Rails project. 

>**Tip**: It is very important that you do not lose `master.key` file. It is not and should not be added to the repository. Rails project adds `master.key` to `.gitignore` by default, so that it is excluded from version control. There is no way of recovering it if you permanently delete the file. In such cases, you might have to delete `credentials.yml.enc` and run `rails credentials:edit` to generate both the files from scratch. 

While the container is up and running on a terminal, Get into another terminal to access shell within the container:

```bash
docker-compose exec --user $(id -u):$(id -g) app /bin/bash
```

That takes you to the terminal within the container. Then you'll have a chance to set up credentials.

Client secret is the value from the Auth0 Application you just created (*BookShelf* in this case). You can find this and the other properties in the same _Settings_ tab where you added the callback URL.

When you are in the terminal within the container, run these commands:

```bash
EDITOR=nano rails credentials:edit
```

That command will warn you about `nano` editor not having enough permission. Not to worry. Hit `Enter`. That'll open up the file in an editor within the terminal. It will have `secret_key_base` entry by default, just leave that alone. Add following records right after `secret_key_base`:

```yml
# ....
auth0:
 client_id: <YOUR_AUTH0_CLIENT_ID>
 secret: <YOUR_AUTH0_CLIENT_SECRET>
```

Please make sure to replace `<YOUR_AUTH0_CLIENT_ID>` and `<YOUR_AUTH0_CLIENT_SECRET>` with the values from your Auth0 Application. The ones shown above are placeholders. Also, leave a new line after `secret` as the last line.

You can save the changes by following the keystrokes given in the editor:

- `Ctrl + o` will save the changes;
- `Ctrl + x` will close the editor;

**Warning:** the problem with using an editor in the terminal is, if you leave any syntax errors, it is difficult to open it again. `rails credentials:edit` throws error while trying to fix the very syntax error that is causing the issue. You might have to pull it from your previous commit and redo the changes. 

You can verify if your changes are saved properly by running the following command within the same shell:

```
rails credentials:show
```

You can now `exit` out of the shell.

To instruct rails to load secrets from the encrypted file. So, you need to uncomment `config.require_master_key = true` in the `config/environments/production.rb` file.

You will also set up the `RAILS_MASTER_KEY` on Heroku environment. But that can wait, as the local environment has the master key in the file `master.key`.

### Add Middleware Strategy

Omniauth has many implementations called strategies. This one from Auth0 allows the Auth0 client to interact with your Rails application. Add a new file named `auth0.rb` within `config/initializers` folder and input the following code:

```rb
# config/initializers/auth0.rb

Rails.application.config.middleware.use OmniAuth::Builder do
  provider(
    :auth0,
    Rails.application.credentials.auth0[:client_id],
    Rails.application.credentials.auth0[:secret],
    '<YOUR_AUTH0_DOMAIN>',
    callback_path: "/auth/oauth2/callback",
    authorize_params: {
      scope: 'openid email profile',
      audience: 'https://<YOUR_AUTH0_DOMAIN>/userinfo'
    }
  )
end
```

In this code, you will have to replace `<YOUR_AUTH0_DOMAIN>` with your own Auth0 domain (in my case: `tugboat.auth0.com`).

> **Note**: It is important to have `email` within the scope section for you to get the email as part of the information sent from Auth0. Read more about [scopes](https://auth0.com/docs/scopes/current) from Auth0.

### Controller To Handle Auth0 CallBack

Now, add a controller within Rails app to handle callback and failure from Auth0. You'll change it later to add user management and logout functionalities. For now, this will do.

```rb
# app/controllers/auth0_controller.rb
class Auth0Controller < ApplicationController
  def callback
    session[:userinfo] = request.env['omniauth.auth']
    redirect_to '/'
  end

  def failure
    @error_msg = request.params['message']
  end
end
```

And add relevant routes so that Rails can hand over calls to respective controller actions.

```rb
# config/routes.rb
Rails.application.routes.draw do
  root "home#show"
  get "/auth/oauth2/callback" => "auth0#callback"
  get "/auth/failure" => "auth0#failure"
end
```

This file will grow as you build the application. 

Bring the container down if it is already running and boot it up again to ensure middleware changes take effect.

```bash
docker-compose down
docker-compose up
```

If that one test you had is passing the automatic test run initiated by Guard, then you should be good to go.

### The Front Page Comes Alive

You are now ready to add that much-awaited Login button.

{% highlight html %}
<!-- app/views/home/show.html.erb -->
<section class="welcome card">
  <h1>Bookshelf</h1>
  <h2>Built on Ruby on Rails secured with Auth0</h2>
  <a class="btn green" href="/auth/auth0">Login</a>
</section>
<%= debug session[:userinfo] %>
{% endhighlight %}

If you had the container up and running with Guard, and the browser is connected to the live reload server, you should see the changes on the browser immediately.

The debug section will be blank for now, as there is no `userinfo` within session yet.

Click on that login link and it should take you to a beautiful page provided by [Auth0]. Just use the sign in option provided by Google (or any provider you've switched on within Auth0).

Once you authorize the app to use your domain, it should redirect back to the same home page on your app.

But this time, the `userinfo` from the session is printed for you on the page, along with email and name.  

**Congratulations!** You have effectively set up a trustable cloud authentication system that you can build upon. 

## Travis CI Integration

Next up is to get help from a nice bot. [Travis] allows you to test the application when you commit new changes to [GitHub] repository or when you create pull requests and even when you merge pull requests.

Once you set up a login within Travis-CI, you should be able to add your git repository to the list.

Click on that small `+` button just beside _My repositories_ on Travis Home Page. It should list down your git repositories. If you cannot find it, try _Sync Account_ once.

Flip the repository on using the checkbox on the left.

Click on the small cog that denotes settings and you should be able to select when do you want to build.

Switch on _Build only if .travis.yml is present_ option.

Back at the project folder, add a file named `.travis.yml` to the project root folder with this content:

```yml
language: ruby
cache: bundler
rvm:
  - 2.5.0
services:
  - postgresql
before_script:
  - cp config/database.travis.yml config/database.yml
  - psql -c 'create database auth0app_test;' -U postgres
script:
  - bin/rake db:migrate RAILS_ENV=test
  - bin/rake 
```

Translating that, you are instructing Travis to use `ruby`. The command `bundle install` is the one that'll take a long time, so you are caching that for future use. You are asking for a `postgresql` service to enable database.

The actual `script` section enables database migration. You do not have any database yet, but worth making it future proof. 

The section named`before_script` instructed Travis to use the new `database.travis.yml` by copying it to default `database.yml`. You've also created a database for test region. This is required as the original `database.yml` is adapted to run tests locally in your docker container test environment. But where is the `database.travis.yml` file coming from? That's the one you'll create next:

```yml
# config/database.travis.yml
test:
  adapter: postgresql
  database: auth0app_test
```

**Commit and push** your changes to `staging` branch. You should see Travis coming alive once the changes are pushed. You can watch the build and test progress on the tab named _Current_ on the repository page of Travis CI.

The first build **failed!**. Get comfortable reading through the error messages on the Travis build log.

You can see that missing `RAILS_MASTER_KEY` is the reason. You can set it up on Travis repository settings page. You can access it under 'more options' menu. 

>**Tip:** There was a strange behavior on Travis once, where I couldn't find _Settings_ as an entry under _More Options_ menu and I had to go back to _Add Repository_ page to get into settings. **Refresh the repository page** showing _failed build_ in bright red. That would also help you bring things back to normal.

Under _Environment Variables_ section, add a variable named `RAILS_MASTER_KEY` and fill the value field with the key stored at `config/master.key`. Click _Add_ to save the environment variable. Ensure you disable `Display value in build log`, that would defeat the whole purpose of keeping secrets.

Go back to the _Current_ tab on your [TravisCI][Travis]repository page and use the option _Restart Build_.

You should see a **green and happy** badge showing the test was successful. 

It took over 3 minutes originally and you should see it *coming down* to a minute the second time onwards when the cache is used.

Now that your own quality gate is ready, you can move on to publishing your application.

## Go Live With Heroku

Heroku allows you to create apps for staging and production environment. It also allows automatic deployment from different branches. Free tier allows you to run your app in production mode and test it out.

Sign up to [Heroku] first and set up a link to your GitHub profile. You can do this within [Applications](https://dashboard.heroku.com/account/applications) tab in **Account Settings**.

Once a link to GitHub profile is in place, You need to follow these steps to create a new pipeline:

1. Come back to the [Heroku home page](https://dashboard.heroku.com/apps) and create a new pipeline within your portfolio. Use the `new` menu at the top right corner.
2. While creating the pipeline, connect to the _GitHub repository_ where you've stored the app.

Step 2 above will ensure your app is ready for automatic deployment on successful test runs on GitHub (via TravisCI).

### Setup Staging App

Within the _staging_ area on the pipeline page, click on **Add app** -> **create new app**. Give it a name like `bookshelfstaging`. 

Once you create the app, use the arrow menu at the top to access an option named _configure automatic deployment_. Select `staging` branch for automatic deployment, check the option to _wait for CI_ and click on _Enable Automatic Deploy_. You can close that pop up once your changes are applied.

Now that your staging app is ready. You can click on the name you have given under _staging area_ to opens up the detailed staging page. There you'll have fine-grained control over all aspects of the application. 

For now, search for a _Postgres_ addon under _Resources_ tab. Select _Heroku Postgres_ from the search results, select _Hobby dev - free_ option if you don't want to pay now and click on _Provision_.

Your next stop should be the _Settings_ tab where you can _Reveal Config Vars_ within the  _Config Vars_ section. Add a key named `RAILS_MASTER_KEY` and the value for the key will be from `config/master.key`. Click _Add_ to save the changes.

You should be able to see a `DATABASE_URL` in there, which was added by default when you added _Heroku Postgres_ addon. But where does this environment variable go? That would be the `config/database.yml` file.

Change the `production` section to look like this:

```yml
# ...
production:
  url: <%= ENV['DATABASE_URL'] %>
```

In fact, the `database.yml` file has this instruction commented above the `production` section. You can just un-comment the section while commenting out the previous `production` section. 

Commit the change to `database.yml` file and push it to the staging branch. 

This is probably the highest point of the movie. You should be able to see a lot of things coming together. You can watch these live:

* Travis triggers a build and tests the new changes
* While Travis build is in progress, Heroku Pipeline page shows *One check pending*
* Heroku starts to build the app once Travis tests are completed. You can view the _Build log_ as the build progresses.
* Heroku shows a hash for the deployed version along with a message _Deployed just now_.

**You are almost there!** Now you should be able to open the staging region of the app in the browser. Heroku shows that as an option on the arrow menu. In this case, it launches https://bookshelfstaging.herokuapp.com/. 

![Heroku automatic deployment](https://cdn.auth0.com/blog/docker-ruby/show_heroku_deployment_after_travis_test.gif)

Just one problem. The *Login* link on the home page is broken. But a helpful message _Callback URL mismatch_ is shown by Auth0. That's the hint.

The error page also showed a link to _Applications Settings Page_ to easily go back to your Auth0 client. Add the callback URL shown on the error page. Now the _Allowed Callback URLs_ box should look like:

```
http://localhost:3000/auth/oauth2/callback,https://<YOUR_STAGING_APP_NAME>.herokuapp.com/auth/oauth2/callback
```
Use the staging app name that comes up in the URL instead of <YOUR_STAGING_APP_NAME> in the URL above. Note the delimiter `,` right after the first URL. **Remember to save** the changes to settings. That covers both local and staging environment. 

If you go back to the app on the browser and click on the _Login_ link now, you should see the familiar Auth0 login page. If you try and log in, it should come back to the app with the home page showing user details in plain text.

That's it. 

### Setup Production Region

There is nothing new here. You have already done all that in the staging area. You'll have to repeat the steps.

* Create another application under _production_ section. 
* Use _Configure automatic deployment_ option to select deployment from `master` branch this time. 
* Remember to enable _wait for CI_.
* Add a _Heroku Postgres_ addon.
* **No need** to touch `database.yml`. You are already covered there.
* Add `RAILS_MASTER_KEY` config variable.

But how do you deploy something to production? That's when you go back to a very well known workflow on GitHub.

You create a [Pull Request](https://help.github.com/articles/creating-a-pull-request/) on your repository. Helpfully, GitHub shows a message that shows recently published branches, along with an option to _Compare and Pull Request_.  Click on that and it should take you to a new pull request page.

Give it a good title and description. Watch out for the message _Able to merge_ with a green tick. That's a sign that `master` branch can receive changes from your `staging` branch. This will be helpful when more than one person gets to push changes.

Once you create the pull request, the detailed PR page shows _One check pending_ and starts to build and test the pull request via Travis CI.

Note: testing the pull request takes time as the bundler cache is not used from the staging branch.

Once all Travis Tests are over, you'll see _All checks_ have passed with the merge button turning bright green. You can now safely _merge the pull request_ to `master` branch. Click on _Merge pull request_ button and then on _Confirm Merge_ button. 

Once the pull request is merged, Travis starts the final test, this time for the commit added to the `master` branch via the PR. You can **watch the magic** as it unfolds within the Heroku pipeline page.

>**Tip:** If you want to cut down on one of these builds while following the tutorial, Travis CI settings allow you to switch off _Build for PRs_. In reality, you'd want to leave that option switched on when you have several pull requests coming in.

Your production app within Heroku should show that the production app has been successfully built and deployed, with a hash. 

In case it shows no activity, give the page a _Refresh_. If your build fails, check the logs. `RAILS_MASTER_KEY` is an important one that can trip you off when it is not set up properly. The _Deploy_ tab within each app gives you an option to manually trigger deploy when you make minor configuration changes.

Now, use the _Open app in browser_ option from Heroku app. You should see the familiar page. And if you click on the _Login_ link, you should see familiar failure!

Auth0 callback URL mismatch. Take that URL shown on that error page and add it to _Allowed Callback URLs_ section within Auth0 client.

It looks like this now:

```bash
http://localhost:3000/auth/oauth2/callback,https://<YOUR_STAGING_APP_NAME>.herokuapp.com/auth/oauth2/callback,
https://<YOUR_PRODUCTION_APP_NAME>.herokuapp.com/auth/oauth2/callback
```

Replace `<YOUR_PRODUCTION_APP_NAME>` with your actual production app name. The easiest way is to copy the URL shown on the Auth0 error page, just like what you did while setting up staging.

Back at the browser, if you load the production application and try _Login_, you should get the user details back on home screen.

So much for a full-scale workflow. You are done. From here on out, building your app is where you'd spend your time.

**Troubleshooting**: 
It will be useful for you to be aware of possible issues that you may run into. Lookout for these:

1. In case you get **Cookie Overflow** error, consult this [troubleshooting page](https://auth0.com/docs/quickstart/webapp/rails) from Auth0. Rails have a separate block for *enabling/disabling* caching within the `development.rb`. You might need to enable caching and change `config.cache_store = :null_store` to `:memory_store`

2. You may also run into **CSRF-Detected** error while trying to log in if session store is not configured properly. Check [this issue](https://github.com/omniauth/omniauth-oauth2/issues/58) and [this one](https://github.com/mkdynamic/omniauth-facebook/issues/73) to see if you have run into one of those scenarios. Setting domain to `:all` while configuring `config/initializers/session_store.rb` helped in development, but introduced the same issue on production. You might find alternative methods in [this wiki page](https://github.com/plataformatec/devise/wiki/How-To:-Use-subdomains).

### To The Explorer In You

As you navigate through Heroku pages, you'll see an option for **Review Application**. That'll help you create apps for each pull request, just to see how your application will look like once the pull request is merged.

There are also options to create containers within Heroku and use the local docker image you have created. Explore and remember to let me know if you run into something interesting.

## Developing the App

Take in another cup of your favorite drink. You are going put the authentication to good use in this last mile of the run.

The idea is to allow users to move books between shelves. 

A little primer on Rails components.

* Models are abstractions to database records. They represent the domain.
* Controllers respond to user interactions. They talk to the models and provide data to the presentation layer.
* Helpers are utility functions that support controllers and sometimes Views.
* Views are HTML pages that you see in the browser. They are generated dynamically using the data provided by controllers. There can be static pages too.

You'll create all these now.

### Models and DB

Start by creating necessary models with the usual commands. You'd just need three models:

* A model for Users
* A model for Books
* Another one for Shelf

While it is possible to run commands from the host terminal by creating a temporary container each time, it may not be the quickest solution. It is better to get into a bash prompt in the terminal within the container.

Make sure that the container is running Rails server and guard. On another terminal, run this (while you are in the project folder):

```bash
docker-compose exec --user $(id -u):$(id -g) app /bin/bash
```

That should take you to a terminal within the container. 

As an aside, you might have used bash alias from part 1 to shrink docker commands. The one I use for this in my .bashrc looks like:

```bash
# ~/.bashrc or ~/.zshrc
# suffixed to the end of the file 
alias de='docker-compose exec --user $(id -u):$(id -g)'
```

You give up flexibility to add other flags, but this alias shrinks the previous command by 42 characters and looks like this:

```bash
de app /bin/bash
```

Now that you are on a terminal within the container, you can run rails commands as usual. Go ahead and create three models.

```bash
rails g model User email:string
rails g model Book title:string author:string
rails g model Shelf place:integer user:references book:references
```

>Note: `rails g scaffold Book title:string` will create all the routes, controller, and actions along with tests and helper files. Try it and see if you'd like it.

`rails g` stands for `rails generate`. That should create database migration files, models, and tests. Before you apply the migrations to the database, **there is one important addition** to the shelves migration file.  Leave the block `create_table` as it is. Add an index to mark the combination of book and user as a unique combination.

```rb
# db/migrate/2018...._create_shelves.rb
class CreateShelves < ActiveRecord::Migration[5.2]
  def change
    create_table :shelves do |t|
      t.integer :place
      t.references :user, foreign_key: true
      t.references :book, foreign_key: true
      t.timestamps
    end
    add_index :shelves, [:book_id,:user_id], unique: true
  end
end
```

Remember to save the file before you move on. Of course, you might want to think about a proper indexing strategy for other models.

Time to apply the database changes. Within the same terminal that runs inside the container, you can run `rails db:migrate` to apply changes.

```bash
rails db:migrate
```

The `Shelf` model represents the relationship between models. You'll recognize those from earlier `rails g model` statement. You need to introduce an enum for different types of shelves. 

```rb
# app/models/shelf.rb
class Shelf < ApplicationRecord
  enum place: [:wishlist, :bought, :reading, :done]
  belongs_to :user
  belongs_to :book
  validates :user_id, uniqueness: {scope: :book_id} 
  scope :by_user, ->(user) { where(user_id: user)} 
end
```

For a particular user, one book can be on only one shelf. This constraint is added via `validates` statement and this is in line with the `index` created during migration.

Finally, another scope that filters shelf by a user, this can be used in controllers at a later stage.

Next comes the model for books and it looks like this. 

```rb
# app/models/book.rb
class Book < ApplicationRecord
  has_many :shelves, dependent: :destroy
  has_many :users, through: :shelves
  scope :within_shelf, ->(place,user) {
    joins(:shelves,:users) 
    .where(shelves: {place: place, users: {id: user.id}})
  }
end
```

First two statements set up a `has_many` relationship between shelves and users (through shelves). You need to set up a scope that allows you to pull out books from a particular shelf. You'll use that scope within controllers in a minute.

Finally, the `User` model. It's not huge. In fact, you are not even going to store the name and image attributes to the database. That will be available when the user logs in from Auth0. The only thing you'll store within `User` model is the email.

```rb
# app/models/user.rb
class User < ApplicationRecord
    attr_accessor :name
    attr_accessor :image
    has_many :shelves, dependent: :destroy
    has_many :books, through: :shelves
end
```

Just to hydrate the database, you can create a seed file with a list of books like this:

```rb
# db/seeds.rb
# Shelf.delete_all
# Book.delete_all
books=Book.create([
    {title: 'book1', author: 'author1'},
    {title: 'book2', author: 'author1'},
    {title: 'book3', author: 'author1'},
    {title: 'book4', author: 'author2'},
    {title: 'book5', author: 'author3'},
])
```

Run the seeding command on a shell within the container. If you are still within the terminal that generated models and migrated DB, you are right where you need to be and you can skip the `docker-compose exec` and get to `rails db:seed`:

```bash
docker-compose exec app /bin/bash
rails db:seed
```

This will fill the database with a list of books. In case you want to have a fresh start, you can un-comment the first two lines. Since shelf depends on book model, that one needs to be deleted first. Then all the existing books can be deleted, leaving only newly created books. Needless to say **this can cost you** dearly if you run it in production.

Now that the database is ready, you can exit out of the shell within the container.

Time to tell your app that models are ready and how to respond to users when they ask for it.

### Authentication Helpers

The first one is to set up `Auth0Helper` to allow authentication when necessary.

```rb
# app/helpers/auth0_helper.rb
module Auth0Helper
  private 
    def user_signed_in?
        session[:userinfo].present?
    end
    
    def authenticate_user!
        if user_signed_in?
            @current_user=build_user
        else
            redirect_to root_path
        end
    end
    
    def current_user
        @current_user
    end

    def build_user
        user_info=session[:userinfo]["info"]
        email_id=user_info["email"]
        user=User.find_by(email: email_id)
        user.name=user_info["name"]
        user.image=user_info["image"]
        user
    end
end
```

`session[:userinfo]` is constructed when Auth0 login is successful and callback method is invoked. That's the indication that user is signed in.

`authenticate_user!` is a helper method that allows you to restrict actions to users who have logged in. 

`build_user` uses email ID to fetch the user from DB. Also adds their name and avatar image URL to `user`.

Add this helper to application controller to make it available to other controllers.

```rb
# app/controllers/application_controller.rb
class ApplicationController < ActionController::Base
  include Auth0Helper
end
```

Another helper is required manage state when the application redirects to Auth0 and comes back with user information.

```rb
# app/helpers/session_helper.rb

module SessionHelper
  def get_state
    state = SecureRandom.hex(24)
    session['omniauth.state'] = state
    state
  end
end
```

One more helper to finally log out of this application.

```rb
# app/helpers/logout_helper.rb
module LogoutHelper
  def logout_url
    domain = 'tugboat.auth0.com'
    request_params = {
        returnTo: root_url,
        client_id: Rails.application.credentials.auth0[:client_id]
    }

    URI::HTTPS.build(
      host: domain, 
      path: '/v2/logout',
      query: to_query(request_params))
  end

  private

  def to_query(hash)
    hash.map { |k, v| "#{k}=#{URI.escape(v)}" unless v.nil? }.reject(&:nil?).join('&')
  end
end
```

This appears to be a complex one, but in reality, all it is doing is just constructing the URL for logout with different parameters. Especially, if you piece domain, path, and query, you'd get something like `tugboat.auth0.com/v2/logout?returnTo=http://localhost:3000/&client_id=u1k...`. 

**Auth0 configuration**: This is an important step where you need to configure [Auth0] client with the following _Allowed Logout URLs_:

```bash
http://localhost:3000/, https://<YOUR_STAGING_APP_NAME>.herokuapp.com/, https://<YOUR_PRODUCTION_APP_NAME>.herokuapp.com/
```

Use your Heroku pipeline to get actual names for those URLs above. Remember to _Save changes_. 

### Controllers

Controllers bridge the model and view with the user. You would need controllers to handle users, books, and shelves.

```rb
# app/controllers/auth0_controller.rb
class Auth0Controller < ApplicationController
  include LogoutHelper
  def logout
      reset_session
      redirect_to logout_url.to_s
  end

  def callback
    session[:userinfo] = request.env['omniauth.auth']
    find_or_create_user!
    redirect_to '/books'
  end

  def failure
    @error_msg = request.params['message']
  end

  private

    def find_or_create_user!
        email_id=session[:userinfo]["info"]["email"]
        User.find_or_create_by!(email: email_id)
    end
end
```

This controller is something you have set up earlier. It is now ready to create users on the successful callback from Auth0.

This controller uses the email ID returned to the callback to find an existing user or create a new user when no such email is present in the database. 

Plain and simple use of the information sent from Auth0. There are loads of other information returned along, which you can find when you inspect `session[:userinfo]`. In fact, the `<%= debug session[:userinfo] %>` introduced to the view was already showing all that information that came along. You can find good use of it to design your user model.

As you can see, `logout` action has also been added here and it uses the `logout_url` helper function you've set up earlier.

Books need a controller too. That's what you'd do next.

```rb
# app/controllers/books_controller.rb
class BooksController < ApplicationController
  before_action :authenticate_user!
  before_action :set_book, only: [:show, :edit, :update, :destroy]

  def index
    if(params[:place].nil?)
      @books=Book.all
    else 
      @books=Book.within_shelf(
        params[:place],
        current_user)
    end
  end

  def show
    current_shelf=@book.shelves.by_user(current_user).first
    if current_shelf.blank?
      @shelf=Shelf.new
    else 
      @shelf=current_shelf
    end 
  end

  def new
    @book = Book.new
  end

  def create
    @book = Book.new(book_params)
    if @book.save
      redirect_to @book
    else
      render :new
    end
  end

  private
    def set_book
      @book = Book.find(params[:id])
    end

    def book_params
      params.require(:book).permit(:title, :author)
    end
end
```

That's a slimmed down version of the controller to make it easy to understand. Rails uses REST architecture and you can see the controller is ready to handle the usual CRUD (Create, Read, Update, Delete) operations. `before_action :authenticate_user!` takes care of authentication before any user can create/amend a book. `authenticate_user` comes from the `Auth0Helper` you've set up earlier.

* The `index` method provides a list of books. The parameter `place` shows where the book is.
* The `show` method displays a single book.
* The `new` method routes to a form allowing you to create a new book.
* The method `create` saves the book sent via the `new` form
* The method `set_book` finds the current book being used for all actions (except for `index` as it involves more than one book) 

`book_params` method is also an important one that protects you from any malicious extra parameters sent in by the aliens.

>**Note**: Just like scaffolding, you can run `rails g resources Books name:string` to generate boilerplate for necessary Model and Controllers. You'll get both HTML and JSON templates generated for you to play around. But writing your own actions helps you think more about each of the actions. Playing with both scaffolding and writing code on your own can help you learn Rails internals.

Go ahead and create another controller for shelves. This controller binds books to users. 

```rb
# app/controllers/shelves_controller.rb
class ShelvesController < ApplicationController
  before_action :authenticate_user!
  before_action :set_shelf, only: [:update]

  def create
    @shelf=Shelf.find_or_create_by(
      user_id: current_user.id, 
      book_id: shelf_params[:book_id],
      place: shelf_params[:place]
    )
    redirect_to @shelf.book
  end

  def update
      @shelf.update(shelf_params)
      redirect_to @shelf.book
  end

  private

    def set_shelf
      @shelf = Shelf.find(params[:id])
    end
    def shelf_params
      params.require(:shelf).permit(:place, :book_id)
    end
end
```

To simplify things, you'll set up create and update actions. While creating the shelf, you'll have to pass in the user along with other params. But while updating, the user is already set up, so you just need to pass rest of the params.

You need to give one last visit to the home controller.

```rb
# app/controllers/home_controller.rb
class HomeController < ApplicationController
  
  before_action :authenticate_user!, only: [:profile]
  
  def show
  end

  def profile
  end
end
```

As you'll remember, `show` action is the home page when the user is not logged in. You need to add `profile` action and make it available only when the user is logged in. This is achieved through the use of `authenticate_user`.

You are now ready to show off your actions to users with views. 

### Views

Views present information from controllers coming from models in HTML format (and also JSON if you are using scaffolding along with `jbuilder` gem). 

HTML files are stored in `erb` format that allows Ruby programming inside the templates before final HTML files are generated for each request. It is a preprocessor for Rails views.

Start from the top. `application.html.erb` is the base template for all of your Rails controllers.

{% highlight html %}
<!-- app/views/layouts/application.html.erb -->
<!DOCTYPE html>
<html>
  <head>
  <!-- leave everything within head tag intact -->
  <!-- unless you know what you are doing -->
  </head>
  <body>
    <header><%= render 'layouts/navbar' %></header>
    <%= yield %>
  </body>
</html>
{% endhighlight %}

Note that there is only one line you need to introduce. The line starting with `<header>`. This is to add a partial for navigation. Partial is sort of a component or piece of code that can be plugged in. You may see they resemble ReactJS components if you are coming from that end of the universe. Include that partial within this `application.html.erb` and that'll show up on all pages. By the way, that small `<%= yield %>` at the bottom is the placeholder for rest of your pages.

On to the navigation partial now, you need to create a new file named `_navbar.html.erb`. The `_` prefix marks a partial file:

{% highlight html %}
<!-- app/views/layouts/_navbar.html.erb-->
<nav>
    <div> 
        <%= image_tag "book.svg",class: "logo" %>
        <h1>
        <%= link_to "BookShelf", books_path%>
        </h1>
    </div>
    <ul>
        <% if user_signed_in? %>
            <li><%= link_to "Profile",profile_path %></li>
            <li><%= link_to "Logout",auth_logout_path %></li> 
        <% end %>
    </ul>
</nav>
{% endhighlight %}

That's just an image and a title on the left and a link to _Profile_ and _Logout_ on the right. 

Place that `book.svg` file under `app/assets/images/` folder. Without that file, Rails will throw an error. You can use your own logo or the one from the repository to get going.

Few more views to handle books, and you'll be done.

Start with a view to creating new books. Create folders as required based on the path given in the comments below:

{% highlight html %}
<!-- app/views/books/new.html.erb-->
<div class="card">
  <h1>New Book</h1>
  <%= render 'form', book: @book %>
  <%= link_to 'Back', books_path %>
</div>
{% endhighlight %}

The view names are usually derived from controller actions. That's how Rails knows which view to render.

The corresponding `new` action from controllers gives context under `@book`. That'll be used in the form partial to create a book. That's up next.

{% highlight html %}
<!-- app/views/books/_form.html.erb-->
<%= form_with(model: book, local: true) do |form| %>
  <div class="field">
    <%= form.label :title %>
    <%= form.text_field :title, id: :book_title %>
  </div>

  <div class="field">
    <%= form.label :author %>
    <%= form.text_field :author, id: :book_author %>
  </div>

  <div class="actions">
    <p></p><%= form.submit %>
  </div>
<% end %>
{% endhighlight %}

Rails takes care of creating a form that can send a POST request to controller action `create` when this form is submitted.

Now that you can create books, next is to show already created book. That's easy.

{% highlight html %}
<!-- app/views/books/show.html.erb -->
<article class="book card">
  <h1><%= @book.title %></h1>
  <p>
    <strong>Author:</strong>
    <%= @book.author %>
  </p>
  <%= render 'shelf_form', shelf: @shelf %>
</article>
{% endhighlight %}

Except for the last line, everything else is just business as usual. Use the `@book` instance variable from the controller to get book details and render them. The last line shows a form for the current shelf for the book so that the user can easily change the shelf if required. 

The groundwork for this activity was already done within books controller's `show` action. If you review that action, you'll see `@shelf` was prepared for this moment.

You'll use that to render a small form that has nothing but a drop-down showing current shelf, and an option to move it to another shelf.

This form lives in a partial.

{% highlight html %}
<!-- app/views/books/_shelf_form.html.erb-->
<%= form_with(model: shelf, local: true) do |form| %>
  <div class="field">
    <%= form.label :place,"Shelf"%>
    <%= form.select(:place,
        options_for_select(Shelf.places.keys,shelf.place),
        :include_blank=>true,
        id: :shelf_place) 
    %>
  </div>
  <%= form.hidden_field :book_id, :value=>params[:id] %>
  <div class="actions">
    <p></p><%= form.submit "Move to shelf" %>
  </div>
<% end %>
{% endhighlight %}

The variable `shelf` was passed by the `show` template. This partial makes use of it to render a select drop-down with all `Shelf` places from keys. The move to shelf button invokes `create` action if `shelf` is empty. It'll invoke `update` action if `shelf` already has valid values.

Final sprint. Index of all books and by their place in the shelf. This again takes two files. One to show index and another to show a filter for shelves as top menu.

{% highlight html %}
<!-- app/views/books/index.html.erb -->
<div class="books">
  <%= render 'shelf_list' %>
  <%= link_to "Add New Book", new_book_path , class: 'new_book' %>
  <section class="book_list">
      <% @books.each do |book| %>
        <article class="book_entry">
          <h2><%= link_to book.title.capitalize, book %></h2>
          <h3><%= book.author %></h3 >
        </article>
      <% end %>
  </section>
</div>
{% endhighlight %}

That view takes care of listing all the books sent from `index` action within books controller.

In addition, it has a link to add new books at the top. But there is a partial at the top that lists option to filter books by shelves.

The `shelf_list` partial gives a list of links that will filter books.

{% highlight html %}
<!-- app/views/books/_shelf_list.html.erb -->
<section class="shelves">
    <%= link_to "All",books_path %>
    <% Shelf.places.keys.each do |shelf| %>
        <%= link_to shelf.capitalize,books_path(place: shelf) %>
    <% end %>
</section>
{% endhighlight %}

That partial adds links to each type of shelf. Passes each shelf as a parameter to books controller. If you look backward, the `index` action from books controller used a parameter `params[:place]. This is where it is coming from.

The profile view is the one outstanding and a small one to finish views.

{% highlight html %}
<!-- app/views/home/profile.html.erb -->
<section class="profile card">
  <h2><img src='<%= current_user.image %>' /></h2>
  <h1><%= current_user.name %></h1>
  <%= link_to "Logout",auth_logout_path, class: "btn red" %>
</section>
{% endhighlight %}

Now, go ahead and tell Rails about the URLs you are prepared to answer. That'll be done through routes.

### Routes

Routes allow you to lay down list or URLs that the app will respond to. First stop is to **set up the routes** for books, shelves, profile and log out. Add routes to the file `config/routes.rb`. The file looks like this:

```rb
# config/routes.rb
Rails.application.routes.draw do
  root 'home#show'
  get "/auth/oauth2/callback" => "auth0#callback"
  get "/auth/failure" => "auth0#failure"
  get "/profile" => "home#profile"
  get "/auth/logout" => "auth0#logout"
  resources :books
  resources :shelves
end
```

That's some heavy lifting. Lot of changes that **the container needs restarting**. Stop it with `Ctrl + C` and run these on the same terminal:

```bash
docker-compose down
docker-compose up
```

Try loading http://localhost:3000/ now. It should redirect you to the login page. _It may not be pretty, yet_. 

It may even have previous login details retained. You can try _Logout_ and _Login_ again. Once you log in, it should take you to book list.

If it does, the soul of the application is alive and kicking. You just need to _beautify_ it.

### Styles

The application views may not be very impressive at first sight. But you should be able to add styles that'll breathe life into them.

`app/assets/stylesheets/` folder holds stylesheets for the application. `application.css` builds all stylesheets into assets. You are free to add as many stylesheets as you want.

You can also use [Sass] files as Rails comes with built-in support. 

For example, add a new file `header.scss`. Add following style rules. 

```scss
/* app/assets/stylesheets/header.scss */
nav {
    background: rgba(100,250,100,.5);
    display: flex;
    border-radius: 2px;
    margin-bottom: 10px;
    justify-content: space-between;
    div { display: flex; }
    .logo { width: 3em; }
    h1 { margin: auto .1em; }
    ul {
      list-style: none;
      display: flex;
      justify-content: space-around;
      padding: 0;
    }
    li { padding: .5em; }
}
```

If you are logged in and on the books index page, that change should reflect on the page navigation bar. Adding new files sometimes might need a manual refresh instead of live reloading. But once you've added a new file, changes to the files are automatically applied to the view on the browser.

Another example, the Home page.

```scss
/* app/assets/stylesheets/home.scss */
.card {
    max-width: 18em;
    padding: 1em;
    border-radius: 3px;
    box-shadow: 0 2px 4px grey;
    text-align: center;
    margin: 0 auto;
    background: rgba(255,255,255,.5);
}
```

The file `home.scss` should already be available. Just add those styles, save the file and on the web page, click on _Logout_ to go back go home page.

Have a look at [CSS reference at MDN](https://developer.mozilla.org/en-US/docs/Web/CSS) if you are new to stylesheets. Also, worth checking [Sass]. Truly, CSS with superpowers. Apart from nesting selector styles, it has several other features. A lot of them are and will come into native CSS. 

I suggest you pull stylesheets from the [repository](https://github.com/vijayabharathib/dockerized-rails-app) to save yourself some time. You'll see about 4 `.scss` files, take all of them.

### Deploy

You've done a lot of work. In fact, too many files that they should already be on Git. Commit and Push your changes.

* Watch tests as they run on [Travis CI][Travis]. Check.
* Watch [Heroku] deploy the app to staging. Check.
* Open staging app and create a book. Uh ho!

**There is something missing.** That is, you need to migrate the database schema to Heroku. Remember running `rails db:migrate`? You need to tell Heroku to run that command whenever you deploy. Heroku has a release feature mentioned in [this post](https://mentalized.net/journal/2017/04/22/run-rails-migrations-on-heroku-deploy/) will help.

You need to set up a `Procfile` within the project root directory.

```rb
web: bundle exec puma -C config/puma.rb
release: rails db:migrate
```

The first one `web` declares the web server. The second one `release` is the one that will be executed once build is completed.

_More Options_ menu on Heroku gives you control over a console within Heroku. You'll find an option _Run Console_ within the application (staging or production) page. You can use that console to run rails commands such as `rails db:create`, `rails db:rollback` and `rails db:migrate` when required. You can ignore `rails db:migrate` as it is automated as part of the release. But you should indeed run `rails db:seed` to hydrate the staging database. 

Open staging environment in the browser. You can do this from Heroku page itself. Try and create a book manually. Move books around shelves. Things should work like they've worked in development now.

The reason? `release` tag in the `Procfile` migrated the database schema. So users and books can be created without any trouble.

Great! Take a break, you deserve it!

And you know how to take this to production? Remember creating a pull request between _staging_ and _master_ branches on [GitHub]? The same process should push things to production on Heroku, once you merge the pull request.

**Logs:**

There are three logs on Heroku that you'll find useful.

1. If you open staging pipeline on Heroku, you'll be able to see **log generated during build** under **Activity** tab. This shows the build activities and their status.
2. The same activity tab will also show **Release log** generated while running the `release` activity in `Procfile`. In this case, it would show database migrations.
3. You'll also be able to see general application runtime log using **More** options menu at the top right corner.

Use these to find out what's happening when you run into an issue.

## Debugging

Here is a bonus when you run into issues. You need a few more blades in your swiss army knife to debug the application. You can use these 4 options depending on what you are debugging.

### Debug Information on Views

This one is the easiest one. Just include a `<% debug %>` tag to the views (`erb` files) and that shows up on the pages. 

You have already done that on the `app/views/home/show.html.erb` file. You have included `<%= debug session[:userinfo] %>` to look into what was returned from [Auth0].

**Warning:** This tag will be rendered on staging and production as well. To make it available for development region, you can change that line to show the following:

{% highlight html %}
<% if Rails.env.development? %>
 <%= debug session[:userinfo] %>
<% end %>
{% endhighlight %}

But really, you should think about removing that line altogether before you commit and push the code. You don't want rails to check for the environment in production, each time someone visits that home page, do you? 

### Logging to Console

Logging to console is a serious business. But, that's exactly what you are going to do.

Let's say you tried setting up a secret key base, stored the Auth0 client secret encrypted and one MASTER_KEY to rule them all. But things are not working, you want to see if the decryption works ok.

Adding that information to a view will be disastrous right? Everyone will see that information on the screen.

**But adding to the console is not safe either.** If someone gains access to your server logs, they will be able to figure out a lot of things from the logs alone. You don't want to hand them the key to your whole app.

Try that here. Add this line to `show` method within `app/controllers/home_controller.rb` file.

```rb
    logger.debug Rails.application.secrets.auth0_client_secret
```

That's single, but a long line. Once you save the file, go to http://localhost:3000 on your browser and check your console log. There it is, bright as day, your encrypted Auth0 client secret.

**But that's about what you shouldn't do.** You can use logger for printing out information that is harmless. You can remove it after debugging. You can leave it if it would add value to production. You take a call.

### Rails Console On Terminal

This is quite straightforward. On-demand access to Rails console on the terminal. You'll get into a shell within the container and then run `rails c`. Start the container using `docker-compose up` and then issue these commands:

```bash
docker-compose exec app /bin/bash
rails console
```

The console gives you access to Rails application. You can interact with ActiveRecord and general Rails functionalities. Issue a command like `User.all` and `Book.all` to find out what's happening.

More on [Rails console on the guides.](http://guides.rubyonrails.org/command_line.html#rails-console)

### Console Within Views

This one is an interesting option. A console right there in the views on the browser. Your commands are sent to the server and response is sent back to the browser. 

Very powerful. **Very risky**, as it can talk to the server. This is enabled by `web-console` gem in the `Gemfile`. Have a look at the grouping. The gem should be inside `group :development do` section. Which means, this is available only in development environment. Not in test or production environment.

How do you use it? 

Place this line in your views: `<%= console %>`. If you want it on all pages, better put that right under `<% yield %>` statement on `app/views/layouts/application.html.erb` file.

Now, go ahead and revisit the home page. Do you see the console at the bottom? You should (see the troubleshooting section below if you don't).

You get a black section with a prompt `>>`. You can run any command that you run within a Rails console. For example, run `User.all` and you'll see a list of accounts with their email IDs (You should think of that as spammers treasure trove). This works even if you log out and load the home page. That's a big red-flag. **This console will work even without authentication**, as it is directly talking to rails database.

**Troubleshooting:** In case you don't, the terminal log running local server may have an answer. If you see something like `Cannot render console from 172.19.0.1!` that would be due to the container's lack of access. This can be fixed by white-listing container IPs in the development environment:

```rb
# config/environments/development.rb
# ...
config.web_console.whitelisted_ips = ['172.19.0.0/16']
# ... just leave all else intact
```

You need to restart the server and access the page again.

Remember to remove the console. Otherwise, add console conditionally for development region alone. Use the same strategy you've used for `<%= debug %>`.

### Bye Bug!

`byebug` is a gem that allows you to dig into the runtime at any spot of the application. You already have it in your `Gemfile`.

For example, add `byebug` to `show` method in Home controller.

```rb
# app/controllers/home_controller.rb
class HomeController < ApplicationController
  # ...
  def show
    byebug if Rails.env.development?
  end
  # ...
end
```

But that would need interaction on the terminal. `docker-compose up` was not built to handle inputs. It just logs the output. See this [issue](https://github.com/docker/compose/issues/4677).

You need to open up `stdin_open` and `tty` within `docker-compose.yml`. Do not remove anything from the file. Just add the last two flags.

```yml
#...
services:
  db:
    #...
  app:
    build:
    #...
    tty: true
    stdin_open: true
```

While debugging with `byebug`, you need to use `docker-compose run --service-ports app` instead of `docker-compose up`. 

Once the server is ready, reload the home page. If you notice, the home page keeps loading for a long time. That is because `byebug` caught up the execution and waiting for you on the terminal.

Go ahead and have a look at the terminal. `byebug` shows the line where it paused execution. You can type `help` or `var` and hit `Enter` to get some output.

`continue` lets you proceed further. `next` takes you to the next step. Learn more about `byebug` [here](https://github.com/deivid-rodriguez/byebug).

Did you notice *anything strange*? Did the characters you typed on the terminal never came up, but the output did? It did for me. I'm typing in without looking at the characters. If you know how to solve this, please do let me know!

More on debugging in the [Rails Guides](http://guides.rubyonrails.org/debugging_rails_applications.html)

{% include tweet_quote.html quote_text="I just learnt how @TravisCI, @Docker, and @Rails together. Awesome!" %}

## Conclusion

Thank you so much for staying with me so far. I know this is a dense article and it's great you've come this far. Hope this helped you think about creating that one app you had at the back of your mind. Once you get comfortable with the workflow, it encourages you to reuse most of it. Such as the `Gemfile` and `docker-compose.yml`, which makes it easy to spring up new applications.

But that's not all, your journey ends only when you tweak the workflow to improvise it and make it stick to support your habits. 

I invite you to share your thoughts. Anything that can be improved or anything that could speed things up? Share that in the discussion, it will benefit all of us. See you there.

Finally, thanks to [Bruno Krebs](https://twitter.com/brunoskrebs) for his excellent insights and attention to details during the review. 

[GitHub]: https://github.com/
[Travis]: https://travis-ci.org/
[Heroku]: https://dashboard.heroku.com
[Auth0]: http://auth0.com/ 
[Sass]: https://sass-lang.com/
