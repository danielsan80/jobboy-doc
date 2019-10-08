
# About Docker

Per facilitare l'installazione di Symfony ho aggiunto al Dockerfile l'installazione del symfony-cli e di wget.
Poi ho lanciato `symfony new my_project --no-git` per poi spostare il contenuto della directory my_project nella root
del progetto.


# About Symfony

Partendo da un progetto Symfony 4 faccio in genere le seguenti modifiche:

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

5- Modifico le sezioni `autoload` e `autoload-dev` del composer.json, aggiungo l'albero di namespace `Acme`
e cambio il namespace dei tests,
così i test dell'App Symfony saranno in `Tests/App` anziché `App/Tests`
e vi potrò mettere anche i test delle librerie. Aggiungo il  

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
            "App\\": "src/",
            "Acme\\": "lib/Acme/src"
        }
    },
    "autoload-dev": {
        "psr-4": {
            "Tests\\": "tests/"
        }
    },
    
    ...
    
```

9- Qualora si dovessero registrare delle entità in Doctrine, nel suo file di configurazione in `doctrine.orm.mappings.App`
c'è un riferimento a filesystem da aggiungere, ad esempio:

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
                
            AcmePerson:
                is_bundle: false
                type: annotation
                dir: '%kernel.project_dir%/lib/Acme/Person/Entity'
                prefix: 'Acme\Person\Entity'
                alias: AcmePerson

    ...

```

10- A differenza di come scritto della doc di Symfony è preferibile mettere `.env` sotto .gitignore.
Aggiungiamo e versioniamo invece il file `.env.dist`.
La motivazione sta nelle complicazioni che avremmo con Docker se facessimo alla Symfony way.


