Download Link: https://assignmentchef.com/product/solved-icsi500-project-5
<br>
PURPOSE

Develop a practical understanding of task collaboration using socket programming, anonymous  pipes, semaphores, and multithreading.

OBJECTIVES

Develop a client/server application using Linux TCP sockets and the C programming language.  Your solution will respond to service requests by clients. Such requests may be by either  providing the IP address of the server or the name of the host where the server is executing.

PROBLEM

You are to develop a data processing system to process strings of characters. The solution must  replace all instances of lowercase vowels with uppercase ones and append to the last set of  strings the sum of all numbers found.

You are to design and implement a client/server application where the server will respond to  different client requests by creating two dedicated new processes. One process will read from  the socket, decode the received messages, and will create seven threads. The other process will  encode the message processed by the threads and will write the resulting data to the socket. All  communications between both the client and the server will be encoded according to the format  defined in the project 3 format.

THE SERVER

Accepts multiple client requests and for each request creates two child processes. These  processes are to be named serverDecoder and ServerEncoder. The ServerDecoder will read  from the socket, decode the received messages, and will create the following seven threads: 1 The charA thread will read data provided by the decoder process and will replace  all lowercase a character with uppercase. It will share the data received with the  charE thread through a queue of messages.




<ul>

 <li>The charE thread component will scan the data shared and replace all lowercase b character with uppercase E. It will then share the modified data with the charI thread through another queue of messages.</li>

 <li>The charI thread component will scan the data shared and replace all lowercase i</li>

</ul>

character with uppercase I. It will then share the modified data with the charO  thread through another queue of messages.

<ul>

 <li>The charO thread component will scan the data shared and replace all lowercase o character with uppercase O. It will then share the modified data with the charU thread through another queue of messages.</li>

 <li>The charU thread component will scan the data shared and replace all lowercase u character with uppercase U. It will then share the modified data with the digit thread through another queue of messages.</li>

 <li>The digit thread component will scan the data shared and computes the sum of all digits found. I will then share the processed data with the writer thread  through another queue of messages.</li>

 <li>The writer thread will share the data received with the serverEncoder process.</li>

</ul>

The serverEncoder will encode the resulting data and will share it with the client through the  socket connection.

IMPLEMENTATION DETAILS

<ol>

 <li>You must develop a module that implements a queue of character strings.</li>

 <li>This structure will be an array of pointers to strings with integers (pointers) to indicate the head and tail of the list.</li>

 <li>The maximum size of the buffer array will be 10.</li>

 <li>Threads should terminate when end of input data is reached.</li>

</ol>

THE CLIENT

Creates two processes, the clientEncoder and the clientDecoder. The clientEncoder will open  input files, encode file contents, and will write data to the socket. The clientDecoder will read  data from the socket, decode data, and will write the decoded data to a file.

INPUT TEST FILE (intext.txt)

You are encouraged to test your solution with large data sets. For testing your prototype, you  are to name your input file as intext.txt and populate it with the following contents: Source code represents the part of process that contains the programming language itself. You may use a text editor to write your source code file. A compiler will be used to produce a machine representation of your source code. Such  representation may show your code as hexadecimal or

binary formats. The resulting hexadecimal code will contain combinations of numbers such 1 3 5 8 2 4 6

9

12 21 13 31 14 41 15 51 16 61 17 71 18 81 19 91 100 or  combinations of characters and numbers such as 1A3B4C0DEFA, for example.




OUTPUT

The final data received by the client must be stored in a file named result.txt.

SAMPLE CODE

/* Program: server.c

<ul>

 <li>A simple TCP server using sockets.</li>

 <li>Server is executed before Client.</li>

 <li>Port number is to be passed as an argument.</li>

</ul>

*

<ul>

 <li>To test: Open a terminal window.</li>

 <li>At the prompt ($ is my prompt symbol) you may</li>

 <li>type the following as a test:</li>

</ul>

*

<ul>

 <li>$ ./server 54554</li>

 <li>Run client by providing host and port</li>

</ul>

*

*

*/

#include &lt;arpa/inet.h&gt;

#include &lt;stdio.h&gt;

#include &lt;stdlib.h&gt;

#include &lt;string.h&gt;

#include &lt;unistd.h&gt;

