---
title: Erste Schritte mit HTL
description: Erfahren Sie mehr über HTL, das bevorzugte und empfohlene serverseitige Vorlagensystem für das HTML in AEM, und verstehen Sie die wichtigsten Sprachkonzepte und ihre grundlegenden Konstrukte.
exl-id: c95eb1b3-3b96-4727-8f4f-d54e7136a8f9
source-git-commit: 5ab1275c984135fe946f36905bbc979cf19edd80
workflow-type: tm+mt
source-wordcount: '2170'
ht-degree: 56%

---


# Erste Schritte mit HTL {#getting-started-with-htl}

HTML Template Language (HTL) ist das bevorzugte und empfohlene serverseitige Vorlagensystem zum HTML in Adobe Experience Manager. Wie in allen serverseitigen Vorlagensystemen von HTML definiert eine HTL-Datei die an den Browser gesendete Ausgabe, indem sie die HTML selbst, eine grundlegende Darstellungslogik und Variablen angibt, die zur Laufzeit ausgewertet werden sollen.

Dieses Dokument gibt einen Überblick über den Zweck von HTL sowie eine Einführung in grundlegende Konzepte und Konstrukte der Sprache.

>[!TIP]
>
>In diesem Dokument werden der Zweck von HTL und ein Überblick über die grundlegende Struktur und Konzepte des HTL-Codes vorgestellt. Wenn Sie Fragen zur spezifischen Syntax haben, lesen Sie bitte den Abschnitt [HTL-Spezifikation.](specification.md)

## HTL-Ebenen {#layers}

Die in AEM verwendete HTL kann durch eine Reihe von Ebenen definiert werden.

1. **[HTL-Spezifikation](specification.md)** - HTL ist eine plattformunabhängige Open-Source-Spezifikation, die jeder implementieren kann.
1. **[Sling HTL Scripting Engine](specification.md)** - Das Sling-Projekt hat die Referenzimplementierung von HTL erstellt, die von AEM verwendet wird.
1. **[AEM-Erweiterungen](specification.md)** - AEM baut auf der Sling HTL Scripting Engine auf, um Entwicklern benutzerfreundliche AEM-spezifische Funktionen zu bieten.

Diese HTL-Dokumentation konzentriert sich auf die Verwendung von HTL zur Entwicklung AEM Lösungen. Dadurch werden alle drei Ebenen berührt und externe Ressourcen bei Bedarf verknüpft.

## Grundsätzliche HTL-Konzepte {#fundamental-concepts-of-htl}

Die HTML-Vorlagensprache verwendet eine Ausdruckssprache zum Einfügen von Inhaltsteilen in das gerenderte Markup und über HTML5-Datenattribute zum Definieren von Anweisungen über Markup-Blöcke (wie Bedingungen oder Wiederholungen). Wenn HTL in Java-Servlets kompiliert wird, werden die Ausdrücke und die HTL-Datenattribute komplett Server-seitig ausgewertet. Zudem ist in der HTML-Ausgabe nichts mehr davon sichtbar.

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

* **Blockanweisungen** - So zeigen Sie die `<h1>` -Element, ein `data-sly-test` Das Datenattribut HTML5 wird verwendet. HTL stellt mehrere solcher Attribute bereit, die das Anhängen von Verhalten an ein HTML-Element ermöglichen, wobei allen vorangestellt wird. `data-sly`.
* **Ausdruckssprache** - HTL-Ausdrücke werden durch die `${` und `}` Zeichen. Diese Ausdrücke werden zur Laufzeit ausgewertet und ihr Wert wird in den ausgehenden HTML-Stream eingeschleust.

Siehe [HTL-Spezifikation](specification.md) für Details zu beiden Syntaxen.

### Das SLY-Element  {#the-sly-element}

Ein zentrales HTL-Konzept besteht darin, die Möglichkeit zu bieten, bestehende HTML-Elemente zur Definition von Blockanweisungen wiederzuverwenden, wodurch verhindert wird, dass zusätzliche Trennzeichen eingefügt werden, um zu definieren, wo die Anweisung beginnt und endet. Diese unauffällige Annotation des Markups zur Umwandlung einer statischen HTML in eine funktionierende dynamische Vorlage ermöglicht es, dass die Gültigkeit des HTML-Codes nicht gebrochen wird, wodurch weiterhin die ordnungsgemäße Anzeige erfolgt, sogar als statische Dateien.

