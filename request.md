urllib2
---
```py
def _get(self, url):
	request = urllib2.Request(url = url)
	response = urllib2.urlopen(request)
	data = response.read()
	return data

def _post(self, url, params, jsonfmt = True):
	if jsonfmt:
		request = urllib2.Request(url = url, data = json.dumps(params))
		request.add_header('ContentType', 'application/json; charset=UTF-8')
	else:
		request = urllib2.Request(url = url, data = urllib.urlencode(params))
	response = urllib2.urlopen(request)
	data = response.read()
	if jsonfmt: return json.loads(data, object_hook=_decode_dict)
	return data
		
def _decode_list(data):
	rv = []
	for item in data:
		if isinstance(item, unicode):
			item = item.encode('utf-8')
		elif isinstance(item, list):
			item = _decode_list(item)
		elif isinstance(item, dict):
			item = _decode_dict(item)
		rv.append(item)
	return rv

def _decode_dict(data):
	rv = {}
	for key, value in data.iteritems():
		if isinstance(key, unicode):
			key = key.encode('utf-8')
		if isinstance(value, unicode):
			value = value.encode('utf-8')
		elif isinstance(value, list):
			value = _decode_list(value)
		elif isinstance(value, dict):
			value = _decode_dict(value)
		rv[key] = value
	return rv
```
