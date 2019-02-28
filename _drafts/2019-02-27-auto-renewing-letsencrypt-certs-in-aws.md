---
layout: post
title: "Auto-renewing Let's Encrypt SSL Certificates in AWS"
tagline: ""
categories: aws, ssl certificates, let's encrypt
author: "Kylie Hutchison"
---

# Auto-Renewing Let's Encrypt SSL Certificates in AWS

As a white label mortgage app company, we have a lot of custom domains set up in AWS Route 53, and each one needs separate SSL cert. We generate these using [Let's Encrypt](https://letsencrypt.org/) on a server, upload them to AWS ACM, and then attach them to a load balancer. Once that is done, they are all set. That is, for three months. After that, the certs expire. Then what?

We run a cronjob every night on our server that runs “certbot-auto renew --no-bootstrap”. If no certs are expiring, it does nothing, but if there is a certificate that is within 30 days of expiring, Let's Encrypt will go ahead and renew it on our server. What we needed was a way to have that automatically uploaded to AWS and attached to the correct Load Balancer (we have multiple because each load balancer can only have 25 certificates attached).

We did this using a bash script in the renewal-hooks/deploy directory within our Let's Encrypt directory. Any script in this folder is automatically run on a successful certificate renewal.

Our script:
```
#!/bin/bash

function jsonval {
  temp=`echo $json | sed 's/\\\\\//\//g' | sed 's/[{}]//g' | awk -v k="text" '{n=split($0,a,","); for (i=1; i<=n; i++) print a[i]}' | sed 's/\"\:\"/\|/g' | sed 's/[\,]/ /g' | sed 's/\"//g' | grep -w $prop`
  echo ${temp##*|}
}

function getIndex {
  for i in "${!domList[@]}"; do
   if [[ "${domList[$i]}" = "$1" ]]; then
       echo "${i}";
   fi
 done

}

json=$(aws acm list-certificates)

prop='CertificateArn'
certArns=$(jsonval | sed 's/CertificateArn: //g' | sed 's/CertificateSummaryList: //g' | sed 's/\[ //g')
arnList=($certArns)

prop='DomainName'
domNames=`jsonval | sed 's/DomainName: //g' | sed 's/]//g'`
domList=($domNames)
for domain in ${RENEWED_DOMAINS[*]}; do
  ind=`getIndex $domain`
  arn=${arnList[ind]}

  aws acm import-certificate --certificate-arn $arn --certificate file:///etc/letsencrypt/live/$domain/cert.pem --private-key file:///etc/letsencrypt/live/$domain/privkey.pem --certificate-chain file:///etc/letsencrypt/live/$domain/fullchain.pem
done
```

The basic gist of the script is that we pull down all the certificates we have uploaded to AWS using `aws acm list-certificates`, then we parse through it to find the ARN of the current certificate we are going to be renewing. Since this is bash and JSONs don’t really work, we just turned the domains into one list and the indexes into another. We run a function to find the index of the domain that matches the renewed one. The index for the matching domain is the same as the index of the associated ARN that we upload the new certificate to. From there, we just had to run `aws acm import-certificate` passing in the ARN and certificate files. Because we are just replacing the cert via the ARN, we don’t have to worry about any of the configuration getting messed up. It is automatically attached to the same load balancer/listener as the previous cert was.

Important things to note:

1. The jsonval function is one I found off of stack overflow, and not one that I came up with. It's job is to separate the JSON into a list of a certain element it contained. I ran it twice, once to get all the domain names of the certificates, and another time to get all the ARNs of the certificates. Because they were both run on the same JSON, we could be sure that the indexes were the same for the domain and ARN from the same certificate.  It left the headers on, which I didn't want, so I used `sed` to remove anything that wasn't a value.  Bash is probably the worst kind of language to use for parsing a JSON, but this is what Let's Encrypt required and made using AWS CLI possible, so we made it work.
2. The server needed to be set up beforehand to have AWS CLI work. This is how we pull the list of current certificates down and upload the one we renewed.
3. RENEWED_DOMAINS is something that Let's Encrypt passes into the script when it runs and not something we had to worry about figuring out. We loop through it

Anyways, this was our solution to the problem. It’s probably not the prettiest, but it was quick to implement and has been very effective for our purposes. For the first little while, I monitored it to make sure everything was working correctly, but now we don't even do that. We've been auto-renewing our certs successfully for over 6 months now, and it has been really nice not needing to worry about it.
