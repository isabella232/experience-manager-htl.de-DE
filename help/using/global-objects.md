---
title: Globale HTL-Objekte
description: Erfahren Sie mehr über nummerierbare Objekte, Java-unterstützte Objekte und JavaScript-unterstützte Objekte. Suchen Sie die Variablennamen und Beschreibungen für diese globalen HTL-Objekte, die in AEM verwendet werden.
exl-id: ca590b92-f1b3-4e44-a04a-a2c10dff256f
source-git-commit: 7b53eff0652f650ffb8caae0e69aa349b5c548eb
workflow-type: ht
source-wordcount: '223'
ht-degree: 100%

---

# Globale HTL-Objekte {#htl-global-objects}

Ohne etwas angeben zu müssen, bietet HTL Zugriff auf alle Objekte, die nach dem Einschließen von `global.jsp` in JSP verfügbar waren. Diese Objekte sind zusätzlich zu denen vorhanden, die möglicherweise über die [Anwendungs-API](use-api.md) eingeführt werden.

## Aufzählungsobjekte {#enumerable-objects}

Diese Objekte bieten praktischen Zugriff auf häufig verwendete Informationen. Auf ihre Inhalte kann mithilfe der Punkt-Notation zugegriffen werden und sie können mithilfe von `data-sly-list` oder `data-sly-repeat` wiederholt werden.

| Variablenname | Beschreibung |
|--- |--- |
| `properties` | Liste der Eigenschaften der aktuellen Ressource. Unterstützt durch [org.apache.sling.api.resource.ValueMap](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `pageProperties` | Liste der Seiteneigenschaften der aktuellen Seite. Unterstützt durch [org.apache.sling.api.resource.ValueMap](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `inheritedPageProperties` | Liste der vererbten Seiteneigenschaften der aktuellen Seite. Unterstützt durch [org.apache.sling.api.resource.ValueMap](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) |

## Java-unterstützte Objekte {#java-backed-objects}

Jedes der folgenden Objekte wird durch das entsprechende Java-Objekt unterstützt.

Die hilfreichsten Variablen in der folgenden Tabelle sind in Fettdruck hervorgehoben.

| Variablenname | Beschreibung |
|---|---|
| **`component`** | `com.day.cq.wcm.api.components.Component` |
| `componentContext` | `com.day.cq.wcm.api.components.ComponentContext` |
| **`currentDesign`** | `com.day.cq.wcm.api.designer.Design` |
| `currentNode` | `javax.jcr.Node` |
| **`currentPage`** | `com.day.cq.wcm.api.Page` |
| `currentSession` | `javax.servlet.http.HttpSession` |
| `currentStyle` | `com.day.cq.wcm.api.designer.Style` |
| `designer` | `com.day.cq.wcm.api.designer.Designer` |
| `editContext` | `com.day.cq.wcm.api.components.EditContext` |
| `log` | `org.slf4j.Logger` |
| `out` | `java.io.PrintWriter` |
| `pageManager` | `com.day.cq.wcm.api.PageManager` |
| `reader` | `java.io.BufferedReader` |
| **`request`** | `org.apache.sling.api.SlingHttpServletRequest` |
| `resolver` | `org.apache.sling.api.resource.ResourceResolver` |
| **`resource`** | `org.apache.sling.api.resource.Resource` |
| **`resourceDesign`** | `com.day.cq.wcm.api.designer.Design` |
| **`resourcePage`** | `com.day.cq.wcm.api.Page` |
| `response` | `org.apache.sling.api.SlingHttpServletResponse` |
| `sling` | `org.apache.sling.api.scripting.SlingScriptHelper` |
| `slyWcmHelper` | `com.adobe.cq.sightly.WCMScriptHelper` |
| **`wcmmode`** | `com.adobe.cq.sightly.SightlyWCMMode` |
| `xssAPI` | `com.adobe.granite.xss.XSSAPI` |

## JavaScript-unterstützte Objekte {#javascript-backed-objects}

Es ist möglich, die HTL-Logik mit JavaScript zu unterstützen. Die bevorzugte oder empfohlene Methode ist jedoch die Verwendung von [Sling-Modellen](https://sling.apache.org/documentation/bundles/models.html).

<!-- 

Comment Type: draft

<p> </p> 
<p>JS-specific context variables: These supply access to asynchronous implementations of all the Java objects listed below). To write HTL code that is portable to granite.js, you must use the variables provided by aem and sly, not the native Java variables.</p> 
<ul> 
 <li>wcm
  <ul> 
   <li>currentPage</li> 
   <li>nativePage: [com.day.cq.wcm.apiPage]</li> 
   <li>properties: {<i>enumerable</i>}</li> 
  </ul> </li> 
 <li>granite
  <ul> 
   <li>request
    <ul> 
     <li>parameters: {<i>enumerable</i>}</li> 
     <li>nativeRequest: [org.apache.sling.scripting.core.impl.helper.OnDemandReaderRequest]</li> 
     <li>pathInfo
      <ul> 
       <li>nativePathInfo: [SlingRequestPathInfo: path='/content/geometrixx/en/jcr:content/par/text', selectorString='null', extension='html', suffix='null']</li> 
      </ul> </li> 
    </ul> </li> 
   <li>resource
    <ul> 
     <li>nativeResource: [Paragraph, path=/content/geometrixx/en/jcr:content/par/text, type=wcm/foundation/components/text, cssClass=default, column=0/0, diffInfo=[null], resource=[JcrNodeResource, type=wcm/foundation/components/text, superType=null, path=/content/geometrixx/en/jcr:content/par/text]]</li> 
     <li>path: "/content/geometrixx/en/jcr:content/par/text"</li> 
     <li>properties: {sling:resourceType,jcr:created,jcr:lastModified,jcr:createdBy, textIsRich,jcr:lastModifiedBy,jcr:primaryType}</li> 
    </ul> </li> 
   <li>properties: {sling:resourceType,jcr:created,jcr:lastModified,jcr:createdBy, textIsRich,jcr:lastModifiedBy,jcr:primaryType}</li> 
  </ul> </li> 
</ul> 
<p>JS specific non-HTL related variables. Present due to JS-implementation. Generally not used in templating:</p> 
<ul> 
 <li>console: JS Object</li> 
 <li>exports: JS Object</li> 
 <li>module: JS Object</li> 
 <li>setImmediate: JS Function</li> 
 <li>setTimeout: JS Function</li> 
 <li>use: JS Function</li> 
</ul>
-->
