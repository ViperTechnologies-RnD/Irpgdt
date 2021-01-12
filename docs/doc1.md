---
id: doc1
title: Documentation
sidebar_label: Dev Doc
slug: /
---

This is the developer friendly translation of [pardakhtcard Iranian Payment Gateway](https://pardakhtcard.com/doc).

## SEND

### First Stage: Send Data
In the first stage you have to send the *parameters* defined in the tables below, with the **_POST_** method to the following address:

:::important

POST

:::

```
https://pardakhtcard.com/api/payment/send
```
:::note

The datatype returned from **Pardakhtcard.com** is in JSON.

:::

#### Parameters

| Field         |   DataType    |   Description |
| ------------- | :-----------: | :-----: |
| api           |   String      | API KEY that can be Retrieve from your User Panel, only after gateway request verification. |
| amount        |   Integer     |   Transaction Amount in **TOMAN** (Iranian Currency) and should be >= 1000. |
| redirect	    |   String      |    **_urlencode_** Returned address, should be the same as the verified gateway address on the same doamin. |
| invoice_no    |   String      | Your Invoice number |
| description   |   String      |   Description |
| payer_card_no	|   String      |    Payer/Source Card Number (Optional) if you need to lock the payers card in this transaction, enter this value. |
| destination	|   String      |    Destination Card Number (Optional) if you need to payout to a card other than the one registered in the platform, enter this value. (mainly for rewarding purposes) |

:::tip

Successful Response in case of valid request sent (info)

:::

```json
HTTP/1.1 200 OK
{
  "status" : 1,
  "data" : 
    {
    "url" : Payment Gateway Address,
    "token" : Payment Token
    }
}
```

:::warning

Error Message in case there is a problem

:::

```json
HTTP/1.1 422 Error
{
  "status" : failed,
  "error_code" : Error Code,
  "error_msg" : Error Message
}
```
##### Error Codes:
```json
HTTP/1.1 422 Error
{
  "-1" : sending API is required,
  "-2" : Amount should be greater than 1000,
  "-3" : Return (Callback) address is not Correct,
  "-4" : Invoice ID is required,
  "-5" : Description is required,
  "-6" : Duplicate Transaction, Try again in few minutes,
  "-7" : Invalid Transaction code,
  "-8" : Entered Card number for Payout is not Valid,
  "-9" : Invalid Card Number,
  "-10" : Entered Source Card Number is Invalid,
  "-11" : Entered Destination Card Number is Invalid,
  "-12" : Entered OTP/Second Pass is not Valid,
  "-13" : Entered CVV(2) is not Valid,
  "-14" : Card's Expiration Month is Invalid,
  "-15" : Card's Expiration Year is Invalid,
 "failed" : Transaction Has failed due to errors,
}
```

## Offline Payment Service

This service has been introduced recently by our platform.
Utilizing this service, you can integrate the payment gateway on your own website independent of us and our system will handle your transactions in the background.

To use this service you have to send all the parameters in **First Stage** together with the parameters in the table below the the following address:

:::important

POST

:::

```
https://pardakhtcard.com/api/payment/send
```

#### Parameters

| Field         |   DataType    |   Description |
| ------------- | :-----------: | :-----: |
| payment_type	|   String      | To use this service, the value of this parameter should be equal to **_offline_** |
| user_card_no  |   String      |   User's Card Number that you have obtained from your Customer/User. |
| pass_code		|   String      |    Second Pass/OTP that you have obtained from your Customer/User. |
| cvv2	        |   String      | CVV2 Value that you have obtained from Customer/User |
| exp_month	    |   String      |   Expiry **_month_** of the card, valid range 01 to 12. |
| exp_year		|   String      |    Expiry **_year_** of the card, valid format, 2 digits. |


:::tip

Successful Response in case of valid request sent (info)

:::

```json
HTTP/1.1 200 OK
{
  "status" : 1,
  "amount" : Transaction Amount,
  "token" : Transaction Number,
  "amount" : Transaction Amount,
  "invoice_no" : Invoice Number,
  "request_time" : Transaction Request Time,
  "response" : Transaction Result,
}
```

:::warning

Error Message in case there is a problem

:::

```json
HTTP/1.1 422 Error
{
  "status" : failed,
  "error_code" : Error Code,
  "error_msg" : Error Message
}
```
##### Error Codes:

```json
HTTP/1.1 422 Error
{
  "-1" : sending API is required,
  "-2" : Amount should be greater than 1000,
  "-3" : Return (Callback) address is not Correct,
  "-4" : Invoice ID is required,
  "-5" : Description is required,
  "-6" : Duplicate Transaction, Try again in few minutes,
  "-7" : Invalid Transaction code,
  "-8" : Entered Card number for Payout is not Valid,
  "-9" : Invalid Card Number,
  "-10" : Entered Source Card Number is Invalid,
  "-11" : Entered Destination Card Number is Invalid,
  "-12" : Entered OTP/Second Pass is not Valid,
  "-13" : Entered CVV(2) is not Valid,
  "-14" : Card's Expiration Month is Invalid,
  "-15" : Card's Expiration Year is Invalid,
 "failed" : Transaction Has failed due to errors,
}
```

## Reward Payment Service

You can use this service to send send rewards/gifts to your users.

:::note

To use this service(feature), you have to fill and send all the parameters in the **_First Stage_** together with an Extra parameter that is the winner's card number, in case of successful transaction, the money will be transfered to the defined (winner's) card number instead of your account.

