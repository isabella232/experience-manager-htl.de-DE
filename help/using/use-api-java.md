---
title: HTL-Java-Anwendungs-API
seo-title: HTL-Java-Anwendungs-API
description: Mit der Java-Anwendungs-API der HTML-Vorlagensprache – HTL – wird es
  einer HTL-Datei ermöglicht, in einer benutzerdefinierten Java-Klasse auf die Hilfsmethoden
  zuzugreifen.
seo-description: Mit der Java-Anwendungs-API der HTML-Vorlagensprache – HTL – wird
  es einer HTL-Datei ermöglicht, in einer benutzerdefinierten Java-Klasse auf die
  Hilfsmethoden zuzugreifen.
uuid: b340f8f7-a193-45c8-aa39-5c6e2c0194ea
contentOwner: Benutzer
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: Referenz
discoiquuid: 126ebc9d-5f7b-47a4-aea2-c8840d34864c
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 271c355ae56e16e309853b02b8ef09f2ff971a2e

---


# HTL-Java-Anwendungs-API{#htl-java-use-api}

Mit der Java-Anwendungs-API der HTML-Vorlagensprache (HTL) wird es einer HTL-Datei ermöglicht, in einer benutzerdefinierten Java-Klasse auf die Hilfsmethoden zuzugreifen. Dadurch kann die gesamte komplexe Geschäftslogik im Java-Code verkapselt werden, während der HTL-Code nur die direkte Markup-Produktion verarbeiten muss.

## Ein einfaches Beispiel  {#a-simple-example}

Wir beginnen mit einer HTL-Komponente, die über *keine* Anwendungsklasse verfügt. Sie besteht aus einer einzelnen Datei, `/apps/my-example/components/info.html`

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html}

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

Wir fügen zudem etwas Inhalt für diese unter **`/content/my-example/`** darzustellende Komponente hinzu:

### `http://localhost:4502/content/my-example.json` {#http-localhost-content-my-example-json}

```java
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

Wenn auf diesen Inhalt zugegriffen wird, wird die HTL-Datei ausgeführt. Im HTL-Code verwenden wir das Kontextobjekt **`properties`** für den Zugriff auf den `title` und die `description` der aktuellen Ressource und zeigen sie an. Die HTML-Ausgabe lautet:

### `view-source:http://localhost:4502/content/my-example.html` {#view-source-http-localhost-content-my-example-html}

```xml
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Hinzufügen einer Anwendungsklasse {#adding-a-use-class}

Die Komponente **info** benötigt in ihrer gegebenen Form keine Anwendungsklasse zum Ausführen ihrer (sehr einfachen) Funktion. Es gibt Fälle, in denen Sie jedoch Aktionen vornehmen müssen, die nicht in HTL erfolgen können, und daher eine Anwendungsklasse verwenden müssen. Hinweis:

>[!NOTE]
>
>*Eine Anwendungsklasse sollte nur verwendet werden, wenn eine Aktion nicht allein in HTL ausgeführt werden kann.*

Angenommen, Sie möchten, dass die Komponente `info` die Eigenschaften `title` und **`description`** der Ressource anzeigt, jedoch komplett in Kleinschreibung. Da HTL über keine Methode zur Zeichenfolgenkleinschreibung verfügt, benötigen Sie eine Anwendungsklasse. Wir können dies vornehmen, indem eine Java-Anwendungsklasse hinzugefügt und die Datei **`info.html` wie folgt geändert wird:**

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-1}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java}

```java
package apps.my_example.components.info;
 
import com.adobe.cq.sightly.WCMUsePojo;
 
public class Info extends WCMUsePojo {
    private String lowerCaseTitle;
    private String lowerCaseDescription;
 
    @Override
    public void activate() throws Exception {
        lowerCaseTitle = getProperties().get("title", "").toLowerCase();
        lowerCaseDescription = getProperties().get("description", "").toLowerCase();
    }
 
    public String getLowerCaseTitle() {
        return lowerCaseTitle;
    }
 
