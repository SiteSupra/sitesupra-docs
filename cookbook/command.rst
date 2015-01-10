.. index::
    single: Command; Writing your own command
    single: Cookbook; Writing your own command

Writing your own command
========================

General considerations
----------------------

Before getting started, you should read `Symfony's guide to creating basic command <http://symfony.com/doc/current/components/console/introduction.html#creating-a-basic-command>`_ - SiteSupra mostly follows the same approach, with minor differences:

* a command must extend Supra\Core\Console\AbstractCommand (or implement Supra\Core\DependencyInjection\ContainerAware, read more about it in :doc:`di_container` section
* there is no limit on where you store your commands or what namespace are you using - SiteSupra does not autoload commands, so you are free to choose your class structure

Summing up, basic console command can look like this;

.. code-block:: php

    <?php

    namespace Some\Your\Namespace\Command;

    use Supra\Core\Console\AbstractCommand;
    use Symfony\Component\Console\Input\InputInterface;
    use Symfony\Component\Console\Output\OutputInterface;

    class FoobarCommand extends AbstractCommand
    {
        protected function configure()
        {
            $this->setName('foobar:do')
                ->setDescription('Does some foobar');
        }

        protected function execute(InputInterface $input, OutputInterface $output)
        {
            //foobar is happening here
        }
    }

Of course, you can use any of the `helpers <http://symfony.com/doc/current/components/console/introduction.html#console-helpers>`_ bundled in Symfony Console - tables, dialogs and so on.

Registering new Command from your Package
-----------------------------------------

Let's assume that you have a Package created; if not, please refer to :doc:`package` first. When done, just register a
new command instance in package's ``inject()`` method:

.. code-block:: php

    <?php

    namespace Vendor\Package;
    use Supra\Core\DependencyInjection\ContainerInterface
    use Some\Your\Namespace\Command\FoobarCommand;
    use Supra\Core\Package\AbstractSupraPackage;

    class FoobarPackage extends AbstractSupraPackage
    {
        public function inject(ContainerInterface $container)
        {
            //some magic here...

            $container->getConsole()->add(new FoobarCommand());

            //even more magic here...
        }
    }

After that, you can run your command with ``supra/cli.php foobar:do`` (shortcuts like ``supra/cli.php f:d``) are also working.
