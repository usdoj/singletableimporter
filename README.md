# SingleTableImporter

This class is intended as a simple importer for moving source data into a single MySQL table. It provides a few automations, such as text alterations, and date alterations, which is why it might be better than simply importing the file with phpMyAdmin or something similar. It supports currently supports CSV files and single-worksheet Excel spreadsheets, and assumes that the first row of data corresponds exactly to the column names in the MySQL database.

## Dependencies

* PHP 5.3.2 or higher
* MySQL 5.5 or higher

## Dev dependencies

* Composer

## Installation

Use composer to bring this into your PHP project. The composer.json should look like this:

```
{
    "require": {
        "usdoj/singletableimporter": "dev-master"
    },
    "repositories": [
        {
            "type": "vcs",
            "url": "https://github.com/usdoj/singletableimporter.git"
        }
    ]
}
```

After creating a composer.json similar to the above, do a "composer install".

## Usage

To use the library you need to include the autoloader, and then instantiate the object referencing your configuration and source data file. The configuration must be an object of the \Noodlehaus\Config class. Then you simply call the run() method. For example:

```
require_once '/path/to/my/libraries/csvtomysql/vendor/autoload.php';
$config = new \Noodlehaus\Config('/path/to/my/configFile.yml');
$sourceFile = '/path/to/my/sourceFile.csv';
$importer = new \USDOJ\CsvToMysql\Importer($config, $sourceFile);
$importer->run();
```

## Configuration

The library depends on configuration in an \Noodlehaus\Config object. Here are examples (in YAML) of the settings that should exist in that config object:
```
# Database credentials: this is the only required section.
database name: myDatabase
database user: myUser
database password: myPassword
database host: localhost
database table: myTable

# If there are any special characters or phrases that need to be altered when
# importing the data from the CSV file, indicate those here. For example, to
# change all occurences of § with &#167; uncomment the lines below.
#text alterations:
#    "§": "&#167;"
# Additionally you can specify alterations for a particular column only.
#text alterations per column:
#    myDatabaseColumn1:
#        "§": "&#167;"

# If the environment needs to use a proxy, uncomment and fill out this section.
# proxy: 192.168.1.1:8080

# To prevent the use of the proxy for certain URLs, enter partial patterns here.
# proxy exceptions:
#    - .example.com

# To use a certain user agent for remote requests, uncomment and indicate here.
# user agent: Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2228.0 Safari/537.36

# Date values can be in a wide range of formats. This library tries to be
# forgiving, but sometimes you need to specify the specific format. The
# syntax to use here is what would be passed to DateTime::createFromFormat.
# Reference: http://php.net/manual/en/datetime.createfromformat.php
# Note that each column has an array of formats, to allow for multipel formats
# within a single column.
date formats:
    "myDatabaseColumn":
        # Parse date values like 11/7/2011 (November 7th, 2011)
        - "n/j/Y"
        # Parse date values like 11-07-11 (November 7th, 2011)
        - "m-d-y"

# Indicate the columns that are required to have data in order for a row to
# to be imported. For example, if you don't want any rows to be imported
# without titles, make your title column a required column here.
required columns:
    - myDatabaseColumn1

# Set the delimiter for CSV imports. Defaults to a comma.
csv delimiter: ','

# Set the enclosure for CSV imports Defaults to a double-quote.
csv enclosure: '"'
```
