## About

This project is designed to be included from the **require** section of a Drupal site's `composer.json` file.  Doing this allows you to achieve the following things:

* Specify the Drupal modules, themes and libraries you use in your composer.json file, and build them with Composer.
* Automatically build components via Circle CI every commit.
* Use Behat to run tests on your site from Circle CI.
* Automatically deploy your site to your Pantheon dev environment, or some other branch, every time the tests pass.

All of this can be accomplished with only a few light files committed to your repository.

## Setup

Copy the contents of the `examples` directory to the root of your project, renaming files as appropriate:
```
  example.gitignore     -> .gitignore
  example.circle.yml    -> circle.yml
  example.behat.yml     -> behat.yml
  features              -> features
```
You will also need a composer.json file for your project.  The project [example-drupal7-circle-composer](https://github.com/pantheon-systems/example-drupal7-circle-composer) can be used as a template to quickly create your own project; for Drupal 8, see [drupal-composer/drupal-project](https://github.com/drupal-composer/drupal-project).

## Configuration

You must customize the contents of these files to suit the needs of your project.  See the detailed instructions below; more information is also available in the comments inside each file.

#### Composer

Do not use the composer.json file included in the pantheon-systems/circle-scripts project; instead, use one from a project mentioned in the section **Setup**, above.

Set the **name** and **description** in your composer.json file to something appropriate for your project.  

Customize the **require** section to contain the modules and themes needed for your project.  You might want to try using [drush composer-generate](https://www.drupal.org/project/composer_generate) to get started.  If you want to run your site on Pantheon, then you should keep "pantheon-systems/drops-7" as your main component; otherwise, you may replace this with "drupal/drupal" if you prefer.

The custom installers in the **require** section of your composer.json file control the way the components in your project are installed. Always keep these items at the top, so that they are available at the very beginning of the installation process.  Modules and themes listed before the custom installers might not install correctly.

#### Circle CI

Push-to-pantheon for Circle CI is not provided yet; the other parts of the example circle.yml file should run without modification.

See the [Circle CI documentation](https://circleci.com/docs/getting-started) for instructions on how to set up GitHub integration, so that your code will be automatically tested on every commit.

#### Behat

This sample is set up to run a single behat test that confirms that the name of the site was set correctly by `drush site-install`.  Note that the first part of the site name is set by the `SITE_NAME` environment variable that you customize in your .travis.yml file; the second part of the site name is set to `Travis Test Site` on Travis, and `Pantheon Test Site` on Pantheon.

See the [behat documentation](http://docs.behat.org/en/latest/) for further instructions on adding more tests to your project.

## Local Testing

Once setup is complete, doing local testing is a simple matter of:
```
$ composer install
$ ./bin/local-test
```
