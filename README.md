## Composer shared packages plugin
Makes package development easier by sharing them between projects

### Introduction

This Composer plugin simplifies the development of packages that are being used on multiple projects.

It updates the package installation directory (or directories) of each project to replace some packages with symlinks to a central installation location.

This is quite useful when you want to:

  - edit a package on its own repository or on any of the projects that share it;
  - see your changes to a package applied immediately to any installed project that shares that package;
  - define an authenticated `origin` remote with write permissions only once for each package being developed;
  - register only one VCS directory on your VCS GUI client for each package being developed.

#### Features

There are some other Composer plugins with a similar goal of providing this symlinking capability but, at the time of development of this plugin, none had all the features below.

This plugin

- can be installed and configured globally on a development machine, thereby avoiding the need to change the `composer.json` of each project where you want to use it;

- supports custom package installation directories, where some packages may use installer plugins that install them to alternative locations (other than the default `vendor` directory);

- supports customizing the vendor directory location using the `config.vendor-dir` standard Composer setting.

#### Versioning support

Currently, the plugin does not support multiple syde-by-side installations of the same package, with different versions.

When performing a `composer update` on a project, the shared packages will be updated to the versions required by that project. That may break other installed projects that require different versions.

You will need to rembember to manually update a project using `composer update` before you start development on it.

If you perform a `composer update` on a project where a shared package has uncommited changes, Composer will warn you and allow you to either keep, discard or commit those changes before the installation proceeds.

### Requirements

- PHP version >= 5.4
- Operating System: Mac OS X or Linux
- Windows (Vista, Server 2008 or greater) should work but it's not tested

### Installing

The **recommended** setup is to **install the plugin globally** and set its configuration also globally. This way you avoid polluting each project's `composer.json` with information relative to this plugin which will be of no use neither to other developers nor to users of those projects.

##### To install globally on your development machine

If it doesn't exist yet, create a `composer.json` file on the composer's configuration folder (usually at `~/.composer`).

> Ex: `~/.composer/composer.json`

Add `php-kit/composer-shared-packages-plugin` to the `composer.json`'s `require` setting.

Run `composer global update` anywhere.

> The plugin will be active for **all** projects on your machine.
But fear not! No packages will be shared and no project will be affected until you configure it to share **specific** packages.

> Alternatively, you may install the plugin on a project-by-project basis, but configure it globally.

##### To install locally, per project

Add `php-kit/composer-shared-packages-plugin` to the `composer.json`'s `require-dev` setting of those projects that will use shared packages.

Run `composer update` on the project folder.

> On production, this plugin will not be installed and no packages wil be shared if you run `composer install -no-dev`. That is why the plugin name should be added to `required-dev` and not to `require`.

### Configuring

> From this point on, when mentioning `composer.json`, we mean the global one or the project one, depending on the type of installation you have chosen.

> The plugin will load both the global configuration (if one exists) and the project configuration (again, if one exists) and merge them.
Project-specific settings will take precedence over global ones.

Add an extra `shared-packages` configuration section to the `composer.json`.

On that section, you can specify a `match` setting containing an array of `vendor/packages` names or glob patterns.

> If neither a `match` setting exists, nor a name or pattern are specified, no packages will be shared.

You can also specify a `sharedDir` setting that defines the directory path of the central installation location for shared packages. It can be a relative or absolute path and you can also use `~` as an alias to the user's home directory. The default is `~/shared-packages`.

> These configuration settings only apply to the root `composer.json` of your project. Settings specified on packages will have no effect.

> You can use Wikimedia's `composer-merge-plugin` if you need to also merge those settings with the ones from the main project.

#### Example

###### global `composer.json` at `~/.composer`

```json
{
  "require": {
    "php-kit/composer-shared-packages-plugin": "^1.1"
  },
  "extra": {
    "shared-packages": {
      "match": [
        "vendor/package",
        "vendor/prefix-*",
        "vendor/package-*-whatever",
        "vendor/wildcards??",
        "vendor/*",
        "*"
      ],
      "sharedDir": "~/shared-packages"
    }
  }
}
```

### Debugging

You can run Composer in debug mode with the `-vvv` flag to enable the output of information messages, which can be useful for troubleshooting.

Messages output by this plugin will be prefixed with `[shared-packages-plugin]`.

### License

This library is open-source software licensed under the [MIT license](http://opensource.org/licenses/MIT).

Copyright &copy; 2015 by Impactwave Lda <impactwave@impactwave.com>
