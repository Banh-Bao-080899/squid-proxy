# squid-proxy
setup high anonymous with squid

sudo nano /etc/apt/sources.list and add deb:

      deb http://deb.debian.org/debian stretch main contrib non-free
      deb-src http://deb.debian.org/debian stretch main contrib non-free
update:
```
sudo apt clean
sudo apt update
```
Download Squid source and build dependencies
```
sudo apt source squid
```
```
sudo apt build-dep squid3 (or squid)
```

Modify the package:
```
sudo nano squid3-3.5.23/debian/rules
```
In rules add code:
```
DEB_CONFIGURE_EXTRA_FLAGS := BUILDCXXFLAGS="$(CXXFLAGS) $(CPPFLAGS) $(LDFLAGS)" \
                --datadir=/usr/share/squid \
                --sysconfdir=/etc/squid \
                --libexecdir=/usr/lib/squid \
                --mandir=/usr/share/man \
                --enable-inline \
                --disable-arch-native \
                --enable-async-io=8 \
                --enable-storeio="ufs,aufs,diskd,rock" \
                --enable-removal-policies="lru,heap" \
                --enable-delay-pools \
                --enable-cache-digests \
                --enable-icap-client \
                --enable-follow-x-forwarded-for \
                --enable-auth-basic="DB,fake,getpwnam,LDAP,NCSA,NIS,PAM,POP3,RADIUS,SASL,SMB" \
                --enable-auth-digest="file,LDAP" \
                --enable-auth-negotiate="kerberos,wrapper" \
                --enable-auth-ntlm="fake,smb_lm" \
                --enable-external-acl-helpers="file_userip,kerberos_ldap_group,LDAP_group,session,SQL_session,time_quota,unix_group,wbinfo_group" \
                --enable-url-rewrite-helpers="fake" \
                --enable-eui \
                --enable-esi \
                --enable-icmp \
                --enable-zph-qos \
                --enable-ecap \
                --disable-translation \
                --with-swapdir=/var/spool/squid \
                --with-logdir=/var/log/squid \
                --with-pidfile=/var/run/squid.pid \
                --with-filedescriptors=65536 \
                --with-large-files \
                --with-default-user=proxy \
                --with-large-files \
                --with-default-user=proxy \ 
                --enable-http-violations
 ```               
Configure the proxy server,edit /etc/squid/squid.conf and change: 
    ```http_access deny all -> http_access allow all```
    change port proxy server:
    ```
     http_port 443
     http_port 80
    ```
    Elite configs: 
            # Deny headers
              request_header_access Via deny all
              request_header_access Forwarded-For deny all
              request_header_access X-Forwarded-For deny all
              request_header_access Referer deny all
              request_header_access From deny all
              request_header_access User-Agent deny all

            # Deny headers
              reply_header_access Via deny all
              reply_header_access Server deny all
              reply_header_access WWW-Authenticate deny all
              reply_header_access Link deny all
              
              request_header_access Authorization allow all
              request_header_access Proxy-Authorization allow all
              request_header_access Cache-Control allow all
              request_header_access Content-Length allow all
              request_header_access Content-Type allow all
              request_header_access Date allow all
              request_header_access Host allow all
              request_header_access If-Modified-Since allow all
              request_header_access Pragma allow all
              request_header_access Accept allow all
              request_header_access Accept-Charset allow all
              request_header_access Accept-Encoding allow all
              request_header_access Accept-Language allow all
              request_header_access Connection allow all
              request_header_access All deny all

              reply_header_access Allow allow all
              reply_header_access WWW-Authenticate allow all
              reply_header_access Proxy-Authenticate allow all
              reply_header_access Cache-Control allow all
              reply_header_access Content-Encoding allow all
              reply_header_access Content-Length allow all
              reply_header_access Content-Type allow all
              reply_header_access Date allow all
              reply_header_access Expires allow all
              reply_header_access Last-Modified allow all
              reply_header_access Location allow all
              reply_header_access Pragma allow all
              reply_header_access Content-Language allow all
              reply_header_access Retry-After allow all
              reply_header_access Title allow all
              reply_header_access Content-Disposition allow all
              reply_header_access Connection allow all
              reply_header_access All deny all ```
*restart squid: sudo systemctl restart squid
