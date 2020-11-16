# 22 - SSH

SSH is not typically vulnerable but it often a candidate for password reuse attacks.  When enumerating you should check for:

* Version info \(banner grab\)
* Anonymous logins - rare but worth a shot

```text
ssh 10.10.10.5
```
