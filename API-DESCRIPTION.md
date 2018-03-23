
# Client/Server comminication via API description


## Overall flow

1. Registration POST request to /application endpoint
2. Get message list GET /message/list
3. Send message POST /message

Communication with serever is basically a combintation of 2 and 3.

Full api documentation is available at https://api.chip.to/docs/

## Device registration:

When Chip application is launched for the first time on user device, application must send POST request to /application endpoint
the response would be JSON object containing token and secret. Any further request to server side must contain header which is generated based on those values. User device is responsible for storing and securing token and secret.

example response:
```
{
  "token" : "5c7ff3eb458a7ca01981ff6d5716003501dac916",
  "secret" : "936d655a55686c49c1c295d911c82f6811980b8f"
}
```


## Message List

To get list of messages GET request should be sent to endpoint /message/list the response would be JSON object containing: 
messages - array of messages, 
status - current user status (logged in, connected, pending save, pending withdraw etc.), 
expectedResponse - command list, keyboard type, commands are visible etc.
config - may contain additional user settings, show menu item, debug, etc. 

example response:
```
{
  "messages" : [
    {
      "sender" : null,
      "id" : "5ab3c955e6b3e80b00002f31",
      "uuid" : null,
      "isIncoming" : false,
      "expectedResponse" : null,
      "body" : [
        {
          "mime" : "text/plain",
          "data" : "Simon",
          "meta" : null
        }
      ],
      "timestamp" : 1521731925.72,
      "liveMode" : false
    },
    {
      "sender" : null,
      "id" : "5ab3c955e6b3e80b00002f32",
      "uuid" : null,
      "isIncoming" : true,
      "expectedResponse" : {
        "postfix" : null,
        "options" : null,
        "hint" : null,
        "type" : null,
        "showOptions" : null,
        "prefix" : null
      },
      "body" : [
        {
          "mime" : "text/plain",
          "data" : "Hi Simon. Nice to meet you 👋",
          "meta" : null
        }
      ],
      "timestamp" : 1521731925.9100001,
      "liveMode" : false
    },
    {
      "sender" : null,
      "id" : "5ab3c955e6b3e80b00002f33",
      "uuid" : null,
      "isIncoming" : true,
      "expectedResponse" : {
        "postfix" : null,
        "options" : [
          [
            {
              "name" : "Nope, not yet"
            },
            {
              "name" : "Ye, sure"
            }
          ]
        ],
        "hint" : null,
        "type" : "options",
        "showOptions" : true,
        "prefix" : null
      },
      "body" : [
        {
          "mime" : "text\/plain",
          "data" : "Can I have your number? I know we just met but I need it to get you set up. 😊",
          "meta" : null
        }
      ],
      "timestamp" : 1521731925.109,
      "liveMode" : false
    }
  ],
  "status" : {
    "balance" : null,
    "interest" : null,
    "accountDeleteMethod" : "chat",
    "connected" : false,
    "canDeleteAccount" : false,
    "pendingSave" : null,
    "pendingWithdraw" : null,
    "isLoggedIn" : false,
    "liveMode" : false,
    "debugMode" : false
  },
  "expectedResponse" : {
    "postfix" : null,
    "options" : [
      [
        {
          "name" : "Nope, not yet"
        },
        {
          "name" : "Ye, sure"
        }
      ]
    ],
    "hint" : null,
    "type" : "options",
    "showOptions" : true,
    "prefix" : null
  },
  "config" : {
    "menus" : [

    ],
    "overdraft" : null
  }
}
```

#### message body may have one of following mime types:
- text/plain, 
- text/html, 
- image/png, 
- image/gif, 
- image/gif, 
- video/mp4, 
- application/command, 
- application/notification (legacy), 
- application/instantPost

#### application/command - is specific message which is not shown to the user but containing instructions for the client to perform some tasks or start some app flow. Currently it can contain one of the following values:
- input_update
- bank_connect_2
- account_create
- bank_connect
- open_overdraft
- enable_notifications


H3 text/html - can have meta object which can contain card type and additionnal info needed to display that card, if client has support for that card it should present one, else fallback to html version. Card Types could be following:
- balance
- save
- save_cancel
- spending
- countdown
- learn_more
- interest_payout
- help
- goal


#### expectedResponse object may have following fields:

- postfix - user message should be postfixed with content of this field
- options - list of commands available to user
- hint -  placeholder for user input
- type - keyboard type (text, phoneNumber, number)
- showOptions - if true commands from options array should be visible to the user
- prefix - user message should be prefixed with content of this field


## Sending messages

To send message to chip bot POST request must be sent to /message endpoint with following payload:
```
{
  "message": {
    "uuid": "507f191e810c19729de860ea",
    "text": "Simon", 
    "mime": "text/plain",
    "isCommand": false
  }
}
```

### Currently user is only able to sent 2 types of messages first is with mime text/plain and text field containing message and second is with mime image/png and text field should contain link to the image
response to this request would be the same as to /message/list described above

## Push notifications
Currently when receiving push notification on ios client we are forcing client to get latest messages from /message/list endpoint


