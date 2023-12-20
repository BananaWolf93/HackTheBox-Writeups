# HTB - Support Machine

This is a Microsoft Windows Server.

Step 1: Nmap scan:

![[./_resources/HTB_-_Support_Machine.resources/image.png]]
Step 2:  RPCclient:

![[./_resources/HTB_-_Support_Machine.resources/image.1.png]]

Step 3: SMB Dictionary Attack Attempts with MSF:

* common.txt
* fasttrack.txt
* big.txt
* small.txt

These haven't worked but there are still other wordlists to try out.
![[./_resources/HTB_-_Support_Machine.resources/image.2.png]]

Step 4: Hidden RPC service value from MSF:

I am still not sure how to use these services to my advantage. However, apparently with this exposed service accessible via RPCclient, I can enumerate users.

![[./_resources/HTB_-_Support_Machine.resources/image.3.png]]

Step #5: Successful Finding:

I found a successful password (null) with fasttrack.txt as shown in the below dictionary attack results with msfconsole.:

![[./_resources/HTB_-_Support_Machine.resources/image.4.png]]
I was then able to find the following shares on the machine.:

![[./_resources/HTB_-_Support_Machine.resources/image.5.png]]

Step #6: Downloading Files:

I was able to use mget to download interesting files in the sharename "support-tools" as shown below.:
![[./_resources/HTB_-_Support_Machine.resources/image.6.png]]
Step #7: Enumerate SMB Users:

In this case, I was able to enumerate users via SMB using a tool natively installed in Kali linux called enum4linux. The results of a simple '-U' aka user enumeration scan with this tool are shown below.:
![[./_resources/HTB_-_Support_Machine.resources/image.7.png]]
It appears this machine is also a child Domain Controller (DC).:
![[./_resources/HTB_-_Support_Machine.resources/image.8.png]]
Step #8: Name Discovery:

in using Ghidra for the first time, I was able to locate the strings containing usernames and keys using the Search > Find Strings options in Ghidra for the selected file.

![[./_resources/HTB_-_Support_Machine.resources/image.9.png]]![[./_resources/HTB_-_Support_Machine.resources/image.10.png]]

```
0Nv32PTwgYjzg9/8j5TbmvPd3e7WhtWWyuPsyO76/Y+U193E
```

Next, I need to decode the above string in order to be able to use it for an authentication attempt through ldapsearch.

```
nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz
```

Unfortunately, I still don't know how I could have decoded the first string above to obtain the second one. There is still almost no information regarding how this was done except for a script I found online when google dorking around a bit.

Step #9: Ldapsearch Command:

After finally successfully conducting an ldapsearch command using the above key, I obtained the following information.:

```
ldapsearch -x -H ldap://10.10.11.174 -D 'support\ldap' -w 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz' -b "CN=Users,DC=support,DC=htb"
```

