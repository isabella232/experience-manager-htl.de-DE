---
title: Was sind HTL-Blockanweisungen?
description: Erfahren Sie mehr über HTL-Blockanweisungen oder die HTML Vorlagensprache (HTL). Blockanweisungen sind benutzerdefinierte Datenattribute, die direkt zur bestehenden HTML hinzugefügt werden.
exl-id: a517dcef-ab7a-4d4c-a1a9-2e57aad034f7
source-git-commit: 79d299766da07dae001708b396b05c73cd70d4cc
workflow-type: ht
source-wordcount: '1563'
ht-degree: 100%

---

# HTL-Blockanweisungen {#htl-block-statements}

Bei HTML-Vorlagensprachen-Blockanweisungen (HTL) handelt es sich um direkt zur vorhandenen HTML hinzugefügten `data`-Attributen. Dadurch kann eine statische HTML-Musterseite einfach und unauffällig mit Anmerkungen versehen werden, wodurch sie in eine funktionierende dynamische Vorlage umgewandelt wird, ohne die Gültigkeit des HTML-Codes außer Kraft zu setzen.

## Blockübersicht {#overview}

HTL-Block-Plug-ins werden definiert durch `data-sly-*`-Attribute, die auf HTML-Elementen festgelegt sind. Elemente können ein schließendes Tag aufweisen oder sich selbst schließen. Attribute können Werte aufweisen (die statische Zeichenfolgen oder Ausdrücke sein können) oder einfach boolesche Attribute sein (ohne Wert).

```xml
<tag data-sly-BLOCK></tag>                                 <!--/* A block is simply consists in a data-sly attribute set on an element. */-->
<tag data-sly-BLOCK/>                                      <!--/* Empty elements (without a closing tag) should have the trailing slash. */-->
<tag data-sly-BLOCK="string value"/>                       <!--/* A block statement usually has a value passed, but not necessarily. */-->
<tag data-sly-BLOCK="${expression}"/>                      <!--/* The passed value can be an expression as well. */-->
<tag data-sly-BLOCK="${@ myArg='foo'}"/>                   <!--/* Or a parametric expression with arguments. */-->
<tag data-sly-BLOCKONE="value" data-sly-BLOCKTWO="value"/> <!--/* Several block statements can be set on a same element. */-->
```

Alle ausgewerteten `data-sly-*`-Attribute werden aus dem generierten Markup entfernt.

### Bezeichner {#identifiers}

Auf eine Blockanweisung kann auch eine Kennung folgen:

```xml
<tag data-sly-BLOCK.IDENTIFIER="value"></tag>
```

Die Kennung kann von der Blockanweisung auf verschiedene Weise verwendet werden. Hier sind einige Beispiele:

```xml
<!--/* Example of statements that use the identifier to set a variable with their result: */-->
<div data-sly-use.navigation="MyNavigation">${navigation.title}</div>
<div data-sly-test.isEditMode="${wcmmode.edit}">${isEditMode}</div>
<div data-sly-list.child="${currentPage.listChildren}">${child.properties.jcr:title}</div>
<div data-sly-template.nav>Hello World</div>

<!--/* The attribute statement uses the identifier to know to which attribute it should apply it's value: */-->
<div data-sly-attribute.title="${properties.jcr:title}"></div> <!--/* This will create a title attribute */-->
```

Bei den Kennungen der obersten Ebene wird nicht zwischen Groß- und Kleinschreibung unterschieden (da sie über HTML-Attribute festgelegt werden können, bei denen nicht zwischen Groß- und Kleinschreibung unterschieden wird). Bei allen Eigenschaften wird aber zwischen Groß- und Kleinschreibung unterschieden.

## Verfügbare Blockanweisungen {#available-block-statements}

Es gibt eine Reihe von Blockanweisungen. Bei Verwendung für dasselbe Element definiert die folgende Prioritätenliste, wie Blockanweisungen ausgewertet werden:

