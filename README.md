phpunitdemo
===========

What is unit testing ?
------------------------------
	A unit is simply a chunk of functionality that performs a specific action where you can test the outcome.
A unit test, then, is a sanity check to make sure that the chunk of functionality does what it’s supposed to.



Once you’ve written up your set of tests, whenever you make a change to your code, all you have to do is run the set of tests and watch everything pass. That way, you can be reassured that you haven’t inadvertently broken another part of your application.



Benfits for Unit Testing
-----------------------------------
1. Unit Testing reduces the level of bugs in production code.
2. Unit Testing saves you development time.
3. Automated tests can be run as frequently as required.
4. Unit Testing makes it easier to change and refactor code.
5. Unit Testing can improve the design of code especially with Test-Driven Development.
6. Unit Tests are a form of documentation.
8. Unit Testing inspires confidence!
9. Unit Tests are a measure of completion.


Unit Testing in php with PHPunit
---------------------------------------
PHPUnit is a unit testing framework for the PHP programming language. It is an instance of the xUnit architecture for unit testing frameworks that originated with SUnit and became popular with JUnit. PHPUnit was created by Sebastian Bergmann

Installing phpunit :
----------------------------------
It can be easily installed via composer
Steps :
1) In your project root install composer by runnning command
$ curl -s https://getcomposer.org/installer | php
2) Create a composer.json file that will give us required dependancies
{
    "require": {
    },
    "require-dev": {
        "phpunit/phpunit": "3.7.14"
    },
    "autoload": {
        "psr-0": {
            "system": ""
        }
    }
}
3) Run php composer.phar install
This will download all dependancies
4) Create a 'system' folder in your project root
Finally your project structure would look like
 /composer.json
/composer.phar
/system
/vendor
5) Setting up phpunit.xml
    Running PHPUnit will go through your tests using built-in defaults.
 You can override many defaults via the command line, but there is a better way: the phpunit.xml configuration file.
In the root of your project create a phpunit.xml with the following contents:
<?xml version="1.0" encoding="UTF-8"?>
<phpunit colors="true">
    <testsuites>
        <testsuite name="Application Test Suite">
            <directory>./system/Test/</directory>
        </testsuite>
    </testsuites>
</phpunit>
This is an extremely slim, simple configuration file, but it sets two important options:
colors="true" makes sure your test results are in color, and
<directory>./system/Test/</directory> tells PHPUnit where your tests will be located, so you don’t have to manually tell it every time you run your tests.
Create a 'Test' folder inside system
final folder structure would look like
/composer.json
/composer.phar
/system/
/system/Test/
/vendor/

CONVENTIONS
PHPUnit has a few conventions to make your life easier. You do not necessarily have to follow them if you want to do things slightly differently, but for our purposes we are going to stick to them.

File structure and filenames

Your tests should mirror your codebase directly but within its own folder, and test files must match the file they are testing, with Test appended. In our example, if we had the following code
/system/simple.php
Our tests would be structured as:
/system/Test/simpleTest.php
Classnames
Classnames are exactly the same as filenames. Whatever you have named your file should be the name of your class - which should be true for your non-test code anyway!
Method (test) names
Your test method names should start with 'test', in lower case. Method names should be descriptive of what is being tested, and should include the name of the method being tested. This is not a place for short, abbreviated method names.
For example, if you are testing a method called sluggify(), and in one unit test you want to test that it returns expected result, you would name your test testSluggifyStringReturnsExpectedString().
Methods must be public
PHPUnit is unable to run tests that are either protected or private - they must be public

Extends PHPUnit
Your classes must extend the PHPUnit_Framework_TestCase class, or extend a class that does.


Examples :
ASSERTIONS

Assertion verifies a statement made equals true.
e.g
public function testTrueIsTrue()
{
    $test = true;
    $this->assertTrue($test);
}

we asserted that true will assert to true ( if (true == true) )

PHPUnit comes with  with 90 assertions but following are most useful assertions
assertArrayHasKey(), assertEquals(), assertFalse(), assertSame() and assertTrue().










Custom Test
Lets create a file in /system/Simple.php
and have following code there.
Simple.php
<?php

namespace system;

class Simple
{
    public function sluggify($string, $separator = '-', $maxLength = 96)
    {
        $title = iconv('UTF-8', 'ASCII//TRANSLIT', $string);
        $title = preg_replace("%[^-/+|\w ]%", '', $title);
        $title = strtolower(trim(substr($title, 0, $maxLength), '-'));
        $title = preg_replace("/[\/_|+ -]+/", $separator, $title);

        return $title;
    }
}

This is really good example to understand the process, sluggify function just takes the string and returns
a string. And we can easily predicate the outcome. For example
if input string is 'This is a string' , then output string should be 'This-is-a-string'.

Lets create a Test case for this function in /system/Test/SimpleTest.php
SimpleTest.php
<?php

namespace system\Test;

use system\Simple;

class SimpleTest extends \PHPUnit_Framework_TestCase
{
    public function testSluggifyReturnsSluggifiedString()
    {
        $originalString = 'This string will be sluggified';
        $expectedResult = 'this-string-will-be-sluggified';
        $simple = new Simple();
        $result = $simple->sluggify($originalString);
        $this->assertEquals($expectedResult, $result);
    }
}
 and Run command vendor/bin/phpunit   in project root.
And output would be OK (1 test, 1 assertion)



DataProvider
The above example just test for the string and not for numbers,special characters.
PhpUnit comes with a feature called dataProvider , which can pass mulitple aruguments to the testing
function .
e.g
Lets modify our SimpleTest.php to following

<?php

namespace system\Test;

use system\Simple;

class SimpleTest extends \PHPUnit_Framework_TestCase
{
    /**
     * @param string $originalString String to be sluggified
     * @param string $expectedResult What we expect our slug result to be
     *
     * @dataProvider providerTestSluggifyReturnsSluggifiedString
     */
    public function testSluggifyReturnsSluggifiedString()
    {
        $originalString = 'This string will be sluggified';
        $expectedResult = 'this-string-will-be-sluggified';
        $simple = new Simple();
        $result = $simple->sluggify($originalString);
        $this->assertEquals($expectedResult, $result);
    }
    public function providerTestSluggifyReturnsSluggifiedString()
    {
        return array(
            array('This string will be sluggified','this-string-will-be-sluggified'),
            array('THIS STRING WILL BE SLUGGIFIED','this-string-will-be-sluggified'),
            array('This1 string2 will3 be 44 sluggified10','this1-string2-will3-be-44-sluggified10'),
            array('This! @string#$ %$will ()be "sluggified','this-string-will-be-sluggified'),
            array("Tänk efter nu – förr'n vi föser dig bort",'tank-efter-nu-forrn-vi-foser-dig-bort'),
            array('', ''),
        );
    }
}




And Run vendor/bin/phpunit in project root. And output would be
OK (6 tests, 6 assertions)
Which is corresponding to 6 tests