:::

:::important

POST

:::

```
https://pardakhtcard.com/api/payment/send
```
#### Parameters

| Field         |   DataType    |   Description |
| ------------- | :-----------: | :-----: |
| destination	|   String      | Destination Card Number (Optional) if you need to payout to a card other than the one registered in the platform, enter this value. (mainly for rewarding purposes)  |


## Redirect

### Second Stage: Redirect to Payment Page

In case the first tage (sending data) is valid, and you recieve the "status" and "token" values, you have to redirect the user to the payment page.
To do this, you have to put the link that you have requested in the **_url_** parameter, in the form, and redirect the user to the disered url instantly.

:::tip

GET

:::
```
https://pardakhtcard.com/gateway/{token}/payment
```
## Callback

### Third Stage: Callback

Third stage will initiate when the user is coming back (being returned) from the Payment Gateway. in this stage depending on the result of the Transaction, we will redirect the user to the callback address that you have set in the first stage in **_redirect_** parameter.

:::tip

GET

:::
```
http://Your-CallBack-URL?status={transaction_status}&token={token}

```

## Verify

### Fourth Stage: Verify

In case the value of the field "status" that have been obtained in the previous stage is equal to **_1_**, you have to use the actions below to verify the transaction.

:::important

In case the transaction is successful, by calling the verify method, each time it will show the transaction verification message.
to prevent any fraud, the Owner('s website) should check the transaction id **_transId_** of the payment network within it's database to ensure there is no duplicate records **before** supplying any goods to the customer!

this means if the **_transId_** is already in the db records, customer/user should not get any services!
:::

:::tip

POST

:::
```
https://pardakhtcard.com/api/payment/verify

```

#### Parameters

| Field         |   DataType    |   Description |
| ------------- | :-----------: | :-----: |
| api       	|   String      | api code obtained from *pardakhtcard.com* website.  |
| token       	|   String      | token obtained in callback stage.  |

:::tip

Successful Response in case of valid request sent (info)

:::

```json
HTTP/1.1 200 OK
{
  "status" : 1,
  "amount" : Transaction Amount,
  "token" : Transaction Number,
  "amount" : Transaction Amount,
  "invoice_no" : Invoice Number,
  "request_time" : Transaction Request Time,
  "response" : Transaction Result,
}
```

:::warning

Error Message in case there is a problem

:::

```json
HTTP/1.1 422 Error
{
  "status" : failed,
  "error_code" : Error Code,
  "error_msg" : Error Message
}
```
##### Error Codes:

```json
HTTP/1.1 422 Error
{
  "-1" : sending API is required,
  "-7" : Invalid Transaction Code,
  "failed" : Transaction has failed due to Erros,
}
```

#### PHP Example Code:

##### send.php:
```php
<?php

include_once("functions.php");
$api = 'YOUR-API-KEY';
$amount = "amount in Iranian Rial";
$factorNumber = "Invoice Number";
$description = "Description";
$redirect = 'http://YOUR-CALLBACK-URL';
$result = send($api, $amount, $redirect, $factorNumber, $description);
$result = json_decode($result);
if($result->status) {
	$go = $result->data->url;
	header("Location: $go");
} else {
	echo $result->error_msg;
}
```
##### verify.php:
```php
<?php

include_once("functions.php");
$api = 'YOUR-API-KEY';
$token = $_GET['token'];
$result = json_decode(verify($api,$token));
if(isset($result->status)){
	if($result->status == 'success'){
		echo "<h1>transaction has been successfully completed </h1>";
	} else {
		echo "<h1>transaction has failed due to error</h1>";
	}
} else {
	if($_GET['status'] == 0){
		echo "<h1>transaction has failed due to error</h1>";
	}
}
```


##### functions.php:
```php
<?php

function send($api, $amount, $redirect, $mobile = null, $factorNumber = null, $description = null) {
	return curl_post('https://pardakhtcard.com/api/payment/send', [
		'api'          => $api,
		'amount'       => $amount,
		'redirect'     => $redirect,
		'invoice_no' => $factorNumber,
		'description'  => $description,
	]);
}

function verify($api, $token) {
	return curl_post('https://pardakhtcard.com/api/payment/verify', [
		'api' 	=> $api,
		'token' => $token,
	]);
}

function curl_post($url, $params)
{
	$ch = curl_init();
	curl_setopt($ch, CURLOPT_URL, $url);
	curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($params));
	curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false);
	curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
	curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
	curl_setopt($ch, CURLOPT_HTTPHEADER, [
		'Content-Type: application/json',
	]);
	$res = curl_exec($ch);
	curl_close($ch);

	return $res;
}
```

:::note

Dear Implementation Engineer, all of the response codes and string in the example codes provided here are translated to English to make your life easier.
Please return the favor to end users by using the Original "PERSIAN/FARSI" values when you have successfully implemented this.
Thanks.

:::