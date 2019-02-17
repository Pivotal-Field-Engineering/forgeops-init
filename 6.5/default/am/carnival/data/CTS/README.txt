#Build Session Store Steps:

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
                --hostname "dir-cts.sb.gxicloud.com" \
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
/opendj/bin/ldapmodify -c -a -h localhost -p 1389 -D "cn=directory manager" -w am4rgeR0ck -f basedn.ldif
/opendj/bin/ldapmodify -c -a -h localhost -p 1389 -D "cn=directory manager" -w am4rgeR0ck -f cts-add-schema.ldif
/opendj/bin/ldapmodify -c -a -h localhost -p 1389 -D "cn=directory manager" -w am4rgeR0ck -f add-cts-entries.ldif
/opendj/bin/ldapmodify -c -a -h localhost -p 1389 -D "cn=directory manager" -w am4rgeR0ck -f cts-container.ldif
/opendj/bin/ldapmodify -c -a -h localhost -p 1389 -D "cn=directory manager" -w am4rgeR0ck -f cts-indices.ldif

5. Rebuild Index
./rebuild-index --port 4444 --bindDN "cn=directory manager" --bindPassword am4rgeR0ck --baseDN "dc=ocean,dc=com" --rebuildALL --start 0 --trustAll
./stop-ds
./verify-index --baseDN=dc=ocean,dc=com
./start-ds
