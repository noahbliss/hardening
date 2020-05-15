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


## Dealing with LDAP  

A cheat sheet on commands that might save you time and scalp-attached-hair.  


As **Directory Manager** dump ldap contents using ldaps on its default port.  
`ldapsearch -H ldaps://dc01.example.com -x -D 'cn=Directory Manager' -b 'dc=example,dc=com' -W`  

[ldapbrowse](https://github.com/david0/ldapbrowse) can be used as well, but does _not_ accept `-W` for interactive password entry. 
Make sure `export HISTCONTROL=ignorespace` is in your .bashrc/.zshrc so passwords are not stored there!  
```
# THE NEXT LINE STARTS WITH A SPACE! THIS IS TO PREVENT STORAGE IN THE HISTORY FILE!
 password=""
./ldapbrowse -H ldaps://dc01.example.com -b dc=example,dc=com -D 'cn=Directory Manager' -w "$password"
```

Or we can do this as a standard user:  
```
username=""
# NOTE THE SPACE STARTING THE NEXT LINE!
 password=""
./ldapbrowse -H ldaps://dc01.example.com -b dc=example,dc=com -D "uid=$username,cn=users,cn=accounts,dc=example,dc=com" -w "$password" 
```
