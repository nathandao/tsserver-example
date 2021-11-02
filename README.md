# Example usage of TypeScript's tsserver

A small example of how to communicate with TypeScript's `tsserver`. This is
deliberately hard-coded and very minimal.  The intent is to give you a starting
point so you can begin to understand the communication protocol.

## To run it

First: `npm install`. That will install all packages listed in `package.json`
-- most importantly, the `typescript` package.

Then, to actually run it: `./run.sh`

Basically all that does is `tsserver < tsserver.input`, but with the additional
complication that since `tsserver` requires full paths in the filenames it
receives, we first have to do a bit of preprocessing on `tsserver.input` to put
full paths in there.

`tsserver.input` is a sequence of commands that will be sent to `tsserver`, one
per line (which is the format that tsserver requires).

## What it does

`tsserver.input` sends these commands to `tsserver`:

* An `"open"` command to tell `tsserver` to open `example.ts`. `tsserver` does
  not send back a `"response"` to this command, but this does trigger two
  `"event"`s.
* A `"quickinfo"` command to ask for info about the `console` part of
  `console.log`. `tsserver` sends back a short response. The `"displayString"`
  portion of the response is `"var console: Console"`, which is exactly the
  same thing you would see if you opened example.ts in Visual Studio Code and
  hovered over `console`.

## The output

This is the output, modified so that the JSON is pretty-printed.

Note that when you try it yourself with `./run.sh`, you will see that each
response is preceded by a `Content-Length:` header, followed by `\r\n\r\n`.

In response to the `"open"` command, these two events come back (note, these
are `"type": "event"`, not `"type": "response"` -- there is no actual response
to the `"open"` command):

```json
{
  "seq": 0,
  "type": "event",
  "event": "telemetry",
  "body": {
    "telemetryEventName": "projectInfo",
    "payload": {
      "projectId": "e7543aa21d6fbcd724a9a4c5e881b0828891b4724500633db71f643afadcaf3d",
      "fileStats": {
        "js": 0,
        "jsSize": 0,
        "jsx": 0,
        "jsxSize": 0,
        "ts": 1,
        "tsSize": 110,
        "tsx": 0,
        "tsxSize": 0,
        "dts": 5,
        "dtsSize": 1015410,
        "deferred": 0,
        "deferredSize": 0
      },
      "compilerOptions": {
      },
      "typeAcquisition": {
        "enable": false,
        "include": false,
        "exclude": false
      },
      "extends": false,
      "files": false,
      "include": false,
      "exclude": false,
      "compileOnSave": false,
      "configFileName": "tsconfig.json",
      "projectType": "configured",
      "languageServiceEnabled": true,
      "version": "4.4.4"
    }
  }
}

{
  "seq": 0,
  "type": "event",
  "event": "configFileDiag",
  "body": {
    "triggerFile": "~/tsserver-example/example.ts",
    "configFile": "~/tsserver-example/tsconfig.json",
    "diagnostics": []
  }
}
```

In response to the 2 `"quickinfo"` commands, this response comes back:

```json
{
  "seq": 0,
  "type": "response",
  "command": "quickinfo",
  "request_seq": 1,
  "success": true,
  "body": {
    "kind": "type",
    "kindModifiers": "",
    "start": {
      "line": 1,
      "offset": 6
    },
    "end": {
      "line": 1,
      "offset": 11
    },
    "displayString": "type Props = {\n    itemId: string;\n    isDisabled: boolean;\n}",
    "documentation": "",
    "tags": []
  }
}

{
  "seq": 0,
  "type": "response",
  "command": "quickinfo",
  "request_seq": 2,
  "success": true,
  "body": {
    "kind": "interface",
    "kindModifiers": "",
    "start": {
      "line": 6,
      "offset": 11
    },
    "end": {
      "line": 6,
      "offset": 16
    },
    "displayString": "interface Human",
    "documentation": "",
    "tags": []
  }
}
```
