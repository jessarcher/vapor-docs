# Environments

[[toc]]

## Introduction

As you may have noticed, projects do not contain much information or resources themselves. All of your deployments and invoked commands are stored within environments. Each project may have as many environments as needed.

Typically, you will have an environment for "production", and a "staging" environment for testing your application. However, don't be afraid to create more environment for testing new features without interrupting your main staging environment.

## Creating Environments

Environments are "created" by adding a new environment entry to your project's `vapor.yml` file and deploying the environment.

## Vanity URLs

Each environment is assigned a unique "vanity URL" which you may use to access the application immediately after it is deployed. When serving these URLs, Vapor automatically adds "no-index" headers to the response so they are not indexed by search engines.

In addition, these secret URLs will continue to work even when an application is in maintenance mode, allowing you to test your application before disabling maintenance mode and restoring general access.

## Environment Variables

Each environment contains a set of environment variables that provide crucial information to your application during execution, just like the variables present in your application's local `.env` file.

### Updating Environment Variables

You may update an environment's variables via the Vapor UI or using the `env:pull` and `env:push` CLI commands. The `env:pull` command may be used to pull down an environment file for a given environment:

```bash
vapor env:pull production
```

Once this command has been executed, a `.env.{environment}` while will be placed in your application's root directory. To update the environment's variables, simply open and edit this file. When you are done editing the variables, use the `env:push` command to push the variables back to Vapor:

```bash
vapor env:push production
```

:::tip Variables & Deployments

After updating an environment's variables, the new variables will not be utilized until the application is deployed again. In addition, when rolling back to a previous deployment, Vapor will use the variables as they existed at the time the deployment you're rolling back to was originally deployed.
:::

:::warning Environment Variable Limits

Due to AWS Lambda limitations, your environment variables may only be 4kb in total. You should use "secrets" to store very large environment variables.
:::

## Secrets

Due to AWS Lambda limitations, your environment variables may only be 4kb in total. However, "secrets" may be much larger, making them a perfect way to store large environment variables such as Laravel Passport keys. You may create secrets via the Vapor UI or the `secret` CLI command:

```bash
vapor secret production
```

### Passport Keys

Since storing Laravel Passport keys is a common use-case for secrets. You may easily add your project's Passport keys as secrets using the `secret:passport` CLI command:

```bash
vapor secret:passport production
```

:::tip Secrets & Deployments

After updating an environment's secrets, the new secrets will not be utilized until the application is deployed again. In addition, when rolling back to a previous deployment, Vapor will use the secrets as they existed at the time the deployment you're rolling back to was originally deployed.
:::

## Maintenance Mode

When deploying a Laravel application using a traditional VPS like those managed by [Laravel Forge](https://forge.laravel.com), you may have used the `php artisan down` command to place your application in "maintenance mode". To place a Vapor environment in maintenance mode, you may use the Vapor UI or the `down` CLI command:

```bash
vapor down production
```

To remove an environment from maintenance mode, you may use the `up` command:

```bash
vapor up production
```

:::tip Maintenance Mode & Vanity URLs

When an environment is in maintenance mode, the environment's custom domain will display a maintenance mode splash screen; however, you may still access the environment via its "vanity URL"
:::

### Customizing The Maintenance Mode Screen

You may customize the maintenance mode splash screen for your application by placing a `503.html` file in your application's root directory.

## Commands

Commands allow you to execute an arbitrary Artisan command against an environment. You may issue a command via the Vapor UI or using the `command` CLI command. The `command` command will prompt you for the Artisan command you would like to run:

```bash
vapor command production

vapor command production --command="php artisan inspire"
```

## Metrics

A variety of environment performance metrics may be found in the Vapor UI or using the `metrics` CLI command:

```bash
vapor metrics production
vapor metrics production 5m
vapor metrics production 30m
vapor metrics production 1h
vapor metrics production 8h
vapor metrics production 1d
vapor metrics production 3d
vapor metrics production 7d
vapor metrics production 1M
```

## Deleting Environments

Environments may be deleted via the Vapor UI or using the `env:delete` CLI command:

```bash
vapor env:delete testing
```