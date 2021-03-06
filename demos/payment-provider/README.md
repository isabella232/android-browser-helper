# Payment Provider

This demo shows how to make an app that contains the Payment Provider supplied in the `playbilling`
module in the Android Browser Helper.
The code is fairly straightforward, however there's a fair amount of configuration required to get
everything working.

The app is launched and the user presses on the "Launch Browser" button.
This takes them to a website that accepts payments through the Payment Request API.

This website must be set up to accept the application as a Payment Provider - this can be seen in
the website's [manifest.json](https://beer.conn.dev/manifest.json).
If the app's package name and key are not present in this file, the process won't work.

With this relationship set up, when the user presses the pay button on the website, their browser
will try to launch the `PaymentProviderActivity` (defined in our `playbilling` module and included
in this app's `AndroidManifest.xml`).

## Restricting use to Trusted Web Activity

Along with the Payment Activity, this demo includes the `IsReadyToPayService`.
The purpose of this service is to answer `org.chromium.intent.action.IS_READY_TO_PAY` Intents and
tell the browser whether the app is willing to take payments.

For other apps accepting payments, this step may not be necessary and they could be willing to
accept payments at any time.
However, for Trusted Web Activities, they should also only respond to requests from the browser that is showing the Trusted Web Activity.

A Trusted Web Activity could turn this behaviour off if they wanted to act as a Payment Provider
to other apps as well, although that should be a choice made by the developer.
They would also have to encode a whitelist of applications they will accept payment
requests from (we get around this by only allowing the browser displaying the Trusted Web
Activity).

If the Service returns no, then the Activity will finish itself immediately with
`Activity.RESULT_CANCELED`.
(This will also occur if the Activity is launched through `startActivity`, not
`startActivityForResult`.)

## Current State

At the moment, the Activity will launch and attempt to buy one of Play Billing's
[test SKUs](https://developer.android.com/google/play/billing/billing_testing),
`android.test.purchased`. 
It will respond to Chrome with either success or failure depending on whether the user completed
the flow.

Next steps include:

* Getting SKU information from the web page and selecting the right product.
* Strengthening the security (eg, looking at the provided certificate chain).

## Troubleshooting

If you get the error message *"Payment handler did not respond to "paymentrequest" event."*, you
could try:

* See if you app appears in Chrome's Settings under *Payment Methods > Payment apps*.
* Ensure the package name in `manifest.json` matches that in your `build.gradle`.

## Links

* [Docs on creating an Android Payment App](https://developers.google.com/web/fundamentals/payments/payment-apps-developer-guide/android-payment-apps).
* [Sample Android Payment App](https://github.com/GoogleChromeLabs/android-web-payment).
