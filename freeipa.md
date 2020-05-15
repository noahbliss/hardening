# FreeIPA 

FreeIPA is a robust albeit somewhat feature-spartan authentication and policy system for Linux.  

At the time of writing, FreeIPA does _not_ offer a means of browsing its LDAP structure through its own implementation, OpenLDAP-compatible tools seem to work without issue. 

## Disable Anonymous LDAP Connections & Require SSL for Credential Transmission:  
You will need to enter your **Directory Manager password** when prompted...  
```
targetserver=ldap1.example.com

cat << EOF > "ldapharden.txt"
dn: cn=config
changetype: modify
replace: nsslapd-allow-anonymous-access
nsslapd-allow-anonymous-access: rootdse
-
replace: nsslapd-minssf
nsslapd-minssf: 56
EOF

# This will prompt for your password.
ldapmodify -x -D "cn=Directory Manager" -H ldaps://"$targetserver" -W -f ldapharden.txt
rm ldapharden.txt
```
If this fails, ensure that you can run a search using ldaps. You may need to update your trusted certificates.  


