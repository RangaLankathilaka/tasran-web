# tasran-web

## PayHere hosted checkout

`payhere.html` is the browser page used to start PayHere payments, including on Xiaomi devices. Open it with an order ID:

```text
https://www.tasran.com/payhere.html?order_id=ORDER_ID
```

The page loads PayHere's JavaScript SDK and requests the payment object from:

```text
GET https://api.tasran.com/payhere/payment?order_id=ORDER_ID
```

The API must authenticate and authorize the order on the server, generate the PayHere hash with the merchant secret, and return either the payment object directly or under a `payment` property. The object must include `order_id`, `merchant_id`, `amount`, `currency`, and `hash`, plus the normal PayHere fields such as customer name, email, phone, address, city, country, and items. A typical response is:

```json
{
	"payment": {
		"sandbox": false,
		"merchant_id": "YOUR_MERCHANT_ID",
		"order_id": "ORDER_ID",
		"items": "Tasran service",
		"amount": "1000.00",
		"currency": "LKR",
		"hash": "SERVER_GENERATED_HASH",
		"first_name": "Customer",
		"last_name": "Name",
		"email": "customer@example.com",
		"phone": "0771234567",
		"address": "Billing address",
		"city": "Colombo",
		"country": "Sri Lanka"
	}
}
```

Enable CORS for the website origin on this endpoint. Never expose the merchant secret or accept a client-generated hash. PayHere server notifications must be handled at:

```text
https://api.tasran.com/payhere/notify
```

The notification endpoint is the source of truth for payment confirmation. Verify PayHere's notification signature and the amount/order ID on the server before marking an order paid. Do not open `https://www.payhere.lk/pay/checkout` directly; it requires a payment POST flow and is not a browser checkout page.




Configure these URLs:

Hosted checkout URL

https://www.tasran.com/payhere.html?order_id=ORDER_ID


Example:
https://www.tasran.com/payhere.html?order_id=ORD-1001


Your page should request payment data from:
https://api.tasran.com/payhere/payment?order_id=ORD-1001

Configure PayHere server notifications/webhooks as:
https://api.tasran.com/payhere/notify

Do not configure or open this as the checkout page:

https://www.payhere.lk/pay/checkout
The ORDER_ID must be replaced dynamically with the actual order ID. Keep the merchant secret and hash generation on the server only.


flutter run -d chrome \
  --dart-define=PAYHERE_BROWSER_CHECKOUT_URL=https://tasran.com/payhere.html

  flutter build apk --release \
  --dart-define=PAYHERE_BROWSER_CHECKOUT_URL=https://tasran.com/payhere.html