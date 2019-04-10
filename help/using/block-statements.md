---
title: HTL-Blockanweisungen
seo-title: HTL-Blockanweisungen
description: Bei HTML-Vorlagensprachen-Blockanweisungen (HTL) handelt es sich um direkt
  zur vorhandenen HTML hinzugefügten data-Attributen.
seo-description: Bei HTML-Vorlagensprachen-Blockanweisungen (HTL) handelt es sich
  um direkt zur vorhandenen HTML hinzugefügten data-Attributen.
uuid: 0624fb6e-6989-431b-aabc-1138325393f1
contentOwner: Benutzer
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: Referenz
discoiquuid: 58aa6ea8-1d45-4f6f-a77e-4819f593a19d
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: ht
source-git-commit: 7a94b0b010461b29d2b74c9c717e3b218d0ca5a8

---


# HTL-Blockanweisungen {#htl-block-statements}

Bei HTML-Vorlagensprachen-Blockanweisungen (HTL) handelt es sich um direkt zur vorhandenen HTML hinzugefügten `data`-Attributen. Dadurch kann eine statische HTML-Musterseite einfach und unauffällig mit Anmerkungen versehen werden, wodurch sie in eine funktionierende dynamische Vorlage umgewandelt wird, ohne die Gültigkeit des HTML-Codes außer Kraft zu setzen.

## Sly-Element {#sly-element}

Das **&lt;Sly&gt;-Element** wird nicht in der resultierenden HTML angezeigt und kann anstelle von data-sly-unwrap verwendet werden. Das &lt;Sly&gt;-Element soll genauer verdeutlichen, dass das Element nicht ausgegeben wird. Sie können weiterhin data-sly-unwrap verwenden, wenn Sie möchten.

```xml
<sly data-sly-test.varone="${properties.yourProp}"/>
```

Wie bei data-sly-unwrap sollten Sie versuchen, die Verwendung zu minimieren.

## Verwenden Sie {#use}

**`data-sly-use`**: Initialisiert ein Hilfsobjekt (definiert in JavaScript oder Java) und stellt es über eine Variable bereit.

Initialisieren Sie ein JavaScript-Objekt, wobei sich die Quelldatei im selben Verzeichnis wie die Vorlage befindet. Beachten Sie, dass der Dateiname verwendet werden muss:

```xml
<div data-sly-use.nav="navigation.js">${nav.foo}</div>
```

Initialisieren Sie eine Java-Klasse, wobei sich die Quelldatei im selben Verzeichnis wie die Vorlage befindet. Beachten Sie, dass der Klassenname verwendet werden muss und nicht der Dateiname:

```xml
        <div data-sly-use.nav="Navigation">${nav.foo}</div>
```

Initialisieren Sie eine Java-Klasse, wobei diese Klasse als Teil eines OSGi-Bundles installiert wird. Beachten Sie, dass der zugehörige vollqualifizierte Klassenname verwendet werden muss:

```xml
        <div data-sly-use.nav="org.example.Navigation">${nav.foo}</div>
```

Parameter können mithilfe von *options* an die Initialisierung weitergegeben werden. Diese Funktion sollte generell nur durch HTL-Code verwendet werden, der sich selbst in einem `data-sly-template`-Block befindet:

```xml
<div data-sly-use.nav="${'navigation.js' @parentPage=currentPage}">${nav.foo}</div>
```

Initialisieren Sie eine andere HTL-Vorlage, die mithilfe von **`data-sly-call`** abgerufen werden kann:

```xml
<div data-sly-use.nav="navTemplate.html" data-sly-call="${nav.foo}"></div>
```

>[!NOTE]
>
>Weitere Informationen über die Anwendungs-API finden Sie unter:
>
>* [Java-Anwendungs-API](use-api-java.md)
>* [JavaScript-Anwendungs-API](use-api-javascript.md)
>



## unwrap  {#unwrap}

**`data-sly-unwrap`**: Entfernt das Hostelement aus dem generierten Markup und behält seinen Inhalt bei. Dadurch können Elemente ausgeschlossen werden, die als Bestandteil der HTL-Darstellungslogik erforderlich, in der tatsächlichen Ausgabe jedoch nicht erwünscht sind.

Diese Anweisung sollte jedoch sparsam verwendet werden. Im Allgemeinen ist es besser, das HTL-Markup möglichst nah beim vorgesehenen Ausgabe-Markup zu belassen. Versuchen Sie demnach beim Hinzufügen von HTL-Blockanweisungen, die vorhandene HTML soweit möglich einfach mit Anmerkungen zu versehen, ohne neue Elemente einzuführen.

