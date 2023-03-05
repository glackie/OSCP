>find /  -writable 2>/dev/null | cut -d "/" -f2 | sort -u

>`find / -writable 2>/dev/null | cut -d "/" -f 2,3 | grep -v proc | sort -u`

