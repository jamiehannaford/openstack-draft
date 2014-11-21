Here is the top-level overview of how a user will interact with our SDK:

1. User invokes service method:
  
  ```php
  $service->listObjects('{containerName}', ['limit' => 5]);
  ```

2. Service invokes REST method

  ```php
  $request = $this->restApi->headAccount(['Container' => $containerName] + $opts);
  ```
  
  which returns a Request object. It is the role of the `RestApi` object to serialize a `HeadAccount` operation into a Request.
  
3. The Request object is despatched using the HTTP client:

  ```php
  $this->httpClient->send($request);
  ```

4. Something (either the Service or the HTTP client) adds default headers, including `X-Auth-Token`.

5. The Request is sent to the remote API and a response is received.

6. When the `RestApi` serializes an `Operation` into a `Request`, it attaches a subscriber whose role is to convert a JSON Response into a model. So that subscriber will need to be told about the successful transaction:

7. The subscriber is fed the HTTP Request and parses it into a Model. This model is set back into the Event, which passed on to the next subscriber until it reaches the end of the chain.

8. The Model is passed back to the Service, which returns it to the User.

For async operations, instead of returning a singular Model - the user will need to pass in an anonymous function. This handler will be fed each Model as its parsed, allowing the user to do whatever they want to it.
