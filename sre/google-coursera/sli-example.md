Happy user for this journey is a user which could buy in-game currency.

It's request / response type of interaction. So we will define SLO for availability, latency and quality. From the client's point of view there are two requests:
1. /api/getSKUs
2. /api/completePurchase


Where to monitor:
1. We will gather logs from two sides:
    * Load Balancer
        * when we monitor on Load Balancer we cover all the real users
    * BlackBox prober
        * required in addition to Load Balancer to detect the failure of Load Balancer.
        * also check the response body to capture the requests with wrong HTTP status

What to monitor:


Availability SLI (for both of the above urls):
    * Valid events: all requests that reached our load balancer
    * Good events: Requests served with status codes in range [101-499], excluding 408 (it's not a good idea to count "request timeout" as valid) and 429

Latency SLI (for both of the above urls):
    * Valid events: all requests that reached our load balancer
    * Good events: 99% percentile of requests which were served within X time

Correctness SLI:
    * We could get the financial report from payment API to obtain the amount of in-game currency that users bought.
    * We could calculate if user balance is correct as:
        end_of_the_day_balance = start_of_the_day_balance - currency_spent + currency_bougth
    * Valid events: all the users who purchases in-game currency within a day
    * Good Events: the percentage of users with correct balance

Notes:
    * We do not cover the interaction with payment API in our SLI. Because it's out of our control and we could not influence it.
    * When defining SLO we should not make them more strict than Payment API SLAs (our users won't notice this, because of less reliable Payment APIs)
    * Anyway we should monitor the external payment API to define if it's fullfil it's SLAs. If it's not we could investigate the capability to replace it with more reliable one.
