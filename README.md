# Payments Stripe

Supports Stripe Checkout. Name of the gateway: `stripe`

## Usage

Set constant for secret key - `stripe_sk_key`. Test key: `sk_test_51MqAguLX9pf1NFC3NmfWj2741sDHQcWq7GJzfDrS8ozr6nyAMIgXXDJ37YiiHlTjCFvj1nvjqQ3odVKaMevWlKMB00YMxvMnZR`

## Hooks

List of hooks provided and/or implemented by the module

## Examples

Code examples
        
        {% liquid
          assign ids = '["1", "2"]' | parse_json
          assign object = null | hash_merge: gateway: 'stripe', payable_ids: ids, amount_cents: 1001, currency: 'USD'
          function object = 'modules/payments/commands/transactions/create', object: object
          echo object
          echo '------------------'

          function url = 'modules/payments/helpers/pay_url', transaction: object
          echo url
          echo '------------------'
        %}
        <a href="{{ url  }}">Pay</a>
        <script src="https://js.stripe.com/v3/"></script>
        <section>
          <div class="product">
            <img src="https://i.imgur.com/EHyR2nP.png" alt="The cover of Stubborn Attachments" />
            <div class="description">
              <h3>Stubborn Attachments</h3>
              <h5>$20.00</h5>
            </div>
          </div>
          <form action="{{url}}" method="POST">
            <button type="submit" id="checkout-button">Checkout</button>
          </form>
        </section>
        
## TODO
 
- store api calls in gateway_requests, (checkout_session_create, incomming webhook)
- run webhook setup `function res = 'modules/stripe/lib/webhook_endpoints/create/call', stripe_event: 'checkout.session.completed', path: '/webhooks/checkout_session_completed', connect: false, host: context.location.host`, maybe we should use migration?
- use new validations from `core` module
- implement things required by `payments` module
- test whole payment flow

## Versioning

```
git fetch origin --tags
npm version major | minor | patch
```
