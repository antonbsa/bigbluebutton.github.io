---

layout: page
title: "Custom SSL Certificates"
category: greenlight_v3
date: 2022-09-08 16:25:25

---
Greenlight is a web application meaning that it runs and is accessible through the world wide web using a browser.

Historically the web’s protocol has always been HTTP.

After years of upgrades to the HTTP protocol it remained insecure since by design it’s a plaintext protocol meaning that all of your users data will be sent in plaintext through any traversed networks where any person or evolved nodes can easily extract such information which may include confidential data such as credentials, email addresses, files, access codes, …

To secure the HTTP protocol HTTPS was introduced adding an extra layer of security to the plain protocol enabling data confidentiality, integrity and accounting.

The use of HTTPS became essential and all developers world wide are pushing to its use.

We’re on the same team and we’d highly recommend that you set and use Greenlight services through the **HTTPS** secure protocol.

We’ve documented the steps to create publicly valid SSL certificates for your FQDNs for free and in all simplicity using Letsencrypt as part of the installation.

However some organizations may already been having their certificates and willing to use it.

For such cases, these steps will document what to change in order to set and use your certificates.

We expect that you have successfully followed all of the installation steps and having Greenlight running on your system and accessible through HTTP.

We expect that the FQDNs are valid with active DNS records pointing to your system public IP address with web ports opened for inbound traffic.

*If not, kindly follow the installation steps before proceeding.*

---

## Custom Letsencrypt certificates:

If your custom certificates were generated by letsencrypt (certbot) for each of your FQDNs you can copy your certificates data and cache (renewal configurations, archive files, live files,…) or the whole letsencrypt directory ‘**/etc/letsencrypt/**’ into ‘**~/greenlight-run/data/certbot/conf**’ (if that does not include any other irrelevant certificates).

However, the only constraint is that you had generated two standalone certificates, one for each of your FQDNs matching what you have as “**$GL_HOSTNAME.$DOMAIN_NAME**” **and** “**$KC_HOSTNAME.$DOMAIN_NAME**”.

```bash
cd ~
sudo mkdir -p greenlight-run/data/certbot/conf/live/
sudo cp -r /etc/letsencrypt/ greenlight-run/data/certbot/conf
```

You can skip the rest of the steps and start Greenlight3 into **Starting Greenlight.**

---

### Renewing Certificates:

Greenlight will have by default a **certbot** service that will be responsible for automatically renewing your certificates, placing your existing Letsencrypt certificates to ‘**~/greenlight-run/data/certbot/conf**’ with **certbot** service in place will enable it to manage its renewal.

However, if you have not used Letsencrypt to issue your custom certificates, you will be responsible for their renewal.

For that you may need to remove the certbot service since you won’t need it.

To disable the **certbot** service please remove this highlighted lines on **~/greenlight-run/docker-compose.yaml**:

But before let’s stop Greenlight:

```bash
cd ~/greenlight-run
sudo docker compose down
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/51f64a33-e6db-4acf-9917-56e0863c7ef6/Untitled.png)

And **save** the changes.

---

## Placing custom certificates:

We’d start by placing our selves under the home directory:

```bash
cd ~
```

Nginx expects two directories named after each FQDN located under **~/greenlight-run/data/certbot/conf/live** having the following certificates files each: the full chain **fullchain.pem** and the private key **privkey.pem**.

Keycloak instance will also expect the certificate file to be present in a **cert.pem** named file under the Keycloak certificate directroy.

***If you have followed Greenlight without Keycloak guide you are required only to create one directory named after Greenlight FQDN containing its fullchain.pem and privkey.pem.***

So for your custom certificates, we’d have to create two directories named after each FQDN.

For a **DOMAIN_NAME=xlab.bigbluebutton.org, GL_HOSTNAME=gl and KC_HOSTNAME=kc** we’d create two directories **gl.xlab.bigbluebutton.org and kc.xlab.bigbluebutton.org.**

Kindly place the certificates directories under your home directory ****and place in the **fullchain.pem and privkey.pem** of each certificate under its directory along with the **cert.pem** of Keycloak under its certificate directory and make sure that the directories names matches what you have on your **.env as: ‘$GL_HOSTNAME.$DOMAIN_NAME’ and ‘$KC_HOSTNAME.$DOMAIN_NAME’ (required only if Keycloak is enabled)**.

---

## Multi domain and wildcard certificates:

The following guide is expecting two standalone certificates, each targeting one FQDN one for Greenlight and another for Keycloak.

However, those assertions/expectations may be absurd for some deployments who have and willing to use a none standalone certificate for both FQDNs.

For that you’d only need to do this extra step before proceeding:

Kindly copy in the composite certificate files **fullchain.pem, privkey.pem and cert.pem** in your home directory in a directory named after Greenlight FQDN that would match what you have on **.env** file as ‘**$GL_HOSTNAME.$DOMAIN_NAME’**.

Then you’d create a **relative** symbolic link named after Keycloak FQDN that links to the composite certificate directory (As you may have guessed, the directory named after Greenlight FQDN).

For that please edit the following command and run it:

- **<YOUR_GL_FQDN>**: is a placeholder for your Greenlight FQDN, in our example that would be ‘**gl.xlab.bigbluebutton.org**’, for your deployment that should match ‘**$GL_HOSTNAME.$DOMAIN_NAME’**.
- **<YOUR_KC_FQDN>**: is a placeholder for your Keycloak FQDN, in our example that would be ‘**kc.xlab.bigbluebutton.org**’, for your deployment that should match ‘**$KC_HOSTNAME.$DOMAIN_NAME’.**



```bash
sudo ln -s ./**<YOUR_GL_FQDN>** **<YOUR_KC_FQDN>**
```

So following our example, you’d have something similar to:

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5f55448d-2b51-4af5-9881-32756fc27665/Untitled.png)

Now you can follow the rest of the guide and have the expected outcome while using a composite certificate.

---

Following our example we’d have something like:

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a001bc1a-f933-4d8f-8082-49f49b04deab/Untitled.png)

*If you’re using a composite certificate, it’s expected that both directories will have the same content.*

Now we’d copy the certificates directories in their expected location:

- **<YOUR_GL_FQDN>**: is a placeholder for your Greenlight FQDN, in our example that would be ‘**gl.xlab.bigbluebutton.org**’, for your deployment that should match ‘**$GL_HOSTNAME.$DOMAIN_NAME’**.
- **<YOUR_KC_FQDN>**: is a placeholder for your Keycloak FQDN, in our example that would be ‘**kc.xlab.bigbluebutton.org**’, for your deployment that should match ‘**$KC_HOSTNAME.$DOMAIN_NAME’.**

```bash
sudo mkdir -p greenlight-run/data/certbot/conf/live/
sudo cp -r **<YOUR_GL_FQDN>**/ greenlight-run/data/certbot/conf/live/
sudo cp -r **<YOUR_KC_FQDN>**/ greenlight-run/data/certbot/conf/live/ # Only, if you use Keycloak
```

 ******

You can verify that your certificates were copied by listing them:

```bash
sudo ls -R greenlight-run/data/certbot/conf/live/
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ab5b781f-ddd8-4cce-9a5a-bd4d00d432fc/Untitled.png)

