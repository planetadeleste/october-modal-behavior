# October CMS - Modal Behavior #
Modal behavior for [OctoberCMS](http://octobercms.com/).

## Installation ##
Download or clone the repository inside a `/plugins/author/plugin/behaviors/` folder.

## Usage ##
Suppose your plugin name is `Acme\Foo`
In your `controller` class, add the `$implement` item.

```php
<?php namespace Acme\Foo\Controllers;

use Backend\Classes\Controller;

class Categories extends Controller
{
  public $implement = [
    'Backend.Behaviors.FormController',
    'Backend.Behaviors.ListController',
    'Acme.Foo.Behaviors.ModalController'
  ];      
}
```

Edit  the `_list_toolbar.htm` file adding the `onclick` event

```html
<div data-control="toolbar">
    <a
        href="<?= Backend::url('acme/foo/categories/create') ?>"
        onclick="return $.manageModal.createRecord()"
        class="btn btn-primary oc-icon-plus">
        <?= e(trans('acme.foo::lang.category.new')) ?>
    </a>
</div>
```

Edit the `config_list.yaml` file to change `recordUrl` to `recordOnClick`

```yaml
# Link URL for each record
# recordUrl: acme/foo/categories/update/:id
recordOnClick: $.manageModal.clickRecord(:id)
```

### Results ###
![enter image description here](https://box.everhelper.me/attachment/330338/r7lzpnvLFnkALhTZaxr2E4PptDrX4Yrf/397815-k0ZW3uT5vIz6QCaa/screen.png%20OctoberModal%20Behavior)


----------

## Override methods ##

#### overrideAddAssets ####
Overwrite the load asset method. If you override the asset, remember to use your JS function name in the `onclick` event

```php
public function overrideAddAssets()
{
    $this->addJs('your\modal.js');
    $this->addCss('your\modal.css');
}
```

#### overrideOnCreateFormPartial ####
Use your own partial `_create_form.htm` in the create form.
Default used:
```php
<?= Form::open(['id' => 'createForm']) ?>

<div class="modal-header">
    <button type="button" class="close" data-dismiss="popup">&times;</button>
    <h4 class="modal-title"><?= $title ?></h4>
</div>

<?php if (!$this->fatalError): ?>

<div class="modal-body">
    <?= $this->formRender() ?>
</div>
<div class="modal-footer">
    <button
            type="submit"
            data-request="onCreate"
            data-request-data="redirect:0"
            data-hotkey="ctrl+s"
            data-hotkey-mac="cmd+s"
            data-popup-load-indicator
            class="btn btn-primary">
        <i class="fa fa-save"></i> <?= e(trans('backend::lang.form.create')) ?>
    </button>

    <button
            type="button"
            class="btn btn-default"
            data-dismiss="popup">
        <i class="fa fa-times"></i> <?= e(trans('backend::lang.form.cancel')) ?>
    </button>
</div>

<?php else: ?>

<div class="modal-body">
    <p class="flash-message static error"><?= e($this->fatalError) ?></p>
</div>
<div class="modal-footer">
    <button
            type="button"
            class="btn btn-default"
            data-dismiss="popup">
        <i class="fa fa-times"></i> <?= e(trans('backend::lang.form.close')) ?>
    </button>
</div>

<?php endif ?>

<script>
    setTimeout(
            function () {
                $('#createForm input.form-control[type="text"]')[0].focus()
            },
            500
    )
</script>

<?= Form::close() ?>
```

#### overrideOnUpdateFormPartial ####
Use your own partial `_update_form.htm` in the update form.
Default used:
```php
<?= Form::open(['id' => 'updateForm']) ?>

<input type="hidden" name="record_id" value="<?= $recordId ?>"/>

<div class="modal-header">
    <button type="button" class="close" data-dismiss="popup">&times;</button>
    <h4 class="modal-title"><?= $title ?></h4>
</div>

<?php if (!$this->fatalError): ?>

<div class="modal-body">
    <?= $this->formRender() ?>
</div>
<div class="modal-footer">
    <button
            type="submit"
            data-request="onUpdate"
            data-request-data="redirect:0"
            data-hotkey="ctrl+s"
            data-hotkey-mac="cmd+s"
            data-popup-load-indicator
            class="btn btn-primary">
        <i class="fa fa-save"></i> <?= e(trans('backend::lang.form.save')) ?>
    </button>

    <button
            type="button"
            class="btn btn-default"
            data-dismiss="popup">
        <i class="fa fa-times"></i> <?= e(trans('backend::lang.form.cancel')) ?>
    </button>
</div>

<?php else: ?>

<div class="modal-body">
    <p class="flash-message static error"><?= e($this->fatalError) ?></p>
</div>
<div class="modal-footer">
    <button
            type="button"
            class="btn btn-default"
            data-dismiss="popup">
        <i class="fa fa-times"></i> <?= e(trans('backend::lang.form.close')) ?>
    </button>
</div>

<?php endif ?>

<script>
    setTimeout(
            function () {
                $('#updateForm input.form-control[type="text"]')[0].focus()
            },
            500
    )
</script>

<?= Form::close() ?>
```

#### overrideGetTransString ###
This method is used to get the translated title in modal window.
The method return the string like this `{author}.{controller}::lang.{controller_singular}.update_title`.

Default method:
```php
        /**
         * @param null|string $trans
         * @return string
         */
        protected function getTransString( $trans = null ) {
            if(method_exists($this->controller, 'overrideGetTransString')){
                return $this->controller->overrideGetTransString($trans);
            }

            list($author, $plugin, $page, $controller) = explode('\\', get_class($this->controller) );
            $string = strtolower(join('.', [$author, $plugin]) . '::lang.' . strtolower( str_singular($controller) ));

            if(is_string($trans)) {
                $string .= '.' . trim(strtolower($trans));
            }

            return $string;
        }
```
