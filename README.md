# horde-deployment
A composer deployed horde suite

Versions:

- master    A Horde Base setup (development master versions)
- FRAMEWORK_6_0 A Horde Base with versioned/tagged releases to the FRAMEWORK_6 branch (currently alpha)
- FRAMEWORK_6_0_GROUPWARE A setup with with versioned/tagged releases. (currently alpha) Contains ansel (photo), content (tagger), imp (webmail), ingo (mail filters), kronolith (calendar), mnemo (notes), nag (tasks), passwd (password changing), timeobjects (misc data sources), turba (contacts)



## usage

```
git clone https://github.com/maintaina-com/horde-deployment.git horde
cd horde
composer install
```
Then copy the main horde config file to
point your web server root to the web/ subdir

For CI/CD scenarios, best fork and customize your deployment

## Customization

### Adding official horde apps

Install applications by requiring them on the commandline in the deployment dir

The example installs the ansel foto app

```
composer require horde/ansel
```

or edit the composer.json of the deployment and run

```
composer update
```

Then go to horde admin screen and create the conf.php or provide a pre-made conf.php

Finally create the db schema by either using the UI button or run

```
$deployment/web/horde/bin/horde-db-migrate ansel up
```

The installer will automatically add a horde.local.php to the
app's config dir and a registry snippet to make horde find the app.

### Adding custom apps

Adding custom apps is similar to official apps.
Unless the app is available on packagist, however, you need to add the app's
repository. Then you can require it like an official horde app

For a custom app "lunch" hosted on a company's internal gitlab installation, this would be

```
composer config repositories.lunch vcs https://git.mysite.com/development/lunch.git
composer require horde/lunch
Using version dev-master for horde/lunch
./composer.json has been updated
Loading composer repositories with package information
Updating dependencies (including require-dev)
Package operations: 1 install, 0 updates, 0 removals
  - Installing horde/lunch (dev-master d4e8bbd): Cloning d4e8bbdf0a from cache
horde/lunch suggests installing horde/activesync (^2.4)
horde/lunch suggests installing horde/backup (^1)
horde/lunch suggests installing horde/test (^2)
Writing lock file
Generating autoload files

```

Custom apps may need to be registered with your horde base.
To achieve this, either put a snippet into registry.d manually or add it to 
your app's doc/registry.d/ path to have the installer automatically copy it
into your horde/config/registry.d

An app must have at least name key, may provide an API or
define its menu location.

See horde wiki for further details.

Example snippet:
```
lunch> cat doc/registry.d/app-lunch.php
<?php
$this->applications['lunch']['name'] = _('Lunch');
$this->applications['lunch']['provides'] = 'lunch';

```

The installer will automatically add a horde.local.php to the
app's config dir and a registry snippet to make horde find the app.


### pre-made configs

You can provide pre-made configuration for your apps to be pulled into your
deployment upon install/update.

Create a presets/app dir and put files there like prefs.local.php
or a ready to run conf.php for your scenario.

Existing configs will not be overwritten.

Example: Deploy with a premade backends file for passwd app:

```
presets/passwd/backends.local.php
```

