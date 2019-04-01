Logging capabilities for Omnipay gateways via an `EventSubscriberInterface` subscriber for Omnipay payment gateway-specific events.
These events are dispatched via the HTTP client's `EventDispatcherInterface`.
* The omnipay gateway needs to be updated to emit any of the `RequestEvent`, `ResponseEvent` or `ErrorEvent` objects.
For example in the gateway's`sendData()` methods we can do:

    ```PHP
    $event_dispatcher = $this->httpClient->getEventDispatcher();
    $event_dispatcher->dispatch(Constants::OMNIPAY_REQUEST_BEFORE_SEND, new RequestEvent($request));
    ```

    Logging Errors and Responses events can be emitted like so
    ```PHP
    $event_dispatcher->dispatch(Constants::OMNIPAY_REQUEST_ERROR new ErrorEvent($exception));
    $event_dispatcher->dispatch(Constants::OMNIPAY_REQUEST_SUCCESS, new ResponseEvent($response));
    ```

`OmnipayGatewayRequestSubscriber.php` takes in a logger of type `LoggerInterface` which will listen to and log these events.

The subscriber can be set up  to listen to these events when instantiating the HTTP client for the gateway like so:

```PHP
$client = new GuzzleClient();
$gateway = new VindiciaOmnipayGateway($client);
$eventDispatcher = $client->getEventDispatcher();
$eventDispatcher->addSubscriber(new OmnipayGatewayRequestSubscriber($gateway_name, new LoggerClassThatImplementsInterface()));
