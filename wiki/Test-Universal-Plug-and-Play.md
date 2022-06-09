[Universal Plug and Play (upnp)](https://en.wikipedia.org/wiki/Universal_Plug_and_Play) is a service for dealing with the [network address translation](https://en.wikipedia.org/wiki/Network_address_translation) that is required when you are provided with a [dynamic IP address](https://en.wikipedia.org/wiki/IP_address#Dynamic_IP) by your ISP. In practice, upnp is a service offered by your local router that facilitates connections to computers on your local network from the internet at large. Not all routers offer upnp and some routers offer upnp only if you enable it. Check with your router documentation.

To test upnp,

* Learn your external IP address with a service like http://ifconfig.me/
* Run your ethereum client (eth, AlethZero, etc) and figure out which port it's using. Look for a message like `Punched through NAT and mapped local port 30303 onto external port 30303` in the logs. The external port is the one to note down.
* Ask a friend outside of your local network to try `telnet ip port`

If you get a message like `Unable to connect to remote host: Connection refused` then upnp is not working. If you get a connection with some gibberish then it is working.