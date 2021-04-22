# Docker Box

## Xdebug setup

Get active network`s interface name
```bash
$ ifconfig | pcregrep -M -o '^[^\t:]+:([^\n]|\n\t)*status: active' | egrep -o -m 1 '^[^\t:]+'
```

example: return en0

### MacOs Creating or Adding New Network Alias To a Network Card (NIC)

```bash
$ sudo ifconfig en0 down
$ sudo ifconfig en0 alias 10.254.254.254 255.255.255.0
```

### Linux Creating or Adding New Network Alias To a Network Card (NIC)

```bash
$ sudo ifconfig eno1:0 10.254.254.254 up
```

### Windows

see: https://stackoverflow.com/questions/8944860/how-to-create-an-ip-alias-on-windows