---
title: HTL-Ausdruckssprache
seo-title: HTL-Ausdruckssprache
description: Die HTML-Vorlagensprache verwendet eine Ausdruckssprache für den Zugriff
  auf den Datenstrukturen, die die dynamischen Elemente der HTML-Ausgabe bereitstellen.
seo-description: Die HTML-Vorlagensprache verwendet eine Ausdruckssprache für den
  Zugriff auf den Datenstrukturen, die die dynamischen Elemente der HTML-Ausgabe bereitstellen.
uuid: 38b4a259-03b5-4847-91c6-e20377600070
contentOwner: Benutzer
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: Referenz
discoiquuid: 9ba37ca0-f318-48b0-a791-a944a72502ed
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 796c55d3d85e6b5a3efaa5c04a25be1b0b4e54dd

---


# HTL-Ausdruckssprache {#htl-expression-language}

Die HTML-Vorlagensprache verwendet eine Ausdruckssprache für den Zugriff auf den Datenstrukturen, die die dynamischen Elemente der HTML-Ausgabe bereitstellen. Diese Ausdrücke werden durch die Zeichen `${` und `}` voneinander getrennt. Zum Vermeiden von falsch formatierten HTML-Elementen können Ausdrücke nur in Attributwerten, Elementinhalten oder Kommentaren verwendet werden.

```xml
<!-- ${component.path} -->
<h1 class="${component.name}">
    ${properties.jcr:title}
</h1>
```

Ausdrücke können durch Voranstellen eines **`\`**-Zeichens maskiert werden, beispielsweise wird **`\${test}`** als **`${test}`** dargestellt.

>[!NOTE]
>
>Zum Testen der auf dieser Seite bereitgestellten Beispiele kann eine als [Read Eval Print Loop (REPL)](https://github.com/Adobe-Marketing-Cloud/aem-sightly-repl) bezeichnete Live-Ausführungsumgebung verwendet werden.

Die Ausdruckssyntax enthält [Variablen](#variables), [Literale](#literals), [Operatoren](#operators) und [Optionen](#options):

## Variablen {#variables}

Bei Variablen handelt es sich um Container, die Datenwerte oder Objekte speichern. Die Variablennamen werden Bezeichner genannt.

Ohne etwas angeben zu müssen, bietet HTL Zugriff auf alle Objekte, die nach dem Einschließen von `global.jsp` in JSP verfügbar waren. Die Seite [Globale Objekte](global-objects.md) enthält die Liste sämtlicher Objekte, auf die Zugriff über HTL möglich ist.

### Zugriff auf Eigenschaften  {#property-access}

Es gibt zwei Möglichkeiten, auf Variableneigenschaften zuzugreifen, entweder mit Punkt- oder einer Klammer-Notation:

`${currentPage.title}  
${currentPage['title']} or ${currentPage["title"]}`

Die einfachere Punkt-Notation sollte in den meisten Fällen bevorzugt werden. Demgegenüber sollte die Klammer-Notation verwendet werden, um auf Eigenschaften zuzugreifen, die ungültige Bezeichnerzeichen enthalten, oder um dynamisch auf Eigenschaften zuzugreifen. In den folgenden zwei Kapiteln finden Sie Details zu diesen zwei Fällen.

Bei den Eigenschaften, auf die zugegriffen wurde, kann es sich um Funktionen handeln. Das Weitergeben von Argumenten wird jedoch nicht unterstützt. Daher ist nur der Zugriff auf Funktionen möglich, die keine Argumente erwarten. Dazu zählen beispielsweise Getter. Hierbei handelt es sich um eine erwünschte Einschränkung. Sie soll die Menge der in Ausdrücken eingebetteten Logik reduzieren. Bei Bedarf kann die Anweisung [`data-sly-use`](block-statements.md#use) verwendet werden, um Parameter an die Logik weiterzugeben.

Im obigen Beispiel wird ebenfalls gezeigt, dass der Zugriff auf die Java-Getter-Funktionen, beispielsweise `getTitle()`**, auch möglich ist, ohne `get`** Elemente voranzustellen, und durch Kleinschreibung des folgenden Zeichens.

### Gültige Bezeichnerzeichen {#valid-indentifier-characters}

Die Variablennamen, sog. Bezeichner, sind mit bestimmten Regeln konform. Sie müssen mit einem Buchstaben (**`A`**-**`Z`** und **`a`**-**`z`**) oder einem Unterstrich (**`_`**) beginnen. Nachfolgende Zeichen können auch Ziffern (**`0`**-**`9`**) oder ein Doppelpunkt (**`:`**) sein. Unicode-Buchstaben wie **`å`** und **`ü` können in Bezeichnern nicht verwendet werden.**

Wenn wir davon ausgehen, dass der Doppelpunkt (**:**) in AEM-Eigenschaftsnamen gängig ist, ist es praktisch, dass es sich hierbei um ein gültiges Bezeichnerzeichen handelt:

`${properties.jcr:title}`

Die Klammer-Notation kann verwendet werden, um auf Eigenschaften zuzugreifen, die ungültige Bezeichnerzeichen enthalten. Dazu zählt beispielsweise das Leerzeichen im folgenden Beispiel:

`${properties['my property']}`

### Dynamisches Zugreifen auf Mitglieder {#accessing-members-dynamically}

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

```xml
${properties[myVar]}
```

### Tolerante Verarbeitung von Null-Werten {#permissive-handling-of-null-values}

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

```xml
${currentPage.lastModified.time.toString}
```

## Literale {#literals}

Bei einem Literal handelt es sich um eine Notation für das Darstellen eines festen Werts.

### Boolesch  {#boolean}

Boolesch stellt eine logische Entität dar und kann zwei Werte aufweisen: **`true`** und **`false`**.

`${true} ${false}`

### Zahlen {#numbers}

Es gibt nur einen Zahlentyp: positive Ganzzahlen. Demgegenüber werden andere Zahlenformate wie Gleitkommas in Variablen unterstützt, können jedoch nicht als Literale ausgedrückt werden.

`${42}`

### Zeichenfolgen {#strings}

Sie stellen Textdaten dar und können in einfache oder doppelte Anführungszeichen gesetzt sein:

`${'foo'} ${"bar"}`

Neben normalen Zeichen können die folgenden Sonderzeichen verwendet werden:

* **`\\`** Umgekehrter Schrägstrich
* **`\'`** Einfaches Anführungszeichen (oder Apostroph)
* **`\"`** Doppeltes Anführungszeichen
* **`\t`** Tabulator
* **`\n`** Neue Zeile
* **`\r`** Wagenrücklauf
* **`\f`** Formularfeed
* **`\b`** Rücktaste
* `\uXXXX` Durch die vier Hexadezimalziffern XXXX angegebenes Unicode-Zeichen.\
   Im Folgenden finden Sie einige nützliche Unicode-Maskierungssequenzen:

   * **\u0022** für **"**
   * **\u0027** für **'**

