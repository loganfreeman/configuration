gmail
---
```php
return array(
 
    'driver' => 'smtp',
 
    'host' => 'smtp.gmail.com',
 
    'port' => 587,
 
    'from' => array('address' => 'authapp@awesomeauthapp. com', 'name' => 'Awesome Laravel 4 Auth App'),
 
    'encryption' => 'tls',
 
    'username' => 'your_gmail_username',
 
    'password' => 'your_gmail_password',
 
    'sendmail' => '/usr/sbin/sendmail -bs',
 
    'pretend' => false,
 
);
```
