IRC (Internet Relay Chat) is a protocol for real-time text messaging between internet-connected computers created in 1988.

It is mainly used for group discussion in chat rooms called “channels” although it supports private messages between two users, data transfer, and various server-side and client-side commands. 

As of April 2011, the top 100 IRC networks served over 500,000 users at a time on hundreds of thousands of channels.

IRC is a popular method used by [botnet](https://www.radware.com/Security/ddos-knowledge-center/DDoSPedia/botnet/ "botnet") owners to send commands to the individual computers in their botnet. This is done either on a specific channel, on a public IRC network, or on a separate IRC server. 

The IRC server containing the channel(s) that are used to control bots is referred to as a “command and control” or C2 server.



Tool Used for interacting with IRC is 

```
hexachat
```

Or we can also use netcat to connect on the IRC port

For exploitation I learned here how to use IRC and also exploit it
  from below 
https://0xdf.gitlab.io/2019/04/27/htb-irked.html#irc