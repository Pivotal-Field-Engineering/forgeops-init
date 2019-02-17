#Build USER Store Steps:

1. Unzip OpenDJ-3.0.0.zip

#Make folder for Java, unzip Java, and set Java_HOME

2. Run Setup Command:
/opendj/setup \
                --cli \
                --ldapPort "1389" \
                --generateSelfSignedCertificate \
                --enableStartTLS \
                --ldapsPort "1636" \
                --adminConnectorPort "4444" \
                --rootUserDN "cn=Directory Manager" \
                --rootUserPassword "am4rgeR0ck" \
                --hostname "dir-usr.sb.gxicloud.com" \
                --acceptLicense \
                --no-prompt \
                --noPropertiesFile

3. Create Backend
/opendj/bin/dsconfig create-backend \
                --backend-name "userRoot" \
                --set base-dn:"dc=ocean,dc=com" \
                --set enabled:true \
                --type je \
                --port "4444" \
                --bindDN "cn=directory manager" \
                --bindPassword "am4rgeR0ck" \
                --trustAll \
                --no-prompt

4. Run the following LDIFs (in this order)
./ldapmodify -D "cn=Directory Manager" -a -c -p 1389 -w am4rgeR0ck -f opendj_user_schema.ldif
./ldapmodify -D "cn=Directory Manager" -a -c -p 1389 -w am4rgeR0ck -f opendj_deviceprint.ldif
./ldapmodify -D "cn=Directory Manager" -a -c -p 1389 -w am4rgeR0ck -f opendj_userinit.ldif
./ldapmodify -D "cn=Directory Manager" -a -c -p 1389 -w am4rgeR0ck -f opendj_dashboard.ldif
./ldapmodify -D "cn=Directory Manager" -a -c -p 1389 -w am4rgeR0ck -f opendj_kba.ldif
./ldapmodify -D "cn=Directory Manager" -a -c -p 1389 -w am4rgeR0ck -f opendj_oathdevices.ldif
./ldapmodify -D "cn=Directory Manager" -a -c -p 1389 -w am4rgeR0ck -f opendj_user_index.ldif
./ldapmodify -D "cn=Directory Manager" -a -c -p 1389 -w am4rgeR0ck -f base.ldif
./ldapmodify -D "cn=Directory Manager" -a -c -p 1389 -w am4rgeR0ck -f updatedschema.ldif
./ldapmodify -D "cn=Directory Manager" -a -c -p 1389 -w am4rgeR0ck -f user_import.ldif

