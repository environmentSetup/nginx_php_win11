#### 全篇參考(https://codefaq.org/server/how-to-install-nginx-php-mysql-on-windows-10/)

# setup
1. 創立一個 ``webenvironment`` 資料夾
2. 裡面創 `nginx`,`php`,`mysql` 
3. 各自下載完，之後將資料夾放入
- [ ] nginx https://nginx.org/en/download.html
- [ ] php https://windows.php.net/download/
![Alt text](image.png)
- [ ] mysql https://dev.mysql.com/downloads/mysql/
![Alt text](image-1.png)
## 更改系統變量
- [ ] 設定/裝置規格-進階系統設定/進階-環境變量
- [ ] 進webenvironment 找所有環境變量(**#** **可執行檔存放的上一層資料夾名稱**)
![Alt text](image-2.png)
### php
- [ ] 找尋 php.ini-production 改成 php.ini
- [ ] 修改php.ini 找尋   ``extension=/path/to/extension/mysqli.so`` 將其 取消註解
- [ ] 找尋 ``;extension_dir = "ext"`` => ``extension_dir = "F:\webenvironment\php\ext\"``
### mysql
- [ ] 設定完環境變量以後
```
mysqld -u root --initialize-insecure 
mysqld.exe -u root --console
```
自此 就會有mysql/data 資料夾 mysqld -v 就不會報錯了
### 檢查
- [ ] nginx -v **這個剛開始會有錯誤正常**
- [ ] mysqld -v
- [ ] php -v
### 建立batch 快速建置``mysql``,``php``,``nginx 環境``
建立myserver.bat(**# 放在nginx 環境變量資料夾中**)
注意路徑請變更成您的路徑
```
@echo off
F:
cd \webenvironment\nginx\nginx-1.24.0


IF "%1" == "stop" (
	GOTO STOPSERVER
)else IF "%1" == "start" (
	GOTO STARTSERVER
)else (
	echo Use these commands:
	echo.
	echo myserver start
	echo myserver stop
)
GOTO END

:STARTSERVER
QPROCESS * | find /I /N "mysqld.exe">NUL
IF "%ERRORLEVEL%"=="0" (
	echo MYSQLD is already running.
)else (
	RunHiddenConsole.exe mysqld --console
	echo MYSQLD is now running.
)

QPROCESS * | find /I /N "nginx.exe">NUL
IF "%ERRORLEVEL%"=="0" (
	echo NGINX is already running.
)else (
	RunHiddenConsole.exe nginx
	echo NGINX is now running.
)

QPROCESS * | find /I /N "php-cgi.exe">NUL
IF "%ERRORLEVEL%"=="0" (
	echo PHP-CGI is already running.
)else (
	RunHiddenConsole.exe php-cgi -b 127.0.0.1:9000
	echo PHP-CGI is now running.
)

echo.
echo To stop, type "myserver stop"

GOTO END

:STOPSERVER

QPROCESS * | find /I /N "mysqld.exe">NUL
IF "%ERRORLEVEL%"=="0" (
	taskkill /F /IM mysqld.exe>NUL
	echo MYSQLD ended successfully.
)else (
	echo MYSQLD is not running
)

QPROCESS * | find /I /N "nginx.exe">NUL
IF "%ERRORLEVEL%"=="0" (
	::nginx -s stop
	taskkill /F /IM nginx.exe>NUL
	echo NGINX ended successfully.
)else (
	echo NGINX is not running
)

QPROCESS * | find /I /N "php-cgi.exe">NUL
IF "%ERRORLEVEL%"=="0" (
	taskkill /F /IM php-cgi.exe>NUL
	echo PHP-CGI ended successfully.
)else (
	echo PHP-CGI is not running
)

:END
```
RunHiddenConsole(https://redmine.lighttpd.net/attachments/660/RunHiddenConsole.zip)
也放在nginx 資料夾中
# ${workDirectory}/conf/nginx.conf 配置(#詳情見nginx-server repository)


