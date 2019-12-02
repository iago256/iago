# José
cat >> zap.py <<EOF
from zapv2 import ZAPv2
from pprint import pprint
import time, sys
if len(sys.argv) is not 2:
    print('Too short arguments.')
    sys.exit(1)
target = sys.argv[1]
zap = ZAPv2(proxies={'http': 'http://localhost:8080'})
zap.urlopen(target)
scanid = zap.spider.scan(target)
time.sleep(2)
while (int(zap.spider.status(scanid)) < 100):
    print('Spider progress %s: ' + zap.spider.status(scanid))
    time.sleep(2)
print('Spider completed')
# Give the passive scanner a chance to finish
time.sleep(5)
print('Scanning target %s' % target)
scanid = zap.ascan.scan(target)
while (int(zap.ascan.status(scanid)) < 100):
    print('Scan progress %: ' + zap.ascan.status(scanid))
    time.sleep(5)
print('Scan completed')
# Report the results
print('Hosts: ' + ', '.join(zap.core.hosts))
pprint (zap.core.alerts())
html = zap.core.htmlreport()
with open('report_file.html', 'a') as f:
    f.write(html)
EOF
