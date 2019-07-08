---
title: iPay Developer Documentation

language_tabs:
  - html
  - shell

toc_footers:
  - <a href='https://manage.ipygh.com/xmange/get-started'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the iPay Documentation! 

You can use our API to process payments from MTN Mobile Money, Airtel Money, Tigo Cash, Vodafone Cash , VISA and Mastercard


We love feedback. If there's any portion of this documentaion that seems unclear to you kindly drop us a note and we'll review it. Even better you can suggest how we can rephrase it to make it clearer and we'll be sure to consider it in our review.

Now, grab a coffee and sit back . . . we have a ton of information coming up.


# Using iPay

To cater for various needs we have a variety of integration approaches and in this document we will begin with those that require very little effort and end up with a ful
l API you can use.

You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.



## HTML Re-direct

```html

<form method=POST action="https://community.ipaygh.com/gateway">
    <input type=hidden name=merchant_key value="tk_c74ba68d-946a-ebc8-d908-e49dc61f89bc" />
    <input type=hidden name=success_url value="" />
    <input type=hidden name=cancelled_url value="" />
    <input type=hidden name=deferred_url value="" />
    Invoice ID <input type=text name=invoice_id value="" />
    <table cellspacing=0px cellpadding=0px border=0>
        <tr >
            <td align=right><b>GH&cent;</b><input name=total type=text size=10 /><input type=submit /></td>
        </tr>
    </table>
</form>

```

Try clicking on the link below to get a sense of how the re-direct works. You may use the test mobile numbers **0260000000** and **0240000000**

                <script  src="https://community.ipaygh.com/static/gateway/js/uuid.js"></script>

            <form id="test_pos" method=POST action="https://community.ipaygh.com/gateway">
                <input type=hidden name=merchant_key value="tk_c74ba68d-946a-ebc8-d908-e49dc61f89bc" />
                <input type=hidden name=success_url value="https://community.ipaygh.com/documentation/" />
                <input type=hidden name=cancelled_url value="https://community.ipaygh.com/documentation/" />
                <input type=hidden name=deferred_url value="https://community.ipaygh.com/documentation/" />
                <input type=hidden name=total value=1 />
                <input type=hidden name=invoice_id id=invoice_id value="" />

                <a href="javascript:{}" onclick="document.getElementById('invoice_id').value = uuid.v1(); document.getElementById('test_pos').submit(); return false;" > Make payment</a>
            </form>
                
A sample code that makes use of the re-direct 'API' is shown on th right.

The thing to note is that iPay requires each transaction to be identified by a unique invoice ID and an amount due (in GHS for the avoidance of doubt). Below is a list of other arguments that can be sent in the POST request:

<table>
    <tr>
        <td><b>Parameter</b></td>
        <td><b>Required</b></td>
        <td><b>Description</b></td>
    </tr>
    <tr>
        <td>merchant_key</td>
        <td>Y</td>
        <td>The value of <i>merchant_key</i> identifies the merchant who is using the gateway. It is usually obtained by registering for the iPay gateway service. At the moment, registering for the iPay service is FREE.</td>
    </tr>
    <tr>
        <td>invoice_id</td>
        <td>Y</td>
        <td>A 25-character value that identifies the order. It must be unique!</td>
    </tr>
    <tr>
        <td>total</td>
        <td>Y</td>
        <td>The total payment due for the cart. Please note that the gateway will *NOT* compute this. We want the merchant to compute this him/herself and to add in any tax or shipping elements they may want to. *Future versions may compute this value if it is not present but the current version does not*</td>
    </tr>
    <tr>
        <td>ipn_url</td>
        <td>N</td>
        <td>This specifies the url to which iPay will send notification once payment is received.</td>
    </tr>
    <tr>
        <td>success_url</td>
        <td>N</td>
        <td>The page to which iPay will redirect the user after user completes the iPay checkout process. <b><i>Please note that this does not mean that payment has been received!</i></b></td>
    </tr>
    <tr>
        <td>cancelled_url</td>
        <td>N</td>
        <td>The page to which iPay will redirect the user after user cancels the order.</td>
    </tr>
    <tr>
        <td>deferred_url</td>
        <td>N</td>
        <td>The page to which iPay will redirect the user after user chooses a payment option which allows deferred payments (eg in bank payments)</td>
    </tr>
    <tr>
        <td>currency</td>
        <td>N</td>
        <td>Optional. Can be any of GHS, GBP, EUR or USD. If provided, the total is considered to be in this currency. Settlement currently occurs in GHS. This means, although, you clients can pay in foreign currency, we currently can only transfer funds in GHS. Another thing to note is using any other currency besides GHS, will disable all GHS payment methods (this includes but is not limited to the mobile money payment methods). Defaults to GHS</td>
    </tr>
