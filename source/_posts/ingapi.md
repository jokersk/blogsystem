---
title: Instagram api 教學
date: 2016-10-20 18:24:50
tags:
---



## Step 1. Instagram Access Token
參考
https://rudrastyh.com/tools/access-token
https://www.instagram.com/developer/authentication/


取得token 之後，就可以進行下一步 
## Step 2. Get photos	

		<?php
		//Get data from instagram api
		$hashtag = 'abc';
		
		$access_token = "the token you get above";
			
			$url = 'https://api.instagram.com/v1/tags/'.$hashtag.'/media/recent?access_token=' . $access_token;
			
			$curl_connection = curl_init($url);
			curl_setopt($curl_connection, CURLOPT_CONNECTTIMEOUT, 30);
			curl_setopt($curl_connection, CURLOPT_RETURNTRANSFER, true);
			curl_setopt($curl_connection, CURLOPT_SSL_VERIFYPEER, false);
			
			//Data are stored in $data
			$data = json_decode(curl_exec($curl_connection), true);
			curl_close($curl_connection);
			

			var_dump($data) ;
		?>

這裡 var_dump($data); 后會看到 一個 image 的 array, 按照平時的php foreach 方法就可以list 到圖片

上面用的是 hash tag 的方法。
如果想取得整個此用戶的照片又要怎麼呢？ 

下面可以用user id 的方法

先取得user id ，記住， usder id 不是 user name . 我們要通過 user name 取得user id 
先運行一下下面的代碼
		
		<?php
		
		
		  	$username = "your user name";
		
		
			$url = "https://api.instagram.com/v1/users/search?q=" . $username . "&access_token=" . $access_token;
			
			
			$curl_connection = curl_init($url);
			curl_setopt($curl_connection, CURLOPT_CONNECTTIMEOUT, 30);
			curl_setopt($curl_connection, CURLOPT_RETURNTRANSFER, true);
			curl_setopt($curl_connection, CURLOPT_SSL_VERIFYPEER, false);
			
			//Data are stored in $data
			$data = json_decode(curl_exec($curl_connection), true);
			curl_close($curl_connection);
			

			var_dump($data) ;
		?>


var_dump($data) 裡面有一個 user id 
再用這個user id 執行下面的 
		

		<?php
		//Get data from instagram api
		
		
			$user_id = "your user id";
		
		
			$url = "https://api.instagram.com/v1/users/" . $user_id . "/media/recent?access_token=" . $access_token;
			
			
			$curl_connection = curl_init($url);
			curl_setopt($curl_connection, CURLOPT_CONNECTTIMEOUT, 30);
			curl_setopt($curl_connection, CURLOPT_RETURNTRANSFER, true);
			curl_setopt($curl_connection, CURLOPT_SSL_VERIFYPEER, false);
			
			//Data are stored in $data
			$data = json_decode(curl_exec($curl_connection), true);
			curl_close($curl_connection);
			

			var_dump($data) ;
		?>

這裡 var_dump($data) 就是想要的東西了

