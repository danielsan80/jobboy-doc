
# About Docker

Per facilitare l'installazione di Symfony ho aggiunto al Dockerfile l'installazione del symfony-cli e di wget.
Poi ho lanciato `symfony new my_project --no-git` per poi spostare il contenuto della directory my_project nella root
del progetto.


# About Symfony

Per facilitare l'installazione di Symfony ho aggiunto al Dockerfile l'installazione del symfony-cli e di wget.
Poi ho lanciato `symfony new my_project --no-git` per poi spostare il contenuto della directory my_project nella root
del progetto.

Symfony così com'è non mi va bene così faccio in genere le seguenti modifiche:

1- Sposto il contenuto di `services.yaml` in `services_default.yaml`

`cp config/services.yaml config/services_default.yaml`

2- Creo la directory `services`

`mkdir config/services`

3- e in `services.yaml` metto solo le import di `service_default.yaml` e `services/`

```
# config/services.yaml`

imports:
    - { resource: 'services_default.yaml'}
    - { resource: 'services/'}
```

4- Faccio puntare il namespace `App` a `src/App` anziché a `src`

```
# config/services_default.yaml`

  ...

  App\:
    resource: '../src/*'
    exclude: '../src/{DependencyInjection,Entity,Migrations,Tests,Kernel.php}'

  App\Controller\:
    resource: '../src/Controller'
    tags: ['controller.service_arguments']
    
  ...
  
```

diventa

```
# config/services_default.yaml`

  ...

  App\:
    resource: '../src/App/*'
    exclude: '../src/App/{DependencyInjection,Entity,Migrations,Tests,Kernel.php}'
    
  App\Controller\:
    resource: '../src/App/Controller'
    tags: ['controller.service_arguments']
    
  ...
    
```

5- Modifico di conseguenza le sezioni `autoload` e `autoload-dev` del composer.json

```
# composer.json

    ...

    "autoload": {
        "psr-4": {
            "App\\": "src/"
        }
    },
    "autoload-dev": {
        "psr-4": {
            "App\\Tests\\": "tests/"
        }
    },
    
    ...
    
```

diventa:

```
# composer.json

    ...

    "autoload": {
        "psr-4": {
            "App\\": "src/App/"
            "Acme\\": "src/Acme/"
        }
    },
    "autoload-dev": {
        "psr-4": {
            "Tests\\": "tests/"
        }
    },
    
    ...
    
```

6- Sposto quindi il contenuto di `src` in `src/App`

7- Correggo il Kernel di conseguenza

```
# src/App/Kernel.php

    ...

    public function getProjectDir(): string
    {
        return \dirname(__DIR__);
    }
    
    ...

```

diventa

```
# src/App/Kernel.php

    ...

    public function getProjectDir(): string
    {
        return \dirname(\dirname(__DIR__));
    }
    
    ...

```

8- Qualora si dovessero aggiungere delle rotte si creerà la cartella `routes`
e in tal caso i riferimenti al file system dovranno tener conto della cartella `src/App`.

Ad esempio:

```
# config/routes/annotations.yaml

controllers:
    resource: ../../src/App/Controller/
    type: annotation

```

9- Qualora si usasse Doctrine anche nel suo file di configurazione in `doctrine.orm.mappings.App`
c'è un riferimento a filesystem da adattare

```
# config/packages

...

doctrine:
    
    ...
    
    orm:
        auto_generate_proxy_classes: true
        naming_strategy: doctrine.orm.naming_strategy.underscore
        auto_mapping: true
        mappings:
            App:
                is_bundle: false
                type: annotation
                dir: '%kernel.project_dir%/src/App/Entity'
                prefix: 'App\Entity'
                alias: App

    ...

```

10- A differenza di come scritto della doc di Symfony è preferibile mettiamo `.env` sotto .gitignore.
Aggiungiamo e versioniamo invece il file `.env.dist`.
La motivazioni sta nelle complicazioni che avremmo con Docker se facessimo alla Symfony way.

11- Forse Flex non funzionerà più correttamente e neppure la generazione del codice.

