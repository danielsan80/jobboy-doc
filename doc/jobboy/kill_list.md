## KillList

Come il PauseControl serve a mettere in comunicazione due processi PHP,
quello del ConsoleCommand che richiede di interrompere o non eseguire un processo e il JobBoy Worker.

E' possibile aggiungere l'id di un processo alla KillList, rimuoverlo una volta ucciso,
ottenere il primo o tutti gli id in lista oppure sapere se un id è presente nella lista.

L'IterationMaker la utilizza e da priorità all'uccisione dei processi in lista.
