https://duckdns.org/

```
mkdir /opt/duckdns
cd /opt/duckdns
vim duck.sh
echo url="https://www.duckdns.org/update?domains=exampledomain&token=a7c4d0ad-114e-40ef-ba1d-d217904a50f2&ip=" | curl -k -o ~/duckdns/duck.log -K -
chmod 700 duck.sh
crontab -e
*/5 * * * * /opt/duckdns/duck.sh >/dev/null 2>&1
```
