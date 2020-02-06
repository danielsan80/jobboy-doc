# JobBoy Example

This is part of the JobBoy project [documentation](../README.md)

This is the [JobBoy Example](https://github.com/danielsan80/jobboy-example) documentation.

This is a Symfony example application using JobBoyBundle (and JobBoy).


## Getting started

Clone and start the project, install the dependencies, then run the tests:

```
git clone git@github.com:danielsan80/jobboy-example.git <project_dir>
cd <project_dir>
./dc up -d
./dc enter
cp .env.dist .env
vim .env
composer install
test-all
```

Now you can start the example process:

```
sf jobboy:process:start --code create_data_file --parameters=data/create_data_file/parameters.json 
```
Now the `Process` is saved in the `ProcessRepository` in `starting` status and no one is changing it.

So start the worker to iterate the processes including the new one.

```
sf jobboy:work
```

A few seconds later the process will be `completed` and the result
will be in the file `var/jobs/create_data_file/output.json`

Obviously the `jobboy:work` command should be added to Cron or Supervisord. An example with Cron:
```
//$ crontab -e
... 

* * * * * /usr/bin/php /var/www/project/bin/console jobboy:work --timeout=300 --idle-time=30 --env=prod
```

You can pause the worker so it stops iterating on processes going in a kind of idle time:
```
sf jobboy:work:pause 
```

And you can unpause it so it restarts iteriting:
```
sf jobboy:work:unpause 
```

Add --wait if you need a synchronous behaviour for pause or unpause: 
```
sf jobboy:work:pause --wait
```

You can execute the process in a command too waiting the end of the process (just for debug purpose):
```
sf jobboy:process:execute --code=create_data_file --parameters=data/create_data_file/parameters.json 
```

You can remove old processes:
```
sf jobboy:process:clear --days=90 
``` 

You can list all the processes...
```
sf jobboy:process:list 
```

...or only the active ones...
```
sf jobboy:process:list --active 
```

...and you can show the details of one... 
```
sf jobboy:process:list --show b793c
```

...or the details of the first one
```
sf jobboy:process:list --show-first
```


You can kill a process with:
```
sf jobboy:process:kill --id b793c
```

You can kill the current process with:

```
sf jobboy:process:kill --current
``` 





That's all.


## How it works

Questo progetto è un esempio di utilizzo di [JobBoy](https://github.com/danielsan80/jobboy).

Si tratta di un applicativo Symfony 4 (vedi le [note](./jobboy-example/notes.md)).

In questa applicazione è stato aggiunto il JobBoyBundle ed è stato implementato un job di esempio `create_data_file`.

Il `create_data_file` crea un file .jsonl contenente n anagrafiche generate con
[Faker](https://github.com/fzaninotto/Faker).

Per farlo impiega diverse iterazioni sospendendo il lavoro e salvando lo stato di avanzamento nel Process di JobBoy.

Per definire un job in JobBoy è necessario sviluppare un set di ProcessHandler, che implementino l'interfaccia
`ProcessHandlerInterface` e che gestiscano le varie casistiche nelle quali il job si può
trovare.

E' possibile tralasciare alcune casistiche e delegarne la gestione a dei ProcessHandler generici 
(con indice di priorità maggiore di 100). 

> La priority può trarre in inganno: il valore di default è 100, se si vuole registrare un ProcessHandler con
priorità più bassa sarà necessario impostare la priority ad un valore > 100 (110 ad esempio).  

Lo scopo di questo approccio è liberare periodicamente la memoria suddividendo il lavoro in più processi PHP eseguiti
sequenzialmente.

Inoltre la suddivisione del lavoro in più ProcessHandler permette di scalare sul numero di ProcessHandler piuttosto che
aumentare la complessità del programma che lo descrive.

Infine risulta molto più semplice testare i singoli ProcessHandler.

I ProcessHandler implementati per il `create_data_file` sono i seguenti:

- **Initialize**: Eseguito una volta sola prepara la `working_dir` e vi inizializza un file temporaneo nel quale
verranno scritti i vari record generati salvandosi il path nel `ProcessStore` del `Process`. Inoltre inizializza a zero
il contatore dei record scritti. Porta il `Process` dallo stato `starting` a `runnning`.
- **Iterate**: E' il cuore del lavoro, verrà eseguito più volte ed ogni volta genererà un numero limitato di record 
salvandoli in chunk di alcuni record nel file temporaneo, aggiornando di conseguenza il contatore nel `ProcessStore`.
Raggiunto il numero di record da generare porterà il `Process` dallo stato `running` a `ending`
- **Finalize**: Eseguito una sola volta per spostare il file temporaneo ormai pronto nella posizione finale
richiesta nei parametri del `Process`. Porta il `Process` dallo stato `ending` a `completed`.
- **FreeHandled**: E' generico e registrato quindi con una priorità più bassa (110). Il suo scopo è portare nello
stato `failing` eventuali `Process` rimasti `handled` il che può verificarsi se viene interrotto il worker
durante un'iterazione.
- **Fail**: Quando il `Process` è in `failing` questo `ProcessHandler` fa il tear down del lavoro, cancellando il file
temporaneo, dopo di ché lo porterà in `failed`.

- **Dummy**: E' generico e registrato quindi con una priorità più bassa (120). Il suo scopo è quello di coprire le
casistiche rimaste scoperte portando progressivamente il Process nello stato di `completed` o `failed`. Serve
a "chiudere i buchi" durante lo sviluppo di un nuovo job ma anche ad impedire temporaneamente che un job scritto
male blocchi la coda lasciando indefinitamente il Process in uno stato attivo.


I ProcessHandler devono essere registrati nel DIC di Symfony come servizi con il tag `jobboy.process_handler`.

## TO DO
- Aggiungere un esempio di utilizzo dello StepManager
- Aggiungere un esempio del ProcessHandler Dummy


## Resources
- [Notes](./jobboy-example/notes.md)
