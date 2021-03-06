---
title: AJAX Irrwege
order: 40
---

Wie im vorigen Kapitel gelernt ist AJAX die englische Abkürzung für „Asynchrones 
Javascript und XML“. Mit AJAX kann man Webseiten verbessern – man kann sie aber 
auch verschlechtern.

## Schlechte Verwendung von AJAX

Stellen Sie sich vor Sie haben eine Website mit 8 statischen HTML-Seiten, die
durch einfache Links verbunden sind. Wenn man AJAX kennen lernt, kommt man vielleicht
auf die Idee: statt normaler Links verwendet man nur noch `load()`  Was spricht
dafür?

* Man zeigt seine fortgeschrittenen Programmierkenntnisse (und kommt sich ent- sprechend cool vor)
* Das Laden der einzelnen Seite geht schneller, da nur der Inhalt, nicht aber z.B. der HTML `<head>` geladen werden muß. Das spart dutzende Byte.

Nun  surfen wir durch diese Site und beobachten in Firebug welche http-Requests
gemacht werden 

![AJAX statt normaler Links - eine schlechte Idee](/images/ajax-bad.png)

Beim Anklicken des „Links“ wird jeweils ein GET 
Request abgesetzt. Aber: die URL in der Adresszeile des Browsers bleibt immer
gleich.  Keine der „Seiten“ hat eine eigene URL.
Das hat viele Auswirkungen, unter anderem:

* Man kann keinen Link zu einer bestimmten Seite setzen – nur zur „Startseite“
* Man kann kein Lesezeichen (Bookmark, Favoriten) auf eine bestimmte Seite setzen
* Google „sieht“ den Text der einzelnen Seite nicht 

Vergleicht man Vor- und Nachteile sieht man schnell, dass in diesem Fall die
Version ohne AJAX (ganz normale Links) besser wäre.

§
  
Wann ist der Einsatz von AJAX sinnvoll? Wann sollte man darauf verzichten? Um
diese Frage zu beantworten brauchen wir eine Idealvorstellung wie eine Web-Site oder
Web-Applikation funktionieren soll. Ein Prinzip haben wir schon kennen 
gelernt, in diesem Kapitel kommt ein zweites, technischeres dazu:

1. Progressive Enhancement / Graceful Degradation
2. REST

**Progressive Enhancement** wurde im Kapitel 
[jQuery](/jquery/unobstrusive/) bereits vorgestellt: Eine Webseite sollte
auch im primitivsten Ausgabegerät noch funktionieren, also z.B. ohne Javascript.
Was Javascript an zusätzlichen Features bringt soll nicht auf Kosten der
Grundfunktion geben, sondern nur die vorhandene Funktion erweitern.



## REST – Representational State Transfer

Der Begriff “REST” wurde von Roy Fielding 2000 in seiner Dissertation definiert .
Fielding hat unter anderem an der HTTP-Spezifikation und an WebDAV
mitgearbeitet. In seiner Dissertation stellt er grundlegende Überlegungen zur Architektur von
verteilten Systemen an. 

Die Grundfrage könnte man so stellen: das Web ist ein sehr erfolgreiches 
verteiltes System, viele Hersteller entwickeln dafür Software (Microsoft,
Mozilla, IBM, …) und die Zusammenarbeit funktioniert. Es gibt viele Komponenten die erfolgreich
zusammenspielen (Browser, Server, Proxies, Suchmaschinen, Browser-Erweiterungen, 
…). 

**Welche Eigenschaften des Webs führen dazu, dass dieses
Zusammenspiel funktioniert?**

§

REST ist – nach der Analyse von Fielding – die zugrundeliegende **Architektur** des
Webs.  Wenn man versucht, beim Bau einer Web-Applikation die Prinzipien von REST
einzuhalten dann wird diese Applikation sich besonders gut in die vorhandene
Infrastruktur des Web (Server, Browser, Proxies, Caches, etc.) einpassen und davon profitieren.

Wenn man REST missachtet wird die Infrastruktur nicht funktionieren.

