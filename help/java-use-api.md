---
title: HTL-Java-Anwendungs-API
description: Die HTL-Java-Anwendungs-API ermöglicht einer HTL-Datei den Zugriff auf Hilfsmethoden in einer benutzerdefinierten Java-Klasse.
exl-id: 9a9a2bf8-d178-4460-a3ec-cbefcfc09959
source-git-commit: da2375a2390f0254dba9745d9f4970e88788e5d5
workflow-type: tm+mt
source-wordcount: '1518'
ht-degree: 39%

---


# HTL-Java-Anwendungs-API {#htl-java-use-api}

Die HTL-Java-Anwendungs-API ermöglicht einer HTL-Datei den Zugriff auf Hilfsmethoden in einer benutzerdefinierten Java-Klasse.

## Nutzungsszenario {#use-case}

Die HTL-Java-Anwendungs-API ermöglicht einer HTL-Datei den Zugriff auf Hilfsmethoden in einer benutzerdefinierten Java-Klasse über `data-sly-use`. Dadurch kann die gesamte komplexe Geschäftslogik im Java-Code verschachtelt werden, während der HTL-Code nur die direkte Markup-Produktion verarbeiten muss.

Ein Java-Anwendungs-API-Objekt kann ein einfacher POJO sein, der von einer bestimmten Implementierung über den Standardkonstruktor von POJO instanziiert wird.

Die Anwendungs-API-POJOs können auch eine öffentliche Methode namens init mit der folgenden Signatur bereitstellen:

```java
    /**
     * Initializes the Use bean.
     *
     * @param bindings All bindings available to the HTL scripts.
     **/
    public void init(javax.script.Bindings bindings);
```

Die `bindings`-Karte kann Objekte enthalten, die dem derzeit ausgeführten HTL-Skript einen Kontext geben, den das Anwendungs-API-Objekt für seine Verarbeitung nutzen kann.

## Ein einfaches Beispiel  {#a-simple-example}

Dieses Beispiel zeigt die Verwendung der Anwendungs-API.

