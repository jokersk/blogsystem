---
title: curl
tags:
---

*post*

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
所以加上去也應該不會死錯人。