.. index::
    single: Block; Creating new block
    single: Cookbook; Creating new block

Creating custom Block
=====================

This tutorial will help you to create simple block with manageable HTML content.

.. note::

    This tutorial assumes that you've already read the section about Blocks and Editables.

.. note::

    This tutorial assumes that you've already created your sample CMS package.

1. Create configuration for your block
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Create a class, that would represent your block configuration.
It should extend abstract *BlockConfig* class.

.. code-block:: php
    :linenos:

    <?php

    namespace MySamplePackage\Blocks;

    use Supra\Package\Cms\Pages\Block\Config\BlockConfig;

    class MyTextBlock extends BlockConfig
    {
    }


Override **configureAttributes** method, to setup block title and description.

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


2. Create block template
~~~~~~~~~~~~~~~~~~~~~~~~

Create Twig file named **my_text_block.html.twig** in **Resources/view/blocks/** directory with the following content.

.. code-block:: html
    :linenos:

    <div>{{ property('my_content', 'html') }}</div>

This will create property *'my_content'* with HTML editable.

.. note::

    You may override template name by calling *$mapper->template('MyPackage:path/to/file.html.twig')* inside *BlockConfig::configureAttributes()* method.

3. Register block in CMS
~~~~~~~~~~~~~~~~~~~~~~~~

The last step to do, is to add newly created block into CMS.

If your package extends AbstractSupraCmsPackage, then just override getBlocks method of it:

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

Otherwise, this can be done by calling *BlockCollection::addConfig()* on package initialization finish.

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


Now your block should appear in block menu.