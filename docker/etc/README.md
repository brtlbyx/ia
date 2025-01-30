# Benutzer- und Gruppenrechte vom Host

Damit die Applicationsdaten korrekt funktionieren, müssen die Benutzer- und Gruppenrechte
aus /etc/passwd und /etc/group angepasst werden.

im Terminal eingeben

```
id
```

Die Ausgabe sollte etwa so aussehen:

```
uid=XXX(username) gid=XX(group) groups=YY(groupy)
```

Benutze die UID und die GID, damit die Benutzerrechte übernommen werden.