    public String getLowerCaseDescription() {
        return lowerCaseDescription;
    }
}
```

In den folgenden Abschnitten zeigen wir die unterschiedlichen Teile des Codes.

### Lokale vs. gebündelte Java-Klasse  {#local-vs-bundle-java-class}

Die Java-Anwendungsklasse kann auf zwei Arten installiert werden: **lokal** oder **gebündelt**. *In diesem Beispiel wird eine lokale Installation verwendet.*

In einer lokalen Installation wird die Java-Quelldatei zusammen mit der HTL-Datei im selben Repository-Ordner platziert. Die Quelle wird automatisch nach Bedarf kompiliert. Es ist kein separater Kompilierungs- oder Verpackungsschritt erforderlich.

In einer Bundle-Installation muss die Java-Klasse mithilfe des standardmäßigen AEM-Bundle-Bereitstellungsmechanismus (siehe [Gebündelte Java-Klasse](#bundled-java-class)) in einem OSGi-Bundle kompiliert und bereitgestellt werden.

>[!NOTE]
>
>Eine **lokale Java-Anwendungsklasse** wird empfohlen, wenn die Anwendungsklasse für die betroffene Komponente spezifisch ist.
>
>Eine **gebündelte Java-Anwendungsklasse** wird empfohlen, wenn der Java-Code einen Service implementiert, auf den über mehrere HTL-Komponenten zugegriffen wird.

### Java-Paket ist der Repository-Pfad {#java-package-is-repository-path}

Wenn eine lokale Installation verwendet wird, muss der Paketname der Anwendungsklasse mit dem des Repository-Ordner-Speicherorts übereinstimmen, wobei alle Bindestriche im Pfad durch Unterstriche im Paketnamen ersetzt werden.

In diesem Fall befindet sich **`Info.java`** unter **`/apps/my-example/components/info`**. Daher lautet das Paket :**`apps.my_example.components.info`**

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-1}

```java
package apps.my_example.components.info;
 
import com.adobe.cq.sightly.WCMUsePojo;
 
public class Info extends WCMUsePojo {

   ...

}
```

>[!NOTE]
>
>Bei der Verwendung von Bindestrichen in den Namen von Repository-Elementen handelt es sich um eine empfohlene Praxis in der AEM-Entwicklung. Bindestriche sind jedoch in Java-Paketnamen ungültig. Daher müssen **alle Bindestriche im Repository-Pfad für den Paketnamen in Unterstriche umgewandelt werden**.

### Erweitern `WCMUsePojo` {#extending-wcmusepojo}

Es gibt zwar etliche Möglichkeiten, eine Java-Klasse mit HTL (siehe Alternativen zu `WCMUsePojo`) zu integrieren, jedoch besteht die einfachste Möglichkeit darin, die `WCMUsePojo`-Klasse zu erweitern:

#### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-2}

```java
package apps.my_example.components.info;
 
import com.adobe.cq.sightly.WCMUsePojo;
 
public class Info extends WCMUsePojo {
    
    ...
}
```

### Initialisieren der Klasse {#initializing-the-class}

Wenn die Anwendungsklasse von **`WCMUsePojo`** erweitert wird, wird die Initialisierung vorgenommen, indem die Methode **`activate` überschrieben wird:**

### /apps/my-example/component/info/Info.java {#apps-my-example-component-info-info-java-3}

```java
...

public class Info extends WCMUsePojo {
    private String lowerCaseTitle;
    private String lowerCaseDescription;
 
