<div align="center">
  <img src="https://raw.githubusercontent.com/YanxinTang/clipboard-online/master/images/clipboard-icon.png" style="display: inline-block; vertical-align: middle;">
  <h1 style="display: inline-block; vertical-align: middle;">clipboard-online</h1>
</div>

![GitHub release (latest by date)](https://img.shields.io/github/v/release/YanxinTang/clipboard-online)

clipboard-online is an application to share cilpboard text between Windows and iOS

## Documentation

【[中文](https://github.com/YanxinTang/clipboard-online/blob/master/README_zh.md)】【[English](https://github.com/YanxinTang/clipboard-online/blob/master/README.md)】

## Download

1. Directly download

    You can download latest release exe from [here](https://github.com/YanxinTang/clipboard-online/releases)

2. From source code(only windows now)

    Before you build, make sure you have installed golang. If not, maybe you need [this](https://golang.org/dl/)

    - `git clone git@github.com:YanxinTang/clipboard-online.git`
    - `cd clipboard-online`
    - `go get github.com/akavel/rsrc`
    - Build
      - Bash: `./build.sh`
      - PowerShell：`.\build.ps1`
    - You can find release bin at `release` directory

## Usage

### For iOS users

1. Run `clipboard-online` on your windows
2. Setup shortcuts on you iPhone/iPad (Open link from safari)
    - Copy:
      - iCloud: [https://www.icloud.com/shortcuts/f463a1e431c94c60b8a5c65305eb819f](https://www.icloud.com/shortcuts/f463a1e431c94c60b8a5c65305eb819f)
      - ![Copy](./images/copy.png)
    - Paste:
      - iCloud: [https://www.icloud.com/shortcuts/90e7a2af70df4707a17dece8c263afc5](https://www.icloud.com/shortcuts/90e7a2af70df4707a17dece8c263afc5)
      - ![Paste](./images/paste.png)
3. Set ip address and authkey (default is empty string). Reference [https://www.kejiwanjia.com/jiaocheng/zheteng/83030.html](https://www.kejiwanjia.com/jiaocheng/zheteng/83030.html)
4. Have fun...😊

### For Android users

1. HTTP Shortcuts: [https://meta.appinn.net/t/topic/20322](https://meta.appinn.net/t/topic/20322)

2. Tasker: [https://github.com/YanxinTang/clipboard-online/issues/26](https://github.com/YanxinTang/clipboard-online/issues/26)

Thanks for the hard working by [@xiozf](https://github.com/xiozf), [@Befod](https://github.com/Befod) and someone else contributed for the Android approach!

## Configuration

`clipboard-online.exe` will create two file which are `config.json` and `log.txt` in the execute path when first running

You can make customization by editing `config.json`

### `config.json`

- `port`
  - type: `string`
  - default: `"8086"`

- `logLevel`
  - type: `string`
  - default: `"warning"`
  - values: `"panic"`, `"fatal"`, `"error"`, `"warning"`, `"info"`, `"debug"`, `"trace"`

- `authkey`
  - type: `string`
  - default: `''`

- `authkeyExpiredTimeout`
  - type: `int64`
  - default: `30`

- `tempDir`
  - type: `string`
  - default: `./temp`

- `reserveHistory`
  - type: `Boolean`
  - default: `false`

- `notify`
  - type: `object`
  - children:
    - `copy`
      - type: `Bollean`
      - default: `false`
    - `paste`
      - type: `Boolean`
      - default: `false`

## API

The default http server will listen `8086` port and you can't chanage that since hardcoded.

### Common headers

#### Required

- `X-API-Version`: indicates version of api

#### Optional

- `X-Client-Name`: indicates name of device
- `X-Auth`: hashed authkey. Value from `md5(config.authkey + timestamp/30)`

### 1. Get windows clipboard

> Request

- URL: `/`
- Method: `GET`

> Reponse

- Body: `json`

```json
// 200 ok

{
  "type": "text",
  "data": "clipboard text on the server"
}

{
  "type": "file",
  "data": [
    {
      "name": "filename",
      "content": "base64 string of file bytes"
    }
    ...
  ]
}

```

### 2. Set windows clipboard

> Request

- URL: `/`
- Method: `POST`
- Headers:
  - `X-Content-Type`: indicates type of request body content
    - `required`
    - values: `text`, `file`, `media`

- Body: `json`

For text:

```json
{
  "data": "text you want to set"
}
```

For file:

```json
{
  "data": [
    {
      "name": "filename",
      "base64": "base64 string of file bytes"
    }
  ]
}
```

> Reponse

Reponse body is empty. If set successfully, status code will be `200`
