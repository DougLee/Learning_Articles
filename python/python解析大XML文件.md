
## 解析大文件

## 使用iterparse

```python
from lxml import etree


events = ('start', 'end', 'start-ns', 'end-ns')
context = etree.iterparse("test.xml", events=events, html=True)

nsmap = {}
for event, elem in context:
    print(event, elem)
    if event == "start-ns":
        ns, url = elem
        nsmap[ns] = url
    if event == "end":
        if elem.tag == fixtag('', 'record', nsmap):
            print(elem)
            elem.clear()
```

## 