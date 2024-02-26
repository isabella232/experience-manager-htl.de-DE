---
title: Erste Schritte mit HTL
description: Lernen Sie HTL kennen, das bevorzugte und empfohlene Server-seitige Vorlagensystem für HTML in AEM, und verstehen Sie die wichtigsten Konzepte der Sprache und ihre grundlegenden Konstrukte.
exl-id: c95eb1b3-3b96-4727-8f4f-d54e7136a8f9
source-git-commit: 88edbd2fd66de960460df5928a3b42846d32066b
workflow-type: ht
source-wordcount: '2147'
ht-degree: 100%

---


# Erste Schritte mit HTL {#getting-started-with-htl}

HTML Template Language (HTL) ist das bevorzugte und empfohlene Server-seitige Vorlagensystem für HTML in Adobe Experience Manager. Wie bei allen Server-seitigen HTML-Templating-Systemen definiert eine HTL-Datei die an den Browser gesendete Ausgabe, indem sie den HTML-Code selbst, einige grundlegende Darstellungslogiken sowie Variablen angibt, die zur Laufzeit ausgewertet werden.

Dieses Dokument gibt einen Überblick über den Zweck der HTL sowie eine Einführung in die grundlegenden Konzepte und Konstrukte der Sprache.

>[!TIP]
>
>Dieses Dokument stellt den Zweck der HTL und einen Überblick über ihre grundlegende Struktur und Konzepte vor. Wenn Sie Fragen zur spezifischen Syntax haben, sehen Sie bitte in der [HTL-Spezifikation](specification.md) nach.

## HTL-Ebenen {#layers}

HTL, wie es in AEM verwendet wird, kann durch eine Reihe von Ebenen definiert werden.

1. **[HTL-Spezifikation](specification.md)** – HTL ist eine quelloffene, plattformunabhängige Spezifikation, die von jeder Person frei implementiert werden kann.
1. **[Sling HTL Scripting Engine](specification.md)** – Das Sling-Projekt hat die Referenzimplementierung von HTL erstellt, die von AEM verwendet wird.
1. **[AEM-Erweiterungen](specification.md)** – AEM baut auf der Sling HTL Scripting Engine auf, um Entwickelnden praktische, AEM-spezifische Funktionen zu bieten.

Diese HTL-Dokumentation konzentriert sich auf die Verwendung von HTL zur Entwicklung von AEM-Lösungen. Als solche berührt sie alle drei Ebenen und verknüpft bei Bedarf externe Ressourcen.

## Grundsätzliche HTL-Konzepte {#fundamental-concepts-of-htl}

Die HTML-Vorlagensprache verwendet eine Ausdruckssprache, um Teile des Inhalts in das gerenderte Markup einzufügen, und HTML5-Datenattribute, um Anweisungen über Blöcke von Markup (wie Bedingungen oder Iterationen) zu definieren. Wenn HTL in Java-Servlets kompiliert wird, werden die Ausdrücke und die HTL-Datenattribute komplett Server-seitig ausgewertet. Zudem ist in der HTML-Ausgabe nichts mehr davon sichtbar.

