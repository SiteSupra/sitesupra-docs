.. index::
    single: Block; Creating new block
    single: Cookbook; Creating new block

Creating Custom Block
=====================

This tutorial will help you to create a simple block with manageable HTML content.

.. note::
    This tutorial assumes that you've already read the section about :doc:`../docs/blocks_and_editables` and already have sample :doc:`CMS Package <package>` configured.

Block Configuration
-------------------

.. TODO it would be goof to give a default folder where to store the class

Create a class, that would represent your block configuration.
It should extend abstract ``BlockConfig`` class:

.. code-block:: php
    :linenos:

    <?php

    namespace MySamplePackage\Blocks;

    use Supra\Package\Cms\Pages\Block\Config\BlockConfig;

    class MyTextBlock extends BlockConfig
    {
    }

Override ``configureAttributes`` method to define block title and description.

.. code-block:: php
    :linenos:

    <?php

    namespace MySamplePackage\Blocks;

    use Supra\Package\Cms\Pages\Block\Config\BlockConfig;

    class MyTextBlock extends BlockConfig
    {
        public function configureAttributes(AttributeMapper $mapper)
        {
            $mapper->title('My Text Block')
                ->description('This block provides you WYSIWYG editor.');
        }
    }


Create Block Template
---------------------

Create Twig file named ``my_text_block.html.twig`` in ``Resources/view/blocks/`` directory with the following code in there:

.. code-block:: html
    :linenos:

    <div>{{ property('my_content', 'html') }}</div>

This will dynamically create block property named ``'my_content'`` and link CMS WYSIWYG editor to that property.

.. note::

    You may override template name by calling ``$mapper->template('MyPackage:path/to/file.html.twig')`` inside ``BlockConfig::configureAttributes()`` method.

Register Your Block in CMS
--------------------------

The last but not least step is register the block with CMS.
If your package extends ``AbstractSupraCmsPackage``, then just override ``getBlocks`` method:

.. code-block:: php
    :linenos:

    <?php

    namespace MySamplePackage;

    use Supra\Package\Cms\AbstractSupraCmsPackage;

    class MySamplePackage extends AbstractSupraCmsPackage
    {
        ...

        public function getBlocks()
        {
            return array(new Blocks\MyTextBlock());
        }
    }

Otherwise, this can be done by calling ``BlockCollection::addConfig()`` on package initialization finish.

.. code-block:: php
    :linenos:

    <?php

    namespace MySamplePackage;

    use Supra\Core\Package\AbstractSupraPackage;

    class MySamplePackage extends AbstractSupraPackage
    {
        ...

        public function finish(ContainerInterface $container)
        {
            $blockCollection = $container['cms.pages.blocks.collection'];
            /* @var $blockCollection \Supra\Package\Cms\Pages\Block\BlockCollection */

            $blockCollection->addConfig(new MyTextBlock(), $this);
        }
    }


That's all. Your block is now registered and should appear in site block list.