```
givenName: leopoldo
distinguishedName: CN=levine.leopoldo,CN=Users,DC=support,DC=htb
instanceType: 4
whenCreated: 20220528111337.0Z
whenChanged: 20220528111338.0Z
uSNCreated: 12891
uSNChanged: 12906
company: support
streetAddress: Skipper Bowles Dr
name: levine.leopoldo
objectGUID:: zaT1TYtnNUKvrkK/fHjf0Q==
userAccountControl: 66048
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132982100175089241
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAG9v9Y4G6g8nmcEILVwQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: levine.leopoldo
sAMAccountType: 805306368
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=support,DC=htb
dSCorePropagationData: 20220528111337.0Z
dSCorePropagationData: 16010101000000.0Z
mail: levine.leopoldo@support.htb
# raven.clifton, Users, support.htb
dn: CN=raven.clifton,CN=Users,DC=support,DC=htb
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: raven.clifton
sn: raven
c: US
l: Chapel Hill
st: NC
postalCode: 27514
givenName: clifton
distinguishedName: CN=raven.clifton,CN=Users,DC=support,DC=htb
instanceType: 4
whenCreated: 20220528111352.0Z
whenChanged: 20220528111353.0Z
uSNCreated: 12908
uSNChanged: 12923
company: support
streetAddress: Skipper Bowles Dr
name: raven.clifton
objectGUID:: r4Ljo7fDek6FZN1CBI375w==
userAccountControl: 66048
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132982100331339215
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAG9v9Y4G6g8nmcEILWAQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: raven.clifton
sAMAccountType: 805306368
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=support,DC=htb
dSCorePropagationData: 20220528111353.0Z
dSCorePropagationData: 16010101000000.0Z
mail: raven.clifton@support.htb
# bardot.mary, Users, support.htb
dn: CN=bardot.mary,CN=Users,DC=support,DC=htb
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: bardot.mary
sn: bardot
c: US
l: Chapel Hill
st: NC
postalCode: 27514
givenName: mary
distinguishedName: CN=bardot.mary,CN=Users,DC=support,DC=htb
instanceType: 4
whenCreated: 20220528111408.0Z
whenChanged: 20220528111409.0Z
uSNCreated: 12925
uSNChanged: 12940
company: support
streetAddress: Skipper Bowles Dr
name: bardot.mary
objectGUID:: bp+GlFYgwUiy169DiKxEfg==
userAccountControl: 66048
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132982100486339253
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAG9v9Y4G6g8nmcEILWQQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: bardot.mary
sAMAccountType: 805306368
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=support,DC=htb
dSCorePropagationData: 20220528111408.0Z
dSCorePropagationData: 16010101000000.0Z
mail: bardot.mary@support.htb
# cromwell.gerard, Users, support.htb
dn: CN=cromwell.gerard,CN=Users,DC=support,DC=htb
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: cromwell.gerard
sn: cromwell
c: US
l: Chapel Hill
st: NC
postalCode: 27514
givenName: gerard
distinguishedName: CN=cromwell.gerard,CN=Users,DC=support,DC=htb
instanceType: 4
whenCreated: 20220528111424.0Z
whenChanged: 20220528111424.0Z
uSNCreated: 12942
uSNChanged: 12957
company: support
streetAddress: Skipper Bowles Dr
name: cromwell.gerard
objectGUID:: t5fIUmTNZEmsOEoXkg1PfA==
userAccountControl: 66048
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132982100642589204
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAG9v9Y4G6g8nmcEILWgQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: cromwell.gerard
sAMAccountType: 805306368
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=support,DC=htb
dSCorePropagationData: 20220528111424.0Z
dSCorePropagationData: 16010101000000.0Z
mail: cromwell.gerard@support.htb
# monroe.david, Users, support.htb
dn: CN=monroe.david,CN=Users,DC=support,DC=htb
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: monroe.david
sn: monroe
c: US
l: Chapel Hill
st: NC
postalCode: 27514
givenName: david
distinguishedName: CN=monroe.david,CN=Users,DC=support,DC=htb
instanceType: 4
whenCreated: 20220528111439.0Z
whenChanged: 20220528111440.0Z
uSNCreated: 12959
uSNChanged: 12974
company: support
streetAddress: Skipper Bowles Dr
name: monroe.david
objectGUID:: BAScccXiIEKhwgp//rBwwA==
userAccountControl: 66048
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132982100797120581
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAG9v9Y4G6g8nmcEILWwQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: monroe.david
sAMAccountType: 805306368
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=support,DC=htb
dSCorePropagationData: 20220528111439.0Z
dSCorePropagationData: 16010101000000.0Z
mail: monroe.david@support.htb
# west.laura, Users, support.htb
dn: CN=west.laura,CN=Users,DC=support,DC=htb
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: west.laura
sn: west
c: US
l: Chapel Hill
st: NC
postalCode: 27514
givenName: laura
distinguishedName: CN=west.laura,CN=Users,DC=support,DC=htb
instanceType: 4
whenCreated: 20220528111455.0Z
whenChanged: 20220528111456.0Z
uSNCreated: 12979
uSNChanged: 12994
company: support
streetAddress: Skipper Bowles Dr
name: west.laura
objectGUID:: bqAMeaq42kGIZbfMnxXxRA==
userAccountControl: 66048
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132982100954464244
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAG9v9Y4G6g8nmcEILXAQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: west.laura
sAMAccountType: 805306368
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=support,DC=htb
dSCorePropagationData: 20220528111455.0Z
dSCorePropagationData: 16010101000000.0Z
mail: west.laura@support.htb
# langley.lucy, Users, support.htb
dn: CN=langley.lucy,CN=Users,DC=support,DC=htb
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: langley.lucy
sn: langley
c: US
l: Chapel Hill
st: NC
postalCode: 27514
givenName: lucy
distinguishedName: CN=langley.lucy,CN=Users,DC=support,DC=htb
instanceType: 4
whenCreated: 20220528111510.0Z
whenChanged: 20220528111511.0Z
uSNCreated: 12996
uSNChanged: 13011
company: support
streetAddress: Skipper Bowles Dr
name: langley.lucy
objectGUID:: T9fnf6QIlE2uz+4YhFZ3aw==
userAccountControl: 66048
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132982101109308007
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAG9v9Y4G6g8nmcEILXQQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: langley.lucy
sAMAccountType: 805306368
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=support,DC=htb
dSCorePropagationData: 20220528111511.0Z
dSCorePropagationData: 16010101000000.0Z
mail: langley.lucy@support.htb
# daughtler.mabel, Users, support.htb
dn: CN=daughtler.mabel,CN=Users,DC=support,DC=htb
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: daughtler.mabel
sn: daughtler
c: US
l: Chapel Hill
st: NC
postalCode: 27514
givenName: mabel
distinguishedName: CN=daughtler.mabel,CN=Users,DC=support,DC=htb
instanceType: 4
whenCreated: 20220528111526.0Z
whenChanged: 20220528111527.0Z
uSNCreated: 13013
uSNChanged: 13028
company: support
streetAddress: Skipper Bowles Dr
name: daughtler.mabel
objectGUID:: iWH2yMa7h0e1dPAKT9MtgA==
userAccountControl: 66048
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132982101262745576
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAG9v9Y4G6g8nmcEILXgQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: daughtler.mabel
sAMAccountType: 805306368
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=support,DC=htb
dSCorePropagationData: 20220528111526.0Z
dSCorePropagationData: 16010101000000.0Z
mail: daughtler.mabel@support.htb
# stoll.rachelle, Users, support.htb
dn: CN=stoll.rachelle,CN=Users,DC=support,DC=htb
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: stoll.rachelle
sn: stoll
c: US
l: Chapel Hill
st: NC
postalCode: 27514
givenName: rachelle
distinguishedName: CN=stoll.rachelle,CN=Users,DC=support,DC=htb
instanceType: 4
whenCreated: 20220528111542.0Z
whenChanged: 20220528111543.0Z
uSNCreated: 13030
uSNChanged: 13045
company: support
streetAddress: Skipper Bowles Dr
name: stoll.rachelle
objectGUID:: Oe9hWbyotkWg+Aty/bcKYw==
userAccountControl: 66048
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132982101422902140
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAG9v9Y4G6g8nmcEILXwQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: stoll.rachelle
sAMAccountType: 805306368
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=support,DC=htb
dSCorePropagationData: 20220528111542.0Z
dSCorePropagationData: 16010101000000.0Z
mail: stoll.rachelle@support.htb
# ford.victoria, Users, support.htb
dn: CN=ford.victoria,CN=Users,DC=support,DC=htb
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: ford.victoria
sn: ford
c: US
l: Chapel Hill
st: NC
postalCode: 27514
givenName: victoria
distinguishedName: CN=ford.victoria,CN=Users,DC=support,DC=htb
instanceType: 4
whenCreated: 20220528111557.0Z
whenChanged: 20220528111558.0Z
uSNCreated: 13048
uSNChanged: 13063
company: support
streetAddress: Skipper Bowles Dr
name: ford.victoria
objectGUID:: igFAMPhgAEqMFr/4HUIY5A==
userAccountControl: 66048
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132982101581183009
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAG9v9Y4G6g8nmcEILYAQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: ford.victoria
sAMAccountType: 805306368
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=support,DC=htb
dSCorePropagationData: 20220528111558.0Z
dSCorePropagationData: 16010101000000.0Z
mail: ford.victoria@support.htb
# Administrator, Users, support.htb
dn: CN=Administrator,CN=Users,DC=support,DC=htb
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Administrator
description: Built-in account for administering the computer/domain
distinguishedName: CN=Administrator,CN=Users,DC=support,DC=htb
instanceType: 4
whenCreated: 20220528110156.0Z
whenChanged: 20220815141220.0Z
uSNCreated: 8196
memberOf: CN=Group Policy Creator Owners,CN=Users,DC=support,DC=htb
memberOf: CN=Domain Admins,CN=Users,DC=support,DC=htb
memberOf: CN=Enterprise Admins,CN=Users,DC=support,DC=htb
memberOf: CN=Schema Admins,CN=Users,DC=support,DC=htb
memberOf: CN=Administrators,CN=Builtin,DC=support,DC=htb
uSNChanged: 81954
name: Administrator
objectGUID:: ltGa4T+PO0uTHnjAEEcLlw==
userAccountControl: 512
badPwdCount: 2
codePage: 0
countryCode: 0
badPasswordTime: 133050646215309340
lastLogoff: 0
lastLogon: 133050463576432249
logonHours:: ////////////////////////////
pwdLastSet: 133027269567293588
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAG9v9Y4G6g8nmcEIL9AEAAA==
adminCount: 1
accountExpires: 0
logonCount: 62
sAMAccountName: Administrator
sAMAccountType: 805306368
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=support,DC=htb
isCriticalSystemObject: TRUE
dSCorePropagationData: 20220528111947.0Z
dSCorePropagationData: 20220528111947.0Z
dSCorePropagationData: 20220528110344.0Z
dSCorePropagationData: 16010101181216.0Z
lastLogonTimestamp: 133050463409576933
# Guest, Users, support.htb
dn: CN=Guest,CN=Users,DC=support,DC=htb
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Guest
description: Built-in account for guest access to the computer/domain
distinguishedName: CN=Guest,CN=Users,DC=support,DC=htb
instanceType: 4
whenCreated: 20220528110156.0Z
whenChanged: 20220815141838.0Z
uSNCreated: 8197
memberOf: CN=Guests,CN=Builtin,DC=support,DC=htb
uSNChanged: 81985
name: Guest
objectGUID:: lHQIHI+KY06QsghOU1eULw==
userAccountControl: 66080
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
logonHours:: ////////////////////////////
pwdLastSet: 132982103352120821
primaryGroupID: 514
objectSid:: AQUAAAAAAAUVAAAAG9v9Y4G6g8nmcEIL9QEAAA==
accountExpires: 0
logonCount: 0
sAMAccountName: Guest
sAMAccountType: 805306368
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=support,DC=htb
isCriticalSystemObject: TRUE
dSCorePropagationData: 20220528110344.0Z
dSCorePropagationData: 16010101000001.0Z
lastLogonTimestamp: 133050467185908670
# search result
search: 2
result: 0 Success
# numResponses: 43
# numEntries: 42
```