Bei den oben nicht aufgeführten Zeichen führt das Voranstellen eines umgekehrten Schrägstrichs zur Anzeige eines Fehlers.

Im Folgenden finden Sie einige Beispiele zur Verwendung der Zeichenfolgenmaskierung:

```xml
<p>${'it\'s great, she said "yes!"'}</p>
<p title="${'it\'s great, she said \u0022yes!\u0022'}">...</p>
```

was die folgende Ausgabe zur Folge hat, da HTL die kontextspezifische Maskierung anwendet:

```xml
<p>it&#39;s great, she said &#34;yes!&#34;</p>
<p title="it&#39;s great, she said &#34;yes!&#34;">...</p>
```

### Arrays  {#arrays}

Bei einem Array handelt es sich um einen geordneten Satz an Werten, auf die mit einem Namen und einem Index verwiesen werden kann. Die Typen der zugehörigen Elemente können kombiniert werden.

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

```xml
${[1,2,3,4]}
${myArray[2]}
```

Arrays sind hilfreich, um eine Liste der Werte anhand der Vorlage bereitzustellen.

```xml
<ul data-sly-list="${[1,2,3,4]}">
  <li>${item}</li>
</ul>
```

## Operatoren  {#operators}

### Logische Operatoren {#logical-operators}

Diese Operatoren werden für gewöhnlich mit Booleschen Werten verwendet. Sie geben jedoch wie in JavaScript tatsächlich den Wert eines der angegebenen Operanden zurück. Bei der Verwendung mit nicht Booleschen Werten geben sie möglicherweise einen nicht Booleschen Wert zurück.

Wenn ein Wert in **`true`** umgewandelt werden kann, ist der Wert sozusagen wahr. Wenn ein Wert in **`false`** umgewandelt werden kann, ist der Wert sozusagen falsch. Werte, die in **`false` umgewandelt werden können, sind: undefinierte Variablen, Null-Werte, die Zahl 0 und leere Zeichenfolgen.**