Manchmal ist jedoch möglicherweise kein Element an der exakten Position vorhanden, an der eine Blockanweisung eingefügt werden muss. In solchen Fällen ist es möglich, eine `sly` -Element, das automatisch aus der Ausgabe entfernt wird, während die angehängten Blockanweisungen ausgeführt und der Inhalt entsprechend angezeigt wird.

Im folgenden Beispiel...

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

...gibt etwas wie das folgende HTML aus, aber nur, wenn sowohl eine `jcr:title`- als auch eine `jcr:description`-Eigenschaft definiert sind und keine der beiden leer ist:

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

Eine Sache, die Sie beachten sollten, ist nur die Verwendung der `sly` -Element ein, wenn mit der Blockanweisung kein vorhandenes Element hätte kommentiert werden können. Dies liegt daran, dass `sly` -Elemente verhindern, dass der von der Sprache angebotene Wert die statische HTML ändert, wenn sie dynamisch wird.

Wenn das vorherige Beispiel beispielsweise in eine `div` -Element und dann das hinzugefügte `sly` -Element missbräuchlich sein:

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

und `div` -Element mit der Bedingung hätte kommentiert werden können:

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

HTL-Kommentare sind HTML-Kommentare mit einer zusätzlichen JavaScript-artigen Syntax. Der gesamte HTL-Kommentar und alles darin Enthaltene werden vollständig durch den Prozessor ignoriert und aus der Ausgabe entfernt.

Die Inhalte der HTML-Standardkommentare werden jedoch weitergegeben und die Ausdrücke im Kommentar werden ausgewertet.

HTML-Kommentare dürfen keine HTL-Kommentare enthalten und umgekehrt.

### Spezielle Kontexte  {#special-contexts}

Um HTL bestmöglich zu verwenden, ist es wichtig, die Konsequenzen zu verstehen, die sich daraus ergeben, dass sie auf der HTML-Syntax basiert.

