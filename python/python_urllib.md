# python delete
```python
from urllib import urlencode
from urllib2 import urlopen
from urllib2 import urlopen,Request
url='http://alu:8080/wm/staticflowpusher/json'
data={"switch": "00:01:8c:90:d3:4c:14:01", 
"name":"100.2.2.2_100.10.0.2", 
"cookie":"0", 
"active":"true"}
import json
data=json.dumps(data)
r=Request(url,data)
r.get_method=lambda:'DELETE'
res=urlopen(r)
result=res.read()
```
关键就是 
r.get_method=lambda:"DELETE"