#### Logisches NICHT {#logical-not}

**`${!myVar}`** gibt **`false`** zurück, sofern sein einzelner Operand in `true` umgewandelt werden kann; gibt andernfalls **`true`** zurück.

Dieser Operator kann zum Beispiel zum Umkehren einer Testbedingung verwendet werden. Dazu zählt beispielsweise, dass ein Element nur dann angezeigt wird, wenn keine untergeordneten Seiten vorhanden sind:

```xml
<p data-sly-test="${!currentPage.hasChild}">current page has no children</p>
```

#### Logisches UND  {#logical-and}

**`${varOne && varTwo}`** gibt `varOne` zurück, sofern fehlerhaft; gibt andernfalls **varTwo** zurück.

Dieser Operator kann verwendet werden, um zwei Bedingungen gleichzeitig zu testen, beispielsweise zum Verifizieren des Vorhandenseins von zwei Eigenschaften:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

Der logische UND-Operator kann auch verwendet werden, um HTML-Attribute bedingt darzustellen, da HTL Attribute mit dynamisch festgelegten Werten entfernt, die als false oder zu einer leeren Zeichenfolge ausgewertet werden. Im folgenden Beispiel wird das Attribut **`class`** nur gezeigt, wenn **`logic.showClass`** wahr ist und wenn **`logic.className`** vorhanden und nicht leer ist:

```xml
<div class="${logic.showClass && logic.className}">...</div>
```

#### Logisches ODER {#logical-or}

**`${varOne || varTwo}`** gibt **varOne** zurück, sofern wahr; gibt andernfalls **varTwo** zurück.

Dieser Operator kann verwendet werden, um zu testen, ob eine der zwei Bedingungen zutrifft, beispielsweise zum Verifizieren des Vorhandenseins von mindestens einer Eigenschaft:

```xml
<div data-sly-test="${properties.jcr:title || properties.jcr:description}">...</div>
```

Da der logische ODER-Operator die erste wahre Variable zurückgibt, gestaltet sich seine Verwendung auch sehr praktisch, wenn es darum geht, Ausweichwerte bereitzustellen.

Zeigen Sie HTML-Attribute bedingt an, da HTL Attribute mit Werten entfernt, die durch Ausdrücke festgelegt wurden, welche als false oder zu einer leeren Zeichenfolge ausgewertet werden. Im folgenden Beispiel wird demnach der Titel **`properties.jcr:`** angezeigt, sofern er vorhanden und nicht leer ist. Ansonsten wird als Ausweichlösung **`properties.jcr:description`** angezeigt, sofern er vorhanden und nicht leer ist. Sonst wird die Meldung „no title or description provided“ (Kein Titel oder keine Beschreibung bereitgestellt) angezeigt:

```xml
<p>${properties.jcr:title || properties.jcr:description || "no title or description provided"}</p>
```

### Bedingter (dreifacher) Operator {#conditional-ternary-operator}

**`${varCondition ? varOne : varTwo}`** gibt **`varOne`** zurück, wenn **`varCondition`** wahr ist; gibt andernfalls **`varTwo`** zurück.

Dieser Operator kann für gewöhnlich verwendet werden, um Bedingungen in Ausdrücken zu definieren. Dazu zählt beispielsweise das Anzeigen einer anderen Meldung anhand des Status der Seite:

```xml
<p>${currentPage.isLocked ? "page is locked" : "page can be edited"}</p>
```

Wichtig zu beachten ist, dass, da Doppelpunkte in Bezeichnern nicht erlaubt sind, dreifache Operatoren nach Möglichkeit durch ein Leerzeichen getrennt werden sollten, um dem Parser Klarheit zu verschaffen:

```xml
<p>${properties.showDescription ? properties.jcr:description : properties.jcr:title}</p>
```

### Vergleichsoperatoren  {#comparison-operators}

Die Gleich- und Ungleich-Operatoren unterstützen nur Operanden identischen Typs. Wenn die Typen nicht übereinstimmen, wird ein Fehler angezeigt.

* Zeichenfolgen sind identisch, wenn sie dieselbe Folge von Zeichen aufweisen.
* Zahlen sind identisch, wenn sie denselben Wert aufweisen.
* Boolesche Werte sind identisch, wenn beide **`true`** oder **`false`** sind.

