
# Reflection 

### How does the handle_connection work ? What's inside it ?
  
  ### Commit 1 :
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
	
### Commit 2 :

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