*If you’re using a composite certificate, it’s expected that both directories will have the same content.*

*If you have followed the guides to remove Keycloak it’s expected to not having its certificate directory.*

---

## Starting Greenlight:

We now need to update our nginx configuration file to open **HTTPS** traffic, you can do so manually by going to **./data/nginx/sites.template-*** files ****and uncommenting (Removing the **#** prefix) all lines for the HTTPS binding and SSL certificate files locations.

Or, for your convivence you can run:

```bash
cd ~/greenlight-run
sed -i "/#listen.*/s/#//g" ./data/nginx/sites.template-*
sed -i "/#ssl_certificate.*/s/#//g" ./data/nginx/sites.template-*
sed -i "/#.*\/data\/certbot\/conf\/live\/.*/s/#//g" docker-compose.yml
```

Now we restart the services:

```bash
sudo docker compose down && sudo docker compose up -d
```

Accessing your Greenlight and Keycloak instances through their HTTPS enabled URLs should be possible now.

So for a **DOMAIN_NAME=xlab.bigbluebutton.org, GL_HOSTNAME=gl and KC_HOSTNAME=kc.**

You can access Greenlight through:  [https://gl.xlab.bigbluebutton.org/](http://gl.xlab.bigbluebutton.org/) and Keycloak through [https://kc.xlab.bigbluebutton.org](http://gl.xlab.bigbluebutton.org/).

When encountering any issues with nginx you can restart it by:

```bash
sudo docker compose restart nginx
```

You can verify the validity of your SSL certificates through your browser, when accessing your URLs you should have a valid SSL status:

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/24697341-237a-4a88-97b6-6a72b9aa5b41/Untitled.png)

**Congratulations** on following the steps until this point, now you have a production ready setup with our recommended configuration set and apt for using.

---

**Greenlight v3 is still in its Alpha version.**

For any suggestions, notes or any encountered issues please refer to the official repository and the community group:

***GitHub Repository:*** [https://github.com/bigbluebutton/greenlight/](https://github.com/bigbluebutton/greenlight/)

***Community Group:*** [https://groups.google.com/g/bigbluebutton-greenlight](https://groups.google.com/g/bigbluebutton-greenlight)

---

If have **not** followed **Greenlight without Keycloak** guide,please follow this guide ***Greenlight with Keycloak*** to make a minimal configuration for Keycloak and integrate it with your Greenlight instance.