§

In der REST-Terminologie ist das Web ist eine Ansammlung von „**Ressourcen**“. In
diesem Skriptum wird für Ressourcen auch der Begriff „**Dokumente**“ verwendet. Wir
gehen hier nicht direkt von Fieldings Dissertation aus, sondern von der vereinfachten
Darstellung in 
[Tilkov, Stefan(2007): A Brief Introduction to REST. In: InfoQ](http://www.infoq.com/articles/rest-introduction)

Die REST-Prinzipien sind (vereinfacht):

1.  Gib jedem Dokument eine eindeutige URL.
2.  Dokumente sollen Links auf andere Dokumente enthalten.
3.  Verwende die HTTP-Methoden GET, POST, PUT, DELETE;  Verwende sie ihrer Bedeutung entsprechend.
4.  Ein Dokument kann auf verschiedene Arten repräsentiert werden (kann in verschiedenen Datentypen geliefert werden: HTML, XML, JSON, …).
5.  Zustandslosigkeit = Statelessness.

Im Folgenden finden Sie zu jedem dieser Punkte eine kurze Erklärung. 

### Jedes Dokument soll eine eindeutige URL haben

Wenn jedes Dokument eine URL hat, dann ist es verlinkbar, kann in Lesezeichen
gespeichert werden, kann von Google gefunden und verlinkt werden, etc.
Wenn Sie eine statische Webseite bauen erzeugen Sie die URLs beim Festlegen der 
Dateinamen. 

Bei einer PHP-Applikation sind es die Dateinamen und die GET-
Parameter, die die URL ausmachen. In der Datenbank-Applikation, die wir in den
Kapiteln [PHP MYSQL](/php-mysql/) und [PHP MYSQL 2](/php-mysql-2/) gebaut haben, 
waren die URLs:

* http://ich.multimediatechnology.at/projekt-1/index.php 
* http://ich.multimediatechnology.at/projekt-1/personen.php  
* http://ich.multimediatechnology.at/projekt-1/person.php?pid=1 
* http://ich.multimediatechnology.at/projekt-1/person.php?pid=2 
* http://ich.multimediatechnology.at/projekt-1/person.php?pid=3

und so weiter. Das erfüllt schon das REST-Prinzip.

§

In Kapitel [SECURITY - Unsichere direkte Objektreferenz](/security/a4-referenz/)
haben wir erfahren, wie man mit der RewriteEngine von Apache und der
Verwendung von Slugs die Preisgabe von IDs vemeiden kann:

* http://ich.multimediatechnology.at/projekt-1/
* http://ich.multimediatechnology.at/projekt-1/personen
* http://ich.multimediatechnology.at/projekt-1/person/max_mustermann 
* http://ich.multimediatechnology.at/projekt-1/person/nina_nocheinbeispiel
* http://ich.multimediatechnology.at/projekt-1/person/klaus_maria_nachname


Im Konkreten Beispiel müsste man in die Datei .htaccess im Ordner projekt-1 schreiben:

<plain filename=".htaccess">
RewriteEngine On

RewriteRule ^personen$     personen.php  [L]
RewriteRule ^person/(.*)   person.php?slug=$1  [L]
</plain>

#### Umsetzung 

die Umstellung einer bestehenden Applikation 
auf diese URLs hat Auswirkungen falls sie relative Links 
verwendet haben, um z.B. auf Stylesheets zu verweisen. Deswegen rate ich von
einer nachträglichen Umstellung der URLs ab – das ist sehr viel mehr Arbeit als man
denkt. 

### Dokumente sollen Links auf andere Dokument enthalten

Dieses Prinzip haben wir in allen bisher gezeigten Beispielen erfüllt.
Einzige Ausnahme: der im Kapitel [AJAX](/applied-javascript/ajax/) verwendete
Wetter-Service von Yahoo liefert nicht Links auf die dazugehörigen Bilder,
sondern nur IDs, aus denen man dann (mit zusätzlichem Hintergrundwissen) die
URLs konstruieren muss.

#### Umsetzung

dieses Prinzip müssen Ihre Web-Applikationen auf jeden Fall erfüllen.


### HTTP-Methoden GET, POST, PUT, DELETE

Wie sollen diese Methoden verwendet werden? Jede Methode hat eine Bedeutung:

* **GET** Eine Repräsentation einer Ressource soll geholt werden. Das verändert 
  nichts an der Ressource, diese Methode ist also harmlos.
* **POST** Eine Ressource (die bisher noch keine URL hatte) soll neu erzeugt 
  werden. z.B. könnte ein POST auf `/dings/` eine neue Ressource mit der 
  URL `/dings/17` erzeugen
* **PUT** Diese Methode dient zum Speichern einer Ressource unter der  angegebenen URL. 
  Diese Methode steht bei normalen Web-Formularen aber nicht zur Verfügung. Deswegen 
  wird statt dessen meist ein POST auf eine übergeordnete Ressource verwendet. 
* **DELETE** Diese Methode dient zum Löschen einer Ressource. Auch diese Methode 
  steht bei normalen Web-Formularen nicht zur Verfügung.  

#### Umsetzung

Bei der Programmeirung mit simplem PHP ist wichtig, dass Sie GET und POST korrekt
einsetzen.  Erst mit Frameworks wie Ruby on Rails oder dem ZEND Framework für
PHP werden sie die anderen Methoden verwenden. 

### Ein Dokument – mehrere Repräsentationen

Ein Dokument kann auf verschiedene Arten repräsentiert werden: z.B. kann die
Darstellung einer Person in HTML, XML und JSON erfolgen. Die HTML-Variante kennen Sie 
schon:

<htmlcode>
<h1>Details zu einer Person</h1>
<p><img src="http://multimediaart.at/media/profil/edvard_1_2.jpg" />
Herr Edvard Paul Beispieler hat insgesamt 4 Werke in dieser Datenbank.
Er hat den Usernamen fhs14287.</p>
<ul>
<li><a href='werk/24'>The Thin Red Line</a></li>
<li><a href='werk/50'>Der böse Wolf</a></li>
<li><a href='werk/83'>nimm zwei, schatz</a></li>
<li><a href='werk/303'>the neighbour.</a></li>
</ul>
</htmlcode>

§

Dieselbe Person könnte man auch als XML darstellen, z.B. als:

<plain caption="Daten einer Person als XML">
<person>
<image ref='http://multimediaart.at/media/profil/edvard_1_2.jpg' />
<vorname>Edvard Paul</vorname>
<nachname>Beispieler</nachname>
<username>fhs14287</username>
<werke>
<werk ref='http://ich.multimediatechnology.at/projekt-1/werk/24'>The Thin Red Line</werk>
<werk ref='http://ich.multimediatechnology.at/projekt-1/werk/50'>Der böse Wolf</werk>
<werk ref='http://ich.multimediatechnology.at/projekt-1/werk/83'>nimm zwei, schatz</werk>
<werk ref='http://ich.multimediatechnology.at/projekt-1/werk/303'>the neighbour.</werk>
</werke>
</person>
</plain>

§

Dieselbe Person als JSON:

<plain caption="Daten einer Person als JSON">
{"image":"http://multimediaart.at/media/profil/edvard_1_2.jpg",
 "vorname":"Eduard Paul",
 "nachname":"Beisteiner",
 "werk":[
    {"titel":"The Thin Red Line",
     "url":"http://ich.multimediatechnology.at/projekt-1/werk/24"},
    {"titel":"Der böse Wolf",
     "url":"http://ich.multimediatechnology.at/projekt-1/werk/50"},
    {"titel":"nimm zwei, schatz",
     "url":"http://ich.multimediatechnology.at/projekt-1/werk/83"},
    {"titel":"the neighbour.",
     "url":"http://ich.multimediatechnology.at/projekt-1/werk/303"}]}
</plain>

§

Besonders praktisch ist das, wenn ich Teile meiner Applikation später
AJAXifizieren will: ich kann die schon vorhandenen Ressourcen weiter nutzen, aber einfach die XML-
oder JSON-Repräsentation abfragen.

$

Ob die Repräsentation als Teil der URL, als URL-Parameter oder nur als
http-Header spezifiziert werden soll ist umstritten. RESTfull ist eigentlich nur die Abfrage
im http-Header:

<plain caption="HTTP Request: Accept-Header verlangt html als Antwort">
GET /mini/person/3 HTTP/1.1
Host: ich.multimediatechnology.at 
Accept: text/html
</plain>

Bzw.

<plain caption="HTTP Request: Accept-Header verlangt xml als Antwort">
GET /mini/person/3 HTTP/1.1
Host: ich.multimediatechnology.at 
Accept: application/xml
</plain>

$

Eine beliebte Vereinfachung ist die Verwendung von Endungen in der URL:

* http://ich.multimediatechnology.at/projekt-1/person/83.html 
* http://ich.multimediatechnology.at/projekt-1/person/83.xml 
* http://ich.multimediatechnology.at/projekt-1/person/83.json

#### Umsetzung

Diesen Aspekt von REST werden sie wahrscheinlich erst
bei der Verwendung von Frameworks umsetzten.

## Zustandslosigkeit = Statlessness.

Zustandslosigkeit ist ein sehr wichtiges Prinzip im Web. Was das bedeutet zeigt
man am einfachsten an einem Gegenbeispiel: Wenn ich im 
[Katalog der FH-Bibliotek](http://alephino.fh-salzburg.ac.at/) 
nach Büchern suche (hier mit dem Suchwort „Web“) 
erhalte ich zum Beispiel folgende URL:

`http://alephino.fh-salzburg.ac.at/alipac/GYOJPQZTMURYQVHVWMND-00021/find-simple?C1=%28&V1=web&C2=%29&F1=ALL&A1=N&x=0&y=0`

§

Wenn ich diese URL zwei Tage später wieder verwende erhalte ich keine Antwort
mehr, sondern die Fehlermeldung:
 
![Abbildung 52: Fehlermeldung von ALEPHINO, einer Webapplikation mit zuviel State](/images/session-timeout-error.png)

§

Warum ist das so?  Auch zwei Tage später sollte die Ressource „Liste der Bücher
mit dem Wort ‚Web’ im Titel“ noch vorhanden sein!
Die Antwort ist: Alephino legt zu viel Wert auf den State – den Zustand der
Session.

§

Nach dem REST-Prinzip sollte man also den Zustand der Session nur dann
verwenden, wenn er unbedingt notwendig ist. 

§

z.B. könnte man denken „Liste meiner ausgeliehenen Bücher“ sei von der Session abhängig. 
Aber selbst hier könnte man eine fixe URL
verwenden:

`http://alephino.fh-salzburg.ac.at/alipac/entlehnte/fhs007`

Auf diese URL muß es nun Zugriffsbeschränkungen geben: nur ich und die
BibliothekarIn darf zugreifen. Alle anderen erhalten keinen Zugriff sondern den Statuscode
`403 Access Denied`. Die BibliothekarIn hat aber Zugriff auf alle URLs dieser Form.

§

**Achtung**: wir haben schon ein Szenario kennen gelernt, wo die
Zeitbeschränkung von Sessions einen Sinn macht: Zum Verhindern von
[Cross Site Request Forgeries - CSRF](/security/a5-csrf/).  Das
ist aber nur bei Operationen sinnvoll, die etwas am Server ändern,
bzw. die Daten abfragen die besonders geschützt werden müssen.

Die "Liste der Bücher mit dem Wert `Web` im Titel" ist nicht schützenswert.


#### Umsetzung

Diesen Aspekt von REST können und sollen Sie voll umsetzten. 
Verwenden Sie nur die php-SESSION um den Zustand der Applikation zu speichern, und
gehen Sie möglichst sparsam damit um.