Weitere Informationen finden Sie unter [Anzeigekontext-Abschnitt](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#121-display-context) der HTL-Spezifikation für weitere Details.

### Element- und Attributnamen  {#element-and-attribute-names}

Ausdrücke können nur in HTML-Text- oder -Attributwerten platziert werden, jedoch nicht in Elementnamen oder Attributnamen, oder sie wären nicht mehr gültig HTML. Zum dynamischen Festlegen der Elementnamen kann die Anweisung `data-sly-element` für die gewünschten Elemente verwendet werden und zum dynamischen Festlegen der Attributnamen oder sogar zum Festlegen von mehreren Attributen gleichzeitig, die Anweisung `data-sly-attribute`.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### Kontexte ohne Blockanweisungen {#contexts-without-block-statements}

Da HTL Datenattribute zum Definieren von Blockanweisungen verwendet, ist es nicht möglich, solche Blockanweisungen in den folgenden Kontexten zu definieren, und es können dort nur Ausdrücke verwendet werden:

* HTML-Kommentare
* Skriptelemente
* Style-Elemente

Der Grund besteht darin, dass der Inhalt dieser Kontexte Text ist und keine HTML, wobei darin enthaltene HTML-Elemente als einfache Zeichendaten erkannt werden. Ohne echte HTML-Elemente ist die Ausführung von `data-sly`-Attributen demnach nicht möglich.

Dies klingt möglicherweise wie eine wesentliche Einschränkung, ist jedoch erwünscht, da die HTML-Vorlagensprache nicht dazu missbraucht werden sollte, Ausgabe zu generieren, die nicht HTML ist. Im Abschnitt [Anwendungs-API für den Zugriff auf die Logik](#use-api-for-accessing-logic) unten wird erläutert, wie zusätzliche Logik über die Vorlage abgerufen werden kann, die verwendet werden kann, wenn die komplexe Ausgabe für diese Kontexte vorbereitet werden muss. Zum Beispiel kann die Logik der Komponente zum einfachen Senden von Daten vom Back-End- zum Front-End-Skript eine JSON-Zeichenfolge generieren, die anschließend in einem Datenattribut mit einem einfachen HTL-Ausdruck platziert werden kann.

Im folgenden Beispiel wird das Verhalten für HTML-Kommentare veranschaulicht, jedoch lässt sich in Skript- oder Style-Elementen dasselbe Verhalten beobachten:

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

gibt etwas wie die folgende HTML aus:

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### Explizite Kontexte erforderlich  {#explicit-contexts-required}

Wie im unten folgenden Abschnitt [Automatische kontextsensitive Maskierung](#automatic-context-aware-escaping) erläutert, besteht ein Ziel der HTL darin, das Risiko von Sicherheitslücken beim Cross-Site-Scripting (XSS) zu reduzieren, indem automatisch auf alle Ausdrücke die kontextsensitive Maskierung angewendet wird. Auch wenn HTL automatisch den Kontext der im HTML-Markup platzierten Ausdrücke erkennen kann, analysiert sie nicht die Syntax der Inline-JavaScript oder CSS und verlässt sich daher darauf, dass der Entwickler explizit angibt, welcher exakte Kontext auf solche Ausdrücke anzuwenden ist.

Da die Nichtanwendung der richtigen Maskierung zu XSS-Sicherheitslücken führt, entfernt HTL daher die Ausgabe sämtlicher in Skript- und Style-Kontexten befindlichen Ausdrücke, sofern der Kontext nicht deklariert wurde.

Im Folgenden finden Sie ein Beispiel hinsichtlich der Festlegung des Kontexts für die in Skripts und Styles platzierten Ausdrücke:

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

Weitere Informationen zum Steuern der Maskierung finden Sie im Abschnitt [Ausdruckssprachanzeigekontext](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) Abschnitt der HTL-Spezifikationen.

## Allgemeine HTL-Funktionen  {#general-capabilities-of-htl}

In diesem Abschnitt werden die allgemeinen Funktionen der HTML-Vorlagensprache kurz behandelt.

### Anwendungs-API für den Zugriff auf die Logik  {#use-api-for-accessing-logic}

Mit der Java-Anwendungs-API der HTML-Vorlagensprache (HTL) wird es einer HTL-Datei ermöglicht, in einer benutzerdefinierten Java-Klasse über `data-sly-use` auf die Hilfsmethoden zuzugreifen. Dadurch kann die gesamte komplexe Geschäftslogik im Java-Code verschachtelt werden, während der HTL-Code nur die direkte Markup-Produktion verarbeiten muss.

Siehe Dokument . [HTL-Java-Anwendungs-API](java-use-api.md) für weitere Details.

### Automatische kontextsensitive Maskierung {#automatic-context-aware-escaping}

Siehe folgendes Beispiel:

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

In den meisten Vorlagensprachen führt dieses Beispiel potenziell zu Sicherheitslücken beim Cross-Site-Scripting (XSS), da, selbst wenn alle Variablen automatisch HTML-maskiert sind, das Attribut `href` weiterhin speziell URL-maskiert werden muss. Diese Unterlassung ist einer der häufigsten Fehler, da sie leicht vergessen werden kann, und es schwierig ist, sie automatisiert zu erkennen.

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

Beachten Sie, dass die beiden Attribute unterschiedlich maskiert wurden, da HTL weiß, dass `href` und `src` -Attribute müssen für den URI-Kontext maskiert werden. Falls der URI mit `javascript:` beginnt, wird das Attribut vollständig entfernt, sofern der Kontext nicht explizit in etwas anderes geändert wurde.

Weitere Informationen zum Steuern der Maskierung finden Sie im Abschnitt [Ausdruckssprachanzeigekontext](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) Abschnitt der HTL-Spezifikationen.

### Automatisches Entfernen leerer Attribute {#automatic-removal-of-empty-attributes}

Siehe folgendes Beispiel:

```xml
<p class="${properties.class}">some text</p>
```

Wenn der Wert der Eigenschaft `class` leer ist, entfernt die HTML-Vorlagensprache automatisch das gesamte `class`-Attribut aus der Ausgabe.

Auch dies ist möglich, da HTL die HTML-Syntax versteht und daher Attribute nur dann bedingt mit dynamischen Werten anzeigen kann, wenn ihr Wert nicht leer ist. Dies ist sehr praktisch, da dadurch verhindert wird, dass Attribute mit einem Bedingungsblock versehen werden, wodurch das Markup ungültig und unlesbar geworden wäre.

Zusätzlich ist der im Ausdruck platzierte Variablentyp von Bedeutung:

* **Zeichenfolge:**
   * **nicht leer:** Legt die Zeichenfolge als Attributwert fest.
   * **leer:** Entfernt das Attribut vollständig.

* **Zahl:** Legt den Wert als Attributwert fest.

* **Boolesch:**
   * **true:** Zeigt das Attribut ohne Wert an (als ein boolesches HTML-Attribut
   * **false:** Entfernt das Attribut vollständig.

Im Folgenden finden Sie ein Beispiel dafür, wie ein boolescher Ausdruck die Steuerung eines booleschen HTML-Attributs zulässt:

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

Jede Hilfsvorlage erwartet eine `categories`-Option für das Referenzieren der gewünschten Client-Bibliotheken. Bei dieser Option kann es sich entweder um ein Array von Zeichenfolgenwerten oder um eine Zeichenfolge handeln, die eine kommagetrennte Werteliste enthält.

Im Folgenden finden Sie zwei kurze Beispiele.

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

In diesem Beispiel wird die HTML `head` und `body` -Elemente in verschiedenen Dateien platziert werden, wird die `clientlib.html` dann in jede Datei geladen werden, die sie benötigt.

Der Abschnitt zu den Anweisungen template &amp; call im [HTL-Spezifikation](specification.md) liefert weitere Details darüber, wie das Deklarieren und Aufrufen solcher Vorlagen funktioniert.

### Weitergeben von Daten an den Client {#passing-data-to-the-client}

Die beste und eleganteste Methode, Daten an den Client im Allgemeinen zu übergeben, aber noch mehr mit HTL, besteht darin, `data` -Attribute.

Das folgende Beispiel zeigt, wie die Logik (die auch in Java geschrieben werden kann) verwendet werden kann, um das Objekt, das an den Client übergeben werden soll, bequem in JSON zu serialisieren, was dann einfach in eine `data` Attribut:

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

Von dort aus ist es einfach, sich vorzustellen, wie ein Client-seitiges JavaScript-Element auf dieses Attribut zugreifen und das JSON-Objekt erneut analysieren kann. Dies wäre beispielsweise das entsprechende JavaScript, das in eine Client-Bibliothek platziert werden soll:

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

Wie oben gezeigt, das Markup, das in der `script` -Element kann HTL-Blockanweisungen enthalten und die Ausdrücke müssen keine expliziten Kontexte bereitstellen, da der Inhalt der Handlebars-Vorlage in ihrer eigenen Datei isoliert wurde. In diesem Beispiel wird zudem gezeigt, wie die Server-seitig ausgeführte HTL (wie im Element `h2`) mit einer Client-seitig ausgeführten Vorlagensprache, beispielsweise Handlebars (im Element `h3` gezeigt), kombiniert werden kann.

Eine modernere Technik bestünde jedoch darin, stattdessen das HTML-Element `template` zu verwenden. Damit würde sich die Notwendigkeit erübrigen, die Inhalte der Vorlagen in separaten Dateien zu isolieren.

### Einschränkungen für Erhebungen spezieller Kontexte {#lifting-limitations-of-special-contexts}

In den Sonderfällen, in denen die Einschränkungen der Skript-, Stil- und Kommentarkontexte umgangen werden müssen, ist es möglich, ihren Inhalt in einer separaten HTL-Datei zu isolieren. Alles in seiner eigenen Datei Befindliche wird durch HTL als normales HTML-Fragment interpretiert unter Nichtberücksichtigung des begrenzten Kontexts, in dem es möglicherweise enthalten war.

Ein Beispiel finden Sie weiter unten im Abschnitt [Arbeiten mit Client-seitigen Vorlagen](#working-with-client-side-templates).

>[!CAUTION]
>
>Diese Technik kann Sicherheitslücken bei Cross-Site Scripting (XSS) verursachen, und die Sicherheitsaspekte sollten sorgfältig untersucht werden, wenn dies verwendet werden muss. Es gibt für gewöhnlich bessere Möglichkeiten für die Implementierung desselben Aspekts, als sich auf diese Praktik zu verlassen.
