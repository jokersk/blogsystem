---
title: paypal API
date: 2016-10-07 17:34:27
tags:

---

## 這裡記錄一下 paypal 的 API, 讓以後需要用到的時候可以查到。

#### <span style="color:#83adef">(因為我寫這篇blog的時候已經距離研究這個api的時候有一段日子了，所以我不一定下面的做法都是對的)</span>

paypal 有3種比較常用的api 方法 
* **REST Api**
* **NVP Api**
* **HTML**

---

### REST :
這裡先說流程


代碼最重要:

*這是取得token的方法*


		public function gettoken()
		{


			$ch = curl_init();
			$clientId = "your client id";
			$secret = "your secret";

			curl_setopt($ch, CURLOPT_URL, "https://api.sandbox.paypal.com/v1/oauth2/token");
			curl_setopt($ch, CURLOPT_HEADER, false);
			curl_setopt($ch, CURLOPT_SSL_VERIFYHOST,0);
			curl_setopt($ch, CURLOPT_SSL_VERIFYPEER,0);
			curl_setopt($ch, CURLOPT_POST, true);
			curl_setopt($ch, CURLOPT_RETURNTRANSFER, true); 
			curl_setopt($ch, CURLOPT_USERPWD, $clientId.":".$secret);
			curl_setopt($ch, CURLOPT_POSTFIELDS, "grant_type=client_credentials");
			curl_setopt($ch, CURLOPT_SSLVERSION, 6);

			$result = curl_exec($ch);

			if(empty($result))die(curl_error($ch));
			else
			{
				$json = json_decode($result);

				$return  =  $json->access_token;
			}

			curl_close($ch);
			return $return;

		}

---
*拿到token后就可以創建一個payment*



		public function rest_api(){
			$token = $this->gettoken();
			$payment_json = '{
				"intent": "sale",
				"payer": {
					"payment_method": "paypal"
				},
				"redirect_urls": {
					"return_url": "http://return_URL_here",
					"cancel_url": "http://cancel_URL_here"
				},
				"transactions": [
				{
					"amount": {
						"total": "8.00",
						"currency": "HKD",
						"details": {
							"subtotal": "6.00",
							"tax": "1.00",
							"shipping": "1.00"
						}
					},
					"description": "This is payment description.",
					"item_list": { 
						"items":[
						{
							"quantity":"3", 
							"name":"Hat", 
							"price":"2.00",  
							"sku":"product12345", 
							"currency":"HKD"
						}
						]
					}
				}
				]
			}';


			$ch = curl_init();

			curl_setopt($ch, CURLOPT_URL, "https://api.sandbox.paypal.com/v1/payments/payment");
			curl_setopt($ch, CURLOPT_HEADER, false);
			curl_setopt($ch, CURLOPT_SSL_VERIFYHOST,0);
			curl_setopt($ch, CURLOPT_SSL_VERIFYPEER,0);
			curl_setopt($ch, CURLOPT_POST, true);
			curl_setopt($ch, CURLOPT_RETURNTRANSFER, true); 
			curl_setopt($ch, CURLOPT_HTTPHEADER, array(
				'Authorization: Bearer '.$token,
				'Accept: application/json',
				'Content-Type: application/json'
				));
			curl_setopt($ch, CURLOPT_POSTFIELDS, $payment_json);
			curl_setopt($ch, CURLOPT_SSLVERSION, 6);

			$result = curl_exec($ch);
			curl_close($ch);
			
			$array = json_decode($result);
			// var_dump($array);
			$redirect_urls = $array->links['1']->href;
			header("location: ".$redirect_urls); 

		}

裡面的 
		
		$token = $this->gettoken();

就是用了上面的 get token 方法取的那個token 

其中 

		$redirect_urls = $array->links['1']->href;