1. `data-sly-template`
1. `data-sly-set`, `data-sly-test`, `data-sly-use`
1. `data-sly-call`
1. `data-sly-text`
1. `data-sly-element`, `data-sly-include`, `data-sly-resource`
1. `data-sly-unwrap`
1. `data-sly-list`, `data-sly-repeat`
1. `data-sly-attribute`

Wenn zwei Blockanweisungen dieselbe Priorität haben, erfolgt ihre Auswertungsreihenfolge von links nach rechts.

### use {#use}

`data-sly-use` initialisiert ein Hilfsobjekt (definiert in JavaScript oder Java) und stellt es über eine Variable bereit.

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

Parameter können mithilfe von Optionen an die Initialisierung weitergegeben werden. Diese Funktion sollte generell nur durch HTL-Code verwendet werden, der sich selbst in einem `data-sly-template`-Block befindet:

```xml
<div data-sly-use.nav="${'navigation.js' @parentPage=currentPage}">${nav.foo}</div>
```

Initialisieren Sie eine andere HTL-Vorlage, die mithilfe von `data-sly-call` abgerufen werden kann:

```xml
<div data-sly-use.nav="navTemplate.html" data-sly-call="${nav.foo}"></div>
```

>[!NOTE]
>
>Weitere Informationen über die Anwendungs-API finden Sie unter:
>
>* [Java-Anwendungs-API](use-api-java.md)
>* [JavaScript-Anwendungs-API](use-api-javascript.md)


#### data-sly-use mit Ressourcen {#data-sly-use-with-resources}

Dadurch können Ressourcen direkt mit `data-sly-use` in HTL abgerufen werden, ohne dass dafür Code geschrieben werden muss.

Beispiel:

```xml
<div data-sly-use.product=“/etc/commerce/product/12345”>
  ${ product.title }
</div>
```

