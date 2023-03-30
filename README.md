# Payments Stripe

Supports Stripe Checkout. Name of the gateway: `stripe`

## Installation

        git clone git@github.com:Platform-OS/pos-module-payments-stripe.git modules/payments_stripe

## Usage

Set constant for secret key - `stripe_sk_key`. Test key: `sk_test_51MqAguLX9pf1NFC3NmfWj2741sDHQcWq7GJzfDrS8ozr6nyAMIgXXDJ37YiiHlTjCFvj1nvjqQ3odVKaMevWlKMB00YMxvMnZR`

Setup webhooks by running `function res = 'modules/payments_stripe/commands/setup'`

## Hooks

List of hooks provided and/or implemented by the module

## Examples

### Stripe test card

- Card number: `4242 4242 4242 4242.`
- Use a valid future date, such as `12/34`.
- Use any three-digit CVC (four digits for American Express cards).
- Use any value you like for other form fields.

### Code examples

``` liquid
{% parse_json line_items %}
  [{
    "price_data": {
      "currency": "usd",
      "product_data": {
        "name": "T-shirt"
      },
      "unit_amount": 2000
    },
    "quantity": 1
  }]
{% endparse_json %}
{% liquid
  if context.params.stripe_pay
    assign ids = '["1", "2"]' | parse_json
    assign object = null | hash_merge: gateway: 'stripe', payable_ids: ids, amount_cents: 1001, currency: 'USD'
    function object = 'modules/payments/commands/transactions/create', object: object
    echo object
    echo '------------------'

    assign success_url = 'https://' | append: context.location.host | append: '/success'
    assign failed_url = 'https://' | append: context.location.host | append: '/failed'
    assign stripe_params = null | hash_merge: success_url: success_url, cancel_url: failed_url, line_items: line_items
    function url = 'modules/payments/helpers/pay_url', transaction: object, gateway_params: stripe_params
    log url, type: 'url'

    redirect_to url, status: 303
  endif
%}
<form action="/debug?stripe_pay=1" method="GET">
  <input type='hidden' name="stripe_pay" value="1">
  <button type="submit" id="checkout-button">Checkout</button>
</form>
```

## TODO

- [x] run webhook setup `function res = 'modules/payments_stripe/commands/webhook_endpoints/create', stripe_event: 'checkout.session.completed', path: '/webhooks/checkout_session_completed', connect: false, host: context.location.host`, maybe we should put this code into migration so it will fail until you setup correct stripe key?
- [x] use new validations from `core` module
- [x] find solution for redirect url
- [ ] implement things required by `payments` module, especially `modules/payments/commands/transactions/udpate_status`
- [ ] handle failed or expired payment from stripe?
- [ ] store api calls in gateway_requests, (checkout_session_create, incomming webhook). Maybe we don't need `schema/checkout_session` at all?
- [ ] test whole payment flow, do the payment with test card and wait for the webhook that will update transaction status.

## Versioning

```
git fetch origin --tags
npm version major | minor | patch
```
