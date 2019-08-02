# JobBoyBundle

This is part of the JobBoy project [documentation](../README.md)

This is the [JobBoyBundle](https://github.com/danielsan80/jobboy-bundle) documentation.

JobBoyBundle is the Symfony Bundle to integrate and configure JobBoy in your Symfony app.

## Getting started

Add JobBoyBundle (and JobBoy) to your composer.json

```
composer require dansan/jobboy-bundle
```

Register the JobBoyBundle in your Syfmony app.

```php
<?php
# config/bundles.php

return [
    ...
    JobBoy\Bundle\JobBoyBundle\JobBoyBundle::class => ['all' => true],
];
```

Configure the bundle adding `config/packages/job_boy.yaml`. If you don't configure the bundle
 the default configuration is:

```yaml
# config/packages/job_boy.yaml
job_boy:
  process_repository: in_memory
  process_class: JobBoy\Process\Domain\Entity\Process
```

The InMemory ProcessRepository is only for test purposes so it is a bad idea to not configure the
bundle.



### ProcessRepository on Redis

If you want to store the processes on Redis add the
[Redis ProcessRepository](https://github.com/danielsan80/jobboy-processes-redis)
implementation to your composer.json
(*recommended*)

```
composer require dansan/jobboy-processes-redis
```

The configuration is:

```yaml
# config/packages/job_boy.yaml

parameters:
  env(JOBBOY_REDIS_HOST): ''
  env(JOBBOY_REDIS_PORT): ''

job_boy:
  process_repository: redis

  redis:
    host: '%env(resolve:JOBBOY_REDIS_HOST)%'
    port: '%env(resolve:JOBBOY_REDIS_PORT)%'
```

### ProcessRepository on Doctrine

If you want to store the processes on Doctrine (mysql or mariadb) add the
[Doctrine ProcessRepository](https://github.com/danielsan80/jobboy-processes-doctrine)
implementation to your composer.json

```
composer require dansan/jobboy-processes-doctrine
```

The configuration is:

```yaml
# config/packages/job_boy.yaml

job_boy:
  process_repository: doctrine
```

You need to install [Doctrine and DoctrineMigrations](https://symfony.com/doc/current/doctrine.html).


Ignore from schema updates all table starting with `__`

```yaml
#config/packages/doctrine.yaml

doctrine:
    dbal:
        ...
        schema_filter: ~^(?!__)~

```

Then create a migration (your first one) to create the `__process` table.
For example you could add [this one](./jobboy-bundle/php/Version00000000000000.php) to your `src/Migrations` folder.

This is the approach used in Broadway for the DbalEventStore.
