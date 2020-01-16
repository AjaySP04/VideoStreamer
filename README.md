# Video Streaming Demonstration

1. Streaming
Streaming is a technique in which the server provides the response to a request in chunks. I can think of a couple of reasons why this might be useful:

- Very large responses. Having to assemble a response in memory only to return it to the client can be inefficient for very large responses. An alternative would be to write the response to disk and then return the file with flask.send_file(), but that adds I/O to the mix. Providing the response in small portions is a much better solution, assuming the data can be generated in chunks.

- Real time data. For some applications a request may need to return data that comes from a real time source. A pretty good example of this is a real time video or audio feed. A lot of security cameras use this technique to stream video to web browsers.

2. Implementing Streaming With Flask
- Flask provides native support for streaming responses through the use of generator functions. A generator is a special function that can be interrupted and resumed.

3. Multipart Responses
- The table example above generates a traditional page in small portions, with all the parts concatenated into the final document. This is a good example of how to generate large responses, but something a little bit more exciting is to work with real time data.

- An interesting use of streaming is to have each chunk replace the previous one in the page, as this enables streams to "play" or animate in the browser window. With this technique you can have each chunk in the stream be an image, and that gives you a cool video feed that runs in the browser!

- The secret to implement in-place updates is to use a multipart response. Multipart responses consist of a header that includes one of the multipart content types, followed by the parts, separated by a boundary marker and each having its own part specific content type.

- There are several multipart content types for different needs. For the purpose of having a stream where each part replaces the previous part the multipart/x-mixed-replace content type must be used.


# Limitations of Streaming
When the Flask application serves regular requests the request cycle is short. The web worker receives the request, invokes the handler function and finally returns the response. Once the response is sent back to the client the worker is free and ready to take on another request.

When a request that uses streaming is received, the worker remains attached to the client for the duration of the stream. When working with long, never ending streams such as a video stream from a camera, a worker will stay locked to the client until the client disconnects. This effectively means that unless specific measures are taken, the application can only serve as many clients as there are web workers. When working with the Flask application in debug mode that means just one, so you will not be able to connect a second browser window to watch the stream from two places at the same time.