    @Override
    public void activate() throws Exception {
        lowerCaseTitle = getProperties().get("title", "").toLowerCase();
        lowerCaseDescription = getProperties().get("description", "").toLowerCase();
    }

...

}
```

### Kontext {#context}

In der Regel wird die [aktivierungsmethode](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html) verwendet, um (in Mitgliedervariablen) die Werte zu erstellen und zu speichern, die im HTL-Code benötigt werden, basierend auf dem aktuellen Kontext (z. B. aktuelle Anforderung und Ressource).

Die `WCMUsePojo`-Klasse bietet Zugriff auf denselben Satz an Kontextobjekten, die in einer HTL-Datei verfügbar sind (siehe [Globale Objekte](global-objects.md)).

In einer **`WCMUsePojo`** erweiternden Klasse ist der Zugriff auf Kontextobjekte *nach Name* möglich mit

[`<T> T get(String name, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

Alternativ ist der Zugriff auf häufig verwendete Kontextobjekte direkt über die entsprechende **Convenience-Methode** möglich:

|  |
|---|---|
| [Pagemanager](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/PageManager.html) | [Getpagemanager ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageManager()) |
| [Page](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [Getcurrentpage ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentPage()) |
| [Page](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [Getresourcepage ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourcePage()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [Getpageproperties ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageProperties()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getProperties()) |
| [Designer](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [Getdesigner ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getDesigner()) |
| [Design](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Design.html) | [Getcurrentdesign ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentDesign()) |
| [Stil](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Style.html) | [Getcurrentstyle ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentStyle()) |
| [Component](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/components/Component.html) | [Getcomponent ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getComponent()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [Getinheritedproperties ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse#getInheritedProperties.html()) |
| [Ressource](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/Resource.html) | [Getresource ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResource()) |
| [ResourceResolver](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [Getresourceresolver ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourceResolver()) |
| [Slinghttpservletrequest](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [Getrequest ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getRequest()) |
| [Slinghttpservletresponse](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [Getresponse ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResponse()) |
| [Slingscripthelper](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [Getslingscripthelper ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getSlingScriptHelper()) |

### Getter-Methoden {#getter-methods}

Nach der Initialisierung der Anwendungsklasse wird die HTL-Datei ausgeführt. In dieser Phase ruft HTL für gewöhnlich den Status verschiedener Member-Variablen der Anwendungsklasse ab und rendert sie für die Darstellung.

Um den Zugriff auf diese Werte in der HTL-Datei zu ermöglichen, müssen Sie **gemäß der folgenden Benennungskonvention** benutzerdefinierte Getter-Methoden in der Anwendungsklasse definieren:

* Eine Methode in der Form **`getXyz`** stellt in der HTL-Datei eine Objekteigenschaft mit dem Namen ***xyz*** zur Verfügung.

Beispielsweise führen im folgenden Beispiel die Methoden **`getTitle`** und **`getDescription`** zu den Objekteigenschaften **`title`** und **`description`**, wodurch auf sie im Kontext der HTL-Datei zugegriffen werden kann:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-4}

```java
...

public class Info extends WCMUsePojo {

    ...
 
    public String getLowerCaseTitle() {
        return lowerCaseTitle;
    }
 
    public String getLowerCaseDescription() {
        return lowerCaseDescription;
    }
}
```

### Attribut „data-sly-use“{#data-sly-use-attribute}

Das Attribut **`data-sly-use`** wird verwendet, um die Anwendungsklasse innerhalb Ihres HTL-Codes zu initialisieren. In unserem Beispiel deklariert das Attribut `data-sly-use`, dass wir die Klasse **`Info`** verwenden möchten. Wir können nur den lokalen Namen der Klasse verwenden, da wir eine lokale Installation verwenden (wobei die Java-Quelldatei im selben Ordner wie die HTL-Datei platziert wurde). Bei Verwendung einer Bundle-Installation müssten wir den vollqualifizierten Klassennamen (siehe hierzu das Thema über die [Anwendungsklassen-Bundle-Installation](#LocalvsBundleJavaClass)) angeben.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-2}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Lokaler Bezeichner {#local-identifier}

Der Bezeichner „**`info`**“ (nach dem Punkt in **`data-sly-use.info`**) wird in der HTL-Datei verwendet, um die Klasse anzugeben. Dieser Bezeichner erstreckt sich global in der Datei, nachdem er deklariert wurde. Er ist nicht auf das Element beschränkt, das die Anweisung `data-sly-use` enthält.

### `/apps/my-example/component/info/info.html`{#apps-my-example-component-info-info-html-3}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Abrufen von Eigenschaften {#getting-properties}

Der Bezeichner `info` wird anschließend verwendet, um auf die Objekteigenschaften **`title`** und **`description`** zuzugreifen, die mithilfe der Getter-Methoden `Info.getTitle` und **`Info.getDescription`** zur Verfügung gestellt wurden.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-4}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Ausgabe {#output}

Wenn wir nun auf **`/content/my-example.html`** zugreifen, wird die folgende HTML zurückgegeben:

### `view-source:http://localhost:4502/content/my-example.html` {#view-source-http-localhost-content-my-example-html-1}

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

## Über die Grundlagen hinaus {#beyond-the-basics}

In diesem Abschnitt werden einige weitere Funktionen vorgestellt, die über das obige einfache Beispiel hinausgehen:

* Weitergeben von Parametern an eine Anwendungsklasse
* Gebündelte Java-Anwendungsklasse
* Alternativen zu `WCMUsePojo`

### Weitergeben von Parametern {#passing-parameters}

Parameter können bei der Initialisierung an eine Anwendungsklasse weitergegeben werden. Zum Beispiel können wir Folgendes vornehmen:

### `/content/my-example/component/info/info.html` {#content-my-example-component-info-info-html}

```xml
<div data-sly-use.info="${'Info' @ text='Some text'}">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
    <p>${info.upperCaseText}</p>
</div>
```

Hier geben wir einen Parameter mit dem Namen **`text`** weiter. Die Anwendungsklasse setzt die Zeichenfolge, die wir abrufen, dann in Großbuchstaben und zeigt das Ergebnis mit `info.upperCaseText` an. Hier finden Sie die angepasste Anwendungsklasse:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-5}

```java
package apps.my_example.components.info;
 
import com.adobe.cq.sightly.WCMUsePojo;
 
public class Info extends WCMUsePojo {
    
    ...

    private String reverseText;
    
    @Override
    public void activate() throws Exception {

        ...
        
        String text = get("text", String.class);
        reverseText = new StringBuffer(text).reverse().toString();

    }
 
    public String getReverseText() {
        return reverseText;
    }

    ...
}
```

Der Zugriff auf den Parameter erfolgt über die Methode `WCMUsePojo`

[ `<T> T get(String paramName, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

In unserem Fall ist dies die Anweisung

`get("text", String.class)`

Die Zeichenfolge wird dann umgekehrt und zur Verfügung gestellt über die Methode

**`getReverseText()`**

### Parameter nur aus der data-sly-template weitergeben {#only-pass-parameters-from-data-sly-template}

Auch wenn das obige Beispiel aus technischer Sicht richtig ist, ist es in Wirklichkeit nicht sehr sinnvoll, einen Wert aus HTL zum Initialisieren einer Anwendungsklasse weiterzugeben, wenn der entsprechende Wert im Ausführungskontext des HTL-Codes verfügbar ist (oder der Wert ist trivialerweise statisch, wie oben).

Der Grund besteht darin, dass die Anwendungsklasse immer auf denselben Ausführungskontext wie der HTL-Code zugreifen kann. Dies bringt einen wichtigen Best-Practice-Punkt ans Licht:

>[!NOTE]
>
>Das Weitergeben eines Parameters an eine Anwendungsklasse sollte nur dann erfolgen, wenn die Anwendungsklasse in einer **data-sly-template**-Datei verwendet wird, die wiederum über eine andere HTL-Datei mit Parametern abgerufen wird, die weitergegeben werden müssen.

Lassen Sie uns beispielsweise eine separate `data-sly-template`-Datei neben unserem vorhandenen Beispiel erstellen. Wir nennen die neue Datei `extra.html`. Sie enthält einen **`data-sly-template`**-Block mit dem Namen **`extra`**:

### `/apps/my-example/component/info/extra.html` {#apps-my-example-component-info-extra-html}

```xml
<template data-sly-template.extra="${@ text}"
          data-sly-use.extraHelper="${'ExtraHelper' @ text=text}">
  <p>${extraHelper.reversedText}</p>
</template>
```

Die Vorlage **`extra`** verwendet einen einzelnen Parameter **`text`**. Anschließend initialisiert sie die Java-Anwendungsklasse `ExtraHelper` mit dem lokalen Namen **`extraHelper`** und gibt den Wert des Vorlagenparameters **`text`** als den Anwendungsklassenparameter **`text`** weiter.

Der Text der Vorlage erhält die Eigenschaft `extraHelper.reversedText` (die im Hintergrund `ExtraHelper.getReversedText()` aufruft) und zeigt diesen Wert an.

Wir passen auch unsere vorhandene **`info.html`** für die Verwendung dieser neuen Vorlage an:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-5}

```xml
<div data-sly-use.info="Info" 
     data-sly-use.extra="extra.html">
    
  <h1>${info.lowerCaseTitle}</h1>
  <p>${info.lowerCaseDescription}</p>
    
  <div data-sly-call="${extra.extra @ text=properties.description}"></div>

</div>
```

Die Datei `info.html` enthält nun zwei **`data-sly-use`**-Anweisungen: die ursprüngliche, die die Java-Anwendungsklasse **`Info`** importiert, und eine neue, die die Vorlagendatei unter dem lokalen Namen `extra` importiert.

Beachten Sie, dass wir den Vorlagenblock in der Datei **`info.html`** platzieren könnten, um eine zweite **`data-sly-use`** zu verhindern. Eine separate Vorlagendatei ist jedoch gängiger und besser wiederverwendbar.

Die **`Info`**-Klasse wird wie zuvor verwendet und ruft ihre Getter-Methoden **`getLowerCaseTitle()`** und `getLowerCaseDescription()` durch ihre entsprechenden HTL-Eigenschaften `info.lowerCaseTitle` und **`info.lowerCaseDescription`** auf.

Anschließend führen wir einen **`data-sly-call`** zur Vorlage**`extra`** aus und geben ihr den Wert `properties.description` als den Parameter **`text`** weiter.

Die Java-Anwendungsklasse `Info.java` wird geändert, um den neuen Textparameter zu verarbeiten:

### `/apps/my-example/component/info/ExtraHelper.java` {#apps-my-example-component-info-extrahelper-java}

```java
package apps.my_example.components.info;
 
import com.adobe.cq.sightly.WCMUsePojo;
 
public class ExtraHelper extends WCMUsePojo {
    private String reversedText;
    ...
    
    @Override
    public void activate() throws Exception {
        String text = get("text", String.class);      
        reversedText = new StringBuilder(text).reverse().toString();

        ...
    }
 
    public String getReversedText() {
        return reversedText;
    }
}
```

Der Parameter **`text`** wird mit **`get("text", String.class)`** abgerufen, der Wert wird umgekehrt und über den Getter `reversedText` als HTL-Objekt `getReversedText()` zur Verfügung gestellt.

### Gebündelte Java-Klasse {#bundled-java-class}

Bei einer Bundle-Anwendungsklasse muss die Klasse mithilfe der standardmäßigen OSGi-Bundle-Bereitstellungsmethode kompiliert, verpackt und in AEM bereitgestellt werden. Im Gegensatz zu einer lokalen Installation sollte die Anwendungsklassen-**Paketdeklaration** normal benannt werden:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-6}

```java
package org.example.app.components;
 
import com.adobe.cq.sightly.WCMUsePojo;
 
public class Info extends WCMUsePojo {
    ...
}
```

Zudem muss die Anweisung `data-sly-use` auf den *vollqualifizierten Klassennamen* verweisen und nicht nur auf den lokalen Klassennamen:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-6}

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```

### Alternativen zu `WCMUsePojo`{#alternatives-to-wcmusepojo}

Die gängigste Möglichkeit, eine Java-Anwendungsklasse zu erstellen, besteht darin, `WCMUsePojo` zu erweitern. Es gibt jedoch einige andere Optionen. Für das Verständnis dieser Varianten ist es hilfreich zu wissen, wie die HTL-Anweisung `data-sly-use` im Hintergrund funktioniert.

Angenommen, Sie verfügen über die folgende `data-sly-use`-Anweisung:

**`<div data-sly-use.`** `localName`**`="`** `UseClass`**`">`**

Das System verarbeitet die Anweisung wie folgt:

(1)

* Wenn eine lokale *UseClass.java*-Datei im selben Verzeichnis wie die HTL-Datei vorhanden ist, versuchen Sie, diese Klasse zu kompilieren und zu laden. Wechseln Sie bei Erfolg zu (2).

* Interpretieren Sie andernfalls *UseClass* als einen vollqualifizierten Klassennamen und versuchen Sie, ihn über die OSGi-Umgebung zu laden. Wechseln Sie bei Erfolg zu (2).
* Interpretieren Sie andernfalls *UseClass* als einen Pfad zu einer HTL- oder JavaScript-Datei und laden Sie diese Datei. Wechseln Sie bei Erfolg zu (4).

(2)

* Versuchen Sie, die aktuelle **`Resource`** an *`UseClass.`* anzupassen. Wechseln Sie bei Erfolg zu (3).

* Versuchen Sie andernfalls, die aktuelle ** `Request`** an *`UseClass`* anzupassen. Wechseln Sie bei Erfolg zu (3).

* Versuchen Sie andernfalls, *`UseClass` mit einem Zero-Argument-Konstruktor zu instanziieren. * Wechseln Sie bei Erfolg zu (3).

(3)

* Verbinden Sie in HTL das neu angepasste oder erstellte Objekt mit dem Namen *`localName`*.
* Wenn *`UseClass`* implementiert wird [`io.sightly.java.api.Use`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html) , rufen Sie die `init` Methode auf und übergeben Sie den aktuellen Ausführungskontext (in Form eines `javax.scripting.Bindings` Objekts).

(4)

* Wenn *`UseClass`* ein Pfad zu einer HTL-Datei ist, die eine `data-sly-template` enthält, sollten Sie die Vorlage vorbereiten.

* Wenn andernfalls *`UseClass`* ein Pfad zu einer JavaScript-Anwendungsklasse ist, sollten Sie die Anwendungsklasse vorbereiten (siehe [JavaScript-Anwendungs-API](use-api-javascript.md)).

Im Folgenden finden Sie einige wichtige Punkte zur obigen Beschreibung:

* Jede Klasse, die aus einer `Resource` oder einer `Request` angepasst werden kann oder einen Zero-Argument-Konstruktor aufweist, kann eine Anwendungsklasse sein. Die Klasse muss `WCMUsePojo` nicht erweitern oder gar `Use` implementieren.

* Wenn die Anwendungsklasse * jedoch *`Use` implementiert, wird die zugehörige **`init`**-Methode im aktuellen Kontext automatisch abgerufen, wodurch Sie dort kontextbasierten Initialisierungscode platzieren können.

* Eine `WCMUsePojo` erweiternde Anwendungsklasse ist nur ein Sonderfall der Implementierung von **`Use`**. Sie bietet praktische Kontextmethoden und ihre **`activate`**-Methode wird automatisch über `Use.init` abgerufen.

### Direkte Implementierung der Schnittstellenverwendung {#directly-implement-interface-use}

Auch wenn die einfachste Möglichkeit für die Erstellung einer Anwendungsklasse darin besteht, `WCMUsePojo` zu erweitern, ist es auch möglich, die `[io.sightly.java.api.Use](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html)`-Schnittstelle direkt zu implementieren.

Die Schnittstelle `Use` definiert nur eine Methode:

`[public void init(javax.script.Bindings bindings)](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use#init(javax.script.Bindings))`

Die **`init`**-Methode wird beim Initialisieren der Klasse mit einem **`Bindings`-Objekt abgerufen, das alle Kontextobjekte und die an die Anwendungsklasse weitergegebenen Parameter enthält.**

Alle zusätzlichen Funktionalitäten (wie die Entsprechung von `WCMUsePojo.getProperties()`) müssen explizit mithilfe des Objekts ` [javax.script.Bindings](http://docs.oracle.com/javase/7/docs/api/javax/script/Bindings.html)` implementiert werden. Beispiel:

### `Info.java` {#info-java}

```java
import io.sightly.java.api.Use;

public class MyComponent implements Use {
   ...
    @Override
    public void init(Bindings bindings) {

        // All standard objects/binding are available
        Resource resource = (Resource)bindings.get("resource");
        ValueMap properties = (ValueMap)bindings.get("properties"); 
        ...

        // Parameters passed to the use-class are also available
        String param1 = (String) bindings.get("param1");
    }
    ...
}
```

Die Hauptursache für das eigenständige Implementieren der Schnittstelle `Use` anstelle der Erweiterung von `WCMUsePojo` besteht in der gewünschten Verwendung einer Unterklasse einer bereits als die Anwendungsklasse verwendeten vorhandenen Klasse.

### Aus Ressource anpassbar {#adaptable-from-resource}

Eine weitere Option besteht in der Verwendung einer Hilfsklasse, die über **`org.apache.sling.api.resource.Resource`** angepasst werden kann.

Angenommen, Sie müssen ein HTL-Skript schreiben, dass den mimetyp eines DAM-Assets anzeigt. In diesem Fall wissen Sie, dass sich Ihr HTL-Skript, wenn es abgerufen wird, im Kontext einer **`Resource`** befindet, die einen JCR-**`Node`** mit Knotentyp **`dam:Asset`** umbricht.

Sie wissen, dass ein Knoten vom Typ **`dam:Asset` über eine derartige Struktur verfügt:**

### Repository-Struktur {#repository-structure}

```java
{
  "content": {
    "dam": {
      "geometrixx": {
        "portraits": {
          "jane_doe.jpg": {
            ...
          "jcr:content": {
            ...
            "metadata": {
              ...
            },
            "renditions": {
              ...
              "original": {
                ...
                "jcr:content": {
                  "jcr:primaryType": "nt:resource",
                  "jcr:lastModifiedBy": "admin",
                  "jcr:mimeType": "image/jpeg",
                  "jcr:lastModified": "Fri Jun 13 2014 15:27:39 GMT+0200",
                  "jcr:data": ...,
                  "jcr:uuid": "22e3c598-4fa8-4c5d-8b47-8aecfb5de399"
                }
              },
              "cq5dam.thumbnail.319.319.png": {
                  ...
              },
              "cq5dam.thumbnail.48.48.png": {
                  ...
              },
              "cq5dam.thumbnail.140.100.png": {
                  ...
              }
            }
          }  
        }
      }
    }
  }
}
```

Hier zeigen wir das Asset (ein JPEG-Bild), das bei einer Standardinstallation von AEM als Bestandteil des Beispielprojekts geometrixx integriert ist. Das Asset heißt **`jane_doe.jpg`** und der zugehörige mimetype lautet **`image/jpeg`**.

Um in der HTL auf das Asset zuzugreifen, können Sie ` [com.day.cq.dam.api.Asset](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/granite/asset/api/Asset.html)` als die Klasse in der Anweisung **`data-sly-use`** deklarieren und dann eine Get-Methode des **`Asset`** verwenden, um die gewünschten Informationen abzurufen. Beispiel:

### `mimetype.html` {#mimetype-html}

```xml
<div data-sly-use.asset="com.day.cq.dam.api.Asset">
  <p>${asset.mimeType}</p>
</div>
```

Die Anweisung `data-sly-use`** fordert die HTL auf, die aktuelle `Resource`** für ein **`Asset`** anzupassen, und gibt ihr den lokalen Namen **`asset`**. Anschließend ruft sie die Methode **`getMimeType`** des `Asset` mithilfe der HTL-Getter-Kurzschreibweise auf: `asset.mimeType`.

### Aus Anforderung anpassbar {#adaptable-from-request}

Es ist ebenfalls möglich, jede Klasse, die über **` [org.apache.sling.api.SlingHttpServletRequest](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html)`** angepasst werden kann, als eine Anwendungsklasse zu verwenden.

Wie beim obigen Fall einer use-class-adaptable Klasse `Resource`kann eine use-class-adaptable aus [`SlingHttpServletRequest`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) in der `data-sly-use` Anweisung angegeben werden. Bei der Ausführung wird die aktuelle Anforderung an die gegebene Klasse angepasst und das daraus resultierende Objekt wird in HTL zur Verfügung gestellt.
