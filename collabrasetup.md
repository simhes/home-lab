# collabra

föra att nå hosten ip på ubuntu
(alltså öppna brand väggen)
sudo ufw allow 9980/tcp

sudo ufw allow 8081/tcp
sudo ufw reload


för nc till collabras : för att bli av mer varning: "You have not configured the allow-list for WOPI requests"
docker exec -u www-data nextcloud php occ config:app:set richdocuments wopi_allowlist --value="nextcloud"


för collabra till nc
docker exec -u www-data nextcloud php occ config:app:set richdocuments wopi_allowlist --value="192.168.0.200"