Dies beispielsweise

```xml
<p data-sly-use.nav="navigation.js">Hello World</p>
```

ergibt

```xml
<p>Hello World</p>
```

Dies demgegenüber

```xml
<p data-sly-use.nav="navigation.js" data-sly-unwrap>Hello World</p>
```

ergibt

```xml
Hello World
```

Es ist auch möglich, ein Element bedingt zu umbrechen:

```xml
<div class="popup" data-sly-unwrap="${isPopup}">content</div>
```

## text  {#text}

**`data-sly-text`**: Ersetzt den Kontext seines Hostelements durch den angegebenen Text.

Dies beispielsweise

```xml
<p>${properties.jcr:description}</p>
```

entspricht

```xml
<p data-sly-text="${properties.jcr:description}">Lorem ipsum</p>
```

Beide zeigen den Wert von **`jcr:description` als Absatztext an. ** Der Vorteil der zweiten Methode besteht darin, dass sie die unauffällige Anmerkung der HTML ermöglicht und gleichzeitig den statischen Platzhalterinhalt aus dem ursprünglichen Designer beibehält.

## attribute  {#attribute}

**data-sly-attribute**: Fügt dem Hostelement Attribute hinzu.

Dies beispielsweise

```xml
<div title="${properties.jcr:title}"></div>
```

entspricht

```xml
<div title="Lorem Ipsum" data-sly-attribute.title="${properties.jcr:title}"></div>
```

Beide legen das Attribut `title` auf den Wert von **`jcr:title`** fest. Der Vorteil der zweiten Methode besteht darin, dass sie die unauffällige Anmerkung der HTML ermöglicht und gleichzeitig den statischen Platzhalterinhalt aus dem ursprünglichen Designer beibehält.

Attribute werden von links nach rechts aufgelöst, wobei die am weitesten rechts liegende Instanz eines Attributs (literal oder definiert über **`data-sly-attribute`**) vor anderen, links davon definierten Instanzen desselben Attributs (**`data-sly-attribute`**) Vorrang hat.

Beachten Sie, dass ein Attribut (**`literal`** oder festgelegt über **`data-sly-attribute`**), dessen Wert in einer leeren Zeichenfolge *ausgewertet* wird, aus dem endgültigen Markup entfernt wird. Die einzige Ausnahme von dieser Regel ist, dass ein auf eine leere *literal*-Zeichenfolge festgelegtes *literal*-Attribut beibehalten wird. Beispiel:

```xml
<div class="${''}" data-sly-attribute.id="${''}"></div>
```

produziert

```xml
<div></div>
```

aber

```xml
<div class="" data-sly-attribute.id=""></div>
```

produziert

```xml
<div class=""></div>
```

Geben Sie zum Festlegen mehrerer Attribute ein Schlüssel-Wert-Paare enthaltendes Zuordnungsobjekt weiter, das den Attributen und ihren Werten entspricht. Angenommen,

```xml
attrMap = {
    title: "myTitle",
    class: "myClass",
    id: "myId"
}
```

Dann,

```xml
<div data-sly-attribute="${attrMap}"></div>
```

produziert

```xml
<div title="myTitle" class="myClass" id="myId"></div>
```

## element  {#element}

**`data-sly-element`**: Ersetzt den Elementnamen des Hostelements.

Beispiel:

```xml
<h1 data-sly-element="${titleLevel}">text</h1>
```

Ersetzt den **`h1`** durch den Wert **`titleLevel`**.

Aus Sicherheitsgründen akzeptiert `data-sly-element` nur die folgenden Elementnamen:

```xml
a abbr address article aside b bdi bdo blockquote br caption cite code col colgroup
data dd del dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header i ins
kbd li main mark nav ol p pre q rp rt ruby s samp section small span strong sub 
sup table tbody td tfoot th thead time tr u var wbr
```

Zum Festlegen anderer Elemente muss die XSS-Sicherheit deaktiviert sein ( `@context='unsafe'`).

## test {#test}

**`data-sly-test`:** Entfernt das Hostelement und die zugehörigen Inhalte bedingt. Durch den Wert `false` wird das Element entfernt. Durch den Wert `true` wird das Element beibehalten.

Zum Beispiel werden das Element `p` und die zugehörigen Inhalte nur dargestellt, wenn `isShown` dem Wert `true` entspricht:

