This project is deprecated; it has been replaced with a newer and more comprehensive [example Drupal 7 Circle CI Composer project](https://github.com/pantheon-systems/example-drupal7-circle-composer), which can be used to quickly create your own derived project.

## Original Documentation

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

Set up your project to be tested by Circle CI:

* Log on to https://circleci.com in your web browser.
* Click on the "+", "Add Projects", on the left-hand menu bar.
* Find the repository you would like to configure to test.  Navigate to the right organization, if necessary, and use the "Filter repos..." field to reduce the size of the displayed list.
* Click "Build Project" to enable the repository and build it for the first time.

Next, set up the environment variables used by the push-to-pantheon script:

Variable Name | Value
------------- | --------------------------------------------
SITE_NAME     | The name of the site used in "Site Install"
PSITE         | The name of the Pantheon site to push to
PENV          | The Pantheon site environment to overwrite on each test
PEMAIL        | The email used to log in to a Pantheon User with access to the site
PPASS         | The password for the acount specified by PEMAIL
CI_BOT_EMAIL  | The email address to use in the git commit attribution
CI_BOT_NAME   | The name to use in the git commit attribution

Edit these in Project Settings > Environment Variables (https://circleci.com/gh/ORG/PROJECT/edit#env-vars).

You also need to set up an ssh key, so that the push-to-pantheon script can commit changes to the Pantheon git repository.

* [Create a public/private ssh key pair](https://help.github.com/articles/generating-ssh-keys/). You might want to use a different key than the one you use with GitHub.
* [Add the public ssh key to your Pantheon account](https://pantheon.io/docs/articles/users/loading-ssh-keys/).
* [Add the private ssh key to Circle CI](https://circleci.com/docs/permissions-and-access-during-deployment). You may either leave the "Host" field blank, or set it to "codeserver.$PENV.$PUUID.drush.in", where "$PENV" is replaced with the same value you used in the PENV environment variable, and "$PUUID" is replaced with the [UUID for your Pantheon site](https://pantheon.io/docs/articles/sites/).

See the [Circle CI documentation](https://circleci.com/docs/getting-started) for more information on configuration.

#### Behat

This sample is set up to run a single behat test that confirms that the name of the site was set correctly by `drush site-install`.  Note that the first part of the site name is set by the `SITE_NAME` environment variable that you customize in your .travis.yml file; the second part of the site name is set to `Travis Test Site` on Travis, and `Pantheon Test Site` on Pantheon.

See the [behat documentation](http://docs.behat.org/en/latest/) for further instructions on adding more tests to your project.

## Local Testing

Once setup is complete, doing local testing is a simple matter of:
```
$ composer install
$ ./bin/local-test
```
