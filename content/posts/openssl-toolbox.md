---
title: "OpenSSL Toolbox"
tags: [devops, experiments]
date: 2020-03-18
author: Allemand Sébastien
---

- https://raymii.org/s/articles/OpenSSL_manually_verify_a_certificate_against_a_CRL.html

## Verify SSL Certificate

1. Get certificate
   `openssl s_client -showcerts -connect <domain_name>:443`



## Certificate part

### CSR (Certificate Signing Request)
*CSR is an encoded text that is given to a Certificate Authority when you apply for an SSL Certificate.*  It's generaly generated on the server where the certificate will be instaled. It contains some informatiosn that will be included in the certificate such as the organization name, common name (domain name), locality, and country. It also contains the public key that will be included in the certificate.

#### What is contained in a CSR?
| Name         | Description                                            | Value        |
|--------------|--------------------------------------------------------|--------------|
| Common Name  | The fully qualified domain name (FQDN) of your server. | *.google.com |
| Organization | The legal name of your organization.                   | Google Inc.  |
| Organizational Unit	 | The division of your organization handling the certificate.	| Information Technology IT Department |
| City/Locality	 | 	The city where your organization is located.  | 		Mountain View  |
| State/County/Region | The state/region where your organization is located.  | US  |
| Country | The two-letter ISO code for the country where your organization is location.	  | GB  |
| Email address | An email address used to contact your organization.  | webmaster@google.com  |
| Public Key | The public key that will go into the certificate. | 	The public key is created automatically |
[source](https://www.sslshopper.com/what-is-a-csr-certificate-signing-request.html)


### PEM “Privacy Enhanced Mail”
A PEM file for an X.509 certificate is simply a text file that includes a Base64 encoding of certficiate text and a plain-text header and footer marking the beginning and end of the certificate [source](https://www.ssl.com/faqs/how-can-i-get-my-certificates-in-pem-format/)