* Null- oder undefinierte Variablen sind identisch mit sich selbst und miteinander.

**`${varOne == varTwo}`** gibt **`true`** zurück, wenn **`varOne`** und **`varTwo`** gleich sind.

**`${varOne != varTwo}`** gibt **`true`** zurück, wenn **`varOne`** und **`varTwo`** nicht gleich sind.

Die relationalen Operatoren unterstützen nur Operanden, die Zahlen sind. Bei allen anderen Typen wird ein Fehler angezeigt.

**`${varOne > varTwo}`** gibt **`true`** zurück, wenn **`varOne`** größer als **`varTwo`** ist.

**`${varOne < varTwo}`** gibt **`true`** zurück, wenn **`varOne`** kleiner als **`varTwo`** ist.

**`${varOne >= varTwo}`** gibt **`true`** zurück, wenn **`varOne`** größer als/gleich **`varTwo`** ist.

**`${varOne <= varTwo}`** gibt **`true`** zurück, wenn **`varOne`** kleiner als/gleich **`varTwo`** ist.

### Gruppierungsklammern {#grouping-parentheses}

Der Gruppierungsoperator **`(`** **`)`** steuert die Priorität der Auswertung in Ausdrücken.

`${varOne && (varTwo || varThree)}`

## Optionen {#options}

<!-- 

Comment Type: draft

<p>TODO: review text below.</p>

 -->

Ausdrucksoptionen können sich auf den Ausdruck auswirken und ihn ändern oder als Parameter fungieren, wenn sie zusammen mit Blockanweisungen verwendet werden.

Alles hinter dem **`@`** ist eine Option:

```xml
${myVar @ optOne}
```

Optionen können einen Wert aufweisen, der eine Variable oder ein Literal sein kann:

```xml
${myVar @ optOne=someVar}
${myVar @ optOne='bar'}
${myVar @ optOne=10}
${myVar @ optOne=true}
```

Mehrere Optionen werden durch Kommas voneinander getrennt:

```xml
${myVar @ optOne, optTwo=bar}
```

Es sind auch Parameterausdrücke möglich, die nur Optionen enthalten:

```xml
${@ optOne, optTwo=bar}
```

### Zeichenfolgenformatierung  {#string-formatting}

Option, die die Aufzählungsplatzhalter {*n*} durch den entsprechenden Wert ersetzt:

```xml
${'Page {0} of {1}' @ format=[current, total]}
```

### Internationalisierung  {#internationalization}

Übersetzt die Zeichenfolge mithilfe des aktuellen [Wörterbuchs](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/i18n-translator) in die Sprache der aktuellen *Quelle* (siehe unten). Wenn keine Übersetzung gefunden wird, wird die ursprüngliche Zeichenfolge verwendet.

```xml
${'Page' @ i18n}
```

Die Option „hint“ kann verwendet werden, um Übersetzern einen entsprechenden Kontext bereitzustellen, wodurch der Kontext angegeben wird, in dem der Text verwendet wird:

```xml
${'Page' @ i18n, hint='Translation Hint'}
```

Die Standardquelle für die Sprache lautet „resource“. Der Text wird demnach in dieselbe Sprache übersetzt wie der Inhalt. Dies kann zu „user“ geändert werden. Die Sprache wird demnach anhand des Browsergebietsschemas oder gemäß dem Gebietsschema des angemeldeten Benutzers übernommen:

```xml
${'Page' @ i18n, source='user'}
```

Durch Angabe eines expliziten Gebietsschemas werden die Quelleinstellungen überschrieben:

```xml
${'Page' @ i18n, locale='en-US'}
```

Zum Einbetten von Variablen in einer übersetzten Zeichenfolge kann die Option „format“ verwendet werden:

```xml
${'Page {0} of {1}' @ i18n, format=[current, total]}
```

### Array-Verbund  {#array-join}

HTL zeigt standardmäßig, wenn ein Array als Text angezeigt wird, kommagetrennte Werte (ohne Leerzeichen) an.

Verwenden Sie die Option „join“, um ein anderes Trennzeichen anzugeben:

```xml
${['one', 'two'] @ join='; '}
```

### Anzeigekontext  {#display-context}

Der Anzeigekontext eines HTL-Ausdrucks bezieht sich auf seine Position in der Struktur der HTML-Seite. Wenn der Ausdruck beispielsweise an einer Stelle angezeigt wird, wodurch ein Textknoten einmalig gerendert wird, befindet er sich in einem **`text`-Kontext. ** Wenn er im Wert eines Attributs gefunden wird, befindet er sich in einem **`attribute`-Kontext usw.**

