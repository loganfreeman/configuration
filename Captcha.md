```php
<?php

require_once('vendor/autoload.php');

use Arcanedev\NoCaptcha\NoCaptcha;

$secret  = 'your-secret-key';
$sitekey = 'your-site-key';
$captcha = new NoCaptcha($secret, $sitekey);

if ( ! empty($_POST)) {
    // You need to check also if the $_POST['g-recaptcha-response'] is not empty.
    $response = $_POST['g-recaptcha-response']; 
    $result   = $captcha->verify($response);

    echo $result === true
        ? 'Yay ! You are a human.'
        : 'No ! You are a robot.';

    exit();
}
?>

<form action="?" method="POST">
    <?php echo $captcha->display(); ?>
    <button type="submit">Submit</button>
</form>

<?php
// At the bottom, before the </body> (If you're a good programmer and you listen to your mother)
echo $captcha->script();
?>
```
