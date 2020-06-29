---
title: HTL-JavaScript-Anwendungs-API
description: Die HTML Template Language - HTL - JavaScript Use-API ermöglicht es einer HTML-Datei, auf in JavaScript geschriebenen Helfercode zuzugreifen.
translation-type: tm+mt
source-git-commit: ee712ef61018b5e05ea052484e2a9a6b12e6c5c8
workflow-type: tm+mt
source-wordcount: '324'
ht-degree: 87%

---


# HTL-JavaScript-Anwendungs-API {#htl-javascript-use-api}

Die HTML Template Language (HTL) JavaScript Use-API ermöglicht es einer HTML-Datei, auf in JavaScript geschriebenen Helfercode zuzugreifen. Dadurch kann die gesamte komplexe Geschäftslogik im JavaScript-Code verkapselt werden, während der HTL-Code nur die direkte Markup-Produktion verarbeiten muss.

Die folgenden Konventionen werden verwendet.

```javascript
/**
 * In the following example '/libs/dep1.js' and 'dep2.js' are optional
 * dependencies needed for this script's execution. Dependencies can
 * be specified using an absolute path or a relative path to this
 * script's own path.
 *
 * If no dependencies are needed the dependencies array can be omitted.
 */
use(['dep1.js', 'dep2.js'], function (Dep1, Dep2) {
    // implement processing
  
    // define this Use object's behavior
    return {
        propertyName: propertyValue
        functionName: function () {}
    }
});
```

## Ein einfaches Beispiel  {#a-simple-example}

Wir definieren eine Komponente, `info`, unter

`/apps/my-example/components/info`

Sie enthält zwei Dateien:

* **`info.js`**: Eine die Anwendungsklasse definierende JavaScript-Datei.
* **`info.html`**: Eine die Komponente `info` definierende HTL-Datei. Dieser Code verwendet die Funktionalität von `info.js` durch die Anwendungs-API.

### /apps/my-example/component/info/info.js {#apps-my-example-component-info-info-js}

```java
"use strict";
use(function () {
    var info = {};
    info.title = resource.properties["title"];
    info.description = resource.properties["description"];
    return info;
});
```

### /apps/my-example/component/info/info.html {#apps-my-example-component-info-info-html}

```xml
<div data-sly-use.info="info.js">
    <h1>${info.title}</h1>
    <p>${info.description}</p>
</div>
```

Zudem erstellen wir einen Inhaltsknoten, der die Komponente `info` verwendet unter

`/content/my-example` mit Eigenschaften:

* `sling:resourceType = "my-example/component/info"`
* `title = "My Example"`
* `description = "This is some example content."`

Hier die daraus resultierende Repository-Struktur:

### Repository-Struktur {#repository-structure}

```java
{
  "apps": {
    "my-example": {
      "components": {
        "info": {
          "info.html": {
            ...
          },
          "info.js": {
            ...
          }
        }
      }
    }
 },
 "content": {
    "my-example": {
      "sling:resourceType": "my-example/component/info",
      "title": "My Example",
      "description": "This is some example content."
    }
  }
}
```

Beachten Sie die folgende Komponentenvorlage:

```xml
<section class="component-name" data-sly-use.component="component.js">
    <h1>${component.title}</h1>
    <p>${component.description}</p>
</section>
```

Die entsprechende Logik kann mithilfe des folgenden serverseitigen JavaScript-Elements geschrieben werden, das sich in der Datei `component.js` direkt neben der Vorlage befindet.

```javascript
use(function () {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };

    var title = currentPage.getNavigationTitle() || currentPage.getTitle() || currentPage.getName();
    var description = properties.get(Constants.DESCRIPTION_PROP, "").substr(0, Constants.DESCRIPTION_LENGTH);

    return {
        title: title,
        description: description
    };
});
```

Hier wird versucht, den `title` aus verschiedenen Quellen zu übernehmen und die Beschreibung auf 50 Zeichen zuzuschneiden.

## Abhängigkeiten {#dependencies}

Angenommen, wir verfügen über eine Dienstprogrammklasse, die bereits mit intelligenten Funktionen ausgestattet ist, beispielsweise mit der Standardlogik für den Navigationstitel oder den angemessenen Zuschnitt einer Zeichenfolge auf eine bestimmte Länge:

```javascript
use(['../utils/MyUtils.js'], function (utils) {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };

    var title = utils.getNavigationTitle(currentPage);
    var description = properties.get(Constants.DESCRIPTION_PROP, "").substr(0, Constants.DESCRIPTION_LENGTH);

    return {
        title: title,
        description: description
    };
});
```

## Erweitern  {#extending}

Das Abhängigkeitsmuster kann auch verwendet werden, um die Logik einer anderen Komponente zu erweitern (für gewöhnlich ist dies der `sling:resourceSuperType` der aktuellen Komponente).

Angenommen, die übergeordnete Komponente stellt den `title` bereit und wir möchten zudem eine `description` hinzufügen:

```javascript
use(['../parent-component/parent-component.js'], function (component) {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };

    component.description = utils.shortenString(properties.get(Constants.DESCRIPTION_PROP, ""), Constants.DESCRIPTION_LENGTH);

    return component;
});
```

## Weitergeben von Parametern an eine Vorlage {#passing-parameters-to-a-template}

Falls `data-sly-template`-Anweisungen unabhängig von Komponenten sein können, kann es sinnvoll sein, Parameter an die verknüpfte Anwendungs-API weiterzugeben.

In unserer Komponente rufen wir also eine Vorlage auf, die sich in einer anderen Datei befindet:

```xml
<section class="component-name" data-sly-use.tmpl="template.html" data-sly-call="${tmpl.templateName @ page=currentPage}"></section>
```

Anschließend ist dies die in der Datei `template.html` befindliche Vorlage:

```xml
<template data-sly-template.templateName="${@ page}" data-sly-use.tmpl="${'template.js' @ page=page, descriptionLength=50}">
    <h1>${tmpl.title}</h1>
    <p>${tmpl.description}</p>
</template>
```

Die entsprechende Logik kann mithilfe des folgenden serverseitigen Java-Script-Elements geschrieben werden, das sich in der Datei `template.js` direkt neben der Vorlagendatei befindet:

```javascript
use(function () {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description"
    };

    var title = this.page.getNavigationTitle() || this.page.getTitle() || this.page.getName();
    var description = this.page.getProperties().get(Constants.DESCRIPTION_PROP, "").substr(0, this.descriptionLength);

    return {
        title: title,
        description: description
    };
});
```

Die weitergegebenen Parameter werden im Keyword `this` festgelegt.
