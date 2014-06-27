# Facebook Ads API SDK for PHP

This Ads API SDK is built to facilitate application development for [Facebook Ads API](https://developers.facebook.com/docs/ads-api).

## Installation

The Facebook Ads API SDK requires PHP 5.4 or greater.

### Composer

Facebook Ads API SDK uses composer to manage dependencies. You can follow this [document](https://getcomposer.org/download/) to install composer.

Add the following to your `composer.json` file:

```json
{
    "require": {
        "facebook/php-ads-sdk": "*"
    }
}
```
then install it through composer:

```shell
php composer.phar install --no-dev
```

This SDK and its dependencies will be installed under `./vendor`.

### Alternatives

This repository is written following the [psr-4 autoloading standard](http://www.php-fig.org/psr/psr-4/). Any psr-4 compatible autoloader can be used.

## Usage

### Api main class

The `FacebookAds\Api` object is the foundation of the Ads SDK which encapsulates a `Facebook\FacebookSession` and is used to execute requests against the Graph API.

To instantiate an Api object you will need a valid access token:
```php
use FacebookAds\Api;
use Facebook\FacebookSession;

// Set the default application information to be used with this session
FacebookSession::setDefaultApplication($app_id, $app_secret);
$session = new FacebookSession($access_token);
$api = new Api($session);
```

Once instantiated, the Api object will allow you to start making requests to the Ads API.

### Fields names

Due to the high number of field names in the Ads API existing objects, in order to facilitate your code maintainability, enum-like classes are provided.
These files are stored under the `FacebookAds/Object/Fields` directory.
You can access object properties in the same manner you would usually do in php:

```php
use FacebookAds\Object\AdAccount;

$account = new AdAccount();
$account->name = 'My account name';
echo $account->name;
```

or using the enums:

```php
use FacebookAds\Object\AdAccount;
use FacebookAds\Object\Fields\AdAccountFields;

$account = new AdAccount();
$account->{AdAccountFields::NAME} = 'My account name';
echo $account->{AdAccountFields::NAME};
```

### Object classes

Facebook Ads entities are defined as classes under the `FacebookAds/Object` directory. 

#### Read Objects

```php
use FacebookAds\Object\AdAccount;

$account = new AdAccount($account_id);
$account->read();
```

For some objects, the Ads API doesn't return all available fields by default. The first argument of the object's read method is an array of field names to be requested.

```php
use FacebookAds\Object\AdAccount;
use FacebookAds\Object\Fields\AdAccountFields;

$fields = array(
  AdAccountFields::ID,
  AdAccountFields::NAME,
  AdAccountFields::DAILY_SPEND_LIMIT,
);

$account = new AdAccount($account_id);
$account->read($fields);
```
Requesting an high number of fields my cause the response time to visibly increase, you should always request only the fields you really need.

#### Create Objects

```php
use FacebookAds\Object\AdSet;
use FacebookAds\Object\Fields\AdSetFields;

$account_id = 'act_123123';
$campaign_group_id = '123456';

$set = new AdSet(null, $account_id);
$set->setData(array(
  AdSetFields::NAME => 'My Test AdSet',
  AdSetFields::CAMPAIGN_STATUS => AdSet::STATUS_PAUSED,
  AdSetFields::CAMPAIGN_GROUP_ID => $campaign_group_id,
  AdSetFields::DAILY_BUDGET => '150',
  AdSetFields::START_TIME => (new \DateTime("+1 week"))->format(\DateTime::ISO8601),
  AdSetFields::END_TIME => (new \DateTime("+2 week"))->format(\DateTime::ISO8601),
));
$set->create();
echo $set->id;
```

#### Update Objects

```php
use FacebookAds\Object\AdSet;
use FacebookAds\Object\Fields\AdSetFields;

$ad_set_id = '123456';

$set = new AdSet($ad_set_id);
$set->{AdSetFields::NAME} = 'My new AdSet name';
$set->update();
```

#### Delete Objects

```php
use FacebookAds\Object\AdSet;

$ad_set_id = '123456';

$set = new AdSet($ad_set_id);
$set->delete();
```

## Tests

The 'test' folder contains the test cases.

### Install dependencies

From the root folder run:

```shell
php composer.phar install
```

### Configure tests

1 - Copy the config file template.

```shell
cp test/config.php.dist test/config.php
```

2 - Edit `test/config.php` with your information.


### Run tests

```shell
./vendor/bin/phpunit -c test/
```

To run tests individually:

```shell
./vendor/bin/phpunit -c test/ path/to/class/file
```