>[!TIP]
>
>Zum Ausführen der meisten auf dieser Seite bereitgestellten Beispiele kann eine als [Read Eval Print Loop (REPL)](https://github.com/adobe/aem-htl-repl) bezeichnete Live-Ausführungsumgebung verwendet werden.

### Blöcke und Ausdrücke  {#blocks-and-expressions}

Hier finden Sie ein erstes Beispiel, das in der Datei `template.html` enthalten sein könnte:

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

Es lassen sich zwei verschiedene Arten von Syntaxen unterscheiden:

* **Block-Anweisungen** – Um das `<h1>`-Element bedingt anzuzeigen, wird ein `data-sly-test`-HTML5-Datenattribut verwendet. HTL bietet mehrere solcher Attribute, die es ermöglichen, jedem HTML-Element ein bestimmtes Verhalten zuzuordnen, und die alle mit dem Präfix `data-sly` versehen sind.
* **Ausdruckssprache** – HTL-Ausdrücke werden durch die Zeichen `${` und `}` abgegrenzt. Diese Ausdrücke werden zur Laufzeit ausgewertet, und ihr Wert wird in den ausgehenden HTML-Stream eingeschleust.

Siehe die [HTL-Spezifikation](specification.md) für Details zu beiden Syntaxen.

### Das SLY-Element  {#the-sly-element}

Ein zentrales Konzept von HTL ist die Möglichkeit, vorhandene HTML-Elemente wiederzuverwenden, um Blockanweisungen zu definieren, ohne dass zusätzliche Begrenzungszeichen eingefügt werden müssen, die festlegen, wo die Anweisung beginnt und endet. Diese unauffällige Anmerkung des Markups zur Umwandlung eines statischen HTML-Codes in eine funktionierende dynamische Vorlage bietet den Vorteil, dass die Gültigkeit des HTML-Codes nicht beeinträchtigt wird und daher auch als statische Dateien weiterhin korrekt angezeigt werden.

Es kann jedoch vorkommen, dass an der Stelle, an der eine Blockanweisung eingefügt werden soll, kein Element vorhanden ist. Für solche Fälle ist es möglich, ein spezielles `sly`-Element einzufügen, das automatisch aus der Ausgabe entfernt wird, während die angehängten Blockanweisungen ausgeführt werden und ihr Inhalt entsprechend angezeigt wird.

Im folgenden Beispiel...

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

... wird etwas wie das folgende HTML ausgegeben, aber nur, wenn sowohl eine `jcr:title`- als auch eine `jcr:description`-Eigenschaft definiert sind und keine der beiden leer ist:

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

Zu beachten ist, dass das Element `sly` nur dann verwendet werden sollte, wenn kein vorhandenes Element mit der Blockanweisung versehen werden konnte. Dies liegt daran, dass `sly`-Elemente den Wert der Sprache mindern, der darin besteht, das statische HTML nicht zu verändern, wenn es dynamisch gemacht wird.

Wäre das vorherige Beispiel beispielsweise in ein `div`-Element eingeschlossen worden, dann wäre das hinzugefügte `sly`-Element missbräuchlich:

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

und das Element `div` hätte mit der Bedingung versehen werden können:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

### HTL-Kommentare  {#htl-comments}

Das folgende Beispiel zeigt einen HTL-Kommentar in der ersten Zeile und einen HTML-Kommentar in der zweiten Zeile.

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

HTL-Kommentare sind HTML-Kommentare mit einer zusätzlichen JavaScript-ähnlichen Syntax. Der gesamte HTL-Kommentar und alles, was darin steht, wird vom Prozessor vollständig ignoriert und aus der Ausgabe entfernt.

Der Inhalt von Standard-HTML-Kommentaren wird jedoch durchgelassen und die Ausdrücke innerhalb des Kommentars werden ausgewertet.

HTML-Kommentare können keine HTL-Kommentare enthalten und umgekehrt.

### Spezielle Kontexte  {#special-contexts}

Um HTL bestmöglich zu verwenden, ist es wichtig, die Konsequenzen zu verstehen, die sich daraus ergeben, dass sie auf der HTML-Syntax basiert.

Weitere Einzelheiten finden Sie im Abschnitt [Anzeigekontext](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#121-display-context) der HTL-Spezifikation.

### Element- und Attributnamen  {#element-and-attribute-names}

Ausdrücke können nur in HTML-Text oder Attributwerte eingefügt werden, nicht aber in Element- oder Attributnamen, sonst wäre es kein gültiges HTML mehr. Zum dynamischen Festlegen der Elementnamen kann die Anweisung `data-sly-element` für die gewünschten Elemente verwendet werden und zum dynamischen Festlegen der Attributnamen oder sogar zum Festlegen von mehreren Attributen gleichzeitig, die Anweisung `data-sly-attribute`.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### Kontexte ohne Blockanweisungen {#contexts-without-block-statements}

Da HTL Datenattribute zur Definition von Blockanweisungen verwendet, ist es nicht möglich, solche Blockanweisungen in folgenden Kontexten zu definieren, und es können dort nur Ausdrücke verwendet werden:

* HTML-Kommentare
* Skript-Elemente
* Stil-Elemente

Der Grund besteht darin, dass der Inhalt dieser Kontexte Text ist und keine HTML, wobei darin enthaltene HTML-Elemente als einfache Zeichendaten erkannt werden. Ohne echte HTML-Elemente ist die Ausführung von `data-sly`-Attributen demnach nicht möglich.

Dies mag nach einer erheblichen Einschränkung klingen, ist jedoch erwünscht, da die HTML Template Language nicht dazu missbraucht werden sollte, Ausgaben zu erzeugen, die nicht HTML sind. Im Abschnitt [Anwendungs-API für den Zugriff auf die Logik](#use-api-for-accessing-logic) unten wird erläutert, wie zusätzliche Logik über die Vorlage abgerufen werden kann, die verwendet werden kann, wenn die komplexe Ausgabe für diese Kontexte vorbereitet werden muss. Zum Beispiel kann die Logik der Komponente zum einfachen Senden von Daten vom Back-End- zum Front-End-Skript eine JSON-Zeichenfolge generieren, die anschließend in einem Datenattribut mit einem einfachen HTL-Ausdruck platziert werden kann.

Das folgende Beispiel veranschaulicht das Verhalten für HTML-Kommentare, aber in Skript- oder Stilelementen würde das gleiche Verhalten beobachtet werden:

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

gibt etwas wie das folgende HTML aus:

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### Explizite Kontexte erforderlich  {#explicit-contexts-required}

Wie im unten folgenden Abschnitt [Automatische kontextsensitive Maskierung](#automatic-context-aware-escaping) erläutert, besteht ein Ziel der HTL darin, das Risiko von Sicherheitslücken beim Cross-Site-Scripting (XSS) zu reduzieren, indem automatisch auf alle Ausdrücke die kontextsensitive Maskierung angewendet wird. Während HTL den Kontext von Ausdrücken, die innerhalb von HTML-Markup platziert sind, automatisch erkennen kann, analysiert es nicht die Syntax von Inline-JavaScript oder CSS. Es ist daher darauf angewiesen, dass die Entwicklerin oder der Entwickler explizit angibt, welcher genaue Kontext auf solche Ausdrücke angewendet werden muss.

Da die Nichtanwendung der korrekten Maskierung zu XSS-Schwachstellen führt, entfernt HTL daher die Ausgabe aller Ausdrücke, die sich in Skript- und Stilkontexten befinden, wenn der Kontext nicht angegeben wurde.

Hier ist ein Beispiel dafür, wie der Kontext für Ausdrücke innerhalb von Skripten und Stilen festgelegt wird:

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

Weitere Einzelheiten zur Steuerung des Escapings finden Sie im Abschnitt [Anzeigekontext der Ausdruckssprache](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) der HTL-Spezifikationen.

## Allgemeine HTL-Funktionen  {#general-capabilities-of-htl}

In diesem Abschnitt werden die allgemeinen Funktionen der HTML-Vorlagensprache kurz behandelt.

### Anwendungs-API für den Zugriff auf die Logik  {#use-api-for-accessing-logic}

Mit der Java-Anwendungs-API der HTML-Vorlagensprache (HTL) wird es einer HTL-Datei ermöglicht, in einer benutzerdefinierten Java-Klasse über `data-sly-use` auf die Hilfsmethoden zuzugreifen. Dadurch kann die gesamte komplexe Geschäftslogik im Java-Code verschachtelt werden, während der HTL-Code nur die direkte Markup-Produktion verarbeiten muss.

Siehe das Dokument [HTL-Java-Anwendungs-API](java-use-api.md) für weitere Details.

### Automatische kontextsensitive Maskierung {#automatic-context-aware-escaping}

Siehe folgendes Beispiel:

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

In den meisten Vorlagensprachen führt dieses Beispiel potenziell zu Sicherheitslücken beim Cross-Site-Scripting (XSS), da, selbst wenn alle Variablen automatisch HTML-maskiert sind, das Attribut `href` weiterhin speziell URL-maskiert werden muss. Dieses Versäumnis ist einer der häufigsten Fehler, da es leicht vergessen werden kann und nur schwer automatisiert zu erkennen ist.

Zur Abhilfe maskiert die HTML-Vorlagensprache jede Variable automatisch gemäß dem Kontext, in dem sie platziert wird. Dies ist möglich, da HTL die Syntax von HTML versteht.

Bei Annahme der folgenden `logic.js`-Datei:

```javascript
use(function () {
    return {
        link:  "#my link's safe",
        title: "my title's safe",
        text:  "my text's safe"
    };
});
```

Das anfängliche Beispiel hat die folgende Ausgabe zur Folge:

```xml
<p>
    <a href="#my%20link%27s%20safe" title="my title&#39;s safe">
        my text&#39;s safe
    </a>
</p>
```

Beachten Sie, dass die beiden Attribute unterschiedlich mit Escapezeichen versehen wurden, da HTL weiß, dass die Attribute `href` und `src` für den URI-Kontext mit Escapezeichen versehen werden müssen. Falls der URI mit `javascript:` beginnt, wird das Attribut vollständig entfernt, sofern der Kontext nicht explizit in etwas anderes geändert wurde.

Weitere Einzelheiten zur Steuerung des Escapings finden Sie im Abschnitt [Anzeigekontext der Ausdruckssprache](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) der HTL-Spezifikationen.

### Automatisches Entfernen leerer Attribute {#automatic-removal-of-empty-attributes}

Siehe folgendes Beispiel:

```xml
<p class="${properties.class}">some text</p>
```

Wenn der Wert der Eigenschaft `class` leer ist, entfernt die HTML-Vorlagensprache automatisch das gesamte `class`-Attribut aus der Ausgabe.

Wieder ist dies deswegen möglich, weil HTL die HTML-Syntax versteht und daher Attribute mit dynamischen Werten nur dann anzeigen kann, wenn ihr Wert nicht leer ist. Dies ist sehr praktisch, da es das Hinzufügen eines Bedingungsblocks um die Attribute herum vermeidet, was das Markup ungültig und unlesbar gemacht hätte.

Außerdem ist der Typ der im Ausdruck enthaltenen Variablen von Bedeutung:

* **Zeichenfolge:**
   * **nicht leer:** Setzt die Zeichenfolge als Attributwert.
   * **leer:** Entfernt das Attribut vollständig.

* **Zahl:** Legt den Wert als Attributwert fest.

* **Boolesch:**
   * **true:** Zeigt das Attribut ohne Wert an (als ein boolesches HTML-Attribut
   * **false:** Entfernt das Attribut vollständig.

Hier ist ein Beispiel dafür, wie ein boolescher Ausdruck die Kontrolle über ein boolesches HTML-Attribut ermöglichen würde:

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

Zum Festlegen von Attributen ist die Anweisung `data-sly-attribute` möglicherweise ebenfalls hilfreich.

## Allgemeine Muster mit HTL {#common-patterns-with-htl}

In diesem Abschnitt werden ein paar allgemeine Szenarien vorgestellt. Zudem wird aufgezeigt, wie diese mit der HTML-Vorlagensprache am besten gelöst werden können.

### Laden von Client-Bibliotheken {#loading-client-libraries}

In HTL werden Client-Bibliotheken über eine durch AEM bereitgestellte Hilfsvorlage geladen, auf die über `data-sly-use` zugegriffen werden kann. In dieser Datei sind drei Vorlagen verfügbar, die über `data-sly-call` abgerufen werden können:

* **`css`** – Lädt nur die CSS-Dateien der referenzierten Client-Bibliotheken.
* **`js`** – Lädt nur die JavaScript-Dateien der referenzierten Client-Bibliotheken.
* **`all`** – Lädt alle Dateien der referenzierten Client-Bibliotheken (CSS und JavaScript).

Jede Hilfsvorlage erwartet eine `categories`-Option für das Referenzieren der gewünschten Client-Bibliotheken. Diese Option kann entweder ein Array von Zeichenfolgenwerten oder eine Zeichenfolge mit einer durch Kommata getrennten Werteliste sein.

Es folgen zwei kurze Beispiele.

#### Vollständiges Laden mehrerer Client-Bibliotheken gleichzeitig  {#loading-multiple-client-libraries-fully-at-once}

```xml
<sly data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html"
     data-sly-call="${clientlib.all @ categories=['myCategory1', 'myCategory2']}"/>
```

#### Referenzieren einer Client-Bibliothek in unterschiedlichen Abschnitten einer Seite {#referencing-a-client-library-in-different-sections-of-a-page}

```xml
<!doctype html>
<html data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html">
    <head>
        <!-- HTML meta-data -->
        <sly data-sly-call="${clientlib.css @ categories='myCategory'}"/>
    </head>
    <body>
        <!-- page content -->
        <sly data-sly-call="${clientlib.js @ categories='myCategory'}"/>
    </body>
</html>
```

Wenn in diesem Beispiel die HTML-Elemente `head` und `body` in verschiedenen Dateien platziert sind, müsste die Vorlage `clientlib.html` in jede Datei geladen werden, die sie benötigt.

Der Abschnitt über die Template- und Call-Anweisungen in der [HTL-Spezifikation](specification.md) enthält weitere Details darüber, wie das Deklarieren und Aufrufen solcher Templates funktioniert.

### Weitergeben von Daten an den Client {#passing-data-to-the-client}

Die beste und eleganteste Art, Daten an den Client zu übermitteln, ist im Allgemeinen die Verwendung von `data`-Attributen, erst recht aber bei HTL.

Das folgende Beispiel zeigt, wie die Logik (die auch in Java geschrieben werden könnte) verwendet werden kann, um das Objekt, das an den Client übergeben werden soll, bequem in JSON zu serialisieren, das dann einfach in ein `data`-Attribut eingefügt werden kann:

```xml
<!--/* template.html file: */-->
<div data-sly-use.logic="logic.js" data-json="${logic.json}">...</div>
```

```javascript
/* logic.js file: */
use(function () {
    var myData = {
        str: "foo",
        arr: [1, 2, 3]
    };

    return {
        json: JSON.stringify(myData)
    };
});
```

Von dort aus ist es einfach, sich vorzustellen, wie ein Client-seitiges JavaScript-Element auf dieses Attribut zugreifen und das JSON-Objekt erneut analysieren kann. Dies wäre z. B. das entsprechende JavaScript, das in eine Client-Bibliothek einzufügen wäre:

```javascript
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### Arbeiten mit Client-seitigen Vorlagen  {#working-with-client-side-templates}

Ein spezieller Fall, in dem die im Abschnitt [Einschränkungen für Erhebungen spezieller Kontexte](#lifting-limitations-of-special-contexts) erläuterte Technik legitimerweise verwendet werden kann, besteht im Schreiben von Client-seitigen Vorlagen (beispielsweise Handlebars), die sich in `scrip`-Elementen befinden. Der Grund, weshalb diese Technik in diesem Fall sicher verwendet werden kann, besteht darin, dass das `script`-Element nicht wie angenommen JavaScript-Elemente enthält, sondern weitere HTML-Elemente. Im Folgenden finden Sie ein Beispiel, wie dies funktionieren würde:

```xml
<!--/* template.html file: */-->
<script id="entry-section" type="text/template"
    data-sly-include="entry-section.html"></script>

<!--/* entry-section.html file: */-->
<div class="entry-section">
    <h2 data-sly-test="${properties.entrySectionTitle}">
        ${properties.entrySectionTitle}
    </h2>
    {{#each entry}}<h3><a href="{{link}}">{{title}}</a></h3>{{/each}}
</div>
```

Wie oben gezeigt, kann das Markup, das in das `script`-Element aufgenommen wird, HTL-Blockanweisungen enthalten, und die Ausdrücke müssen keine expliziten Kontexte angeben, da der Inhalt der Handlebars-Vorlage in einer eigenen Datei isoliert wurde. In diesem Beispiel wird zudem gezeigt, wie die Server-seitig ausgeführte HTL (wie im Element `h2`) mit einer Client-seitig ausgeführten Vorlagensprache, beispielsweise Handlebars (im Element `h3` gezeigt), kombiniert werden kann.

Eine modernere Technik bestünde jedoch darin, stattdessen das HTML-Element `template` zu verwenden. Damit würde sich die Notwendigkeit erübrigen, die Inhalte der Vorlagen in separaten Dateien zu isolieren.

### Einschränkungen für Erhebungen spezieller Kontexte {#lifting-limitations-of-special-contexts}

In den besonderen Fällen, in denen es erforderlich ist, die Beschränkungen des Skript-, Stil- und Kommentar-Kontextes zu umgehen, ist es möglich, deren Inhalt in einer separaten HTL-Datei zu isolieren. Alles in seiner eigenen Datei Befindliche wird durch HTL als normales HTML-Fragment interpretiert unter Nichtberücksichtigung des begrenzten Kontexts, in dem es möglicherweise enthalten war.

Ein Beispiel finden Sie weiter unten im Abschnitt [Arbeiten mit Client-seitigen Vorlagen](#working-with-client-side-templates).

>[!CAUTION]
>
>Diese Technik kann zu Schwachstellen beim Cross-Site-Scripting (XSS) führen, und die Sicherheitsaspekte sollten sorgfältig geprüft werden, wenn diese Technik verwendet werden muss. Es gibt für gewöhnlich bessere Möglichkeiten für die Implementierung desselben Aspekts, als sich auf diese Praktik zu verlassen.
