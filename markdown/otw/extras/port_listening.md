# Port Listening


## What is a Port Listener

The idea of a `listener` in a network, refers to the idea of a program waiting for an incoming connection to a specific
port. For instance, if we are listening to `port 12345` we are, generally speaking, standing still and waiting for
something to happen on that port. That port is reserved for that particular process until the process has concluded its 
business.  **In most operating systems, once a port is bound to a listening process, no other process can bind to the same 
port until the original process releases it**. This ensures the integrity of the communication channel. Once we receive an 
incoming `packet`, we might intercept that `packet` and do something with it, or just relay information. It's like setting up a 
receiving station that's constantly monitoring for any incoming transmissions on a particular frequency or channel.

## What can you do with a listener?

Upon receiving the `packet` the program can do a variety of things, including relaying the data to some other process
or even altering the packet to change the meaning of its contents (change `I love you` to `I hate you`).

#### Data Transfer

- File Sharing: A listener can be configured to accept incoming files from a remote location. For example, in FTP or SFTP, a listener is set up to accept file uploads.
- Message Queue: It is also possible for more complex architectures, like `MQTT` in IoT, that a listener could be a message broker that waits for incoming messages from various publishers.

#### Command Execution

- Reverse Shells: In cybersecurity, a listener can be set up to execute commands sent from a remote location. This is commonly used in penetration testing to control a compromised machine.
- Remote Procedure Call (RPC): In distributed systems, a listener can execute functions or procedures based on incoming requests.

#### Logging and Monitoring

- Intrusion Detection Systems (IDS): Listeners can be set up to capture packets and analyze them to detect suspicious activities.
- Traffic Analysis: In network management, listeners can be configured to monitor the type and amount of traffic that is being transmitted over the network.

#### Security Measures

- Firewall Rules: A listener can act as a rudimentary firewall by applying certain rules to incoming packets, allowing or denying them based on various attributes like IP address, packet size, etc.
- SSL Termination: In secure web architectures, a listener can be the endpoint where SSL encryption is terminated, allowing for internal traffic to be unencrypted for performance reasons.

#### Load Balancing
- Distribution of Requests: In web architectures, a listener can be set up to distribute incoming client requests across multiple servers to balance the load.
  
#### Protocol Translation

- Gateway Services: Listeners can serve as a bridge between two different communication protocols. For example, translating HTTP requests into SQL queries for a database.
  
#### Quality of Service (QoS) Management

- Bandwidth Throttling: Some advanced listeners can control the rate of data transfer to maintain a certain level of service quality.
  
#### Event-Driven Architecture

- Webhooks: Listeners can be set up to trigger certain actions in response to specific events, which is commonly used in CI/CD pipelines.


## Real-Life Usage

Suppose we wanted to create a "Messanger App". We can use the following:

``` commandline
➜  ~  ncat -l 1234 > logs.txt
```

We then open a new shell and connect to the port via `ncat` and write some random words.

``` commandline
➜  ~  ncat localhost 1234
hello
this
is
cool
shit
```

Let's see if we were able to write to the file via the `ncat` listener.

``` commandline
➜  ~  cat logs.txt
hello
this
is
cool
shit
➜  ~  
```

Boom. We were able to write to the thing.

Let's try it with the `ncat -v` flag.

``` commandline
➜  ~  ncat -l -v 1234 > logs.txt
Ncat: Version 7.94 ( https://nmap.org/ncat )
Ncat: Listening on [::]:1234
Ncat: Listening on 0.0.0.0:1234
Ncat: Connection from [::1]:56904.
➜  ~  
```

``` commandline
➜  ~  cat logs.txt 
this
is with verbosity
now
```

The `ncat -v` does not change the file's content, only what gets displayed in the shell during the listening.

### Pipe In

If we pipe in some text using the `echo` command, we will receive the text when we connect to the port:

#### Shell 1

``` commandline
➜  ~  echo "Hello World" | ncat -l 1234 > logs.txt

```

#### Shell 2

``` commandline
➜  ~  ncat localhost 1234
Hello World

```

Now, what if we tried to execute some commands? Let's do that. 

#### logs.txt

Contains: `Yoooo this be  the logs`


#### Shell 1

``` commandline
➜  ~  ncat -l 1234 -e /bin/bash

```

#### Shell 2

``` commandline
➜  ~  ncat localhost 1234
cat logs.txt
Yoooo this be  the logs

```

This is also considered a reverse shell. We will create another markdown file explaining a reverse shell in more detail. 



