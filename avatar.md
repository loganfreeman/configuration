In your model:
```php
use Codesleeve\Stapler\ORM\StaplerableInterface;
use Codesleeve\Stapler\ORM\EloquentTrait;

class User extends Eloquent implements StaplerableInterface {
    use EloquentTrait;

    // Add the 'avatar' attachment to the fillable array so that it's mass-assignable on this model.
    protected $fillable = ['avatar', 'first_name', 'last_name'];

    public function __construct(array $attributes = array()) {
        $this->hasAttachedFile('avatar', [
            'styles' => [
                'medium' => '300x300',
                'thumb' => '100x100'
            ]
        ]);

        parent::__construct($attributes);
    }
}
```
In your new view:
```php
<?= Form::open(['url' => action('UsersController@store'), 'method' => 'POST', 'files' => true]) ?>
    <?= Form::input('first_name') ?>
    <?= Form::input('last_name') ?>
    <?= Form::file('avatar') ?>
    <?= Form::submit('save') ?>
<?= Form::close() ?>
```
In your show view:
```php
<img src="<?= $user->avatar->url() ?>" >
<img src="<?= $user->avatar->url('medium') ?>" >
<img src="<?= $user->avatar->url('thumb') ?>" >
```