這裡會因為創建這個payment 之後 paypal 會 return 一串 json 來， 其中包括大概這樣子的東西： 
		
		"links":[
	    {
	      "href":"https://api.sandbox.paypal.com/v1/payments/payment/PAY-6RV70583SB702805EKEYSZ6Y",
	      "rel":"self",
	      "method":"GET"
	    },
	    {
	      "href":"https://www.sandbox.paypal.com/webscr?cmd=_express-checkout&token=EC-60U79048BN7719609",
	      "rel":"approval_url",
	      "method":"REDIRECT"
	    },
	    {
	      "href":"https://api.sandbox.paypal.com/v1/payments/payment/PAY-6RV70583SB702805EKEYSZ6Y/execute",
	      "rel":"execute",
	      "method":"POST"
	    }
	  	]

一個 "link" 的array. 我們要的就是中間的  https://www.sandbox.paypal.com/webscr?cmd=_express-checkout&token=EC-60U79048BN7719609
然後我們用 

		header("location: ".$redirect_urls); 

跳到這里，這時候其實就去了付款的頁面了. 

#### <span style="color:#83adef">(我寫這篇文章的時候，paypal正在換付款的layout，所以有些人看到會是新的界面，有些人看到舊的界面。這個是要看臉的)</span>

在付款的頁面, 買家就會付款購買，然後會跳轉會你之前set的那個return_url里的link,
當然如果買家不買了cancel了,就會跳轉到cancel_url的link里。

<!-- ![GitHub Logo](/images/1.png) -->

### NVP API :
nvp api 需要用到 username , password , 和 signature 
這三個東西在這裡搞 
https://developer.paypal.com/docs/classic/api/apiCredentials/ 


		public function nvp_api($amount,$custom)
		{
			$nvp = array(
				    'LOCALECODE'                        => 'zh_HK',
				    'PAYMENTREQUEST_0_PAYMENTACTION'	=> 'Sale',
				    'PAYMENTREQUEST_0_AMT'              => $amount,
				    'PAYMENTREQUEST_0_CURRENCYCODE'     => 'HKD', 
				    'PAYMENTREQUEST_0_ITEMAMT'          => $amount,
				    'PAYMENTREQUEST_0_CUSTOM'			=> $custom,
				    'L_PAYMENTREQUEST_0_NAME0'          => 'payment name here',
				    'L_PAYMENTREQUEST_0_DESC0'          => 'payment description here',
				    'L_PAYMENTREQUEST_0_AMT0'           => $amount,
					
				    'RETURNURL'                         => return url here,
				    'CANCELURL'				=> cancel url here,
				    'METHOD'				=> 'SetExpressCheckout',
				    'VERSION'				=> '124',
				    'PAYMENTREQUEST_0_SHIPTOSTATE'      => '1', 
				    'PWD'				=> 'your password',
				    'USER'				=> 'your user',
				    'SIGNATURE'			=> 'your signature' 
			);

				$curl = curl_init();
				curl_setopt( $curl , CURLOPT_URL , 'https://api-3t.sandbox.paypal.com/nvp' );
				curl_setopt( $curl , CURLOPT_SSL_VERIFYPEER , false );
				curl_setopt($curl, CURLOPT_SSLVERSION, 6);
				curl_setopt( $curl , CURLOPT_RETURNTRANSFER , 1 );
				curl_setopt( $curl , CURLOPT_POST , 1 );
				curl_setopt( $curl , CURLOPT_POSTFIELDS , http_build_query( $nvp ) );
				$response = urldecode( curl_exec( $curl ) );
				curl_close( $curl );
				$responseNvp = array();
				if ( preg_match_all( '/(?<name>[^\=]+)\=(?<value>[^&]+)&?/' , $response , $matches ) ) {
					foreach ( $matches[ 'name' ] as $offset => $name ) {
						$responseNvp[ $name ] = $matches[ 'value' ][ $offset ];
					}
				}

				if ( isset( $responseNvp[ 'ACK' ] ) && $responseNvp[ 'ACK' ] == 'Success' ) {
					$paypalURL = 'https://www.sandbox.paypal.com/br/cgi-bin/webscr';
					$query = array(
						'cmd'	=> '_express-checkout',
						'useraction' => 'commit', 
						'token'	=> $responseNvp[ 'TOKEN' ]
					);
				 	
				 	 $paypal_link  =  $paypalURL . '?' . http_build_query( $query );
					
				} 
					

				header("location: ".$paypal_link);
			
		}