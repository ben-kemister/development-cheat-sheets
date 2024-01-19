---
title: PHP
tags:
 - php
 - html
---

[PHP](https://www.php.net/) is a general-purpose scripting language geared towards web development. 
<!--more-->
A popular general-purpose scripting language that is especially suited to web development.
It was originally created by Danish-Canadian programmer Rasmus Lerdorf in 1993 and released in 1995.

## Concatenating Strings

```php
echo "first" . "second";
```

## Retrieve URL parameters

```php
<?php

if (isset($_GET['myParam'])) {
    echo "Found 'myParam' in request";
} else {
    echo "No 'myParam' parameter found in request";
}

?>
```

## HTTP response code

```php
function getHttpCode($url){

	$ch = curl_init($url);

	// Exclude the body from the request to try and speed up the response
	curl_setopt($ch, CURLOPT_NOBODY, true);
	
	curl_exec($ch);
	$retcode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
	// $retcode >= 400 -> not found, $retcode = 200, found.
	curl_close($ch);

	return $retcode;
}
```