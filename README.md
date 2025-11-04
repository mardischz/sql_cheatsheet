# 🐬 sql_cheatsheet

### Guía completa para limpiar instalaciones viejas de MySQL, configurar correctamente la versión de Oracle 8.0.43, reparar permisos y verificar la conexión local en macOS.

Ver qué MySQL está tomando tu terminal
```bash
which -a mysql
mysql --version
echo $PATH
```
Si aquí aparece algo de brew (por ejemplo /opt/homebrew/opt/mysql o /usr/local/opt/mysql), sigue con la desinstalación.

Desinstalar el MySQL de Homebrew (para que no estorbe)
```bash
brew list | grep -i mysql || true
brew uninstall --ignore-dependencies mysql mysql@8.0 || true
brew cleanup
brew services cleanup
```
📝 Nota: Si no quieres perder los datos que Homebrew haya creado en su carpeta, NO borres su data-dir.
Si quieres limpiar todo:
```bash
rm -rf "$(brew --prefix)/var/mysql"
```
Poner el MySQL “bueno” (Oracle 8.0.43) en tu PATH de zsh
Añade esto a tu archivo ~/.zshrc:
```bash
echo 'export PATH="/usr/local/mysql/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```
Confirma:
```bash
which mysql
mysql --version
```

Verificar el symlink de /usr/local/bin/mysql
```bash
ls -l /usr/local/bin/mysql
readlink /usr/local/bin/mysql || true
```

Si apunta a /usr/local/mysql/bin/mysql, está perfecto.
Si apunta a algo como /Cellar/mysql/..., elimínalo:
```bash
sudo rm -f /usr/local/bin/mysql
```

Detener/eliminar MySQL de Homebrew (si existiera)
```bash
brew services list
brew services stop mysql || true
brew services stop mysql@8.0 || true
brew list | grep -i mysql || true
brew uninstall --ignore-dependencies mysql mysql@8.0 || true
brew cleanup
brew services cleanup
```

Levantar el servidor de MySQL (Oracle 8.0.43)
```bash
sudo /usr/local/mysql/support-files/mysql.server start
sudo /usr/local/mysql/support-files/mysql.server status
```
Debería mostrar algo como:
### mysql  Ver 8.0.43 for macos15 on x86_64 (MySQL Community Server - GPL)

# Diagnóstico y reparación de errores de arranque
Ver procesos y quién usa el puerto 3306
```bash
pgrep -fal mysqld
lsof -nP -iTCP:3306 | grep LISTEN
```
Ver el final del log de errores
```bash
sudo tail -n 120 /usr/local/mysql/data/*.err
```

Detener cualquier mysqld que siga vivo
```bash
sudo killall mysqld || true
pgrep -fal mysqld
```

### Arreglar permisos y preparar carpeta de socket
```bash
sudo chown -R _mysql:_mysql /usr/local/mysql/data
sudo chmod 750 /usr/local/mysql/data
sudo mkdir -p /var/run/mysql
sudo chown -R _mysql:_mysql /var/run/mysql
```
Limpiar archivos viejos (PID / Socket)
```bash
sudo rm -f /usr/local/mysql/data/*.pid /var/run/mysql/mysql.sock /tmp/mysql.sock
```

Arrancar MySQL y verificar estado
```bash
sudo /usr/local/mysql/support-files/mysql.server start
sudo /usr/local/mysql/support-files/mysql.server status
```
Probar conexión por TCP
```bash
mysql -u root -p -h 127.0.0.1 -P 3306 -e "SELECT VERSION();"
```
Si ves la versión 8.0.43, tu servidor MySQL está funcionando correctamente.






# elviejo
1) Ver qué mysql está tomando tu terminal
```bash
which -a mysql
mysql --version
echo $PATH
```
Si aquí aparece algo de brew (por ejemplo /opt/homebrew/opt/mysql o /usr/local/opt/mysql), seguimos.

Desinstalar el MySQL de Homebrew (para que no estorbe)
```bash
brew list | grep -i mysql || true
brew uninstall --ignore-dependencies mysql mysql@8.0 || true
brew cleanup
brew services cleanup
```
Nota: si no quieres perder los datos que Homebrew haya creado en su carpeta, NO borres su data-dir. Si quieres limpiar todo, sería algo como:

Poner el MySQL “bueno” (Oracle 8.0.43) en tu PATH de zsh

Añade esto a tu ~/.zshrc:
```bash
echo 'export PATH="/usr/local/mysql/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

Confirma:

```bash
which mysql
mysql --version
```
Probablemente es solo un symlink que crea el instalador de Oracle.
```bash
ls -l /usr/local/bin/mysql
readlink /usr/local/bin/mysql || true
```
Si apunta a /usr/local/mysql/bin/mysql, está perfecto.

1) Apagar/eliminar el MySQL de Homebrew (si existiera)
```bash
brew services list
brew services stop mysql || true
brew services stop mysql@8.0 || true
brew list | grep -i mysql || true
brew uninstall --ignore-dependencies mysql mysql@8.0 || true
brew cleanup
brew services cleanup
```

Levantar el servidor de MySQL (Oracle 8.0.43)
```bash
sudo /usr/local/mysql/support-files/mysql.server start
sudo /usr/local/mysql/support-files/mysql.server status
```
Debería decirte algo como mysql Ver 8.0.43 y apuntar a /usr/local/mysql/bin/mysql.


loque me funciono 

Ver procesos y quién usa el puerto 3306
```bash
pgrep -fal mysqld
lsof -nP -iTCP:3306 | grep LISTEN
```
Ver el final del log de errores
```bash
sudo tail -n 120 /usr/local/mysql/data/*.err
```

Detener cualquier mysqld que siga vivo
```bash
sudo killall mysqld || true
pgrep -fal mysqld
```

Arreglar permisos del datadir y preparar carpeta de socket
```bash
sudo chown -R _mysql:_mysql /usr/local/mysql/data
sudo chmod 750 /usr/local/mysql/data
sudo mkdir -p /var/run/mysql
sudo chown -R _mysql:_mysql /var/run/mysql
```

Limpiar archivos viejos (pid/socket)
```bash
sudo rm -f /usr/local/mysql/data/*.pid /var/run/mysql/mysql.sock /tmp/mysql.sock
```

Arrancar MySQL y ver estado
```bash
sudo /usr/local/mysql/support-files/mysql.server start
sudo /usr/local/mysql/support-files/mysql.server status
```

Probar conexión por TCP
```bash
mysql -u root -p -h 127.0.0.1 -P 3306 -e "SELECT VERSION();"
```
