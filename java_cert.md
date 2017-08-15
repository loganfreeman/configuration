[PKIX path building failed](http://magicmonster.com/kb/prg/java/ssl/pkix_path_building_failed.html)


```shell
keytool -list -v -keystore /path/to/cacerts  > java_cacerts.txt
keytool -import -alias example -keystore  /path/to/cacerts -file example.der
```

n your browser, go to the HTTPS URL that Java could not access. Click on the HTTPS certificate chain (there is lock icon in the Internet Explorer, or the domain name left of the URL in firefox) and navigate the certificate hierarchy. At the top there should be a Primary Root CA. This could be missing from your java cacerts file. Note down the Issuer and Serial Number.

To verify the root certificates, determine where the cacerts file is located. By default it is in jre/lib/security/cacerts. The default password for this keystore is `changeit`.

