# Heroku Overview

## The Heroku Dashboard Overview

In the Heroku dashboard, in the settings tab, there is an option to go into maintenance mode. This allows you to take your app offline while showing a user friendly maintenance page to the user instead of, say, a 404 page.

## Installing the Heroku CLI

Need to install a Heroku CLI. Just google it and download the Windows 64 bit installer.

You can also do `npm install -g heroku-cli` but it's strongly recommended that you don't do this because it won't auto-update this way.

If everything worked correctly, you can run `heroku --version` and get a response. I had to re-open `cmder` for this to work.

## Pricing Comparison

If you use the free tier, your app sleeps after 30 mins of inactivity?!?

The hobby tier is 7$/month. A cheaper option is digital ocean for 5$/month. The advantage is that Heroku handles a lot of the maintenance work for you (ex/ load balancing).

# Deploying Apps in Minutes

## Demo: Deploying with the Heroku CLI

- Navigate to the root of your project in the command line
- Run `heroku login` and type in your credentials
- Run `heroku create`
- Run `git push heroku master`
- Login to your Heroku dashboard and you should see your app listed there
- In the top-right corner, there is an `Open app` button. Click it to view your hosted application.

Side note: I didn't realize you can host server only applications. They don't have a user interface, they just return a JSON payload.

## Heroku CLI Overview

`heroku help` (obvious)
`heroku create` (creates an empty container space)
`heroku apps` (lists all apps on your account)
`heroku logs -a <app>` (log output for your app)
`heroku logs -a <app> --tail` (log output for your app in real-time)
`heroku config` (configure environment)
`heroku open` (opens the hosted app in your default browser!)

## Demo: Deploying with Github

Workflow:
- Fork a repository
- In the Heroku dashboard, create a new app
- In the `Deploy` tab, under `Deployment method`, select Github (Connect to Github)
- Under the new `Connect to Github` section, search for a repository to connect to. Find the repository you forked and connect to it.
- Click `Enable Automatic Deploys`
- Can also manually deploy, and once successful, a `View` button appears
- Afterwards, can check deployment information in the `Overview` or `Activity` tab

