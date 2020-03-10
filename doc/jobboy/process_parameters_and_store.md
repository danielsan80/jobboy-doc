## ProcessParameters e ProcessStore

`ProcessParameters` e `ProcessStore` sono due value object immutabili per certe versi molto simili ma che hanno
scopi differenti nel `Process`. Sono entrambi delle `ParametersBag` ed offrono metodi per l'interrogazione
e la manipolazione del loro contenuto. Essendo immutabili i metodi di manipolazione tuttavia restituiscono
nuove istanze di loro stessi con le opportune modifiche.

Il `ProcessParameters` viene creato alla creazione del `Process` e quest'ultimo poi lo congela e non permette
più di modificarlo. I parametri rimangono invariati per tutta la vita del `Process`.

Il `Process` contine due istanze `ProcessStore`: lo `store` e il `reports`.

Lo `store` può essere "manipolato" attraverso alcuni metodi del `Process`. Il suo scopo è quello
di memorizzare contatori, posizioni di file temporanei, il nome dello step corrente,
sottostati. Lo store serve ad memorizzare temporaneamente dati necessari all'esecuzione del processo.
E' a discrezione del dev utilizzatore.

Anche `reports` può essere "manipolato" attraverso alcuni metodi del `Process` (simili a quelli per lo `store`)
ma il suo scopo è quello di conservare dati finalizzati alla lettura da parte dell'utente, durante e a fine processo.
Vi si possono memorizzare contatori o altre informazioni da mostrare in UI come una lista di warning,
oppure la reason in caso di fallimento. Anche in questo caso è a discrezione del dev utilizzatore.

In pratica `store` e `report` sono due canali per memorizzare e trasmettere informazioni, il primo è rivolto
al processo, alle iterazioni successive, mentre il secondo è rivolto all'utente.

Si potrebbe riassumere così:
- `parameters`: read only
- `store`: read/write
- `reports`: write only

Poiché il sistema garantisce che il servizio `MakeIteration` che manipola i `Process` non possa essere eseguito
in concorrenza con se stesso non ci sarà neppure concorrenza nell'accesso ai metodi di manipolazione del `Process`.


