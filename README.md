# custom-trace-route

Traceroute is a tool to that allows you to trace the route network packets will take from one computer to another over a network.

For example, here’s what using it to look at the route from my laptop to dns.google.com:

% traceroute dns.google.com
traceroute: Warning: dns.google.com has multiple addresses; using 8.8.4.4
traceroute to dns.google.com (8.8.4.4), 64 hops max, 52 byte packets
 1  192.168.68.1 (192.168.68.1)  5.575 ms  3.908 ms  5.036 ms
 2  broadband (192.168.1.1)  4.470 ms  12.434 ms  3.374 ms
 3  * * *
 4  63.130.172.45 (63.130.172.45)  40.321 ms  104.606 ms  39.726 ms
 5  72.14.216.236 (72.14.216.236)  41.609 ms
    195.66.236.125 (195.66.236.125)  33.368 ms
    72.14.216.236 (72.14.216.236)  38.354 ms
 6  108.170.246.161 (108.170.246.161)  44.235 ms *
    108.170.246.129 (108.170.246.129)  37.994 ms
 7  dns.google (8.8.4.4)  41.965 ms
    172.253.66.99 (172.253.66.99)  42.097 ms
    dns.google (8.8.4.4)  35.463 ms
You can see that there are 7 hops from my laptop to dns.google.com.

Trace Route is described on it’s man page like this:

The Internet is a large and complex aggregation of network hardware, connected together by gateways.  Tracking the route one's packets follow (or finding the miscreant gateway that's discarding your packets) can be difficult. traceroute utilizes the IP protocol `time to live' field and attempts to elicit an ICMP TIME_EXCEEDED response from each gateway along the path to some host.

So in this challenge we’re going to build a command line tool that does just that.

Step Zero

Like all good programming languages we’re zero indexed!

For this step, I’ll leave you to setup your IDE / editor of choice and programming language of choice.

Step 1

In this step your goal is to create a simple command line program that will accept one argument, the host name and will print out the first line of traceroute output. That should look something like this:

% cctraceroute dns.google.com
traceroute to dns.google.com (8.8.4.4), 64 hops max, 32 byte packets
You can pick your own number of max hops. The packet you send is arbitrary, so I’m using a simple text string: 'codingchallenges.fyi trace route’.

Step 2

In this step your goal is to create the sockets we need to build trace route. That is we need a socket that we can use to send packets out and a socket that we will receive the responses on. We will use two sockets, one to send a UDP message and one to receive the ICMP message to let us know the message we sent timed out. By default traceroute uses destination ports in the range 33434 to 33534, you should too.

When you are done, set the TTL option on the sending socket to one and send a message to your destination host. Print out the IP address of the host that responds. That should look something like this:

% cctraceroute dns.google.com
traceroute to dns.google.com (8.8.4.4), 64 hops max, 32 byte packets
1  192.168.68.1 
Don’t forget the IP addresses you see might not match mine.

Step 3

In this step your goal is to print out the hostname of the first step if there is one. If not print the IP address again. That should then look like this:

% cctraceroute dns.google.com
traceroute to dns.google.com (8.8.4.4), 64 hops max, 32 byte packets
1  192.168.68.1 (192.168.68.1)
Step 4

In this step your goal is to keep sending packets with an incremental TTL until a packet reaches the destination host. Handle the case where there is no response from a host and print the hop and the wildcard: i.e.: 3 * * *.

That will look like this (again the number of hops and the IP addresses will vary).

% cctraceroute dns.google.com
traceroute to dns.google.com (8.8.4.4), 64 hops max, 32 byte packets
1  192.168.68.1 (192.168.68.1) 
2  broadband (192.168.1.1) 
3  *  * *
4  63.130.172.45 (63.130.172.45) 
5  72.14.216.236 (72.14.216.236) 
6  *  * *
7  dns.google (8.8.4.4) 
Step 5

In this step your goal is to record the round trip latency to each hop along the way that will look something like this:

% cctraceroute dns.google.com
traceroute to dns.google.com (8.8.4.4), 64 hops max, 32 byte packets
1  192.168.68.1 (192.168.68.1) 5.131 ms
2  broadband (192.168.1.1) 4.999 ms
3  *  * *
4  63.130.172.45 (63.130.172.45) 30.561 ms
5  195.66.236.125 (195.66.236.125) 29.541 ms
6  74.125.242.97 (74.125.242.97) 36.641 ms
7  142.251.52.151 (142.251.52.151) 32.878 ms
8  dns.google (8.8.4.4) 35.859 ms
