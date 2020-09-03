## Begin by browsing the web, looking for background information
  
    a.	What do they do? Do they have a sales department? Are they hiring?
    
    b. Location information (badge readers, break areas, security, fencing)
  
    c.	Contact information
  
    d.	Phone and fax numbers, emails, titles, pictures
  
    e.	Company structure

## Google hacks

   Limit search to a single domain ```site:Microsoft.com```
    
   Filter specific subdomains ```site:Microsoft.com -site:www.microsoft.com```
    
   filetype, inurl, intitle are also useful
    
   ```Intitle:”netbotz appliance” “OK” -filetype:pdf```

## Google Hacking (GHDB) https://www.exploit-db.com/google-hacking-database

## Email harvesting

  Hunter.IO- Search by domain; free plan includes ~20 searches per month
  
   Theharvester searches search engines for email addresses
    
   ```Theharvester -d cisco.com -b google >google.txt```

## Whois enumeration
  
   Standard: ```whois nintendo.com```
  
   Reverse: ```whois 50.7.67.186```

## OWASP Amass
  
  Full featured reconnaissance tool; go to for many bounty hunters

## Recon-ng - full-featured reconnaissance framework
  
   ```Whois_poc```  - find POC information
  
   ```XSSed``` – find XSS vulnerabilities
  
   Google_site – search for additional subdomains

## DNS enumeration
  
   ```Host -t ns megacorpone.com``` (nameservers)
  
   ```Host -t mx megacorpone.com``` (mail servers)
  
   ```Host www.megacorpone.com``` (web server)

## DNS zone transfers 

   ```Host -l megacorpone.com ns1.megacorpone.com``` <domain name> <dns server>

## Automated tools

   DNSRecon - ```Dnsrecon -d megacorpone.com -t axfr```

   DNSenum - ```Dnsenum zonetransferme.com```