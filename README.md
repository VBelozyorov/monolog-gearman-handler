# monolog-gearman-handler
Monolog handler for sending logs to Gearman. May be usefull for async recording those logs on another machine.

## Requirements
As you see from package name, this one is useless without [ Gearman PHP extension ](http://php.net/manual/ru/book.gearman.php).
Since Composer cannot install it, you need to do it yourself.

## Install
```shell
composer require vbelozyorov/monolog-gearman-handler
```
This will install this package itself and, [Monolog](https://packagist.org/packages/monolog/monolog), if not yet installed.

## Configure
Because handler constructor requires Gearman client, which in turn requires host and port of Gearman server, you need to be able
to provide all this when logger will initialized.

Here is example for YAML config for Symfony2:
```yml
services:
    gearman.client:
        class: GearmanClient
        calls:
            - [addServer, [%logs.gearman_host%, %logs.gearman_port%]]

    monolog.gearman_handler:
        class: Monolog\Handler\GearmanHandler
        arguments:
            - '@gearman.client'
        calls:
            - [setPrefix, ['project_alias']]

monolog:
    channels: [gearman]
    handlers:
        gearman:
            type:   service
            id:     monolog.gearman_handler
            level:  debug
            channels: [gearman]

        main:
            channels: ['!gearman']
```

In this example separate Monolog channell named `gearman` is registered. And handler `main` using in Symfony app by default
excludes from this new channel. This way event logs like `kernel.debug` won't be published there.

How you get Gearman logger with this config:
```php
$logger = $this->getContainer()->get('monolog.logger.gearman');
```
