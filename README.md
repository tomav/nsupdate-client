# nsupdate-client


A simple client to update DNS record from a remote client to a DNS server using nsupdate protocol, like No-IP or DynDNS for your own domain names.

Tested on Linux and OSX.

## Requirements

* A DNS Server configured to accept updates (see below)
* Firewall with port 53/UDP open on the server
* dnsutils package on your server & clients

## Configure your DNS server

Generate a public/private key pair:

    dnssec-keygen -a HMAC-MD5 -b 512 -n HOST www.domain.com.

Configure your DNS zone like this:

    zone "domain.com" {
      type master;
      file "/etc/bind/domain.com.hosts";
      allow-update { key "www.domain.com."; };
    };

    key "www.domain.com" {
      algorithm hmac-md5;
      secret "the-value-of-the-generated-key-that-you-can-see-in-public-and-private-keys ==";
    };

Restart your DNS Server

    /etc/init.d/bind9 restart


## Usage on your client

Clone the project 

    git clone git@github.com:tomav/nsupdate-client.git

Copy the generated public/private keys on your machine. They must not be separated.

And then choose the command that fit your needs:

    With static IP
    ./nsupdate-client -k Kdomain.com+xxxxxx.private -r sub.domain.com -l 600 -t A -d 192.168.1.100

    With internal dynamic IP (where ethX is your interface name, like eth0, en1, ppp2...)
    ./nsupdate-client -k Kdomain.com+xxxxxx.private -r sub.domain.com -l 600 -t A -d if_ethX

    With external dynamic IP 
    ./nsupdate-client -k Kdomain.com+xxxxxx.private -r sub.domain.com -l 600 -t A -d external

    With CNAME
    ./nsupdate-client -k Kdomain.com+xxxxxx.private -r sub.domain.com -l 600 -t CNAME domain.com


