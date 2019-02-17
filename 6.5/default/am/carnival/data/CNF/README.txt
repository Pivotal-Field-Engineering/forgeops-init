#Build Configuration Store Steps:

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
                --hostname "dir-cnf.sb.gxicloud.com" \
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

4. Run the following LDIFs (in order)
/opendj/bin/ldapmodify -c -a -h localhost -p 1389 -D "cn=directory manager" -w am4rgeR0ck -f opendj_config_schema.ldif
/opendj/bin/ldapmodify -c -a -h localhost -p 1389 -D "cn=directory manager" -w am4rgeR0ck -f opendj_user_schema.ldif
/opendj/bin/ldapmodify -c -a -h localhost -p 1389 -D "cn=directory manager" -w am4rgeR0ck -f add-config-entries.ldif
/opendj/bin/ldapmodify -c -a -h localhost -p 1389 -D "cn=directory manager" -w am4rgeR0ck -f opendj_dashboard.ldif
/opendj/bin/ldapmodify -c -a -h localhost -p 1389 -D "cn=directory manager" -w am4rgeR0ck -f opendj_kba.ldif
/opendj/bin/ldapmodify -c -a -h localhost -p 1389 -D "cn=directory manager" -w am4rgeR0ck -f opendj_user_schema.ldif
/opendj/bin/ldapmodify -c -a -h localhost -p 1389 -D "cn=directory manager" -w am4rgeR0ck -f opendj_deviceprint.ldif
/opendj/bin/ldapmodify -c -a -h localhost -p 1389 -D "cn=directory manager" -w am4rgeR0ck -f opendj_userinit.ldif
/opendj/bin/ldapmodify -c -a -h localhost -p 1389 -D "cn=directory manager" -w am4rgeR0ck -f opendj_user_index.ldif

5. Create backend Index
./dsconfig create-backend-index --port 4444 --hostname localhost --bindDN "cn=directory manager" --bindPassword am4rgeR0ck --backend-name userRoot --index-name "sunxmlkeyvalue" --set "index-type:equality" --set "index-type:substring" --trustAll --no-prompt
   
./dsconfig create-backend-index --port 4444 --hostname localhost --bindDN "cn=directory manager" --bindPassword am4rgeR0ck --backend-name userRoot --index-name "iplanet-am-user-federation-info-key" --set "index-type:equality" --trustAll --no-prompt 

./dsconfig create-backend-index --port 4444 --hostname localhost --bindDN "cn=directory manager" --bindPassword am4rgeR0ck --backend-name userRoot --index-name "sun-fm-saml2-nameid-infokey" --set "index-type:equality" --trustAll --no-prompt

6. Rebuild Index
./rebuild-index --port 4444 --bindDN "cn=directory manager" --bindPassword am4rgeR0ck --baseDN "dc=ocean,dc=com" --rebuildALL --start 0 --trustAll
./stop-ds
./start-ds
