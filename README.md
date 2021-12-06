# apaleo-pipeline (to postgres)

Preconfigured containerised meltano pipeline, which uses `tap-apaleo` and `target-prostgres` to copy apaleo data into a postgres database.

## How to use
1. Copy `docker-compose.yml` (only this file is needed)
2. Replace CHANGEME1, CHANGEME2 and CHANGEME3 with strong passwords
3. Install connected apaleo app by clicking here:    
https://app.apaleo.com/apps/connected-apps/create?clientCode=TAPAPALEO&clientName=tap-apaleo&secret=tap-apaleo%20is%20a%20Singer%20tap%20for%20apaleo.&clientScopes=%5B%22maintenances.read%22,%22rateplans.read-corporate%22,%22reservations.read%22,%22setup.read%22%5D&piiMode=Retrieve
4. Enter values from previous step for `TAP_APALEO_CLIENT_ID` and `TAP_APALEO_CLIENT_SECRET`.
3. Generate SSL certs (see below) or delete following lines if SSL transfer encryption is not required (not recommended):
```
      - ./server.crt:/var/lib/postgresql/server.crt:ro
      - ./server.key:/var/lib/postgresql/server.key:ro
    command: >
      -c ssl=on
      -c ssl_cert_file=/var/lib/postgresql/server.crt
      -c ssl_key_file=/var/lib/postgresql/server.key
```
4. Start and daemonise pipeline with `docker-compose up -d`
5. Postgres database `apaleo` with apaleo data is exposed on port 5432 with username `postgres` and the new password for CHANGEME1.


## Generate selfsigned SSL cert (not accepted by Power BI)

```
openssl req -new -text -passout pass:abcd -subj /CN=localhost -out server.req
openssl rsa -in privkey.pem -passin pass:abcd -out server.key
openssl req -x509 -in server.req -text -key server.key -out server.crt

chown 999:999 server.key #as expected from postgres
chmod 600 server.key #as expected from postgres
```

## Generate SSL with certbot

```
certbot certonly --standalone -d DOMAIN
cp /etc/letsencrypt/live/DOMAIN/privkey.pem ./server.key
cp /etc/letsencrypt/live/DOMAIN/fullchain.pem ./server.cert

chown 999:999 server.key #as expected from postgres
chmod 600 server.key #as expected from postgres
```

*Note:* After the certificate renewal, the certificate must be copied again and the service needs to be restarted.

## Consulting / Hosting / Open for work
I'm a freelance Full Stack Developer & Data Analyst from near Hamburg. I can help you with setting up or hosting the data pipeline and creating meaningful reports in Power BI. Please get in touch:

Felix Koch  
felix@tagungshotels.info  
+49 4266 999 999 9  
[Make an appointment](https://meetings.hubspot.com/felix137)  
[https://felixkoch.de](https://felixkoch.de)  
[Imprint](https://tagungshotels.info/impressum)