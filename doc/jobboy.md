# JobBoy

This is part of the JobBoy project [documentation](../README.md)

This is the [JobBoy](https://github.com/danielsan80/jobboy) documentation.

JobBoy is the core library of the JobBoyProject.

## Introduction

JobBoy nasce dall'esigenza di gestire dei processi batch.

Inizialmente era stato sviluppato JobMan (contrazione di JobManager) che si proponeva di gestire molti più
aspetti ma con i primi utilizzi ci siamo resi conto che alcune feature non le avremmo
utilizzate per ragioni di semplicità a scapito di una presunta minore efficienza.

Così è nata l'idea di realizzare un JobMan più piccolo, portandoci un po' del codice
di JobMan e l'esperienza accumulata nel realizzarlo.

Forse un giorno JobBoy crescerà e diventerà un nuovo JobMan.

## Example ##
A Symfony4 example application using JobBoy is available [here](./jobboy-example.md) 


## Getting started

To start using JobBoy the better way is to install the [JobBoyBundle](./jobboy-bundle.md) in your Symfony app.

## Development

Per iniziare clonare e avviare il progetto, poi eseguire i test.

```
git clone git@github.com:danielsan80/jobboy.git <project_dir>
cd <project_dir>
./dc up -d
./dc enter
test-all
```

Prima di fare `./dc up -d` la prima volta è meglio fare `cp .env.dist .env` e modificare il `.env`
opportunamente.

Se non ci sono test `@ignored` (e ad ora non ci sono) è sufficiente eseguire `test`
anziché `test-all`.

Se si usa PhpStorm (>=2018.03), per visualizzare l'uml in PlantUML presente dei .md installare Graphviz
(sulla macchina host)


```
sudo apt-get install graphviz
```


## How it works

- [Il Process](./jobboy/process.md)
- [Il ProcessStatus](./jobboy/process_status.md)
- [Le date del Process](./jobboy/process_dates.md)
- [ProcessParameters e ProcessStore](./jobboy/process_parameters_and_store.md)
- [Il Clock](./jobboy/clock.md)
- [Il Lock](./jobboy/lock.md)
- [Il ProcessRepository](./jobboy/process_repository.md)
- [IterationMaker](./jobboy/iteration_maker.md)
- [ProcessIterator](./jobboy/process_iterator.md)
- [ProcessHandlers](./jobboy/process_handlers.md)
- [Il PauseControl](./jobboy/pause_control.md)
- [Gli Application services](./jobboy/application_services.md)
- [L'EventBus](./jobboy/event_bus.md)
- [I Console Command](./jobboy/console_commands.md)
- [Il Bundle](./jobboy/bundle.md)
- [JobBoy e JobMan](./jobboy/jobman.md)


## Credits

Thanks to [Broadway](https://github.com/broadway/broadway) for the inspiring good code
(EventBus, DbalEventStore, Assertions, ...)

Thanks to [Akeneo](https://github.com/akeneo/pim-community-dev) for the original AkeneoBatchBundle(v1.7) design. 

## Resources

[Notes](jobboy/notes.md)

## To do
- test overlay dei `work`
- Ottimizzare `RedisProcessRepository`
- Ottimizzare `DoctrineProcessRepository`?

