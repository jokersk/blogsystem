---
title: curl PHP
date: 2016-11-15 12:34:33
tags:
---


curl 是做不了跳板的，只系取得url裡面的內容。適合做api 
可以用來用get 和 post ， 把CURLOPT_POST設置成true 就是post


		$ch = curl_init();
		curl_setopt($ch, CURLOPT_URL, "http://SomeDomain/SamplePath");
		curl_setopt($ch, CURLOPT_POST, true); // 啟用POST
		curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query( array( "a"=>"123", "b"=>"321") )); 
		curl_setopt($ch, CURLOPT_SSLVERSION, 6);
		$result = curl_exec($ch);
		curl_close($ch);

裡面的

		curl_setopt($ch, CURLOPT_SSLVERSION, 6);

不一定要用到，不過有一次在一個server上無論如何也用不到curl,然後折騰半天,加了這個就成功了。

如果加入
		
		curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);

就會將結果存起來，不過直接輸出



