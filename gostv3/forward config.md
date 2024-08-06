```json
{
  "services": [
    {
      "name": "forward-service-tcp",
      "addr": ":62714",
      "handler": {
        "type": "tcp"
      },
      "forwarder": {
        "nodes": [
          {
            "name": "target-server-tcp",
            "addr": "163.53.18.104:11091"
          }
        ]
      }
    },
    {
      "name": "forward-service-udp",
      "addr": ":62715",  // 使用不同的端口
      "handler": {
        "type": "udp"
      },
      "forwarder": {
        "nodes": [
          {
            "name": "target-server-udp",
            "addr": "163.53.18.104:11091"
          }
        ]
      }
    }
  ]
}
```