```xml
<p data-sly-test="${isShown}">text</p>
```

Das Testergebnis kann zur Variable zugewiesen werden, die später verwendet werden kann. Dies wird für gewöhnlich zum Erstellen der „if else“-Logik verwendet, da keine explizite else-Anweisung vorliegt:

```xml
<p data-sly-test.abc="${a || b || c}">is true</p>
<p data-sly-test="${!abc}">or not</p>
```

Die Variable ist nach ihrer Festlegung global in der HTL-Datei.

Im Folgenden finden Sie einige Beispiele zum Vergleichen von Werten:

```xml
<div data-sly-test="${properties.jcr:title == 'test'}">TEST</div>
<div data-sly-test="${properties.jcr:title != 'test'}">NOT TEST</div>

<div data-sly-test="${properties['jcr:title'].length > 3}">Title is longer than 3</div>
<div data-sly-test="${properties['jcr:title'].length >= 0}">Title is longer or equal to zero </div> 

<div data-sly-test="${properties['jcr:title'].length > aemComponent.MAX_LENGTH}">
    Title is longer than the limit of ${aemComponent.MAX_LENGTH}
</div>
```

## repeat {#repeat}

Bei data-sly-repeat können Sie ein Element basierend auf der angegebenen Liste mehrmals *wiederholen*.

```xml
<div data-sly-repeat="${currentPage.listChildren}">${item.name}</div>
```

Dies funktioniert genauso wie data-sly-list, mit der Ausnahme, dass Sie kein Containerelement benötigen.

Das folgende Beispiel zeigt, dass Sie für Attribute auch auf das *Element* verweisen können:

```xml
<div data-sly-repeat="${currentPage.listChildren}" data-sly-attribute.class="${item.name}">${item.name}</div>
```

## list {#list}

**`data-sly-list`**: Wiederholt den Inhalt des Hostelements für jede Aufzählungseigenschaft im angegebenen Objekt.

Im Folgenden finden Sie eine einfache Schleife:

```xml
<dl data-sly-list="${currentPage.listChildren}">
    <dt>index: ${itemList.index}</dt>
    <dd>value: ${item.title}</dd>
</dl>
```

Die folgenden standardmäßigen Variablen sind im Umfang der Liste verfügbar:

**`item`**: Das aktuelle Elemente in der Wiederholung.

**`itemList`**: Objekt, das die folgenden Eigenschaften enthält:

**`index`**: Zähler auf Basis null ( `0..length-1`).

**`count`**: Zähler auf Basis eins ( `1..length`).

`first`: `true`, wenn das aktuelle Element das erste Element ist.

**`middle`**: `true`, wenn das aktuelle Element weder das erste noch das letzte Element ist.

**`last`**: `true` wenn das aktuelle Element das letzte Element ist.

**`odd`**: `true` `index` ist ungerade.

**`even`**: `true` `index` ist gerade.

Durch das Definieren eines Bezeichners für die Anweisung `data-sly-list`** können Sie die Variablen `itemList`** und `item` umbenennen. **`item`** wird *** `<variable>`*** und **`itemList`** wird **`*<variable>*List`**.

```xml
<dl data-sly-list.child="${currentPage.listChildren}">
    <dt>index: ${childList.index}</dt>
    <dd>value: ${child.title}</dd>
</dl>
```

Sie können auch dynamisch auf Eigenschaften zugreifen:

```xml
<dl data-sly-list.child="${myObj}">
    <dt>key: ${child}</dt>
    <dd>value: ${myObj[child]}</dd>
</dl>
```

## Ressource  {#resource}

**`data-sly-resource`**: Enthält das Ergebnis der Darstellung der angegebenen Ressource durch die Sling-Auflösung und den Darstellungsprozess.

Zu einer einfachen Ressource gehören:

```xml
<article data-sly-resource="path/to/resource"></article>
```

Optionen ermöglichen einige zusätzliche Varianten:

Ändern des Ressourcenpfads:

```xml
<article data-sly-resource="${ @ path='path/to/resource'}"></article>
<article data-sly-resource="${'resource' @ prependPath='my/path'}"></article>
<article data-sly-resource="${'my/path' @ appendPath='resource'}"></article>
```

Hinzufügen (oder Ersetzen) einer Auswahl:

```xml
<article data-sly-resource="${'path/to/resource' @ selectors='selector'}"></article>
```