</table>                                                                             



## Direct Integration Methods

For a more transparent integration, you can use the API described below. 

This generally involves making a call to the mobile agent end point. This triggers a payment request on the user's phone. After user cancels or processes payment, an asynchronous call is made to your Instant Payment Notificationi (IPN) URL. Your IPN URL is responsible for performing the necessary action once payment is completed or cancelled. 

The process for handling payment notifications is described in the next section.



# Handling payment notifications

Once the buyer pays or cancels an invoice, depending on your site setup, iPay can do two things:

* Send an email informing you of the payment or cancellation
* Send a notification to an IPN (instant payment notification) URL for your application to take some automated action

It is worth stressing the IPN notification from iPay is not confirmation of a payment. Rather it is a prompt that an event of interest has happened to one of your invoices. It is your application's responsibility to query the iPay gateway for details on the event using the status check end point. While this may sound complicated, it is not. Here's how it works:

1. Once an invoice is paid or cancelled, iPay does a GET to your IPN URL with the invoice_id paramter. Suppose, your IPN url is https://test.example.com/notify and a buyer pays or cancels invoice with invoice ID AA123, iPay will perform the GET request https://test.example.com/notify?invoice_id=AA123 This call is meant to prompt your application that an event of interest has occured with respect to this invoice.
2. Your application will then make the GET call https://community.ipaygh.com/v1/gateway/json_status_chk?invoice_id=AA123&merchant_key=YOUR_MERCHANT_KEY to receive details of the event
3. iPay responds to the query in the step above with a JSON object. Details of the response object are described as part of the status check end point.






# API Documentation

## Request payment

This end point generates a payment request and usually results in sending a payment request to the user.

> To initiate a payment request, use the code sample below

```html

```

```shell
# Sample payment initiation call
curl -X POST \
-F 'merchant_key=tk_c74ba68d-946a-ebc8-d908-e49dc61f89bc' \
-F 'invoice_id=AA123' \
-F 'total=1' \
-F 'pymt_instrument=0260000000' \
-F 'extra_wallet_issuer_hint=airtel' \
https://community.ipaygh.com/v1/mobile_agents_v2
```

> The above command returns JSON structured like this:

```json
{
    "message": "Invoice successfully initiated", 
    "status": "new", 
    "success": true
}
```


### HTTP Request

`POST https://community.ipaygh.com/v1/mobile_agents_v2`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
merchant_key | Y | Specify your merchant_key
invoice_id | Y | Provide a unique invoice ID for each request.
total | Y | Indicate the amount being requested in GHS
pymt_instrument | Y | Specify the mobile money number for which payment is being requested
extra_wallet_issuer_hint | Y | Must either be mtn, airtel, vodafone or tigo depending on customer's wallet. If you have been enabled for VISA/MASTERCARD requests, then you can also pass visa_mastercard. Kindly note for VISA/MASTERCARD requests pymt_instrument must be 00000000
extra_* | N | You can provide many other parameters which iPay will store for you. These are returned when you check the status of an invoice. This feature is useful for storing extra data that has meaning for your application


<aside class="notice">
You must replace <code>merchant_key</code> with your personal API key and replace pymt_instrument with the number of the person making the payment..
</aside>




## Check on the status of a payment

```html

```

```shell

curl https://community.ipaygh.com/v1/gateway/json_status_chk?invoice_id=AA123\&merchant_key=tk_c74ba68d-946a-ebc8-d908-e49dc61f89bc
```

> The above command returns JSON structured like this:

```json
{"AA123": 
    {
        "tran_type": "test", 
        "payment_instrument": "*****000", 
        "invoice_id": "AA123", 
        "buyer_email": null, 
        "buyer_lastname": null, 
        "buyer_phone": null, 
        "narration": "Demo", 
        "buyer_firstname": null, 
        "extra": {
            "channel": "Airtel", 
            "wallet_issuer_hint": "airtel"
        }, 
        "gw_invoice_id": "00091272", 
        "amount": "1.00", 
        "status": "paid", 
        "as_at": "2017-03-25T10:22:15.741484+00:00"
    }
}
```


This endpoint retrieves the status of a payment request.

### HTTP Request

`GET https://community.ipaygh.com/v1/gateway/json_status_chk?invoice_id=&merchant_key=`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
merchant_key | Y | Specify your merchant_key
invoice_id | Y | Provide the id of the invoice whose status you want to check


<aside class="success">
For a successful payment, <b>status</b> will be <b>paid</b>
</aside>





