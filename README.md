
# Reflection 

## How does the handle_connection work ? What's inside it ?
  
  ## Commit 1 :
 - ```rust
    fn handle_connection(mut stream: TcpStream) {
	```
	The function takes a TcpStream as an argument. This object is used to collect to request made when an user connects to the URL using the TCP protocol.

 - ```rust
    let  buf_reader  =  BufReader::new(&mut  stream);
	```
	The buf_reader is used to allow us to read the request line by line, instead of reading everything at once. 

 - ```rust
    let  http_request:  Vec<_> = buf_reader...
	```
	We stock the request in a Vector of String extracting the informations from the buf_reader.

 - ```rust
    .lines()
	```
	This returns an iterator of the BufReader that reads lines from buf_reader.

 - ```rust
    .map(|result|  result.unwrap())
	```
	This applies the unwrap() function to each result.

 - ```rust
    .take_while(|line|  !line.is_empty())
	```
	This stops reading when it encounters an empty line(""). 

 - ```rust
	.collect();
	```
	This collects all the processed lines into a Vec<String>.
	
## Commit 2 :

- ```rust
	let status_line = "HTTP/1.1 200 OK";
	```
This defines the first line of the HTTP response, indicating a 200 OK status (success).

- ```rust
	let contents = fs::read_to_string("hello.html").unwrap();
	```
This line reads the hello.html file and stores its content in the contents variable.

- ```rust
	let length = contents.len();
	```
The content length is calculated to define the Content-Length header, which is necessary when sending an http response.

- ```rust
	let response =
    format!("{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}");
	```
Here, we format the complete HTTP response, including:
1. The status line (status_line).
2. The Content-Length header, which is essential to inform the client about the response body size.
3. A blank line (\r\n\r\n), separating headers from the body.
4. The HTML file content (contents).

- ```rust
	stream.write_all(response.as_bytes()).unwrap();
	```
Finally, the response is sent to the client using stream.write_all().
unwrap() ensures that any write error will cause the program to panic.



## Commit 3 :

Previously, our web server responded with hello.html regardless of the requested URL. Now, we have modified handle_connection to distinguish between valid and invalid requests:
1. If the request is for /, the server responds with hello.html (HTTP 200 OK).
2. Otherwise, it responds with 404.html (HTTP 404 NOT FOUND).

```rust
	let buf_reader = BufReader::new(&stream);
	let request_line = buf_reader.lines().next().unwrap().unwrap();
```
We read only the first line of the HTTP request instead of storing all lines in a Vec<String>. And, next().unwrap().unwrap() ensures that we retrieve and unwrap the first line safely.

- ```rust
	if request_line == "GET / HTTP/1.1" {
  ```
If the request matches GET / HTTP/1.1, we serve hello.html. Otherwise, we return a 404 NOT FOUND response.

- ```rust
	let status_line = "HTTP/1.1 200 OK";
	let contents = fs::read_to_string("hello.html").unwrap();
	```
	We read hello.html if the request is valid. Otherwise, we return:

	- ```rust
		let status_line = "HTTP/1.1 404 NOT FOUND";
		let contents = fs::read_to_string("404.html").unwrap();
		```
- ```rust
	let response = format!("{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}");
	stream.write_all(response.as_bytes()).unwrap();
	```
	The response is formatted properly with status, content length, and body.


### Why Refactoring is Needed

Initially, we had redundant code in both if and else blocks. To improve maintainability and readability, we refactored the logic.

- Before Refactoring: 

```rust
	if request_line == "GET / HTTP/1.1" {
		let status_line = "HTTP/1.1 200 OK";
		let contents = fs::read_to_string("hello.html").unwrap();
		let length = contents.len();
		
		let response = format!("{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}");
		stream.write_all(response.as_bytes()).unwrap();
	} else {
		let status_line = "HTTP/1.1 404 NOT FOUND";
		let contents = fs::read_to_string("404.html").unwrap();
		let length = contents.len();
		
		let response = format!("{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}");
		stream.write_all(response.as_bytes()).unwrap();
	}
```

- After Refactoring: 

```rust
	let (status_line, filename) = if request_line == "GET / HTTP/1.1" {
    	("HTTP/1.1 200 OK", "hello.html")
	} else {
    	("HTTP/1.1 404 NOT FOUND", "404.html")
	};

	let contents = fs::read_to_string(filename).unwrap();
	let length = contents.len();

	let response = format!("{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}");

	stream.write_all(response.as_bytes()).unwrap();
```

- Improvements of Refactoring:

	1. Eliminate Redundant Code
	2. Improve Readability
	3. Enhance Maintainability


### Screenshots

- If we try to access http://127.0.0.1:7878 :

![Hello page](screenshots/[3]hello.png)

- If we try to access http://127.0.0.1:7878/bad:

![404 Error Page](screenshots/[3]404-error.png)




	
