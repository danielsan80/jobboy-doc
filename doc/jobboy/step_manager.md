## StepManager

Per semplici Job potrebbe essere sufficiente un ProcessHandler per ogni stato
(`starting`, `running`, `failing`, `ending`) o al più un paio per lo stato di `running`, il cuore
del Job.

Per Job un po' meno semplici ma con un flusso lineare è stato definito il concetto di Step.
Un Job può essere considerato una sequenza di Step i quali fanno ciascuno una parte del lavoro.
Ogni Step può svolgere il suo lavoro su più iterazioni ovviamente.

Per realizzare questo con JobBoy si possono creare diversi ProcessHandler che gestiscono
lo stato di `running` memorizzando nello Store lo step corrente.

Lo StepManager fa proprio questo gestendo per voi la transizione tra uno step e l'altro.

Il componente è distribuito con JobBoy sotto forma di un utility avanzata che può essere usata
oppure no a discrezione dello sviluppatore.

Per usarlo bisogna registrare gli Step nello StepRegistry per poi usare lo StepManager nei ProcessHandler.  
I ProcessHandler devono essere consapevoli dello Step che gestiscono.

Sono presenti la HasStepDataInterface e l'AbstractStepProcessHandler che la implementa a supporto.

Per l'integrazione con Symfony, attraverso il JobBoyBundle è sufficiente applicare,
oltre al tag `jobboy.process_handler` per aggiungere il ProcessHandler al registro,
anche il tag `jobboy.step` specificando la `position` (0 di default).

```
// config/services/jobs/do_something/process_handlers.yaml

  Acme\Demo\Jobs\DoSomething\Steps\DoA:
    lazy: true
    tags:
      - name: jobboy.process_handler
      - name: jobboy.step
        position: 100

  Acme\Demo\Jobs\DoSomething\Steps\DoB:
    lazy: true
    tags:
      - name: jobboy.process_handler
      - name: jobboy.step
        position: 110
  ...
```

Dovrebbe esserci (non nel momento in cui scrivo) un esempio di Job nel [jobboy-example](./doc/jobboy-example.md)



