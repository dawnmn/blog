**安装chrome**
```
yum install https://dl.google.com/linux/direct/google-chrome-stable_current_x86_64.rpm
google-chrome -version
```
安装chromedriver
[官网下载](https://chromedriver.chromium.org/downloads)，注意选择与chrome对应的版本（可能需要翻墙）
```
unzip chromedriver_linux64.zip
mv chromedriver /usr/bin
chromedriver -version

# 启动
chromedriver --port=4444
```
**PHP 测试**
```
composer require php-webdriver/webdriver
```
chrome相关的几处：
[ChromeDriverTest.php](https://github.com/php-webdriver/php-webdriver/blob/main/tests/functional/Chrome/ChromeDriverTest.php)
[ChromeDriver](https://github.com/php-webdriver/php-webdriver/wiki/ChromeDriver)
[ChromeOptions](https://github.com/php-webdriver/php-webdriver/wiki/ChromeOptions)
```
<?php
require_once "vendor/autoload.php";

use Facebook\WebDriver\Chrome\ChromeOptions;
use Facebook\WebDriver\Chrome\ChromeDriver;
use Facebook\WebDriver\Remote\DesiredCapabilities;
use Facebook\WebDriver\WebDriverBy;

putenv('WEBDRIVER_CHROME_DRIVER=/usr/bin/chromedriver');

$options = new ChromeOptions();
$options->addArguments(['--no-sandbox', '--headless']);

$caps = DesiredCapabilities::chrome();
$caps->setCapability(ChromeOptions::CAPABILITY, $options);

$driver = ChromeDriver::start($caps);
$driver->get("https://www.baidu.com/");

$element = $driver->findElement(WebDriverBy::xpath("/html/body/div[1]/div[1]/div[5]/div/div/div[3]/div/a[1]/div"));
echo $element->getText();

$driver->quit();
```