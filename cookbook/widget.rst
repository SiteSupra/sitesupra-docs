.. index::
    single: Widget; Creating new widget
    single: Cookbook; Creating new widget

Creating custom Widget
=====================

This tutorial will help you to create simple widget with manageable HTML content.

.. note::

    This tutorial assumes that you've already read the section about :doc:`../docs/widgets_and_editables`.

.. note::

    This tutorial assumes that you've already created your sample :doc:`CMS Package <package>`.

1. Create configuration for your widget
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Create a class, that would represent your widget configuration.
It should extend abstract ``widgetConfig`` class.

.. code-widget:: php
    :linenos:

    <?php

    namespace MySamplePackage\widgets;

    use Supra\Package\Cms\Pages\widget\Config\widgetConfig;

    class MyTextwidget extends widgetConfig
    {
    }


Override ``configureAttributes`` method, to setup widget title and description.

.. code-widget:: php
    :linenos:

    <?php

    namespace MySamplePackage\widgets;

    use Supra\Package\Cms\Pages\widget\Config\widgetConfig;

    class MyTextwidget extends widgetConfig
    {
        public function configureAttributes(AttributeMapper $mapper)
        {
            $mapper->title('My Text widget')
                ->description('This widget provides you WYSIWYG editor.');
        }
    }


2. Create widget template
~~~~~~~~~~~~~~~~~~~~~~~~

Create Twig file named ``my_text_widget.html.twig`` in ``Resources/view/widgets/`` directory with the following content.

.. code-widget:: html
    :linenos:

    <div>{{ property('my_content', 'html') }}</div>

This will create property ``'my_content'`` with HTML editable.

.. note::

    You may override template name by calling ``$mapper->template('MyPackage:path/to/file.html.twig')`` inside ``widgetConfig::configureAttributes()`` method.

3. Register widget in CMS
~~~~~~~~~~~~~~~~~~~~~~~~

The last step to do, is to add newly created widget into CMS.

If your package extends AbstractSupraCmsPackage, then just override getwidgets method of it:

.. code-widget:: php
    :linenos:

    <?php

    namespace MySamplePackage;

    use Supra\Package\Cms\AbstractSupraCmsPackage;

    class MySamplePackage extends AbstractSupraCmsPackage
    {
        ...

        public function getwidgets()
        {
            return array(new widgets\MyTextwidget());
        }
    }

Otherwise, this can be done by calling ``widgetCollection::addConfig()`` on package initialization finish.

.. code-widget:: php
    :linenos:

    <?php

    namespace MySamplePackage;

    use Supra\Core\Package\AbstractSupraPackage;

    class MySamplePackage extends AbstractSupraPackage
    {
        ...

        public function finish(ContainerInterface $container)
        {
            $widgetCollection = $container['cms.pages.widgets.collection'];
            /* @var $widgetCollection \Supra\Package\Cms\Pages\widget\widgetCollection */

            $widgetCollection->addConfig(new MyTextwidget(), $this);
        }
    }


Now your widget should appear in widget menu.