Außer in Kontexten vom Typ script (JS) und style (CSS) erkennt HTL automatisch den Kontext der Ausdrücke und maskiert sie entsprechend, um XSS-Sicherheitsprobleme zu verhindern. Im Fall von Skripts und CSS muss das gewünschte Kontextverhalten explizit festgelegt werden. Zusätzlich kann das Kontextverhalten zudem in jedem anderen Fall explizit festgelegt werden, in dem das automatische Verhalten überschrieben werden soll.

Im Folgenden finden sich drei Variablen in drei unterschiedlichen Kontexten: **`properties.link`** ( `uri`-Kontext), **`properties.title`** (**`attribute`**-Kontext) und **`properties.text`**(**`text`**-Kontext). HTL maskiert diese unterschiedlich entsprechend den Sicherheitsanforderungen ihrer jeweiligen Kontexte. In normalen Fällen, wie im folgenden, ist keine explizite Kontexteinstellung erforderlich:

```xml
<a href="${properties.link}" title="${properties.title}">${properties.text}</a>
```

Für die sichere Markup-Ausgabe (d. h. wo der Ausdruck als solches in ausgewertet wird), wird der `html`html-Kontext verwendet:

```xml
<div>${properties.richText @ context='html'}</div>
```

Für Style-Kontexte muss ein expliziter Kontext festgelegt werden:

```xml
<span style="color: ${properties.color @ context='styleToken'};">...</span>
```

Für Skript-Kontexte muss ein expliziter Kontext festgelegt werden:

```xml
<span onclick="${properties.function @ context='scriptToken'}();">...</span>
```

Maskierung und XSS-Schutz können auch deaktiviert werden:

```xml
<div>${myScript @ context='unsafe'}</div>
```

### Kontexteinstellungen  {#context-settings}

| Kontext | Wann ist sie einzusetzen? | Funktion |
|--- |--- |--- |
| text | Standard für inhaltsimmanente Elemente | Kodiert alle HTML-Sonderzeichen. |
| html | Zur sicheren Markup-Ausgabe | Filtert HTML zum Erfüllen der AntiSamy-Richtlinienregeln,wodurch entfernt wird, was nicht mit den Regeln übereinstimmt. |
| attribute | Standard für Attributwerte | Kodiert alle HTML-Sonderzeichen. |
| uri | Zum Anzeigen von Links und Pfaden Standard für die Attributwerte href und src | Validiert den URI für die Schreibung als ein href- oder src-Attributwert, gibt bei fehlerhafter Validierung keine Werte aus. |
| number | Zum Anzeigen von Zahlen | Validiert den URI dahingehend, ob er eine Ganzzahl enthält,gibt bei fehlerhafter Validierung 0 aus. |
| attributeName | Standard für data-sly-attribute beim Festlegen von Attributnamen | Validiert den Attributnamen,gibt bei fehlerhafter Validierung keine Werte aus. |
| elementName | Standard für data-sly-element | Validiert den Elementnamen,gibt bei fehlerhafter Validierung nichts aus. |
| scriptToken | Für JS-Bezeichner, literale Zahlen oder literale Zeichenfolgen | Validiert das JavaScript-Token,gibt bei fehlerhafter Validierung keine Werte aus. |
| scriptString | In JS-Zeichenfolgen | Kodiert Zeichen, die aus der Zeichenfolge ausbrechen würden. |
| scriptComment | In JS-Kommentaren | Validiert den JavaScript-Kommentar, gibt bei fehlerhafter Validierung keine Werte aus. |
| styleToken | Für CSS-Bezeichner, Zahlen, Dimensionen, Zeichenfolgen, hexadezimale Farbwerte oder Funktionen. | Validiert das CSS-Token,gibt bei fehlerhafter Validierung keine Werte aus. |
| styleString | In CSS-Zeichenfolgen | Kodiert Zeichen, die aus der Zeichenfolge ausbrechen würden. |
| styleComment | In CSS-Kommentaren | Validiert den CSS-Kommentar, gibt bei fehlerhafter Validierung keine Werte aus. |
| unsafe | Trifft nur zu, wenn keiner der oben genannten Punkte den Auftrag vornimmt | Deaktiviert die Maskierung und den XSS-Schutz vollständig. |

