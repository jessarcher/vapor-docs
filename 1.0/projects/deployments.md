# Deployments

[[toc]]

## Introduction

Of course, one of the primary features of Laravel Vapor are atomic, zero-downtime deployments. Unlike many other features of Vapor, deployments are **only initiated via the Vapor CLI**. During deployment, Vapor will run the `build` steps of your `vapor.yml` file on the local machine that the deployment is running on. This might be your personal machine or a continuous integration platform.

## Initiating Deployments

To initiate a deployment, execute the `deploy` CLI command from the root of your application:

```bash
vapor deploy production
```

## Build Hooks

You may define build hooks for an environment using the `build` key within your `vapor.yml` file. These commands are executed on the local machine that the deployment is running on and may be used to prepare your application for deployment. During deployment, your application is built within a temporary `.vapor` directory that is created by the CLI and all of your `build` commands will run within that temporary directory:

```yaml
id: 3
name: vapor-app
environments:
    production:
        memory: 1024
        database: vapor-app
        cache: vapor-cache
        build:
            - 'composer install --no-dev --classmap-authoritative'
            - 'php artisan event:cache'
        deploy:
            - 'php artisan migrate --force'
```

## Deploy Hooks

You may define deployment hooks for an environment using the `deploy` key within your `vapor.yml` file. These commands are executed against the deployed environment **before it is activated for general availability**. If any of these commands fail, the deployment will not be activated. You may review the output / logs from your deployment hooks via the Vapor UI's deployment detail screen:

```yaml
id: 3
name: vapor-app
environments:
    production:
        memory: 1024
        database: vapor-app
        cache: vapor-cache
        build:
            - 'composer install --no-dev --classmap-authoritative'
            - 'php artisan event:cache'
        deploy:
            - 'php artisan migrate --force'
```

## Assets

During deployment, Vapor will automatically extract all of the assets in your Laravel project's `public` directory and upload them to S3. In addition, Vapor will create a AWS CloudFront (CDN) distribution to distribute these assets efficiently around the world.

Because all of your assets will be served via S3 / CloudFront, you should always generate URLs to these assets using Laravel's `asset` helper. Vapor injects an `ASSET_URL` environment variable which Laravel's `asset` helper will use when constructing your URLs:

```php
<img src="{{ asset('img.jpg') }}">
```

On subsequent deployments, only the assets that have changed will be uploaded to S3, while unchanged assets will be copied over from the previous deployment.

### URLs Within CSS

Sometimes, your CSS may need to reference asset URLs, such as a `background-image` property that references an image via URL. Obviously, you are not able to use the PHP `asset` helper within your CSS. For this reason, Vapor will automatically prepend the correct asset base URL to all relative URLs in your CSS during the build process. After your build steps have executed, Vapor performs this action against any CSS files in your application's `public` directory (including directories nested under `public`).

## Redeploying

Sometimes you may need to simply redeploy a given environment without rebuilding or redeploying it. For example, you may wish to do this after updating an environment variable. To accomplish this, you may use the Vapor UI or the `redeploy` CLI command:

```bash
vapor redeploy production
```

## Rollbacks

To rollback to a previous deployment, you may select the deployment in the Vapor UI and click the "Rollback To" button, or you may use the `rollback` CLI command. The `rollback` command's "--select" option will allow you to select which deployment to rollback to from a list of recent deployments. If the `rollback` command is executed without this option, it will simply rollback to the most previous successful deployment:

```bash
vapor rollback production

vapor rollback production --select
```

:::warning Variables, Secrets, & Rollbacks

When rolling back to a previous deployment, Vapor will use the environment's variables and secrets as they existed at the time the deployment you're rolling back to was originally deployed.
:::