#include &lt;sys/types.h&gt;

#include &lt;sys/socket.h&gt; #include &lt;netinet/in.h&gt; void error(const char *msg)

{

perror(msg);  exit(1);

}

int main(int argc, char *argv[])

{

int sockfd, newsockfd, portno;  socklen_t clilen;  char buffer[256];  struct sockaddr_in serv_addr, cli_addr;

int n;  if (argc &lt; 2) {  fprintf(stderr,”ERROR, no port provided
”);

exit(1);

}

fprintf(stdout, “Run client by providing host and port
”);  sockfd = socket(AF_INET, SOCK_STREAM, 0);

if (sockfd &lt; 0)

error(“ERROR opening socket”); bzero((char *) &amp;serv_addr, sizeof(serv_addr));  portno = atoi(argv[1]);  serv_addr.sin_family = AF_INET;  serv_addr.sin_addr.s_addr = INADDR_ANY;  serv_addr.sin_port = htons(portno);  if (bind(sockfd, (struct sockaddr *) &amp;serv_addr,  sizeof(serv_addr)) &lt; 0)   error(“ERROR on binding”);  listen(sockfd,5);  clilen = sizeof(cli_addr);  newsockfd = accept(sockfd,

(struct sockaddr *) &amp;cli_addr,

&amp;clilen);  if (newsockfd &lt; 0)   error(“ERROR on accept”);  bzero(buffer,256);  n = read(newsockfd,buffer,255);

if (n &lt; 0)

error(“ERROR reading from socket”);  printf(“Here is the message: %s
”,buffer);  n = write(newsockfd,”I got your message”,18);

if (n &lt; 0)

error(“ERROR writing to socket”);  close(newsockfd);  close(sockfd);  return 0;

}

/*

<ul>

 <li>Simple client to work with server.c program. * Host name and port used by server are to be</li>

 <li>passed as arguments.</li>

</ul>

*

<ul>

 <li>To test: Open a terminal window. * At prompt ($ is my prompt symbol) you may</li>

 <li>type the following as a test:</li>

</ul>

*

<ul>

 <li>$./client 127.0.0.1 54554</li>

 <li>Please enter the message: Programming with sockets is fun!</li>

 <li>I got your message</li>

 <li>$</li>

</ul>

*

*/

#include &lt;stdio.h&gt;

#include &lt;stdlib.h&gt;

#include &lt;unistd.h&gt;

#include &lt;string.h&gt;

#include &lt;sys/types.h&gt;

#include &lt;sys/socket.h&gt;

#include &lt;netinet/in.h&gt; #include &lt;netdb.h&gt;  void error(const char *msg)

{

perror(msg);  exit(0);

}

int main(int argc, char *argv[])

{

int sockfd, portno, n;  struct sockaddr_in serv_addr;  struct hostent *server;  char buffer[256];

if (argc &lt; 3) {

fprintf(stderr,”usage %s hostname port
”, argv[0]);  exit(0);

}

portno = atoi(argv[2]);  sockfd = socket(AF_INET, SOCK_STREAM, 0);

if (sockfd &lt; 0)   error(“ERROR opening socket”);  server = gethostbyname(argv[1]);  if (server == NULL) {  fprintf(stderr,”ERROR, no such host
”);

exit(0);

}

bzero((char *) &amp;serv_addr, sizeof(serv_addr));  serv_addr.sin_family = AF_INET;  bcopy((char *)server-&gt;h_addr,   (char *)&amp;serv_addr.sin_addr.s_addr,  server-&gt;h_length);  serv_addr.sin_port = htons(portno);  if (connect(sockfd,(struct sockaddr *)  &amp;serv_addr,sizeof(serv_addr)) &lt; 0)   error(“ERROR connecting”);  printf(“Please enter the message: “);  bzero(buffer,256);  fgets(buffer,255,stdin);  n = write(sockfd,buffer,strlen(buffer));

if (n &lt; 0)

error(“ERROR writing to socket”);  bzero(buffer,256);  n = read(sockfd,buffer,255);

if (n &lt; 0)

error(“ERROR reading from socket”);  printf(“%s
”,buffer);  close(sockfd);  return 0;

}




DOCUMENTATION

Your program should be developed using GNU versions of the C compiler. It should be layered,  modularized, and well commented.