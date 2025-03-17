
# Reflection 

### How does the handle_connection work ? What's inside it ?
  
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
	


