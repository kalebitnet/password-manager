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

## What will I use?
For the server I'm going to use Go. Because its ready to ***go*** with
networking out of the box its an obvious choice. Along with strong TLS support
in both the standard and x libaries this makes a lot of the network-based
functions easier. I assume "golang.org/x" means "Golang eXtended".

For the webapp I'll actually use some UI framework. I really don't care to do
that but in the year 2026 I don't have much of a choice. I do not want to use
NodeJS, Dino or Bun if I can help it. If I have to I will do research into which
may be the best. Please do not give me your opinion, but if there are technical
aspects to consider (concurrency, memory allocation, some third thing) then I
am maybe, possibly, potentially even, willing to listen.

For the desktop application I will use C++ because it is portable. What will
likely happen here is I will create a portable library that I can just
copy-paste for API bindings. The frontend will have to be OS specific. I will
likely put minimal effort into this because the webapp version will exist. The
point is exposure.

For the mobile my options are limited. Already using C++ as a basis is great
for Android and iOS, then using Kotlin/Java and Swift for pure UI I think is the
right move. Downside is I do not have an apple smart cellphone or apple tablet
so I won't be able to know how it feels but thats OK because I don't care.

Browser extensions are just going to be that. Its javascript. Its HTML and CSS.
Pretty simple. I'm probably going to do this first since it will be easiest.

## Design Rough Draft
Because the API is the most important aspect of this that is what I should start
with.

### API
> <domain>/api/

This I think is a good start. I've seen this elsewhere so I assume it is good
practice. t is obvious as to what the URL is for and what it is doing. Something
else I've seen is the use of API keys. So I can do something like
<domain>/api/key-auth
<domain>/api/confirm-token
<domain>/api/do-thing
or
<domain>/api/do-thing&key?=abc

My understanding is that having queries in the URL is insecure so I think I
prefer the former. That way I can put more information in something like JSON.
This will require more research. If you have an opinion I would like to hear
your reasoning as to what may be better. The top three look like RPC which I
kind of like but what makes the most technical sense is what I want to do.
Trying to push my comfort zone.

Due to the nature of passwords being CRUDable, I'm thinking a simple RESTful
CRUD API is for the best.

### Storage
I'm fanatic about doing everything myself if it isn't in the standard library. I
am sure I'm not alone and I'm sure I'm not alone in learning how unreasonable
that is.

The database is going to be very simple. It will be compressed then encrypted.
Or vice-versa. It won't be relational because I don't know how to do that, but
also that is overkill for what I'm trying to do. I'm thinking it will be a JSON
document store similar to what I did with my dailies project. This time however,
I will actually draw it out instead of just going at it. The database
automatically sorts alphabetically, but options are available to sort by
creation date or expiry date.

### What a "password" is
The password is a jumble of letters, numbers and characters. They are stored
encrypted with the salt and the other parameters involved. I will be using
Argon2-id as there is official support by the Go library. When sending password
data to the client it is unclear of how much memory, CPU cycles or how many
threads the client can handle. So the password is decrypted on the server and
then relying on TLS to securely transfer the data. From there how data is stored
or updated client-side is based on the client implementation.

A password isn't just a code. Its an experience.

Its also part of a data structure. That would probably look something like
type PasswordData struct {
    password string 
    memory uint32
    time uint32
    threads uint8
    keyLen uint32
    salt []byte // Memory, time, threads, keyLen and salt are all necessary
                // for the argon2id functions
}

type LoginInfo struct {
    id uint // Used to keep track of when one has been added; for more
            // efficient updating
    url string
    username string
    passwordData PasswordData
    creation string // RFC3339 format
    expiry string // RFC3339 format or the string "f" if no expiration
    tags string // CSV, no whitespace, multiword tags uses hyphens
    notes string
}

When data is being prepared to be transfered to the client, the structure will
probably look something like this:
type UnsafeInfo struct {
    id uint
    url string
    username string
    password string // The only change from LoginInfo
    creation string
    expiry string
    tags string
    notes string
}

For transfering between the server and the client, protobuf is probably the best
option. This way updates can be made lighter on the network and I get some nice
exposure to it :)

### Users?
There would not be users. Much of my inspiration is coming from KeepassXC. If
you look at the data structures above you will notice that those fields are
basically the same. I mention this because the idea of an identity file would
be the best option. This is where the security of the application begins to
really break down. The passwords are useless if they can't be used.

My solution is to allow for a temporary password so that an identity file can be
downloaded from the server. Once the password is used it expires. After 5
minutes or so it expires. This is a temporary breach of security but does not
last long enough to warrent ***too much*** concern.

## Final Notes
This is just a draft of my idea. There is quite a lot to learn but I'm excited !
This README will not be updated. Instead I will create other markdown files that
describe some of my thought processes into the design. The client-side stuff
will be developed in different repos. Another aspect that I did not mention is
the webapp. This is built into this server but is an optional aspect if ran.


