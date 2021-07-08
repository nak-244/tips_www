# .htaccessでURLをwww有りに統一するクールな書き方
```
RewriteEngine On
RewriteCond %{HTTP_HOST} !^www\.
RewriteRule ^(.*)$ http://www.%{HTTP_HOST}/$1 [R=301,L]
```