Hinzufügen, Ersetzen oder Entfernen von Mehrfachauswahlen:

```xml
<article data-sly-resource="${'path/to/resource' @ selectors=['s1', 's2']}"></article>
```

Hinzufügen einer Auswahl zu vorhandenen:

```xml
<article data-sly-resource="${'path/to/resource' @ addSelectors='selector'}"></article>
```

Entfernen einiger Auswahlen aus vorhandenen:

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors='selector1'}"></article>
```

Entfernen sämtlicher Auswahlen:

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors}"></article>
```

Überschreibt den Ressourcentyp der Ressource:

```xml
<article data-sly-resource="${'path/to/resource' @ resourceType='my/resource/type'}"></article>
```

Ändert den WCM-Modus:

```xml
<article data-sly-resource="${'path/to/resource' @ wcmmode='disabled'}"></article>
```

Die decoration-Tags von AEM sind standardmäßig deaktiviert. Mit der Option „decorationTagName“ können sie zurückgeholt werden und mit „cssClassName“ können diesem Element Klassen hinzugefügt werden.

```xml
<article data-sly-resource="${'path/to/resource' @ decorationTagName='span',
cssClassName='className'}"></article>
```

>[!NOTE]
>
>AEM bietet klare und einfache Logik, die die decoration-Tags steuert, die enthaltene Elemente umschließen. Weitere Informationen finden Sie unter [Decoration-Tag](https://helpx.adobe.com/de/experience-manager/6-4/sites/developing/using/decoration-tag.html) in der Dokumentation zu Entwicklungskomponenten.

## include {#include}

**`data-sly-include`**: Ersetzt den Inhalt des Hostelements durch das durch die angegebene HTML-Vorlagendatei (HTL, JSP, ESP usw.) generierte Markup, wenn er durch das entsprechende Vorlagenmodul verarbeitet wird. Der Darstellungskontext der *eingeschlossenen Datei* enthält den aktuellen HTL-Kontext nicht (den der *einzuschließenden Datei*). Demzufolge muss das aktuelle Element **`data-sly-use`** für das Einschließen der HTL-Dateien in der eingeschlossenen Datei wiederholt werden (in einem solchen Fall ist es für gewöhnlich besser, **`data-sly-template`** und `data-sly-call` zu verwenden).

Ein einfaches include-Beispiel:

```xml
<section data-sly-include="path/to/template.html"></section>
```

JSPs können auf gleiche Weise eingeschlossen werden:

```xml
<section data-sly-include="path/to/template.jsp"></section>
```

Mit Optionen können Sie den Dateipfad ändern:

```xml
<section data-sly-include="${ @ file='path/to/template.html'}"></section>
<section data-sly-include="${'template.html' @ prependPath='my/path'}"></section>
<section data-sly-include="${'my/path' @ appendPath='template.html'}"></section>
```

Sie können auch den WCM-Modus ändern:

```xml
<section data-sly-include="${'template.html' @ wcmmode='disabled'}"></section>
```

## template & call  {#template-call}

`data-sly-template`: Definiert eine Vorlage. Das Hostelement und die zugehörigen Inhalte werden durch HTL nicht ausgegeben

`data-sly-call`: Ruft eine mit „data-sly-template“ definierte Vorlage auf. Der Inhalt der abgerufenen Vorlage (optional parametrisiert) ersetzt den Inhalt des Hostelements des Aufrufs.

Definieren Sie eine statische Vorlage und rufen Sie sie anschließend auf:

```xml
<template data-sly-template.one>blah</template>
<div data-sly-call="${one}"></div>
```

Definieren Sie eine dynamische Vorlage und rufen Sie sie dann mit Parametern auf:

```xml
<template data-sly-template.two="${ @ title}"><h1>${title}</h1></template>
<div data-sly-call="${two @ title=properties.jcr:title}"></div>
```

In einer anderen Datei befindliche Vorlagen können mithilfe von `data-sly-use` initialisiert werden. Beachten Sie, dass in diesem Fall `data-sly-use` und `data-sly-call` auch im selben Element platziert werden könnten:

```xml
<div data-sly-use.lib="templateLib.html">
    <div data-sly-call="${lib.one}"></div>
    <div data-sly-call="${lib.two @ title=properties.jcr:title}"></div>
</div>
```

Die Vorlagenverschachtelung wird unterstützt:

```xml
<template data-sly-template.nav="${ @ page}">
    <ul data-sly-list="${page.listChildren}">
        <li>
            <div class="title">${item.title}</div>
            <div data-sly-call="${nav @ page=item}" data-sly-unwrap></div>
        </li>
    </ul>
</template>
<div data-sly-call="${nav @ page=currentPage}" data-sly-unwrap></div>
```

## i18n- und Gebietsschema-Objekte {#i-n-and-locale-objects}

Wenn Sie i18n und HTL verwenden, können Sie jetzt auch benutzerdefinierte Gebietsschema-Objekte übergeben.

```xml
${'Hello World' @ i18n, locale=request.locale}
```

## URL-Manipulation {#url-manipulation}

Eine neue Gruppe von URL-Manipulationen ist verfügbar.

Siehe folgende Beispiele zu ihrer Nutzung:

Fügt die HTML-Erweiterung zu einem Pfad hinzu.

```xml
<a href="${item.path @ extension = 'html'}">${item.name}</a>
```

Fügt die HTML-Erweiterung und einen Selektor zu einem Pfad hinzu.

```xml
<a href="${item.path @ extension = 'html', selectors='products'}">${item.name}</a>
```

Fügt die HTML-Erweiterung und ein Fragment (#value) zu einem Pfad hinzu.

```xml
<a href="${item.path @ extension = 'html', fragment=item.name}">${item.name}</a>
```

## In AEM 6.3 unterstützte HTL-Funktionen {#htl-features-supported-in-aem}

Die folgenden neuen HTL-Funktionen werden in Adobe Experience Manager (AEM) 6.3 unterstützt:

### Zahlen-/Datumsformatierung {#number-date-formatting}

AEM 6.3 unterstützt native Formatierungen von Zahlen und Daten, ohne benutzerdefinierten Code zu schreiben. Dies unterstützt auch die Zeitzone und das Gebietsschema.

Die folgenden Beispiele zeigen, dass das Format zuerst festgelegt wird und dann der Wert, der formatiert werden muss:

```xml
<h2>${ 'dd-MMMM-yyyy hh:mm:ss' @
           format=currentPage.lastModified,
           timezone='PST',
           locale='fr'}</h2>

<h2>${ '#.00' @ format=300}</h2>
```

>[!NOTE]
>
>Ausführliche Informationen zu dem Format, das Sie verwenden können, finden Sie unter [HTL-Spezifikation](https://github.com/Adobe-Marketing-Cloud/htl-spec/blob/master/SPECIFICATION.md).

### data-sly-use mit Ressourcen {#data-sly-use-with-resources}

Dadurch können Ressourcen direkt in HTL mit data-sly-use abgerufen werden und Sie müssen keinen Code schreiben, um die Ressource zu erhalten.

Beispiel:

```xml
<div data-sly-use.product=“/etc/commerce/product/12345”>
  ${ product.title }
</div>
```

### Request-attributes {#request-attributes}

In *datensly-include* und *data-sly-resource* können Sie jetzt *requestAttributes* weitergeben, um sie im empfangenden HTL-Skript zu verwenden.

So können Sie Parameter in Skripten oder Komponenten ordnungsgemäß weitergeben.

```xml
<sly data-sly-use.settings="com.adobe.examples.htl.core.hashmap.Settings" 
        data-sly-include="${ 'productdetails.html' @ requestAttributes=settings.settings}" />
```

Java-Code der Einstellungsklasse, die Zuordnung wird zum Weitergeben in requestAttributes verwendet:

```xml
public class Settings extends WCMUsePojo {

  // used to pass is requestAttributes to data-sly-resource
  public Map<String, Object> settings = new HashMap<String, Object>();

  @Override
  public void activate() throws Exception {
    settings.put("layout", "flex");
  }
}
```

Beispielsweise können Sie über ein Sling-Model den Wert der angegebenen requestAttributes verbrauchen.

In diesem Beispiel wird *layout* über die Zuordnung aus der Anwendungsklasse eingefügt:

```xml
@Model(adaptables=SlingHttpServletRequest.class)
public class ProductSettings {
  @Inject @Optional @Default(values="empty")
  public String layout;

}
```

### Fehlerbehebung für @extension {#fix-for-extension}

Das Attribut @extension funktioniert in allen Szenarios in AEM 6.3, bevor Sie ein Ergebnis wie *www.adobe.com.html* haben können. Außerdem wird überprüft, ob die Erweiterung hinzugefügt oder nicht hinzugefügt werden soll.

```xml
${ link @ extension = 'html' }
```