Step #10: Bloodhound usage:

I need to setup bloodhound and import the bloodhound-python module from GH to excecute the following command.:
```
bloodhound-python -c all -d support.htb -u ldap -p 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz' -ns 10.129.113.225
```

After setting up bloodhound and bloodhound-python, I entered in the above command and after investigating a bit with a hint from a discussion forum, I realized that the user "support@support.htb" has generic login to the DCs and the password is set to never expire either. This was after reading the following hint: "In bloodhound find support user then find group memberships then find outbound control rights for each group."
![[./_resources/HTB_-_Support_Machine.resources/image.12.png]]
Step 11: LDAPSEARCH useage:

I was then able to use the command below to pull and find the password for this user as shown in the next screenshot.:
```
ldapsearch -x -H ldap://10.10.11.174 -D 'support\ldap' -w 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz' -b 'CN=support,CN=Users,DC=support,DC=htb'
```

![[./_resources/HTB_-_Support_Machine.resources/image.11.png]]

The password as shown in the above screenshot is:
```
info: Ironside47pleasure40Watchful
```

Step #12:

I need to following the guide shown in this link : <https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/resource-based-constrained-delegation-ad-computer-object-take-over-and-privilged-code-execution>

For some reason, despite having added the "recon " and "powermad" modules into the default user path for ps modules directory, I am unable to used powerview. I have absolutely no idea why. I see no mention anywhere on why I'm getting so many errors. I can import the module powermad but not powerview.  I've spent another few hours trying to figure this issue out and have not been able to figure it out. I think what I may need to do is remove powermad and powerview and redownload them to ensure I have the latest one. At this point, that is the only thing I haven't tried. This did not work either. I will end this lab attempt and wait until the official writeup comes out.
