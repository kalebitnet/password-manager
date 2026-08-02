# The point is to do it
I'm creating this project just to do something. I have bigger things in mind but
have lost the touch. So why not make something that I find useful but make it my
own?

## How to use
Hosting this to the open internet is discouraged. Instead, host a VPN. It is
meant to be used within a private network.

This is a network-based password manager that you can host on your home network.
Its less of a product and more of a toy. My expertise is ***not*** in security,
so I waive all responsibility if something goes wrong.

It can be hosted on your local machine or remote machine.
***I highly discourage hosting this on the cloud, even with a VPN***.

## Considerations
There are particular learning goals with this project. I want to first create an
API so that the user interface can be custom made. This can be
a browser extension, a webapp or a mobile app.

From this I can build a browser extension, webapp and mobile app without needing
to subscribe to some third-party service. If the internet goes out then I can't
very well use someone else's API.

So the part that I care most about is designing the API and analyzing network
behaviour. Using pfsense, I can set up my own Certificate Authority for TLS and
see how well that does. Thats optional but knowing how to set stuff up like this
is important.