>[!NOTE]
>
>Dieses Beispiel wurde vereinfacht, um seine Verwendung zu veranschaulichen. In einer Produktionsumgebung wird die Verwendung von [Sling-Modelle.](https://sling.apache.org/documentation/bundles/models.html)

Wir beginnen mit einer HTL-Komponente namens `info`, das keine Anwendungsklasse hat. Sie besteht aus einer einzelnen Datei, `/apps/my-example/components/info.html`

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

Wir fügen zudem etwas Inhalt für diese unter `/content/my-example/` darzustellende Komponente hinzu:

```xml
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

Wenn auf diesen Inhalt zugegriffen wird, wird die HTL-Datei ausgeführt. Im HTL-Code verwenden wir das context-Objekt `properties` , um auf die `title` und `description` und zeigen sie an. Die Ausgabedatei `/content/my-example.html` lautet:

```html
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Hinzufügen einer Anwendungsklasse {#adding-a-use-class}

Die `info` -Komponente in der gegebenen Form benötigt keine Anwendungsklasse, um ihre einfache Funktion auszuführen. Es gibt Fälle, in denen Sie jedoch Aktionen vornehmen müssen, die nicht in HTL erfolgen können, und daher eine Anwendungsklasse verwenden müssen. Hinweis:

>[!NOTE]
>
>Eine Anwendungsklasse sollte nur verwendet werden, wenn eine Aktion nicht allein in HTL ausgeführt werden kann.

Angenommen, Sie möchten, dass die Komponente `info` die Eigenschaften `title` und `description` der Ressource anzeigt, jedoch komplett in Kleinschreibung. Da HTL über keine Methode zur Zeichenfolgen-Kleinschreibung verfügt, benötigen Sie eine Anwendungsklasse. Dazu können wir eine Java-Anwendungsklasse hinzufügen und `/apps/my-example/component/info/info.html` wie folgt:

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

Zusätzlich erstellen wir `/apps/my-example/component/info/Info.java`.

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

Siehe [Javadocs für `com.adobe.cq.sightly.WCMUsePojo`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) für weitere Details.

Lassen Sie uns nun durch die verschiedenen Teile des Codes gehen.

### Lokale vs. gebündelte Java-Klasse  {#local-vs-bundle-java-class}

Die Java-Anwendungsklasse kann auf zwei Arten installiert werden:

* **Lokal** - In einer lokalen Installation wird die Java-Quelldatei neben der HTL-Datei im selben Repository-Ordner abgelegt. Die Quelle wird automatisch nach Bedarf kompiliert. Es ist kein separater Kompilierungs- oder Verpackungsschritt erforderlich.
* **Bundle** - In einer Bundle-Installation muss die Java-Klasse mithilfe des standardmäßigen AEM-Bundle-Bereitstellungsmechanismus in einem OSGi-Bundle kompiliert und bereitgestellt werden (siehe den Abschnitt [Gebündelte Java-Klasse](#bundled-java-class)).

Beachten Sie diese beiden Punkte, um zu erfahren, welche Methode wann verwendet werden soll:

* Eine **lokale Java-Anwendungsklasse** wird empfohlen, wenn die Anwendungsklasse für die betroffene Komponente spezifisch ist.
* Eine **gebündelte Java-Anwendungsklasse** wird empfohlen, wenn der Java-Code einen Service implementiert, auf den über mehrere HTL-Komponenten zugegriffen wird.

In diesem Beispiel wird eine lokale Installation verwendet.

### Java-Paket ist Repository-Pfad {#java-package-is-repository-path}

Wenn eine lokale Installation verwendet wird, muss der Paketname der Anwendungsklasse mit dem des Repository-Ordner-Speicherorts übereinstimmen, wobei alle Bindestriche im Pfad durch Unterstriche im Paketnamen ersetzt werden.

In diesem Fall befindet sich `Info.java` unter `/apps/my-example/components/info`. Daher lautet das Paket `apps.my_example.components.info`:

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

### Erweitern   `WCMUsePojo` {#extending-wcmusepojo}

Es gibt verschiedene Möglichkeiten, eine Java-Klasse mit HTL zu integrieren (siehe den Abschnitt [Alternativen zu `WCMUsePojo`](#alternatives-to-wcmusepojo)), ist die einfachste, die `WCMUsePojo` -Klasse. Beispiel `/apps/my-example/component/info/Info.java`:

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo

    ...
}
```

### Initialisieren der Klasse {#initializing-the-class}

Wenn die Anwendungsklasse von `WCMUsePojo`, wird die Initialisierung durch Überschreiben der `activate` -Methode, in diesem Fall in `/apps/my-example/component/info/Info.java`

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

Die Methode [aktivieren](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) wird für gewöhnlich verwendet, um anhand des aktuellen Kontexts (beispielsweise der aktuellen Anforderung und Ressource) die in Ihrem HTL-Code erforderlichen Werte vorab zu berechnen und zu speichern (in Nutzervariablen). 

Die `WCMUsePojo` -Klasse bietet Zugriff auf denselben Satz von Kontextobjekten, die in einer HTL-Datei verfügbar sind (siehe das Dokument [Globale Objekte.](global-objects.md))

In einer `WCMUsePojo` erweiternden Klasse ist der Zugriff auf Kontextobjekte nach Name möglich mit

[`<T> T get(String name, Class<T> type)`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)

Alternativ kann auf häufig verwendete Kontextobjekte direkt über die in dieser Tabelle aufgeführte geeignete Convenience-Methode zugegriffen werden.

| Object | Praktische Methode |
|---|---|
| [`PageManager`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/PageManager.html) | [`getPageManager()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getPageManager--) |
| [`Page`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/Page.html) | [`getCurrentPage()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getCurrentPage--) |
| [`Page`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/Page.html) | [`getResourcePage()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResourcePage--) |
| [`ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) | [`getPageProperties()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getPageProperties--) |
| [`ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) | [`getProperties()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getProperties--) |
| [`Designer`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [`getDesigner()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getDesigner--) |
| [`Design`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/designer/Design.html) | [`getCurrentDesign()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getCurrentDesign--) |
| [`Style`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/designer/Style.html) | [`getCurrentStyle()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getCurrentStyle--) |
| [`Component`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/components/Component.html) | [`getComponent()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getComponent--) |
| [`ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) | [`getInheritedProperties()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getInheritedPageProperties--) |
| [`Resource`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/Resource.html) | [`getResource()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResource--) |
| [`ResourceResolver`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [`getResourceResolver()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResourceResolver--) |
| [`SlingHttpServletRequest`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [`getRequest()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getRequest--) |
| [`SlingHttpServletResponse`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [`getResponse()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResponse--) |
| [`SlingScriptHelper`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [`getSlingScriptHelper()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getSlingScriptHelper--) |

### Getter-Methoden {#getter-methods}

Nach der Initialisierung der Anwendungsklasse wird die HTL-Datei ausgeführt. In dieser Phase ruft HTL für gewöhnlich den Status verschiedener Member-Variablen der Anwendungsklasse ab und rendert sie für die Darstellung.

Um den Zugriff auf diese Werte aus der HTL-Datei zu ermöglichen, müssen Sie benutzerdefinierte Getter-Methoden in der Anwendungsklasse gemäß der folgenden Benennungskonvention definieren:

* Eine Methode in der Form `getXyz` stellt in der HTL-Datei eine Objekteigenschaft mit dem Namen `xyz` zur Verfügung.

In der folgenden Beispieldatei `/apps/my-example/component/info/Info.java`, die Methoden `getTitle` und `getDescription` Ergebnis in den Objekteigenschaften `title` und `description` Zugänglichkeit im Kontext der HTL-Datei.

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

### Attribut data-sly-use {#data-sly-use-attribute}

Das Attribut `data-sly-use` wird verwendet, um die Anwendungsklasse innerhalb Ihres HTL-Codes zu initialisieren. In unserem Beispiel deklariert das Attribut `data-sly-use`, dass wir die Klasse `Info` verwenden möchten. Wir können nur den lokalen Namen der Klasse verwenden, da wir eine lokale Installation verwenden (wobei die Java-Quelldatei im selben Ordner wie die HTL-Datei platziert wurde). Wenn wir eine Bundle-Installation verwenden würden, müssten wir den voll qualifizierten Klassennamen angeben.

Beachten Sie die Verwendung in diesem `/apps/my-example/component/info/info.html` Beispiel.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Lokale Kennung {#local-identifier}

Die Kennung `info` (nach dem Punkt in `data-sly-use.info`) wird in der HTL-Datei verwendet, um die Klasse anzugeben. Diese Kennung erstreckt sich global in der Datei, nachdem sie deklariert wurde. Er ist nicht auf das Element beschränkt, das die Anweisung `data-sly-use` enthält.

Beachten Sie die Verwendung in diesem `/apps/my-example/component/info/info.html` Beispiel.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Abrufen von Eigenschaften {#getting-properties}

Die Kennung `info` wird anschließend verwendet, um auf die Objekteigenschaften `title` und `description` zuzugreifen, die mithilfe der Getter-Methoden `Info.getTitle` und `Info.getDescription` zur Verfügung gestellt wurden.

Beachten Sie die Verwendung in diesem `/apps/my-example/component/info/info.html` Beispiel.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Ausgabe {#output}

Wenn wir jetzt auf `/content/my-example.html` gibt Folgendes zurück: `/content/my-example.html` -Datei.

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

>[!NOTE]
>
>Dieses Beispiel wurde vereinfacht, um seine Verwendung zu veranschaulichen. In einer Produktionsumgebung wird die Verwendung von [Sling-Modelle.](https://sling.apache.org/documentation/bundles/models.html)

## Über die Grundlagen hinaus {#beyond-the-basics}

In diesem Abschnitt werden einige weitere Funktionen vorgestellt, die über das oben beschriebene einfache Beispiel hinausgehen.

* Übergeben von Parametern an eine Anwendungsklasse
* Gebündelte Java-Anwendungsklasse

### Weitergeben von Parametern {#passing-parameters}

Parameter können bei der Initialisierung an eine Anwendungsklasse weitergegeben werden. Zum Beispiel können wir Folgendes vornehmen:

Weitere Informationen finden Sie im Sling [Dokumentation zur HTL-Scripting-Engine](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#passing-parameters-to-java-use-objects)

### Gebündelte Java-Klasse {#bundled-java-class}

Mit einer gebündelten Anwendungsklasse muss die Klasse mithilfe des standardmäßigen OSGi-Bundle-Bereitstellungsmechanismus in AEM kompiliert, verpackt und bereitgestellt werden. Im Gegensatz zu einer lokalen Installation sollte die Anwendungsklasse-Paketdeklaration normal wie in diesem `/apps/my-example/component/info/Info.java` Beispiel.

```java
package org.example.app.components;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    ...
}
```

Und die `data-sly-use` -Anweisung muss auf den vollständig qualifizierten Klassennamen verweisen, im Gegensatz zum lokalen Klassennamen wie in diesem `/apps/my-example/component/info/info.html` Beispiel.

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```