>[!TIP]
>
>Siehe auch den Abschnitt [Pfad nicht immer erforderlich](#path-not-required).

### unwrap  {#unwrap}

`data-sly-unwrap` entfernt das Host-Element aus dem erstellten Markup und behält seinen Inhalt bei. Dadurch können Elemente ausgeschlossen werden, die als Bestandteil der HTL-Darstellungslogik erforderlich, in der tatsächlichen Ausgabe jedoch nicht erwünscht sind.

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

### set {#set}

`data-sly-set` definiert eine neue Kennung mit einem vordefinierten Wert.

```xml
<span data-sly-set.profile="${user.profile}">Hello, ${profile.firstName} ${profile.lastName}!</span>
<a class="profile-link" href="${profile.url}">Edit your profile</a>
```

### text {#text}

`data-sly-text` ersetzt den Inhalt seines Host-Elements durch den angegebenen Text.

Dies beispielsweise

```xml
<p>${properties.jcr:description}</p>
```

entspricht

```xml
<p data-sly-text="${properties.jcr:description}">Lorem ipsum</p>
```

Beide zeigen den Wert von `jcr:description` als Absatztext an. Der Vorteil der zweiten Methode besteht darin, dass sie die unauffällige Anmerkung der HTML ermöglicht und gleichzeitig den statischen Platzhalterinhalt aus dem ursprünglichen Designer beibehält.

### attribute  {#attribute}

`data-sly-attribute` fügt dem Host-Element Attribute hinzu.

Dies beispielsweise

```xml
<div title="${properties.jcr:title}"></div>
```

entspricht

```xml
<div title="Lorem Ipsum" data-sly-attribute.title="${properties.jcr:title}"></div>
```

Beide legen das Attribut `title` auf den Wert von `jcr:title` fest. Der Vorteil der zweiten Methode besteht darin, dass sie die unauffällige Anmerkung der HTML ermöglicht und gleichzeitig den statischen Platzhalterinhalt aus dem ursprünglichen Designer beibehält.

Attribute werden von links nach rechts aufgelöst, wobei die am weitesten rechts liegende Instanz eines Attributs (Literal oder definiert über `data-sly-attribute`) vor anderen, links davon definierten Instanzen desselben Attributs (`data-sly-attribute`) Vorrang hat.

Beachten Sie, dass ein Attribut (`literal` oder festgelegt über `data-sly-attribute`), dessen Wert in einer leeren Zeichenfolge ausgewertet wird, aus dem endgültigen Markup entfernt wird. Die einzige Ausnahme von dieser Regel ist, dass ein auf eine leere Literal-Zeichenfolge festgelegtes Literal-Attribut beibehalten wird. Beispiel:

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

### element  {#element}

`data-sly-element` ersetzt den Elementnamen des Host-Elements.

Beispiel:

```xml
<h1 data-sly-element="${titleLevel}">text</h1>
```

Ersetzt `h1` durch den Wert `titleLevel`.

Aus Sicherheitsgründen akzeptiert `data-sly-element` nur die folgenden Elementnamen:

```xml
a abbr address article aside b bdi bdo blockquote br caption cite code col colgroup
data dd del dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header i ins
kbd li main mark nav ol p pre q rp rt ruby s samp section small span strong sub
sup table tbody td tfoot th thead time tr u var wbr
```

Zum Festlegen anderer Elemente muss die XSS-Sicherheit deaktiviert sein ( `@context='unsafe'`).

### test {#test}

`data-sly-test` entfernt bedingt das Host-Element und seinen Inhalt. Durch den Wert `false` wird das Element entfernt. Durch den Wert `true` wird das Element beibehalten.

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

### repeat {#repeat}

Mit `data-sly-repeat` können Sie ein Element basierend auf der angegebenen Liste mehrmals wiederholen.

```xml
<div data-sly-repeat="${currentPage.listChildren}">${item.name}</div>
```

Dies funktioniert genauso wie `data-sly-list`, mit der Ausnahme, dass Sie kein Container-Element benötigen.

Das folgende Beispiel zeigt, dass Sie für Attribute auch auf das *Element* verweisen können:

```xml
<div data-sly-repeat="${currentPage.listChildren}" data-sly-attribute.class="${item.name}">${item.name}</div>
```

### list {#list}

`data-sly-list` wiederholt den Inhalt des Host-Elements für jede aufzählbare Eigenschaft in dem angegebenen Objekt.

Im Folgenden finden Sie eine einfache Schleife:

```xml
<dl data-sly-list="${currentPage.listChildren}">
    <dt>index: ${itemList.index}</dt>
    <dd>value: ${item.title}</dd>
</dl>
```

Die folgenden standardmäßigen Variablen sind im Umfang der Liste verfügbar:

* `item`: Das aktuelle Elemente in der Wiederholung.
* `itemList`: Objekt, das die folgenden Eigenschaften enthält:
* `index`: Zähler auf Basis null ( `0..length-1`).
* `count`: Zähler auf Basis eins ( `1..length`).
* `first`: `true`, wenn das aktuelle Element das erste Element ist.
* `middle`: `true`, wenn das aktuelle Element weder das erste noch das letzte Element ist.
* `last`: `true`, wenn das aktuelle Element das letzte Element ist.
* `odd`: `true`, wenn `index` ungerade ist.
* `even`: `true`, wenn `index` gerade ist.

Durch das Definieren eines Bezeichners für die Anweisung `data-sly-list` können Sie die Variablen `itemList` und `item` umbenennen. `item` wird `<variable>` und `itemList` wird `<variable>List`.

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

### Ressource  {#resource}

`data-sly-resource` enthält das Ergebnis des Renderings der angegebenen Ressource durch den Sling-Auflösungs- und Rendering-Prozess.

Zu einer einfachen Ressource gehören:

```xml
<article data-sly-resource="path/to/resource"></article>
```

#### Pfad nicht immer erforderlich {#path-not-required}

Beachten Sie, dass die Verwendung eines Pfads mit `data-sly-resource` nicht erforderlich ist, wenn Sie bereits über die Ressource verfügen. Wenn Sie bereits über die Ressource verfügen, können Sie sie direkt verwenden.

Beispielsweise ist Folgendes richtig.

```xml
<sly data-sly-resource="${resource.path @ decorationTagName='div'}"></sly>
```

Aber auch Folgendes ist völlig akzeptabel.

```xml
<sly data-sly-resource="${resource @ decorationTagName='div'}"></sly>
```

Aus den folgenden Gründen wird empfohlen, die Ressource direkt zu verwenden, wenn dies möglich ist.

* Wenn Sie bereits über die Ressource verfügen, ist eine erneute Auflösung mithilfe des Pfads zusätzliche, unnötige Arbeit.
* Die Verwendung des Pfads, wenn Sie bereits über die Ressource verfügen, kann zu unerwarteten Ergebnissen führen, da Sling-Ressourcen umschlossen oder synthetisch sein können und nicht im angegebenen Pfad bereitgestellt werden.

#### Optionen {#resource-options}

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
>AEM bietet klare und einfache Logik, die die decoration-Tags steuert, die enthaltene Elemente umschließen. Weitere Informationen finden Sie unter [Dekorations-Tag](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/implementing/developing/full-stack/components-templates/decoration-tag.html?lang=de) in der Dokumentation zu Entwicklungskomponenten.

### include {#include}

`data-sly-include` ersetzt den Inhalt des Host-Elements durch das von der angegebenen HTML-Vorlagendatei (HTL, JSP, ESP usw.) erzeugte Markup. wenn er durch das entsprechende Vorlagenmodul verarbeitet wird. Der Darstellungskontext der eingeschlossenen Datei enthält nicht den aktuellen HTL-Kontext (den der einzuschließenden Datei). Demzufolge muss das aktuelle Element `data-sly-use` für das Einschließen der HTL-Dateien in der eingeschlossenen Datei wiederholt werden (in einem solchen Fall ist es für gewöhnlich besser, `data-sly-template` und `data-sly-call` zu verwenden).

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

### Request-attributes {#request-attributes}

In `data-sly-include` und `data-sly-resource` können Sie jetzt `requestAttributes` übergeben, um sie in dem empfangenden HTL-Skript zu verwenden.

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

Zum Beispiel können Sie über ein Sling-Modell den Wert des angegebenen `requestAttributes` nutzen.

In diesem Beispiel wird das Layout über die Zuordnung aus der Anwendungsklasse eingefügt:

```xml
@Model(adaptables=SlingHttpServletRequest.class)
public class ProductSettings {
  @Inject @Optional @Default(values="empty")
  public String layout;

}
```

### template und call  {#template-call}

Vorlagenblöcke können wie Funktionsaufrufe verwendet werden: In ihrer Deklaration können sie Parameter erhalten, die dann beim Aufruf übergeben werden können. Sie ermöglichen auch Rekursionen.

`data-sly-template` definiert eine Vorlage. Das Host-Element und die zugehörigen Inhalte werden durch HTL nicht ausgegeben

`data-sly-call` ruft eine mit data-sly-template definierte Vorlage ab. Der Inhalt der abgerufenen Vorlage (optional parametrisiert) ersetzt den Inhalt des Hostelements des Aufrufs.

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

## sly-Element {#sly-element}

Das HTML-Tag `<sly>` kann verwendet werden, um das aktuelle Element zu entfernen, sodass nur die untergeordneten Elemente angezeigt werden. Seine Funktionalität ist ähnlich wie die des `data-sly-unwrap`-Blockelements:

```xml
<!--/* This will display only the output of the 'header' resource, without the wrapping <sly> tag */-->
<sly data-sly-resource="./header"></sly>
```

Obwohl es sich nicht um ein gültiges HTML 5-Tag handelt, kann das `<sly>`-Tag in der endgültigen Ausgabe mit `data-sly-unwrap` angezeigt werden:

```xml
<sly data-sly-unwrap="${false}"></sly> <!--/* outputs: <sly></sly> */-->
```

Das `<sly>`-Element soll verdeutlichen, dass es sich nicht um eine Ausgabe handelt. Wenn Sie möchten, können Sie weiterhin `data-sly-unwrap` verwenden.

Wie bei `data-sly-unwrap` sollten Sie versuchen, dies so wenig wie möglich zu verwenden.
