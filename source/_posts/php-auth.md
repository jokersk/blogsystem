---
title: php Auth
date: 2017-01-24 12:39:58
tags:
---

.htaccess  放這個, 放在其他RewriteRule上面

		  RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

php 里放這個
		
		if (empty($_SERVER['HTTP_AUTHORIZATION'])) {

			 header('WWW-Authenticate: Basic realm="My Realm"');
			 header('HTTP/1.0 401 Unauthorized');
			 echo 'Text to send if user hits Cancel button';
			 exit;
		}
		else{
			
			var_dump($_SERVER['PHP_AUTH_USER'], $_SERVER['PHP_AUTH_PW']);
		}