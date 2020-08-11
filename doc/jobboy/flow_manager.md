## FlowManager

Laddove lo StepManager non fosse sufficiente perché il flusso del nostro Job non è lineare,
perché ci sono rami opzionali, cicli, alberi diversi per il completamento e per il fallimento, 
è necessario definire un flusso di esecuzione.

Per questi casi è stato definito il concetto di Flow.
Un Job può essere considerato un grafo di nodi e di transizioni tra questi nodi.

Il FlowManager è un'utility come lo StepManager che permette di definire il flusso di esecuzione del Job,
legando ogni nodo ad un ProcessHandler.

Anche il FlowManager componente è distribuito con JobBoy sotto forma di un utility avanzata che può essere usata
oppure no a discrezione dello sviluppatore.

Per usarlo bisogna registrare le Transition tra i Node nel TransitionRegistry per poi usare il FlowManager
nei ProcessHandler.  
I ProcessHandler devono essere consapevoli del Node che gestiscono e del trigger che scatenano al completamento
del lavoro di loro competenza (attraverso il parametro $on). 

Sono presenti la HasNode interface e l'AbstractFlowProcessHandler che la implementa a supporto.

Per l'integrazione con Symfony, attraverso il JobBoyBundle è sufficiente applicare,
oltre al tag `jobboy.process_handler` per aggiungere il ProcessHandler al registro,
anche i seguenti tag:
- `jobboy.flow.entry` per specificare che il corrente è il nodo di entrata del flusso. Può esserci una sola entry
- `jobboy.flow.exit` per specificare che il corrente è un nodo di uscita del flusso.
  Va specificato il trigger di uscita attraverso la proprietà `on`
- `jobboy.flow.node_change` per specificare che dal corrente è possibile
  spostarsi su un altro nodo/venire da un altro nodo 
  allo scattare di un trigger.
  Va specificato il nome del nodo da cui si viene attraverso la proprietà `from` OPPURE quello
  in cui si intende andare attraverso la proprietà `to`, specificando sempre il trigger attraverso la proprietà `on`
  
```
// config/services/jobs/do_something/process_handlers.yaml

  Acme\Demo\Jobs\DoSomething\Steps\DoA:
    lazy: true
    tags:
      - name: jobboy.process_handler
      - name: jobboy.flow.entry
      - name: jobboy.flow.node_change
        to: b
        on: done
      - name: jobboy.flow.node_change
        to: c
        on: error

  Acme\Demo\Jobs\DoSomething\Steps\DoB:
    lazy: true
    tags:
      - name: jobboy.process_handler
      - name: jobboy.flow.node_change
        to: d
        on: done

  Acme\Demo\Jobs\DoSomething\Steps\DoC:
    lazy: true
    tags:
      - name: jobboy.process_handler
      - name: jobboy.flow.exit
        on: done

  Acme\Demo\Jobs\DoSomething\Steps\DoD:
    lazy: true
    tags:
      - name: jobboy.process_handler
      - name: jobboy.flow.exit
        on: done
      - name: jobboy.flow.node_change
        to: a
        on: retry  
  

  ...
```

Dovrebbe esserci (non nel momento in cui scrivo) un esempio di Job nel [jobboy-example](./doc/jobboy-example.md)



