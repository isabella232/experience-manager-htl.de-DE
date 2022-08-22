---
title: AEM-Erweiterungen
description: AEM bietet Erweiterungen der HTL-Spezifikation, die Sie als Entwickler AEM können.
source-git-commit: 6d97bc5d0ab89dffaf56a54c73c94d069bb31ca6
workflow-type: tm+mt
source-wordcount: '308'
ht-degree: 0%

---


# AEM-Erweiterungen {#aem-extensions}

Ähnlich wie bei [Apache Sling-Erweiterungen der HTL-Spezifikation,](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#extensions-of-the-htl-specification-1) AEM bietet einige zusätzliche Ausdrucksoptionen, die die Arbeit mit AEM Konzepten direkt in den HTL-Skripten erleichtern.

## i18n {#i18n}

Dasselbe [drei zusätzliche Optionen](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#i18n) wie in Apache Sling kann zusammen mit `i18n`:

* `locale`
* `hint`
* `basename`

In AEM [Internationalisierungsunterstützung](https://experienceleague.adobe.com/docs/experience-manager-65/developing/components/internationalization/i18n-dev.html) für HTL wird mithilfe der API aus der `com.day.cq.i18n` Paket.

## data-sly-include {#data-sly-include}

AEM `data-sly-include` kann zusätzliche `wcmmode` -Option, die die [WCM-Modus](https://developer.adobe.com/experience-manager/reference-materials/cloud-service/javadoc/com/day/cq/wcm/api/WCMMode.html) für das enthaltene Skript. Die zulässigen Werte sind die Namen der verfügbaren Enum-Konstanten.

## data-sly-resource {#data-sly-resource}

Zusätzlich zu Pfaden und `Resources`, die `data-sly-resource` Block-Elemente können auch mit [`Maps`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Map.html) oder [`Records`.](https://github.com/apache/sling-org-apache-sling-scripting-sightly-runtime/blob/master/src/main/java/org/apache/sling/scripting/sightly/Record.java) Bei beiden Ansätzen wird die `resourceName` Die String-Eigenschaft muss angegeben werden. Sein Wert wird verwendet, um eine [Synthetische Ressource](https://www.javadoc.io/doc/org.apache.sling/org.apache.sling.api/latest/org/apache/sling/api/resource/SyntheticResource.html) wird im Rendering-Kontext enthalten sein. Die übrigen Eigenschaften der `Record` oder `Map` wurde an `data-sly-resource` wird wie üblich verwendet `Resource` Eigenschaften. Wenn die Variable `sling:resourceType` -Eigenschaft in dieser Zuordnung fehlt, wird angenommen, dass der Ressourcentyp entweder der Wert der `resourceType` [Ausdrucksoption](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#229-resource) oder der Ressourcentyp der aktuellen Ressource, die das Rendering steuert.

Für die folgenden Zuordnungs-/Datensatzeigenschaften, die im Skriptbereich als `map`:

```javascript
{
    resourceName: "myText",
    "sling:resourceType": "core/wcm/components/text/v2/text",
    "text": "Hello World!"
}
```

Und mit dem folgenden Markup:

```html
<div class="outer" data-sly-resource="${map}"></div>
```

Die folgende Ausgabe wird erwartet:

```html
<div class="outer">
    <div class="myText">
        <div data-cmp-data-layer="{&quot;text-e58d65c472&quot;:{&quot;@type&quot;:&quot;core/wcm/components/text/v2/text&quot;,&quot;xdm:text&quot;:&quot;<p>Hello world!</p>&quot;}}" id="text-e58d65c472" class="cmp-text">
            <p>Hello world!</p>
        </div>
  </div>
</div>
```
