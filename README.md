# SubdomainEnumeration
All about subdomain enumeration

## Useful sources
* [Jason Haddix The Bug Hunters Methodology v3(ish)](https://docs.google.com/presentation/d/1R-3eqlt31sL7_rj2f1_vGEqqb7hcx4vxX_L7E23lJVo/edit?usp=sharing)
* [Esoteric sub-domain enumeration techniques - Bharath, from Bugcrowd's LevelUp 2017](https://www.youtube.com/watch?v=e_Gq99CKAys )
* https://www.huque.com/talks/2013-11-dnssec-tutorial-huque.pdf
* https://blog.sweepatic.com/art-of-subdomain-enumeration/
* https://appsecco.com/books/subdomain-enumeration/
* https://pentester.land/cheatsheets/2018/11/14/subdomains-enumeration-cheatsheet.html

## Scrapping

### Certificate Transparency Databases
```
https://crt.sh/?q=%25.[fqdn]
https://censys.io/certificates?q=[fqdn]
https://transparencyreport.google.com/https/certificates
https://developers.facebook.com/tools/ct/
https://www.entrust.com/ct-search/?domain=[fqdn]&includeExpired=true&exactMatch=false
```
### Search Engines

#### Google:
```
site:*.[fqdn]
https://developers.google.com/custom-search/json-api/v1/overview
https://developers.google.com/custom-search/json-api/v1/reference/cse/list
https://console.developers.google.com/apis/credentials //Create Api Key
https://cse.google.com/cse/ //Create cx
curl https://www.googleapis.com/customsearch/v1?key=[GoogleApiKey]&cx=[cx]&fields=items(link)&q=inurl:[fqdn]&fields=queries,items(displayLink)
```

#### Bing:
```
site:.[fqdn]
https://docs.microsoft.com/uk-ua/rest/api/cognitiveservices/bing-web-api-v7-reference
https://msdn.microsoft.com/library/ff795620.aspx //query lang
https://azure.microsoft.com/en-us/try/cognitive-services/#search
https://docs.microsoft.com/en-us/azure/cognitive-services/bing-web-search/
curl 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=site:[fqdn]&count=50&fields=_type' -H 'Ocp-Apim-Subscription-Key:[Your key]'
```

### DNS Aggregators
```
https://www.virustotal.com/#/domain/[fqdn]
https://www.virustotal.com/vtapi/v2/domain/report?apikey=[apiKey]&domain=[fqdn] //API
https://dnsdumpster.com/
https://searchdns.netcraft.com/?restriction=site+contains&host=*.[fqdn]&lookup=wait..&position=limited
http://ptrarchive.com/tools/search3.htm?label=[fqdn]&date=ALL
https://www.threatcrowd.org/searchApi/v2/domain/report/?domain=[fqdn]
https://www.threatminer.org/domain.php?q=[fqdn]&api=True&rt=5
https://findsubdomains.com/subdomains-of/[fqdn]
https://securitytrails.com/list/apex_domain/[fqdn]
https://api.securitytrails.com/v1/domain/[fqdn]/subdomains" -H 'apikey: [apikey]' //API

```

### Zone Transfer
https://www.acunetix.com/blog/articles/dns-zone-transfers-axfr/
```bash
dig +short ns zonetransfer.me
dig axfr zonetransfer.me @nsztm1.digi.ninja.
```

### DNSSEC Zone walking

#### NSEC
##### Tools
```
sudo apt-get install ldns-utils
sudo yum install ldns (sudo yum install epel-release -y)

ldns-walk @name_server domain_name
```
##### Manual
```bash
dig nsec [fqdn] +short
fqdn=[fqdn]; x=$fqdn; while [[ $x != "$1." ]];do x=$(dig nsec $x +short | cut -d' ' -f1); [[ $x = *'root-servers.net.'* ]] && break; echo $x; done
```
```
#!/bin/bash
x=$1
while [[ $x != "$1." ]];do
    x=$(dig nsec $x +short | cut -d' ' -f1)
    [[ $x = *'root-servers.net.'* ]] && exit 0; echo $x
done
```
#### NSEC3
##### Tools
```
# Installing nsec3walker
$ wget https://dnscurve.org/nsec3walker-20101223.tar.gz
$ tar -xzf nsec3walker-20101223.tar.gz
$ cd nsec3walker-20101223
$ make

# Collect NSEC3 hashes of a domain
$ ./collect insecuredns.com > insecuredns.com.collect

# Undo the hashing, expose the sub-domain information.
$ ./unhash < insecuredns.com.collect > insecuredns.com.unhash
```

### Enum Tools
* https://github.com/OWASP/Amass
* https://github.com/subfinder/subfinder
* https://github.com/blechschmidt/massdns
* https://github.com/aboul3la/Sublist3r
* https://github.com/guelfoweb/knock
* https://github.com/OJ/gobuster
* https://github.com/evilsocket/dnssearch
* https://github.com/bbhunter/fierce-domain-scanner
* https://github.com/TheRook/subbrute
