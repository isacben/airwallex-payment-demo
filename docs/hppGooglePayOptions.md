# **Hpp googlepay options design**
- [**Hpp googlepay options design**](#hpp-googlepay-options-design)
  - [**Required Options:**](#required-options)
  - [**Optional Options:**](#optional-options)
    - [**TransactionInfo**](#transactioninfo)
    - [**PaymentOptions**](#paymentoptions)
    - [**CardParameters**](#cardparameters)
    - [**ButtonOptions**](#buttonoptions)
  - [**Code sample:**](#code-sample)
  - [**Use cases**](#use-cases)
    - [**How to save shopper’s billing info in intent?**](#how-to-save-shoppers-billing-info-in-intent)
    - [**How to save shopper’s shipping info in intent?**](#how-to-save-shoppers-shipping-info-in-intent)
## **Required Options:**

|**name**|**type**|**description**|
| :-: | :-: | :-: |
|countryCode|string|<p>The ISO 3166-1 alpha-2 country code where the transaction is processed. This property is required for merchants who process transactions in European Economic Area (EEA) countries and any other countries that are subject to [Strong Customer Authentication](https://developers.google.com/pay/api/web/guides/resources/sca) (SCA). Merchants must specify the acquirer bank country code.</p><p>**Note:** When you support Brazilian combo cards like Mastro, the countryCode must be BR.</p>|
## **Optional Options:**
### **TransactionInfo**

|**name**|**type**|**description**|
| :-: | :-: | :-: |
|transactionId|string|A unique ID that identifies a transaction attempt. Merchants can use an existing ID or generate a specific one for Google Pay transaction attempts. This field is required when you send callbacks to the Google Transaction Events API.|
|displayItems|[DisplayItem\[ \]](https://developers.google.com/pay/api/web/reference/response-objects#DisplayItem)|All of the available charges for the current payment request. This is only populated in the payment sheet if you use [Authorize Payments](https://developers.google.com/pay/api/web/guides/tutorial#authorize-payments) or [Dynamic Price Updates](https://developers.google.com/pay/api/web/guides/tutorial#price-updates). This field is required if you implement support for Authorize Payments or Dynamic Price Updates.|
|totalPriceLabel|string|Custom label for the total price within the display items.|
|checkoutOption|string|<p>Affects the submit button text displayed in the Google Pay payment sheet.</p><p>- DEFAULT: Standard text applies for the given totalPriceStatus (default).</p><p>COMPLETE\_IMMEDIATE\_PURCHASE: The selected payment method is charged immediately after the payer confirms their selections. This option is only available when totalPriceStatus is set to FINAL.</p>|
### **PaymentOptions**

|**name**|**type**|**description**|
| :-: | :-: | :-: |
|**merchantInfo**|<p>{</p><p>`    `/\*\*</p><p>`     `\* Merchant name encoded as UTF-8.</p><p>`     `\*/</p><p>`    `merchantName?: string;</p><p>`    `/\*\*</p><p>`     `\*The fully qualified domain of the requesting merchant.</p><p>`     `\*/</p><p>`    `merchantOrigin?: string;</p><p>`  `};</p>|**For hpp mode, we will use the airwallex merchant id in google.**<br>This object provides information about the merchant that requests payment data.|
|paymentDataCallbacks|[PaymentDataCallbacks](https://developers.google.com/pay/api/web/reference/request-objects#PaymentDataCallbacks)|This object declares the callbacks used for Dynamic Price Updates.|
### **CardParameters**

|**name**|**type**|**description**|**default value**|
| :-: | :-: | :-: | :-: |
|allowedAuthMethods|CardAuthMethod[]|<p>Fields supported to authenticate a card transaction.</p><p>- PAN\_ONLY: This authentication method is associated with payment cards stored on file with the user's Google Account. Returned payment data includes personal account number (PAN) with the expiration month and the expiration year.</p><p>CRYPTOGRAM\_3DS: This authentication method is associated with cards stored as Android device tokens. Returned payment data includes a 3-D Secure (3DS) cryptogram generated on the device.</p>|['PAN\_ONLY', 'CRYPTOGRAM\_3DS']|
|allowedCardNetworks|CardNetwork[]|<p>One or more card networks that you support, also supported by the Google Pay API.</p><p>- AMEX(add it in future)</p><p>JCB(add it in future)</p><p>MASTERCARD</p><p>VISA<br>MAESTRO<br><https://developers.google.com/pay/api/web/reference/request-objects#MerchantInfo></p>|['MASTERCARD', 'VISA']|
|allowPrepaidCards|boolean|Set to false if you don't support prepaid cards. Default: The prepaid card class is supported for the card networks specified.|true|
|allowCreditCards|boolean|Set to false if you don't support credit cards. Default: The credit card class is supported for the card networks specified.|true|
|emailRequired|boolean|Whether to collect the email from the buyer. If it is true, Airwallex will save buyer’s email into Billing info when confirming intent.|false|
|billingAddressRequired|boolean|Set to true if you require a billing address. A billing address should only be requested if it's required to process the transaction. Additional data requests can increase friction in the checkout process and lead to a lower conversion rate. If it is true, Airwallex will save buyer’s Billing address when confirming intent.|false|
|billingAddressParameters|<https://developers.google.com/pay/api/web/reference/request-objects#BillingAddressParameters> <br>BillingAddressParameters|The expected fields returned if billingAddressRequired is set to true.||
### **ButtonOptions**

|**name**|**type**|**description**|**default value**|
| :-: | :-: | :-: | :-: |
|buttonColor|string|<p>- default: A Google-selected default value. Currently black but it may change over time (default).</p><p>black: A black button suitable for use on white or light backgrounds.</p><p>white: A white button suitable for use on colorful backgrounds.</p>|default|
|buttonType|string|<p>- book: The "Book with Google Pay" payment button.</p><p>buy: The "Buy with Google Pay" payment button (default).</p><p>checkout: The "Checkout with Google Pay" payment button.</p><p>donate: The "Donate with Google Pay" payment button.</p><p>order: The "Order with Google Pay" payment button.</p><p>pay: The "Pay with Google Pay" payment button.</p><p>plain: The Google Pay payment button without the additional text.</p><p>subscribe: The "Subscribe with Google Pay" payment button.</p>||
|buttonSizeMode|string|<p>- static: Button has a static width and height (default).</p><p>fill: Button size changes to fill the size of its container.</p>|fill|
## **Code sample:**
```
 redirectToCheckout({

    env: getAirwallexEnv(),

    intent\_id: data?.id,

    client\_secret: data?.client\_secret,

    ...

    googlePayRequestOptions: {

      countryCode: 'US',

    }

  });
```
## **Use cases**
### **How to save shopper’s billing info in intent?**
code sample:
```
redirectToCheckout({

    env: getAirwallexEnv(),

    intent\_id: data?.id,

   client\_secret: data?.client\_secret,

    ...

    googlePayRequestOptions: {

       countryCode: 'US',

       billingAddressParameters: {

         format: 'FULL',

       },

       emailRequired: true,

       billingAddressRequired: true,
    }

  });
```
### **How to save shopper’s shipping info in intent?**
currently, we only support save shipping info when creating the intent.