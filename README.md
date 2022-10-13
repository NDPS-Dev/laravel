# atomlite/laravel

Official Laravel library for NTT DATA Payment Service.

## Prerequisites
- A minimum of PHP 7.3 upto 8.1

## Installation
- If your project using composer, run the below command 
    
    ```sh
    composer require ndps/laravel
    ```

- If you are not using composer, download the latest release from the releases section. You should download the laravel.zip file from atomlite/laravel. And place in vendor folder.


## How To Use It

- To handle the request use below function which will provide the request URL.

    ```sh
      public function payment()
	 {
	   include_once base_path('vendor/autoload.php');
	   $transactionRequest = new \NDPS\TransactionRequest();

	   /* Add your return URL */
	   $ru = "http://127.0.0.1:8000/response";

	   /*
	   *Setting all values here
	   */
	   $transactionRequest->setLogin('192');
	   $transactionRequest->setPassword("Test@123");
	   $transactionRequest->setProductId("NSE");
	   $transactionRequest->setAmount('50.55');
	   $transactionRequest->setTransactionCurrency("INR");
	   $transactionRequest->setTransactionAmount('50.55');
	   $transactionRequest->setReturnUrl($ru);
	   $transactionRequest->setClientCode('NAVIN');
	   $transactionRequest->setTransactionId('0010');
	   $transactionRequest->setCustomerName("Test Name");
	   $transactionRequest->setCustomerEmailId("test@test.com");
	   $transactionRequest->setCustomerMobile("9999999999");
	   $transactionRequest->setCustomerBillingAddress("Mumbai");
	   $transactionRequest->setCustomerAccount("639827");
	   $transactionRequest->setReqHashKey("KEY123657234");
	   $transactionRequest->seturl("https://paynetzuat.atomtech.in/paynetz/epi/fts");
	   $transactionRequest->setRequestEncypritonKey("8E41C78439831010F81F61C344B7BFC7");
	   $transactionRequest->setSalt("8E41C78439831010F81F61C344B7BFC7");


	   $url = $transactionRequest->getPGUrl();
	   echo "<br>url:<br>".$url;
	   header("Location: $url");
	  }
    ```
    
- To handle the response use below function which will return the final response array.

    ```sh
       public function response()
	{
	 $decHashKey="KEYRESP123657234";
	 $decSaltKey="8E41C78439831010F81F61C344B7BFC7";
	 $decKey="8E41C78439831010F81F61C344B7BFC7";
				  
	 // to decrypt data
	 $decrypted = $this->decrypt($_POST["encdata"], $decSaltKey, $decKey);
			
	 $array_response = explode('&', $decrypted); //change & to | for production
	 $equalSplit = array();
	 foreach ($array_response as $ar) {
	   $equalSub = explode('=', $ar);
	   if(!empty($equalSub[1]) && !empty($equalSub[0])){
	     $temp = array(
	        $equalSub[0] => $equalSub[1],
	     );
	     $equalSplit += $temp;
	   }
	 }
			
	 $str = $equalSplit["mmp_txn"].$equalSplit["mer_txn"].$equalSplit["f_code"].$equalSplit["prod"].$equalSplit["discriminator"].$equalSplit["amt"].$equalSplit["bank_txn"];
			
	 $signature =  hash_hmac("sha512",$str,$decHashKey,false);
			
	 // signature verification
	 if($signature == $equalSplit["signature"]){
	 	  if($equalSplit['f_code'] == "Ok"){
	 		 echo "Success";
	 		}else if($equalSplit['f_code'] == "F"){
	 		 echo "Failed";
	 		}else{
	 		 echo "Cancelled";	
	 	  }    
	 } else {
	 	echo "signature verification failed";
	 }
       }
    ```
