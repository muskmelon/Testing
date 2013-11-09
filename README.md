## IMAPReaderRouterNode
VERSION: 0.0.1

## Gems:
* none

## Brief:
Send one XenoMessage to it's children when start running.

## Config:
```
loop_delay: 0.5
enabled: true
max_msgs: 3
interval: 2.0
msg_data: "hello world"
msg_context: 
  myhash:
    key1: value1
    key2: value2
msg_command: "mycommand"
```