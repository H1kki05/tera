## Javascript API Implementation

<br>

### Initial Variable Initialization

```js
const api = 'https://teradl-api.dapuntaratya.com';
let mode = 1; // Mode Default = 1
```

<br>

### Make a request to the `get_config` endpoint to get the mode status *(required)*

```js
async function getConfig() {

    const url = `${api}/get_config`;

    const headers = {'Content-Type':'application/json'};
    const data = {
        'method'  : 'GET',
        'mode'    : 'cors',
        'headers' : headers
    };

    const req = await fetch(url, data);
    const response = await req.json();
    return(response.mode);
}
```
- This aims to be an auto switch mode from the client side, because if the admin cookies are invalid, it will automatically switch to mode 1 *(without cookies)*
- Response
    - `{"mode":1}` jika cookie admin invalid
    - `{"mode":2}` jika cookie admin valid
- If you don't do it *(make an auto switcher)*, you will bear the risk of error yourself.

<br>

### **Mode 1** : Dynamic Cookie

#### Make sure the mode first, is free to *initialize* where, whether when the program is *loaded*, or every time you fetch the url

```js
mode = await getConfig(); // If you want auto switch
mode = 1 // If you want manual
```

#### **Requests 1** : Get All File List

```js
const url = 'terabox link you want to search for';

const get_file_url = `${api}/generate_file`;
const headers = {'Content-Type':'application/json'};
const data = {
    'method'  : 'POST',
    'mode'    : 'cors',
    'headers' : headers,
    'body'    : JSON.stringify({'url':url, 'mode':mode})
};

const req = await fetch(get_file_url, data);
const response = await req.json();
```

- Response requests 1
    ```json
    {
        "status": "success",
        "js_token": "js token wes",
        "browser_id": "mbuh pokoke browser id",
        "cookie": "iki cookie ne",
        "sign": "ha iki sign e",
        "timestamp": "This is the timestamp",
        "shareid": "nek jareku iki id file e",
        "uk": "this is your automatic account id",
        "list": [ // Here is the list of files
            {...data_lain, "fs_id":"17428684593055"},
            {...data_lain, "fs_id":"13423453893056"},
            {...data_lain, "fs_id":"19346834756567"},
        ]
    }
    ```
- Mode 1 only generates `fs_id` for each file, so to get the download link you need to send the complete payload on request 2.

#### **Requests 2** : Get URL *(Download & Mirror)* From Each File

```js
const param = {

    // Static, can be used repeatedly as long as the file is in the same response request 1
    'mode' : fashion,
    'uk'        : "uk dari response requests 1",
    'shareid'   : "shareid dari response requests 1",
    'timestamp' : "timestamp dari response requests 1",
    'sign'      : "sign dari response requests 1",
    'js_token'  : "js_token dari response requests 1",
    'cookie'    : "cookie dari response requests 1",

    // Dynamic, each file is different
    'fs_id' : "this is the unique id of each file"
};

const get_link_url = `${api}/generate_link`;
const headers = {'Content-Type':'application/json'};
const data = {
    'method'  : 'POST',
    'mode'    : 'cors',
    'headers' : headers,
    'body'    : JSON.stringify(param)
};

const req = await fetch(get_link_url, data);
const response = await req.json();
```

- Response requests 2
    ```json
    {
        "status": "success",
        "download_link": {
            "url_1": "base url lemot banget tapi kedownload kok",
            "url_2": "mirror url buat streaming",
            "url_3": "mirror url cepet tapi sering error"
        }
    }
    ```

<br>

### **Mode 2** : Static Cookie

#### Make sure the mode first, is free to *initialize* where, whether when the program is *loaded*, or every time you fetch the url

```js
mode = await getConfig(); // If you want auto switch
mode = 2 // If you want manual
```

#### **Requests 1** : Get All File List

```js
const url = 'terabox link you want to search for';

const get_file_url = `${api}/generate_file`;
const headers = {'Content-Type':'application/json'};
const data = {
    'method'  : 'POST',
    'mode'    : 'cors',
    'headers' : headers,
    'body'    : JSON.stringify({'url':url, 'mode':mode})
};

const req = await fetch(get_file_url, data);
const response = await req.json();
```

- Response requests 1
    ```json
    {
        "status": "success",
        "js_token": "js token wes",
        "browser_id": "mbuh pokoke browser id",
        "cookie": "iki cookie ne",
        "sign": "ha iki sign e",
        "timestamp": "This is the timestamp",
        "shareid": "nek jareku iki id file e",
        "uk": "this is your automatic account id",
        "list": [ // Here is the list of files
            {...data_lain, "fs_id":"17428684593055", "link": "https://dm-d.terabox.com/..."},
            {...data_lain, "fs_id":"13423453893056", "link": "https://dm-d.terabox.com/..."},
            {...data_lain, "fs_id":"19346834756567", "link": "https://dm-d.terabox.com/..."},
        ]
    }
    ```
- Mode 2 generates a `link` for each file. This is the base download link *(can be used directly for downloading)*
- If you want to get a mirror link, continue to requests 2 but only by sending the `link`

#### **Requests 2** : Get URL *(Download & Mirror)* From Each File

```js
const param = {

    // Static
    'mode' : fashion,

    // Dynamic, each file is different
    'url' : "pake link dari requests 1 tadi"
};

const get_link_url = `${api}/generate_link`;
const headers = {'Content-Type':'application/json'};
const data = {
    'method'  : 'POST',
    'mode'    : 'cors',
    'headers' : headers,
    'body'    : JSON.stringify(param)
};

const req = await fetch(get_link_url, data);
const response = await req.json();
```

- Response requests 2
    ```json
    {
        "status": "success",
        "download_link": {
            "url_1": "base url lemot banget tapi kedownload kok",
            "url_2": "mirror url buat streaming",
            "url_3": "mirror url cepet tapi sering error"
        }
    }
    ```