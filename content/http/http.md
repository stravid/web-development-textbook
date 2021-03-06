---
title: HTTP
order: 30
---
HTTP in der aktuell gültigen Version 1.1 ist in [RFC 2616](https://www.ietf.org/rfc/rfc2616.txt) definiert. 
HTTP baut auf TCP auf, d.h. die hier dargestellten Daten werden über eine TCP-Verbindung zwischen Client und Server übertragen. 
Im [Kapitel „Drei Standards definieren das Web“](/das-web-und-html/standards/) 
wurde HTTP schon einmal grob vorgestellt; nun werden wir HTTP genauer betrachten.

Ablauf im Überblick
---------------------
Egal ob der Vorgang durch das Eintippen einer URL oder durch das Anklicken eines Links gestartet wird — das Laden einer Webseite über HTTP funktioniert immer gleich. 

1. Der **Browser** analysiert die **URL**: falls Sie eine IP-Adresse enthält geht’s weiter zum nächsten Schritt. Falls sie einen Domain Namen enthält wird dieser mittels DNS-Lookup in die entsprechende IP-Adresse übersetzt.
2. Der Browser baut eine **TCP-Verbindung** zum Server auf, falls in der URL nicht anders angegeben wird  dabei Port 80 angesteuert.
3. Der Browser sendet über die TCP-Verbindung einen **HTTP-Request**; dieser besteht aus einer ersten Zeile (Request-Line), mehreren Header-Zeilen und manchmal einem Body.
4. Der **Webserver** nimmt den Request entgegen und analysiert ihn. Der Webserver entscheidet, ob er zur Beantwortung der Anfrage nur eine bestimmte Datei aus dem Dateisystem zu liest, oder ein Programm aufruft.
5. Der Webserver schickt über die TCP-Verbindung einen **HTTP-Response** an den Browser, dieser besteht aus einer ersten Zeile (Response-Line) mit Statuscode, z. B. „200 OK\n\n“, mehreren Header-Zeilen und der  angeforderten Ressource. 
6. Der Browser nimmt das Dokument in Empfang, **stellt es dar**, und beendet die TCP-Verbindung.

§

![zwei http requests](/images/http-sequence-diagram.svg)

Dieser einfache Ablauf kann durch die Verwendung von Proxies und Caches sowie durch das wiederholte Abrufen von Dokumenten vom selben Server komplizierter werden — das ignorieren wir aber erst einmal.

Aufbau von Request und Response
--------------------------------
Jede Anfrage des Clients und jede Antwort des Servers besteht aus einer ersten Zeile 
mit besonderer Bedeutung, einem Header und einem Body. 
Header und Body funktionieren ähnlich wie bei einer E-Mail, es kann viele
Header-Zeilen geben.  Der Body ist beim Request meist leer.

§

Hier ein Beispiel für einen Request:

      GET /rezensionen/list.php3?no=20 HTTP/1.1
      Host: www.biblio.at
      User-Agent: Mozilla/5.0 (Win98; de-AT) Gecko/20020311
      Accept: text/html;q=0.9,text/plain;q=0.8,*/*;q=0.1
      Accept-Language: de-at, de;q=0.66, en-us;q=0.33
      Accept-Encoding: gzip, deflate, compress;q=0.9
      Accept-Charset: ISO-8859-15, utf-8;q=0.66, *;q=0.66

Die erste Zeile besteht aus: Methode, URL-Fragement und HTTP-Versionsnummer. 
Die meist-verwendete Methode ist GET. Sie erinnern sich:
bei Web-Formularen muss man die Methode angeben, mit der die Daten an den 
Server übertragen werden sollen. Das ist diese Methode.

§

Hier ein Beispiel für eine Server-Antwort:


      HTTP/1.0 200 OK
      Date: Sat, 27 Apr 2002 05:52:57 GMT
      Server: Apache/1.3.9 (Unix) Debian/GNU
      Content-Type: text/html

      <!DOCTYPE html>
      <html><head><title>Rezensionsdatenbank</title>
      <link rel="stylesheet" href="rezensionen.css">
      </head><body>nix</body></html>

Die erste Zeile der Server-Antwort besteht aus der HTTP-Versionsnummer, dem Statuscode und einem erklärenden Text zum Statuscode, der aber nicht standardisiert ist.

Die wichtigsten Statuscodes sind 200 (ok), 404 (not found), 403 (forbidden).
Siehe http://httpstatus.es/

Header
-------

Header-Zeilen gibt es sehr viele; relativ wenige davon werden von Clients und Servern wirklich beachtet. 

### Host

Im Request: `Host: www.biblio.at` Wichtig wenn der Server unter mehreren Domain Names (aber nur einer IP-Adresse) 
erreichbar ist.  Das ist fast immer der Fall, dieser Header ist also fast immer
notwendig.


### User-Agent

Im Request:

      User-Agent: Mozilla/5.0 (Win98; de-AT) Gecko/20020311
      User-Agent: Mozilla/4.0 (compatible; MSIE 5.5; Windows 98; Win 9x 4.90)

Das ist die Selbstoffenbarung des Clients: welcher Browser, welche Version. Die meisten 
Clients lügen, und behaupten sie wären Mozilla, erst in der Klammer folgt die richtige Angabe. 

### Referer

Im Request: `Referer: http://my.app.at/form.html` Welche Seite hat der Client
zuvor angezeigt? URL der vorigen Seite — falls von dort ein Link hierher verfolgt 
wurde oder ein FORM gesendet wurde. Dieser Header kann im Browser deaktiviert werden!

### Date, Server

Im Response: 

    Date: Sat, 27 Apr 2002 05:52:57 GMT
    Server: Apache/1.3.9 (Unix) Debian/GNU

zeigt Datum und Uhrzeit am Server, bzw. die verwendete
Webserver-Software.  Wenn Sie Statistiken über den Marktanteil der verschiedenen
Server sehen, dann basieren diese auf dieser Angabe.

### Content-Type

Im Response: 

    Content-Type: text/html


MIME-Type des im Body gelieferten Dokuments. Andere Werte die 
sie hier häufig antreffen sind:

* text/css
* application/javascript
* image/png
* image/jpg
* image/gif
* image/svg+xml



HTTP abhören
--------------
Wie können Sie HTTP beobachten?  Entweder mit einem allgemeinen
**Netzwerk-Sniffer** wie [Wireshark](http://www.wireshark.org/) oder mit der 
Firefox-Extension [Live HTTP Headers](https://addons.mozilla.org/en-US/firefox/addon/live-http-headers/)
oder [Firebug](https://addons.mozilla.org/en-US/firefox/addon/firebug/?src=search). 

![Abbildung 131: HTTP abhören mit Wireshark (links) und Live HTTP Headers (rechts)](/images/image334.png)

Die folgenden Anwendungsbeispiele wurden mit Live HTTP Headers mitgeschnitten.

Seite laden oder Formulardaten senden mit GET
----------------------------------------------
Die Methode GET wird bei den meisten HTTP-Anfragen verwendet - sowohl bei normalen Links als auch beim Senden von Formulardaten mit GET. Die URL kann dabei ein Fragezeichen gefolgt von Parametern und Werten enthalten. 

      GET /rezensionen/list.php3?no=20 HTTP/1.1
      Host: www.biblio.at
      User-Agent: Mozilla/5.0 (Win98; de-AT) Gecko/20020311
      Accept: text/html;q=0.9,text/plain;q=0.8,*/*;q=0.1
      Accept-Language: de-at, de;q=0.66, en-us;q=0.33
      Accept-Encoding: gzip, deflate, compress;q=0.9
      Accept-Charset: ISO-8859-15, utf-8;q=0.66, *;q=0.66

      HTTP/1.0 200 OK
      Date: Sat, 27 Apr 2002 05:52:57 GMT
      Server: Apache/1.3.9 (Unix) Debian/GNU
      Content-Type: text/html

      <!DOCTYPE html>
      <html>
      <head>
         <title>Rezensionsdatenbank</title>
         <link rel="stylesheet" href="rezensionen.css">
      </head>

Die Länge der übertragenen Daten aus dem Formular ist hier begrenzt durch die Länge der URL. Für größere Datenmengen (z. B. beim Upload von Dateien) gibt es die Methode Post. 

§

Die Header, die mit Accept beginnen, können (laut Standard) dem Aushandeln von Sprache, Datentyp, Encoding dienen; werden aber von Servern und Clients nur teilweise beachtet.


      Accept: text/html;q=0.9,text/plain;q=0.8,*/*;q=0.1 

bedeutet laut Standard, daß der Client das Dokument lieber als HTML als als Plain Text erhalten würde. Im realen Web wird aber unter einer URL immer nur ein Dokumententyp angeboten. Wenn man eine PDF-Version der gleichen Information anbietet, dann geschieht dies unter einer anderen URL.

Accept-Language würde dem Aushandeln der Sprache dienen. Dazu müssten die UserInnen aber im Browser die Sprach-Präferenz konfigurieren:

 
![Abbildung 132: Festlegen der Sprach-Präferenz im Browser Firefox](/images/image335.png)

Da aber kaum jemand diese Konfiguration vornimmt wird die Sprach-Aushandlung kaum verwendet. Einziges mir bekanntes Beispiel einer Webseite die unter der gleichen URL in verschiedenen Sprachen erhältlich ist ist die Homepage von Debian:

 
![Abbildung 133: Homepage von Debian, verschiedene Sprachen bei gleicher URL](/images/image336.png)

Senden von Formulardaten mit Post
----------------------------------
Bei POST werden die Daten aus dem Formular nicht in der URL, sondern im HTTP-Body der Anfrage übertragen. Die Codierung (kaufmännisches-Und zwischen den namen=wert-Paaren, + statt Leerzeichen, %-Schreibweise für Sonderzeichen) bleibt gleich. Hier gibt es keine Beschränkung der Länge.

      POST /rezensionen/list.php3 HTTP/1.1
      Host: www.biblio.at
      User-Agent: Mozilla/5.0 (Win98; de-AT) Gecko/20020311
      Referer: http://www.biblio.at/rezensionen/formular.htm
      Content-Type: application/x-www-form-urlencoded
      Content-Length: 129

      no=20&limit=1&katalog=all&isbn=&nachname=Jellinek&vornam
      e=&titel=&schlagwort1=&schlagwort2=&Bool=AND&verl=&von=&
      bis=&submit=SUCHE

Die Antwort des Servers unterscheidet sich nicht zwischen GET und POST (außer Sie haben das in PHP absichtlich so programmiert).

Umleitung an neue URL
----------------------
Mit dem Statuscode 301 kann der Server anzeigen, dass die Seite an eine neue URL übersiedelt ist. Der Webbrowser schickt dann sofort eine Anfrage an die neue URL, die LeserIn bemerkt so eine Weiterleitung meist gar nicht. 

(Wie auf Seite Fehler! Textmarke nicht definiert. beschrieben, können Sie diese Umleitung von PHP aus mit dem header-Befehl auslösen)

      GET / HTTP/1.1
      Host: www.rezensionen.at
      User-Agent: Mozilla/5.0 (Win98; de-AT) Gecko/20020311
      Accept: text/html;q=0.9,text/plain;q=0.8,*/*;q=0.1
      Accept-Language: de-at, de;q=0.66, en-us;q=0.33
      Accept-Encoding: gzip, deflate, compress;q=0.9
      Accept-Charset: ISO-8859-15, utf-8;q=0.66, *;q=0.66

      HTTP/1.0 301 Moved Permanently
      Date: Sat, 27 Apr 2002 05:52:26 GMT
      Server: Apache/1.3.9 (Unix) Debian/GNU
      Location: http://www.biblio.at/rezensionen/
      Content-Type: text/html; charset=iso-8859-1

      <!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
      <HTML><HEAD>
      <TITLE>301 Moved Permanently</TITLE>
      </HEAD><BODY>
      <H1>Moved Permanently</H1>
      The document has moved <A HREF="http://www.biblio.at/rezensionen/">here</A>.<P>
      </BODY></HTML>
      GET /rezensionen/ HTTP/1.1
      Host: www.biblo.at
      User-Agent: Mozilla/5.0 (Win98; de-AT) Gecko/20020311
      Accept: text/html;q=0.9,text/plain;q=0.8,*/*;q=0.1
      Accept-Language: de-at, de;q=0.66, en-us;q=0.33
      Accept-Encoding: gzip, deflate, compress;q=0.9
      Accept-Charset: ISO-8859-15, utf-8;q=0.66, *;q=0.66


und so weiter.

Authentisierung nach RFC 2617
------------------------------
Der Webserver kann so konfiguriert werden, dass er Dokumente nur nach Eingabe von Username und Passwort liefert. Der Browser zeigt dafür ein Eingabefenster an:

     
![Abbildung 134: Eingabefenster für HTTP Authentisierung in verschiedenen Browsern](/images/image337.png)

Falls eine Authentisierung über diese Methode stattgefunden hat, finden Sie den Usernamen in PHP in der Variable $_SERVER['PHP_AUTH_USER'].

Auf Ebene des HTTP-Protokolls betrachtet funktioniert diese Authentisierung wie folgt: bei der ersten Anfrage des Clients schickt der Server einen Status-Code 401 (nicht autorisiert). 

      GET /pr/ HTTP/1.1
      Host: www.sbg.ac.at
      User-Agent: Mozilla/5.0 (Win98; de-AT) Gecko/20020311
      Accept: text/html;q=0.9,text/plain;q=0.8,*/*;q=0.1
      Accept-Language: de-at, de;q=0.66, en-us;q=0.33
      Accept-Encoding: gzip, deflate, compress;q=0.9
      Accept-Charset: ISO-8859-15, utf-8;q=0.66, *;q=0.66

      HTTP/1.0 401 Unauthorized
      Date: Sat, 27 Apr 2002 06:05:08 GMT
      Server: Apache/1.3.22 (Unix)
      WWW-Authenticate: Basic realm="unineu"
      Content-Type: text/html; charset=iso-8859-1

      <!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
      <HTML><HEAD>
      <TITLE>401 Authorization Required</TITLE>

Daraufhin zeigt der Browser das Passwort-Eingabefenster an. Nach Eingabe von Username und Passwort schickt der Browser die gleiche Anfrage erneut, diesmal aber mit der zusätzlichen Header-Zeile Authorization. In dieser Zeile werden Username und Passwort (leicht verschlüsselt) mitgeschickt. 

Wenn Username und Passwort stimmen, schickt der Server eine positive Antwort und das Dokument. 
Der Browser wird bei allen weiteren Anfragen an diesen Server ebenfalls die Authorization-Zeile mitschicken. 

      GET /pr/ HTTP/1.1
      Host: www.sbg.ac.at
      User-Agent: Mozilla/5.0 (Win98; de-AT) Gecko/20020311
      Accept: text/html;q=0.9,text/plain;q=0.8,*/*;q=0.1
      Accept-Language: de-at, de;q=0.66, en-us;q=0.33
      Accept-Encoding: gzip, deflate, compress;q=0.9
      Accept-Charset: ISO-8859-15, utf-8;q=0.66, *;q=0.66
      Authorization: Basic dHI6cHJyMDBy


      HTTP/1.0 200 OK
      Date: Sat, 27 Apr 2002 06:05:11 GMT
      Server: Apache/1.3.22 (Unix)
      Content-Type: text/html; charset=iso-8859-1

      <html lang="de">
      <head>
      <title>Universit&auml;t Salzburg - B&uuml;ro f&uuml;r Public Relations</title>

HTTPS
------
HTTPS ist HTTP über Secure Socket Layer (SSL) — d.h. auf Ebene der TCP-Verbindung werden alle übertragenen Daten verschlüsselt. Außerdem bietet SSL die Möglichkeit, dass sich der Server und der Client mit einem Zertifikat ausweisen. 

Ob HTTPS oder HTTP verwendet wird, hängt von der Konfiguration des Server ab. Wenn Sie nur Webspace in „Untermiete“ benutzen (wie auf multimediatechnology.at), können Sie HTTPS nicht aktivieren.

Die Verschlüsselung und Entschlüsselung des gesamten Verkehrs braucht CPU-Zeit — der Webserver kann also weniger Anfragen bedienen als mit HTTP. Eine Möglichkeit diese Belastung vom Webserver „fernzuhalten“ ist die Terminierung von SSL auf einem anderen Rechner. 

Die Electronic Frontier Foundation hat eine Kampagne11 gestartet um KonsumentInnen und Website-BetreiberInnen zu überzeugen, dass https immer verwendet werden sollte: „HTTPS Everywhere“.

Damit wäre der gesamte Web-Traffic nicht mehr abhörbar.  



Proxies
--------
Das HTTP-Protokoll sieht die Möglichkeit von Proxies vor. Ein Proxie ist eine „Zwischenstation“ die HTTP-Verkehr weitergibt, der Proxy agiert also auf der einen Seite als HTTP-Server, auf der anderen Seite als HTTP-Client. Im Browser kann ein Proxie konfiguriert werden:


![Abbildung 135: Proxy-Konfiguration in Firefox: Extras - Einstellungen - Erweitert - Netzwerk – Verbindung](/images/image343.png)

Ist ein Proxy konfiguriert dann baut der Browser die HTTP-Verbindung nicht direkt zum Zielrechner auf, sondern zum Proxy, und verändert die Form der ersten Zeile der HTTP-Anfrage: die vollständige URL wird angegebe:

    GET http://www.sbg.ac.at/pr/ HTTP/1.1

Proxies können gleichzeitig als Cache fungieren: Anfragen und Antworten werden gespeichert; erfolgt die gleiche Anfrage noch einmal, kann die gespeicherte Antwort verwendet werden. 

