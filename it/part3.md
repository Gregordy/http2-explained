# 3. Tecniche applicate al contrasto della latenza

Quando si trovano faccia a faccia con questo tipo di problema, le persone si aggregano cercando svincoli. Alcuni di questi sono intuitivi ed intelligenti, altri davvero orrendi accrocchi.

## 3.1 Spriting
<img style="float: right;" src="https://raw.githubusercontent.com/bagder/http2-explained/master/images/spriting.jpg" />

"Spriting" è il termine usato per descrivere la tecnica che combina immagini multiple in un'unica immagine piu ampia. In seguito usando JavaScript o CSS, si puo provare a decomporre l'immagine riassemblata nei singoli componenti.

Un sito utilizzerà spesso questa astuzia per migliorare la percezione di velocità. Acquisire una singola grande immagine in HTTP 1.1 è molto più veloce che richiedere 100 minuscole immagini separate.

Naturalmente, metodo svantaggioso se si desidera mostrare solo una o due immagini frammentate. Lo spriting (spezzettamento?) implica anche che tutte le immagini vengano cancellate dalla cache allo stesso momento, piuttosto che permettere la persistenza dei frammenti richiamati più frequentemente.

## 3.2 Inlining

Lo "inlining" è un'altra tecnica utilizzata per evitare di spedire immagini individuali (in connessioni separate); tale tecnica consiste nell'utilizzare URL di tipo "data" incorporati nel CSS, con benefici e svantaggi simili al caso precedentemente citato dello "spriting".

    .icon1 {
        background: url(data:image/png;base64,<data>) no-repeat;
    }

    .icon2 {
        background: url(data:image/png;base64,<data>) no-repeat;
    }


## 3.3 Concatenazione

Un sito vasto può contenere tantissimi file JavaScript differenti. Gli sviluppatori possono utilizzare dei cosiddetti frontend per concatenare -o combinare- molteplici scipts per far si che il browser possa richiedere un singolo grande file piuttosto che dozzine di file minuscoli. Molti dati sono inviati, seppur ne servano meno e di conseguenza una quantita sempre maggiore di dati deve essere invalidata e "rinfrescata" al modificarsi di ogni singola risorsa inclusa.

Questa prassi è -ovviamente- un inconveniente per gli sviluppatori coinvolti.

## 3.4 Sharding (partizionamento. differenziamento)

L'ultimo trick per incrementare le performance che citerò è spesso chiamato "sharding". In breve, consiste nell'utilizzare il maggior numero di hosts per fornire il servizio. Anche se a prima vista può sembrare strano, c'è una ragione ben precisa dietro questo metodo.

All'inizio, le specifiche HTTP 1.1 dichiaravano che un client fosse autorizzato ad utilizzare un massimo di due [2] connessioni TCP per server/host. Dunque, per non violare le specifiche, gli sviluppatori hanno semplicemente iniziato ad utilizzare alias e nuovi nomi alternativi -et voilà- molte più connessioni disponibili verso il tuo sito, e maggior velocità di caricamento (page load, page speed).

Nel tempo la restrizione fu rimossa; ad oggi un client utilizza facilmente dalle sei alle 8 connessioni per nome-host (per hostname). Sono tuttavia ancora limitate, dunque i siti continuano ad utilizzare questa vecchia tecnica per incrementare il numero di connessioni disponibili per client. Visto che il numero di oggetti richiesti via HTTP continua ad incrementare -come mostrato sopra- l'ampio ammontare di connessioni è utilizzato per garantire che HTTP risponda ad elevate prestazioni, permettendo al sito di caricare velocemente. Non è infrequente per un sito utilizzare dalle 50 alle 100 o più connessioni. Statistiche del httparchive.org mostrano come i top 300mila URL al mondo in media aprano 40 (!) connessioni TCP ciascuno per mostrare il sito; tale trend sembra incrementare lentamente nel tempo.

Un altro scenario in cui utilizzare lo sharding è ad esempio l'hosting di immagini su hosts separati, in modo da non dover utilizzare o memorizzare alcun cookie, ben sapendo che il volume dei cookie è anch'esso in aumento. Utilizzando hosts senza cookie associati permette di aumentare le performances semplicemente riducendo il volume della richiesta HTTP !

L'immagine qui sotto mostra una trace (packe capture) acquisita durante la navigazione su uno dei piu famosi siti Svedesi, e dimostra come le richieste siano distribuite su  svariati hostnames.

![image sharding at expressen.se](https://raw.githubusercontent.com/bagder/http2-explained/master/images/expressen-sharding.jpg)
