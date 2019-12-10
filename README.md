Execute these commands to deploy a postfix relay server that speaks
smtp, smtps (smtp over ssl/tls) and submission (smtp using STARTTLS).
The commands below will listen on 43025, 43465, and 43587.

Be sure to adjust the smtp_user part with a username and password of
your choosing.

The maildomain and the certificate's subject name should match the fully
qualified domain name of the machine running the container.

```bash
cd assets/certs
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes
mv cert.pem cert.crt
mv key.pem key.key
cd ../..
docker build -t postfix .

# Make sure to adjust the arguments of the next command as needed
docker run \
 -p 43025:25 \
 -p 43465:465 \
 -p 43587:587 \
 -v `pwd`/assets/certs:/etc/postfix/certs \
 -e maildomain=riddle2.grammatech.com \
 -e smtp_user=dvitek@riddle2.grammatech.com:12345 \
 --name postfix \
 --rm \
 -d postfix

# Follow log
docker exec -ti postfix tail -f /var/log/mail.log

docker kill postfix
```
