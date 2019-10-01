# Recursive whois for RTIR (Request Tracker for Incident Response)

Default RTIR whois facility uses remote whois servers which in fact are whois services of local registries. To find information for arbitrary IP address can take several consequitive searches. This script works as a whois proxy on the localhost which makes recursive whois searches on relevant whois servers and produces appropriate result.

## Installation
Install dependencies:
```
cpan install Data::Validate::IP
cpan install Data::Validate::Domain
cpan install Net::Nslookup
cpan install Net::Whois::Raw
```
Copy script to some permanent location:
```
root@rtir:~# cp scripts/whois /usr/local/sbin
```
Install xinetd facility and add whois service
```
root@rtir:~# apt install xinetd
root@rtir:~# vi /etc/xinetd.d/whois
service whois
{
        socket_type = stream
        protocol = tcp
        wait = no
        user = nobody
        group = nobody
        groups = no
        server = /usr/local/sbin/whois
                bind = 127.0.0.1
        disable = no
}

root@rtir:~# service xinetd reload
```

Configure RTIR
```
root@rtir:~# vi /opt/rt4/etc/RT_SiteConfig.pm:
Set($whois, {
    0 => {
        Host         => "localhost",
        FriendlyName => "Recursive WHOIS lookup",
    },
.....

root@rtir:~# service apache2 reload
```
