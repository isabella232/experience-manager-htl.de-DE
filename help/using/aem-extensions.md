---
title: AEM-Erweiterungen
description: AEM bietet Erweiterungen der HTL-Spezifikation für AEM an, um Ihnen als Entwickler/in die Arbeit zu erleichtern.
exl-id: d78cb84d-f958-45e2-9c6c-df86a68277d5
source-git-commit: 88edbd2fd66de960460df5928a3b42846d32066b
workflow-type: ht
source-wordcount: '308'
ht-degree: 100%

---

# AEM-Erweiterungen {#aem-extensions}

Ähnlich wie die [Apache Sling-Erweiterungen der HTL-Spezifikation](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#extensions-of-the-htl-specification-1) bietet AEM einige zusätzliche Ausdrucksoptionen, die die Arbeit mit AEM-Konzepten direkt in den HTL-Skripten etwas erleichtern.

## i18n {#i18n}

Die gleichen [drei zusätzlichen Optionen](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#i18n) wie in Apache Sling können zusammen mit `i18n` verwendet werden:

* `locale`
* `hint`
* `basename`

In AEM wird die [Internationalisierungsunterstützung](https://experienceleague.adobe.com/docs/experience-manager-65/developing/components/internationalization/i18n-dev.html?lang=de) für HTL jedoch mit Hilfe der API aus dem `com.day.cq.i18n`-Paket implementiert.

## data-sly-include {#data-sly-include}

In AEM kann `data-sly-include` eine zusätzliche Option `wcmmode` annehmen, die den [WCM-Modus](https://developer.adobe.com/experience-manager/reference-materials/cloud-service/javadoc/com/day/cq/wcm/api/WCMMode.html) für das enthaltene Skript steuert. Die zulässigen Werte sind die Namen der verfügbaren Aufzählungskonstanten.

## data-sly-resource {#data-sly-resource}

Zusätzlich zu Pfaden und `Resources` kann das `data-sly-resource`-Blockelement auch mit [`Maps`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Map.html) oder [`Records` arbeiten.](https://github.com/apache/sling-org-apache-sling-scripting-sightly-runtime/blob/master/src/main/java/org/apache/sling/scripting/sightly/Record.java) Bei beiden Ansätzen muss die Eigenschaft von `resourceName` als Zeichenfolge angegeben werden. Sein Wert wird verwendet, um eine [synthetische Ressource](https://www.javadoc.io/doc/org.apache.sling/org.apache.sling.api/latest/org/apache/sling/api/resource/SyntheticResource.html) zu erstellen, die in den Rendering-Kontext aufgenommen wird. Der Rest der Eigenschaften von `Record` oder `Map`, die an `data-sly-resource` übergeben wurden, werden als normale `Resource`-Eigenschaften verwendet. Wenn die `sling:resourceType`-Eigenschaft in dieser Zuordnung fehlt, wird als Ressourcentyp entweder der Wert der `resourceType`-[Ausdrucksoption](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#229-resource) oder der Ressourcentyp der aktuellen Ressource, die das Rendering steuert, angenommen.

Unter Berücksichtigung der folgenden Zuordnungs-/Datensatz-Eigenschaften, die im Skriptbereich als `map` verfügbar sind:

```javascript
{
    resourceName: "myText",
    "sling:resourceType": "core/wcm/components/text/v2/text",
    "text": "Hello World!"
}
```

Und bei der folgenden Aufschlüsselung:

```html
<div class="outer" data-sly-resource="${map}"></div>
```

Folgende Ausgabe wird erwartet:

```html
<div class="outer">
    <div class="myText">
        <div data-cmp-data-layer="{&quot;text-e58d65c472&quot;:{&quot;@type&quot;:&quot;core/wcm/components/text/v2/text&quot;,&quot;xdm:text&quot;:&quot;<p>Hello world!</p>&quot;}}" id="text-e58d65c472" class="cmp-text">
            <p>Hello world!</p>
        </div>
  </div>
</div>
```
