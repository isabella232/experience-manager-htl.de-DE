---
title: Erste Schritte mit HTL
description: Die von AEM unterstützte Vorlagensprache HTL löst JSP als bevorzugtes und empfohlenes serverseitiges Vorlagensystem für HTML in AEM ab.
translation-type: tm+mt
source-git-commit: c7fa6014cd954a2ccb175e4c3a6be9deb83af890
workflow-type: tm+mt
source-wordcount: '2471'
ht-degree: 92%

---


# Erste Schritte mit HTL {#getting-started-with-htl}

Die HTML-Vorlagensprache (HTL), die von Adobe Experience Manager (AEM) unterstützt wird, ist das bevorzugte und empfohlene serverseitige Vorlagensystem für HTML in AEM. Sie wird an die Stelle von JSP (JavaServer Pages) gesetzt, wie sie in früheren Versionen von AEM verwendet wurde.

>[!NOTE]
>
>Zum Ausführen der meisten auf dieser Seite bereitgestellten Beispiele kann eine als [Read Eval Print Loop (REPL)](https://github.com/Adobe-Marketing-Cloud/aem-htl-repl) bezeichnete Live-Ausführungsumgebung verwendet werden.

## HTL über JSP {#htl-over-jsp}

Für neue AEM-Projekte sollte die HTML-Vorlagensprache verwendet werden, da sie gegenüber JSP mehrere Vorteile hat. Bei bestehenden Projekten macht eine Migration jedoch nur Sinn, wenn abschätzbar ist, dass dies weniger aufwändig ist, als die vorhandenen JSPs für die kommenden Jahre zu verwalten.

Der Wechsel zu HTL ist nicht zwangsläufig eine Alles-oder-Nichts-Entscheidung, da in HTL geschriebene Komponenten mit in JSP oder ESP geschriebenen Komponenten kompatibel sind. Vorhandene Projekte können demnach problemlos HTL für neue Komponenten und gleichzeitig JSP für vorhandene Komponenten verwenden.

HTL-Dateien können sogar in derselben Komponente zusammen mit JSPs und ESPs verwendet werden. Im folgenden Beispiel wird in **Zeile 1** gezeigt, wie eine HTL-Datei aus einer JSP-Datei eingeschlossen wird, und in **Zeile 2**, wie eine JSP-Datei aus einer HTL-Datei eingeschlossen werden kann:

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

### Häufig gestellte Fragen{#frequently-asked-questions}

Bevor wir mit der HTML-Vorlagensprache beginnen können, müssen wir im Voraus einige Fragen in Bezug auf das Thema JSP vs. HTL beantworten.

**Weist HTL Einschränkungen auf, die bei JSP nicht vorhanden sind?** - HTL weist im Vergleich zu JSP keine wirklichen Einschränkungen auf. Was mit JSP erledigt werden kann, sollte auch mit HTL erreichbar sein. HTL ist per Definition in verschiedener Hinsicht strenger als JSP. Was vielleicht in nur einer JSP-Datei erreicht werden kann, muss unter Umständen in eine Java-Klasse oder eine JavaScript-Datei separiert werden, damit es in HTL erreicht werden kann. Dies ist jedoch generell erwünscht, um eine entsprechende Trennung von Belangen zwischen der Logik und dem Markup sicherzustellen.

**Werden JSP-Tag-Bibliotheken durch HTL unterstützt?** - Nein, aber wie im Abschnitt  [Loading Client ](getting-started.md#loading-client-libraries) Librariessection gezeigt, weist das Angebot  [template &amp; ](block-statements.md#template-call) callstatement ein ähnliches Muster auf.

**Können die HTL-Funktionen für ein AEM-Projekt erweitert werden?** - Nein, das können sie nicht. HTL verfügt über leistungsstarke Erweiterungsmechanismen für die Wiederverwendung der Logik (die [Anwendungs-API](getting-started.md#use-api-for-accessing-logic)) und des Markups (die Anweisungen [template &amp; call](block-statements.md#template-call)), die verwendet werden können, um den Code der Projekte zu modularisieren.

**Was sind die Hauptvorteile von HTL im Vergleich zu JSP?** - Sicherheit und Projekteffizienz sind die Hauptvorteile, die im  [Überblick](overview.md) beschrieben werden.

**Wird JSP schließlich verschwinden?** - Für diese Zeilen gibt es derzeit keine Pläne.

## Grundsätzliche HTL-Konzepte {#fundamental-concepts-of-htl}

Die HTML-Vorlagensprache verwendet eine Ausdruckssprache zum Einfügen von Inhaltsteilen in das gerenderte Markup und über HTML5-Datenattribute zum Definieren von Anweisungen über Markup-Blöcke (wie Bedingungen oder Wiederholungen). Wenn HTL in Java-Servlets kompiliert wird, werden die Ausdrücke und die HTL-Datenattribute komplett serverseitig ausgewertet. Zudem ist in der HTML-Ausgabe nichts mehr davon sichtbar.

### Blöcke und Ausdrücke  {#blocks-and-expressions}

Hier finden Sie ein erstes Beispiel, das wie besehen in der Datei **`template.html`** enthalten sein könnte:

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

Es können zwei Syntaxarten unterschieden werden:

* **[Blockanweisungen](block-statements.md)**  - So zeigen Sie die  **&lt;h1>** -Element wird ein  [`data-sly-test`](block-statements.md#test) HTML5-Datenattribut verwendet. HTL stellt mehrere solcher Attribute bereit. Dadurch können HTML-Elementen Verhaltensweisen angehängt werden, wobei allen `data-sly` vorangestellt ist.

* **[Ausdruck Language](expression-language.md)**  - HTML-Ausdruck werden durch Zeichen  `${` und  `}`Zeichen getrennt. Diese Ausdrücke werden zur Laufzeit ausgewertet und ihr Wert wird in den ausgehenden HTML-Stream eingeschleust.

Die zwei Seiten, die oben verknüpft wurden, enthalten die detaillierte Liste der für die Syntax verfügbaren Funktionen.

### Das SLY-Element   {#the-sly-element}

Ein zentrales Konzept von HTL besteht darin, die Möglichkeit zu eröffnen, vorhandene HTML-Elemente zum Definieren von Blockanweisungen erneut zu verwenden. Dadurch erübrigt sich die Notwendigkeit, zusätzliche Trennzeichen einzufügen, um zu definieren, wo die Anweisung beginnt und endet. Diese unauffällige Annotation des Markups zur Umwandlung einer statischen HTML in eine funktionierende dynamische Vorlage ermöglicht es, dass die Gültigkeit des HTML-Codes nicht gebrochen wird, wodurch weiterhin die ordnungsgemäße Anzeige erfolgt, sogar als statische Dateien.

Manchmal ist jedoch möglicherweise kein Element an der exakten Position vorhanden, an der eine Blockanweisung eingefügt werden muss. In diesen Fällen ist es möglich, ein spezielles SLY-Element einzufügen, das automatisch aus der Ausgabe entfernt wird, während gleichzeitig die angehängten Blockanweisungen ausgeführt und die zugehörigen Inhalte entsprechend angezeigt werden.

Beispiel:

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

gibt etwas wie folgendes HTML aus, aber nur, wenn beide vorhanden sind, eine **`jcr:title`**- und eine **`jcr:description`**-Eigenschaft definiert sind und keiner von ihnen leer ist:

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

Zu beachten ist, dass das SLY-Element nur dann verwendet werden darf, wenn kein vorhandenes Element mit der block-Anweisung hätte versehen werden können, da SLY-Elemente den von der Sprache angebotenen Wert abschrecken, den statischen HTML-Code nicht zu ändern, wenn er dynamisch wird.

Wenn das vorherige Beispiel beispielsweise bereits in einem DIV-Element umbrochen worden wäre, wäre das hinzugefügte SLY-Element missbräuchlich:

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

und das DIV-Element könnte annotiert werden mit der Bedingung:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

### HTL-Kommentare   {#htl-comments}

Im folgenden Beispiel wird in **Zeile 1** ein HTL-Kommentar und in **Zeile 2** ein HTML-Kommentar gezeigt:

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

HTL-Kommentare sind HTML-Kommentare mit einer zusätzlichen JavaScript-artigen Syntax. Der gesamte HTL-Kommentar und alles darin Enthaltene werden vollständig durch den Prozessor ignoriert und aus der Ausgabe entfernt.

Die Inhalte der HTML-Standardkommentare werden jedoch weitergegeben und die Ausdrücke im Kommentar werden ausgewertet.

HTML-Kommentare dürfen keine HTL-Kommentare enthalten und umgekehrt.

### Spezielle Kontexte   {#special-contexts}

Um HTL bestmöglich zu verwenden, ist es wichtig, die Konsequenzen zu verstehen, die sich daraus ergeben, dass sie auf der HTML-Syntax basiert.

### Element- und Attributnamen   {#element-and-attribute-names}

Ausdrücke können nur in HTML-Text- oder -Attributwerten platziert werden, jedoch nicht in Elementnamen oder Attributnamen, sonst wäre die HTML nicht mehr gültig. Zum dynamischen Festlegen der Elementnamen kann die Anweisung [`data-sly-element`](block-statements.md#element) für die gewünschten Elemente verwendet werden und zum dynamischen Festlegen der Attributnamen oder sogar zum Festlegen von mehreren Attributen gleichzeitig, die Anweisung [`data-sly-attribute`](block-statements.md#attribute).

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### Kontexte ohne Blockanweisungen {#contexts-without-block-statements}

Da HTL Datenattribute zum Definieren von Blockanweisungen verwendet, ist es nicht möglich, solche Blockanweisungen in den folgenden Kontexten zu definieren, und es können dort nur Ausdrücke verwendet werden:

* HTML-Kommentare
* Skriptelemente
* Style-Elemente

Der Grund besteht darin, dass der Inhalt dieser Kontexte Text ist und keine HTML, wobei darin enthaltene HTML-Elemente als einfache Zeichendaten erkannt werden. Ohne echte HTML-Elemente ist die Ausführung von **`data-sly`**-Attributen demnach nicht möglich.

Dies hört sich möglicherweise wie eine große Einschränkung an, ist jedoch erwünscht, da die HTML-Vorlagensprache nicht zum Generieren von Ausgabe missbraucht werden soll, die nicht HTML ist. Im Abschnitt [Anwendungs-API für den Zugriff auf die Logik](getting-started.md#use-api-for-accessing-logic) unten wird erläutert, wie zusätzliche Logik über die Vorlage abgerufen werden kann, die verwendet werden kann, wenn die komplexe Ausgabe für diese Kontexte vorbereitet werden muss. Zum Beispiel kann die Logik der Komponente zum einfachen Senden von Daten vom Back-End- zum Front-End-Skript eine JSON-Zeichenfolge generieren, die anschließend in einem Datenattribut mit einem einfachen HTL-Ausdruck platziert werden kann.

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

### Explizite Kontexte erforderlich   {#explicit-contexts-required}

Wie im unten folgenden Abschnitt [Automatische kontextsensitive Maskierung](getting-started.md#automatic-context-aware-escaping) erläutert, besteht ein Ziel der HTL darin, das Risiko von Sicherheitslücken beim Cross-Site-Scripting (XSS) zu reduzieren, indem automatisch auf alle Ausdrücke die kontextsensitive Maskierung angewendet wird. Auch wenn HTL automatisch den Kontext der im HTML-Markup platzierten Ausdrücke erkennen kann, analysiert sie nicht die Syntax der Inline-JavaScript oder CSS und verlässt sich daher darauf, dass der Entwickler explizit angibt, welcher exakte Kontext auf solche Ausdrücke anzuwenden ist.

Da die Nichtanwendung der richtigen Maskierung zu XSS-Sicherheitslücken führt, entfernt HTL daher die Ausgabe sämtlicher in Skript- und Style-Kontexten befindlichen Ausdrücke, sofern der Kontext nicht deklariert wurde.

Im Folgenden finden Sie ein Beispiel hinsichtlich der Festlegung des Kontexts für die in Skripts und Styles platzierten Ausdrücke:

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

Weitere Details über das Steuern der Maskierung finden Sie im Abschnitt über den [Ausdruckssprachen-Anzeigekontext](expression-language.md#display-context).

### Einschränkungen für Erhebungen spezieller Kontexte {#lifting-limitations-of-special-contexts}

In den speziellen Fällen, in denen es erforderlich ist, die Einschränkungen der Skript-, Stil- und Kommentarkontexte zu umgehen, ist es möglich, den zugehörigen Inhalt in einer separaten HTL-Datei zu isolieren. Alles in seiner eigenen Datei Befindliche wird durch HTL als normales HTML-Fragment interpretiert unter Nichtberücksichtigung des begrenzten Kontexts, in dem es möglicherweise enthalten war.

Ein Beispiel finden Sie weiter unten im Abschnitt [Arbeiten mit clientseitigen Vorlagen](getting-started.md#working-with-client-side-templates).

>[!CAUTION]
>
>Diese Technik kann Sicherheitslücken beim Cross-Site-Scripting (XSS) hervorrufen. Daher sollten Sicherheitsaspekte sorgfältig geprüft werden, wenn sie verwendet wird. Es gibt für gewöhnlich bessere Möglichkeiten für die Implementierung desselben Aspekts, als sich auf diese Praktik zu verlassen.

## Allgemeine HTL-Funktionen   {#general-capabilities-of-htl}

In diesem Abschnitt werden die allgemeinen Funktionen der HTML-Vorlagensprache kurz behandelt.

### Anwendungs-API für den Zugriff auf die Logik   {#use-api-for-accessing-logic}

Siehe folgendes Beispiel:

```xml
<p data-sly-use.logic="logic.js">${logic.title}</p>
```

Die folgende serverseitig ausgeführte JavaScript-Datei mit dem Namen `logic.js` ist dabei daneben platziert:

```javascript
use(function () {
    return {
        title: currentPage.getTitle().substring(0, 10) + "..."
    };
});
```

Da die HTML-Vorlagensprache es nicht zulässt, Code im Markup zu vermischen, bietet sie stattdessen den Anwendungs-API-Erweiterungsmechanismus, um Code einfach über die Vorlage auszuführen.

Im obigen Beispiel wird ein serverseitiges ausgeführtes JavaScript-Element verwendet, um den Titel auf 10 Zeichen zu kürzen. Es wäre jedoch auch möglich gewesen, Java-Code zu verwenden, um dieselbe Aktion durchzuführen, indem ein vollqualifizierter Java-Klassenname angegeben worden wäre. Im Allgemeinen sollte Geschäftslogik nach Möglichkeit in Java erstellt werden. Wenn für die Komponente jedoch einige ansichtsspezifische Änderungen an den von der Java-API bereitgestellten Elementen vorgenommen werden müssen, kann es praktisch sein, dafür einige serverseitige ausgeführte JavaScript-Elemente zu verwenden.

Weitere Informationen dazu finden Sie in den folgenden Abschnitten:

* Der Abschnitt auf der [`data-sly-use`-Anweisung](block-statements.md#use) erklärt alles, was mit dieser Anweisung getan werden kann.
* Die [Anwendungs-API-Seite](use-api.md) enthält einige Informationen, die dabei helfen, sich zwischen dem Schreiben der Logik in Java oder in JavaScript zu entscheiden.
* Für detaillierte Informationen über das Schreiben der Logik sind die Seiten über die [JavaScript-Anwendungs-API](use-api-javascript.md) und die [Java-Anwendungs-API](use-api-java.md) hilfreich.

### Automatische kontextsensitive Maskierung {#automatic-context-aware-escaping}

Siehe folgendes Beispiel:

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

In den meisten Vorlagensprachen führt dieses Beispiel potenziell zu Sicherheitslücken beim Cross-Site-Scripting (XSS), da, selbst wenn alle Variablen automatisch HTML-maskiert sind, das Attribut `href` weiterhin speziell URL-maskiert werden muss. Diese Unterlassung ist einer der häufigsten Fehler, da es schnell einmal vergessen werden kann und es sich als schwierig erweist, dies automatisiert zu entdecken.

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

Beachten Sie, inwiefern die zwei Attribute unterschiedlich maskiert wurden, da HTL weiß, dass die Attribute `href` und `src` für den URI-Kontext unterschiedlich maskiert werden müssen. Falls der URI mit **`javascript:`** beginnt, wird das Attribut vollständig entfernt, sofern der Kontext nicht explizit in etwas anderes geändert wurde.

Weitere Details über das Steuern der Maskierung finden Sie im Abschnitt über den [Ausdruckssprachen-Anzeigekontext](expression-language.md#display-context).

### Automatisches Entfernen leerer Attribute {#automatic-removal-of-empty-attributes}

Siehe folgendes Beispiel:

```xml
<p class="${properties.class}">some text</p>
```

Wenn der Wert der Eigenschaft `class` leer ist, entfernt die HTML-Vorlagensprache automatisch das gesamte `class`-Attribut aus der Ausgabe.

Auch dies ist möglich, da HTL die HTML-Syntax versteht und daher Attribute bedingt mit dynamischen Werten nur dann anzeigen kann, wenn der zugehörige Wert nicht leer ist. Dies ist extrem praktisch, da sich dadurch das Hinzufügen eines Bedingungsblocks um Attribute herum erübrigt, durch das das Markup ungültig und unlesbar würde.

Zusätzlich ist der im Ausdruck platzierte Variablentyp von Bedeutung:

* **Zeichenfolge:**
   * **nicht leer:** Legt die Zeichenfolge als Attributwert fest.
   * **leer:** Entfernt das Attribut vollständig.

* **Zahl:** Legt den Wert als Attributwert fest.

* **Boolesch:**
   * **true:** Zeigt das Attribut ohne Wert an (als ein boolesches HTML-Attribut
   * **false:** Entfernt das Attribut vollständig.

Im Folgenden finden Sie ein Beispiel dafür, wie ein boolescher Ausdruck die Steuerung eines booleschen HTML-Attributs erlauben würde:

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

Zum Festlegen von Attributen ist die Anweisung [`data-sly-attribute`](block-statements.md#attribute) möglicherweise ebenfalls hilfreich.

## Allgemeine Muster mit HTL {#common-patterns-with-htl}

In diesem Abschnitt werden ein paar allgemeine Szenarien vorgestellt. Zudem wird aufgezeigt, wie diese mit der HTML-Vorlagensprache am besten gelöst werden können.

### Laden von Client-Bibliotheken {#loading-client-libraries}

In HTL werden Client-Bibliotheken über eine durch AEM bereitgestellte Hilfsvorlage geladen, auf die über [`data-sly-use`](block-statements.md#use) zugegriffen werden kann. In dieser Datei sind drei Vorlagen verfügbar, die über [`data-sly-call`](block-statements.md#template-call) abgerufen werden können:

* **`css`** – Lädt nur die CSS-Dateien der referenzierten Client-Bibliotheken.
* **`js`** – Lädt nur die JavaScript-Dateien der referenzierten Client-Bibliotheken.
* **`all`** – Lädt alle Dateien der referenzierten Client-Bibliotheken (CSS und JavaScript).

Jede Hilfsvorlage erwartet eine **`categories`**-Option für das Referenzieren der gewünschten Client-Bibliotheken. Bei dieser Option kann es sich um einen Zeichenfolgenwertbereich handeln oder um eine Zeichenfolge, die eine CSV-Liste enthält.

Im Folgenden finden Sie zwei kurze Beispiele:

### Vollständiges Laden mehrerer Client-Bibliotheken gleichzeitig   {#loading-multiple-client-libraries-fully-at-once}

```xml
<sly data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html"
     data-sly-call="${clientlib.all @ categories=['myCategory1', 'myCategory2']}"/>
```

### Referenzieren einer Client-Bibliothek in unterschiedlichen Abschnitten einer Seite {#referencing-a-client-library-in-different-sections-of-a-page}

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

Im zweiten obigen Beispiel würde, sofern die HTML-Elemente **`head`** und **`body`** in unterschiedlichen Dateien platziert werden würden, die Vorlage **`clientlib.html`** anschließend in jeder Datei geladen werden müssen, die sie benötigt.

Der Abschnitt zu den Anweisungen [template &amp; call](block-statements.md#template-call) enthält weitere Details darüber, wie das Deklarieren und Aufrufen solcher Vorlagen funktioniert.

### Weitergeben von Daten an den Client {#passing-data-to-the-client}

Die beste und eleganteste Möglichkeit, Daten an den Client weiterzugeben, besteht allgemein, und noch mehr mit HTL, in der Verwendung von Datenattributen.

Im folgenden Beispiel wird gezeigt, wie die Logik (die auch in Java geschrieben werden könnte) verwendet werden kann, um eine sehr praktische Serialisierung des an den Client weiterzugebenden Objekts zu JSON vorzunehmen, das anschließend sehr einfach in einem Datenattribut platziert werden kann:

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

Von dort aus ist es einfach, sich vorzustellen, wie ein clientseitiges JavaScript-Element auf dieses Attribut zugreifen und das JSON-Objekt erneut analysieren kann. Dies wäre beispielsweise das entsprechende in eine Client-Bibliothek zu platzierende JavaScript-Element:

```javascript
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### Arbeiten mit clientseitigen Vorlagen   {#working-with-client-side-templates}

Ein spezieller Fall, in dem die im Abschnitt [Einschränkungen für Erhebungen spezieller Kontexte](getting-started.md#lifting-limitations-of-special-contexts) erläuterte Technik legitimerweise verwendet werden kann, besteht im Schreiben von clientseitigen Vorlagen (beispielsweise Handlebars), die sich in **script**-Elementen befinden. Der Grund, weshalb diese Technik in diesem Fall sicher verwendet werden kann, besteht darin, dass das **script**-Element nicht wie angenommen JavaScript-Elemente enthält, sondern weitere HTML-Elemente. Im Folgenden finden Sie ein Beispiel, wie dies funktionieren würde:

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

Wie oben gezeigt, kann das im Element **`script`** einzuschließende Markup HTL-Blockanweisungen enthalten. Zudem müssen die Ausdrücke keine expliziten Kontexte bereitstellen, da die Inhalte der Handlebars-Vorlage in ihrer eigenen Datei isoliert wurden. In diesem Beispiel wird zudem gezeigt, wie die serverseitig ausgeführte HTL (wie im Element **`h2`**) mit einer clientseitig ausgeführten Vorlagensprache, beispielsweise Handlebars (im Element **`h3`** gezeigt), kombiniert werden kann.

Eine modernere Technik bestünde jedoch darin, stattdessen das HTML-Element **`template`** zu verwenden. Damit würde sich die Notwendigkeit erübrigen, die Inhalte der Vorlagen in separaten Dateien zu isolieren.

**Lesen Sie als Nächstes:**

* [Ausdruckssprache](expression-language.md) – für eine detaillierte Erläuterung der in HTL-Ausdrücken verborgenen Möglichkeiten.
* [Blockanweisungen](block-statements.md) – zum Entdecken sämtlicher in HTL verfügbaren Blockanweisungen und deren Verwendung.
