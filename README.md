<p align="center"><img src="https://okazanta.com/assets/okazanta-logo-01.svg" width="150px"></p>

# Deploy [Okazanta](https://github.com/Okazanta/okazanta-legacy), on [Clever Cloud](https://www.clever-cloud.com)

Okazanta is modern open source status page software that anyone can use to create a clean and accessible status page for any kind of services. Check the project [here](https://github.com/Okazanta/okazanta-legacy). It's a [Cachet](https://cachethq.io) fork, and uses components and packages from the original project.

This repository has been configured to work with SQLite on local and PostgreSQL on production, but you can change that if you know what you are doing.

Use Okazanta to deploy a status page on [Clever Cloud](https://www.clever-cloud.com) by following these instructions.

## Prerequisites to work on this repository:

- PHP 7.1
- Git
- Composer

## Install Okazanta

1. Clone the repository
2. Open it on your Terminal
3. Install dependencies with `composer install --no-dev -o`
4. Generate the `APP_KEY` with `php artisan key:generate`

Your `APP_KEY` environement variable should now show a value in your `.env` file. It should look like something like this : `base64:YU+UTSvh2tNXx5...`.

Now you can run `php artisan cachet:install`. You will be asked if you want to configure the app right from terminal, which you can also do by deploying the app on local.

## Deploy and configure on local

To locally deploy your app, run `php artisan serve`. You can start configuring your app from the app interface.

### Optional: work on local with a remote database hosted on Clever Cloud

You can also set up a remote database on Clever Cloud without deploying your app yet. Create a PostgreSQL addon from Clever Cloud console and add the credentials to your `.env` file. After that:

1. In `.config/database.php` replace

```php
'default' => env('DB_DRIVER', 'sqlite'),
```

by

```php
'default' => env('DB_DRIVER', 'pgsql'),
```

2. Run `php artisan migrate` to migrate your app's data to your new database.

When you'll declare you app on Clever Cloud, go to your app's menu, click on **Service dependencies** > "Link addons", and connect it to the PostgreSQL addon right before puching your code.

INSERT HERE SCREESHOT

## Deploy on Clever Cloud

Once your app is ready, don't forget to add and commit changes. Then, open the Clever Cloud Console and create an application, declare it as PHP and choose your preferred deployment method (Git or GitHub). Follow the process, add a **PostgreSQL addon**, you'll be able to add a Redis addon later.

### Environment Variables

Environment variables are dynamically injected with Clever Cloud. That means that, even if you commit it, your `.env` file won't be commited. You'll need to add environment variables to make your app deploy and work.

Click on **Expert** and paste the following environment variables:

```
APP_ENV="production"
APP_KEY=["your-app-key-here"]
CACHE_DRIVER="redis"
CACHET_AUTO_TWITTER="true"
CACHET_BEACON="true"
CACHET_EMOJI="false"
CC_PHP_VERSION="^7.1.3"
CC_POST_BUILD_HOOK="php artisan migrate --force"
CC_WEBROOT="/public"
LOG_CHANNEL="syslog"
QUEUE_DRIVER="database"
SESSION_DRIVER="database"
```

If you set `CACHET_EMOJI`=`true`, add a `GITHUB_TOKEN`to your environment variables. [Learn how to create it here](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token).

Don't forget to click on **UPDATE CHANGES**. Now, copy the command to deploy from your Terminal (or just wait for the deployment to start automatically if you deploy from GitHub). In the meantime, create two more addons:

- a **Redis** addon
- [FS Bucket](https://www.clever-cloud.com/doc/deploy/addon/fs-bucket/) addon if needed

Connect them to your PHP app directly from the Console (your-app > **Service dependencies** > "Link Applications") and **restart your app** to update changes.

Your app should be deployed and up. Enjoy!

## Troubleshooting

If you are having troubles deploying your app, here are a few ressources that might be useful:

- [Cachet](https://docs.cachethq.io/docs/installing-cachet) installation documentation. Beware, the project isn't maintained anymore, so some parts might be outdated.
- [Clever Cloud documentation on Laravel](https://www.clever-cloud.com/doc/deploy/application/php/tutorials/tutorial-laravel/), since this app uses Laravel framework.

### Getting dependency conflicts on local?

**Dependency conflict** might happen depending on your local set up, since this project hasn't been updated in a while. Here are some work-arounds:

- Work directly on [GitHub codespaces](https://github.com/features/codespaces)
- [Enable PHP version switching](https://www.markhesketh.com/switching-multiple-php-versions-on-macos/) if working on macOS