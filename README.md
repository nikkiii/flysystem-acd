# Flysystem Adapter for Amazon Cloud Drive

This is an [Amazon Cloud Drive](https://www.amazon.com/clouddrive) adapter for [Flysystem](http://flysystem.thephpleague.com/).

## Requirements

This requires an updated version of alex-phillips/clouddrive-php (which is pending, the 'streams' pull request)

Until then, composer.json includes a repository entry to override it.

## Installation

Composer is the best way, as with all of Flysystem!

```bash
composer require nikkiii/flysystem-acd
```

## Usage

This is a bit tougher, since there's no automated way to authenticate amazon cloud drive. You'll need a client id and secret from Cloud Drive's API, and to manually pass the url into the authorize method on the CloudDrive account object.

Initial setup would be something like this:

```php
use CloudDrive\Cache\SQLite;
use CloudDrive\CloudDrive;
use CloudDrive\Node;

$cache = new SQLite('email', './cache');

$drive = new CloudDrive('email', 'client id', 'secret', $cache);

$response = $drive->getAccount()->authorize();

if (!$response['success']) {
	print_r($response); // Get the URL from here
}

// Input the resulting redirected url
$url = readline();

$response = $drive->getAccount()->authorize($url);

// Initialize Node
Node::init($drive->getAccount(), $cache);

$flysystem = new Filesystem(new AmazonCloudDrive($drive));

// Access flysystem like usual
```

Repeat usage would be simpler (no authentication url)

```php
use League\Flysystem\Filesystem;
use Nikkii\Flysystem\ACD\AmazonCloudDrive;
use CloudDrive\Cache\SQLite;
use CloudDrive\CloudDrive;
use CloudDrive\Node;

$cache = new SQLite('email', './cache');

$drive = new CloudDrive('email', 'client id', 'secret', $cache);

$response = $drive->getAccount()->authorize();

if (!$response['success']) {
	// Something is wrong
	return;
}

// Initialize Node
Node::init($drive->getAccount(), $cache);

$flysystem = new Filesystem(new AmazonCloudDrive($drive));

// Access flysystem like usual
```