> Aggiornamento 12/2012: La vulnerabilità CSRF è stata mitigata abilitando la richiesta di password per accedere al pannello di amministrazione. La tecnica per sbloccare le funzionalità nascoste del proprio router rimane comunque valida.

Una buona fetta degli utenti ADSL italiani sono a rischio intercettazione di comunicazioni internet e telefoniche, a causa di una **grave vulnerabilità** presente nei modelli di router Telecom ADSL Alice Gate VoIP 2 Plus Wi-Fi e ADSL2+ Wi-Fi N.

# Impatto

Un utente Telecom Italia che visita una pagina web appositamente preparata permette a un malintenzionato di prendere il **completo controllo** del router in maniera **permanente**, esponendo all'attaccante esterno il traffico privato internet e voce fino a che non si effettua esplicitamente il reset delle configurazioni di fabbrica. Alcuni attacchi possibili una volta preso il controllo del router sono:

* **Intercettazione dei i siti visitati dall'utente** e redirezione verso siti copia fasulli per rubare credenziali di mail, social network, home banking, etc modificando i record del DNS. 
* **Ascolto delle chiamate telefoniche**, telefoni analogici compresi, sostituendo l'indirizzo del server VoIP ufficiale con quello di uno preparato per le intercettazioni. 
* **Instradamento di tutto il traffico verso una macchina dell'attaccante** allo scopo di analizzare il traffico e sottrarre dati sensibili. 
* **Apertura dei pannelli di controllo del router verso internet** con il quale l'attaccante mantiene l'accesso remoto al router nel tempo, con una interfaccia web esposta verso internet e tracciata via dns dinamico.

Il numero di utenti che posseggono il router coinvolto è imponente: secondo la [relazione](http://2011annualreport.telecomitalia.com/attachments/RelazioneFinanziariaAnnuale-2011-GruppoTI.pdf) finanziaria annuale di Telecom Italia, gli utenti broadband sono circa 9 milioni. Sui sette modelli di router ADSL distribuiti nella storia di Alice, tre sono stati distribuiti massicciamente negli ultimi cinque anni: Alice Gate VoIP 2 plus Wi-Fi (firmware AGPF, vulnerabile), Alice Gate2 plus Wi-Fi e l'ultimo ADSL2+ Wi-Fi N (AGPWI, vulnerabile). Anche andando cauti con la stima, il numero di possessori del router vulnerabile è molto alto.

<blockquote class="blockwarn"> <p>Lo studio sulla sicurezza dell'apparato è da intendersi a scopo didattico. Verificate la sicurezza del vostro router e proteggetelo utilizzando il <a href="http://disse.cting.org/codes/alice.html">dimostratore</a> pubblicato. L'autore non è responsabile di danni agli apparati o violazioni a sistemi informatici derivanti dall'uso delle tecniche esposte. Ricordo che l'accesso abusivo ad un sistema informatico e perseguibile secondo l'articolo 615-ter del codice penale.
</p></blockquote>

# vulnerabilità

La vulnerabilità risiede nel pannello di controllo del router: effettuando una particolare richiesta HTTP all'URL [http://192.168.1.1/admin.cgi](http://192.168.1.1/admin.cgi) è possibile riscrivere qualsiasi configurazione interna del router. Questo difetto unito alla mancanza di protezioni [CSRF](http://it.wikipedia.org/wiki/Cross-site_request_forgery) permette di forzare il browser dell'utente vittima che visita un sito *trappola* a riconfigurare il proprio router in maniera automatica consegnandone il controllo al maleintenzionato.  

Automatizzare l'**attacco su larga scala** è semplice: l'attaccante pubblica una pagina HTML che cambia il DNS per intercettare le connessioni e effettuare phishing, e espone la interfaccia web/telnet del modem ADSL verso internet mantenendo il controllo del router nel tempo con un DNS dinamico. Pubblicando il link a questa pagina in un sito molto trafficato creerebbe in poche ore una **botnet** di migliaia di router sotto il controllo dell'attaccante, con conseguenze molto gravi. 

> Leggi l'[articolo tecnico](http://disse.cting.org//2012/09/02/alice-gate-agpf-csrf-reconf-vulnerability-details/) su questo difetto e su come si utilizza per personalizzare il proprio router e sbloccare le sue funzionalità nascoste.

> Leggi il paragrafo dell'[articolo tecnico](http://disse.cting.org/2012/09/02/alice-gate-agpf-csrf-reconf-vulnerability-details/) sulla tecnica CSRF utilizzata per prendere il controllo di un router altrui.

<blockquote class="blockwarn"> <p> <a href="http://disse.cting.org/codes/alice.html">Usa il dimostratore per verificare se il tuo router Alice è vulnerabile, proteggerlo e sbloccare le funzionalità nascoste</a> </p></blockquote>

