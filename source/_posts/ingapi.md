---
title: Instagram api 教學
date: 2016-10-20 18:24:50
tags:
---


		$ig_user_name = "this ig user name";
		$url  = "https://www.instagram.com/".$ig_user_name."/media/";
		
		$curl_connection = curl_init($url);
		   curl_setopt($curl_connection, CURLOPT_CONNECTTIMEOUT, 30);
		   curl_setopt($curl_connection, CURLOPT_RETURNTRANSFER, true);
		   curl_setopt($curl_connection, CURLOPT_SSL_VERIFYPEER, false);

		  
		   $data = json_decode(curl_exec($curl_connection), true);
		   curl_close($curl_connection);
		var_dump($data);

