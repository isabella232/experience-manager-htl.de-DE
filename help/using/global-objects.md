---
title: Globale HTL-Objekte
seo-title: Globale HTL-Objekte
description: Ohne etwas angeben zu müssen, bietet HTL Zugriff auf alle Objekte, die
  nach dem Einschließen von global.jsp in JSP verfügbar waren.
seo-description: Ohne etwas angeben zu müssen, bietet HTL Zugriff auf alle Objekte,
  die nach dem Einschließen von global.jsp in JSP verfügbar waren.
uuid: e03affbb-a683-4323-8224-53d8ef59caef
contentOwner: Benutzer
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: Referenz
discoiquuid: fe071a7e-0dae-45c1-9f86-80c558483f87
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 271c355ae56e16e309853b02b8ef09f2ff971a2e

---


# Globale HTL-Objekte{#htl-global-objects}

Ohne etwas angeben zu müssen, bietet HTL Zugriff auf alle Objekte, die nach dem Einschließen von `global.jsp` in JSP verfügbar waren. Diese Objekte sind zusätzlich zu denen vorhanden, die möglicherweise über die [Anwendungs-API](use-api.md) eingeführt werden.

## Aufzählungsobjekte {#enumerable-objects}

Diese Objekte bieten praktischen Zugriff auf häufig verwendete Informationen. Auf ihre Inhalte kann mithilfe der Punkt-Notation zugegriffen werden und sie können mithilfe von `data-sly-list` oder `data-sly-repeat` wiederholt werden.

| Variablenname | Beschreibung |
|--- |--- |
| properties | Liste der Eigenschaften der aktuellen Resource. Unterstützt von [org. apache. sling. api. resource. valuemap](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| pageProperties | Liste der Seiteneigenschaften der aktuellen Page. Unterstützt von [org. apache. sling. api. resource. valuemap](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.hmtl) |
| inheritedPageProperties | Liste der vererbten Seiteneigenschaften der aktuellen Page. Unterstützt von [org. apache. sling. api. resource. valuemap](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) |


## Java-unterstützte Objekte {#java-backed-objects}

Jedes der folgenden Objekte wird durch das entsprechende Java-Objekt unterstützt.

Die hilfreichsten Variablen in der folgenden Tabelle sind in Fettdruck hervorgehoben.

| Variablenname | Beschreibung |  |
|---|---|---|
| `component` | `com.day.cq.wcm.api.components.Component` |  |
| `componentContext` | `com.day.cq.wcm.api.components.ComponentContext` |  |
| `currentDesign` | `com.day.cq.wcm.api.designer.Design` |  |
| `currentNode` | `javax.jcr.Node` |  |
| `currentPage` | `com.day.cq.wcm.api.Page` |  |
| `currentSession` | `javax.servlet.http.HttpSession` |  |
| `currentStyle` | `com.day.cq.wcm.api.designer.Style` |  |
| `designer` | `com.day.cq.wcm.api.designer.Designer` |  |
| `editContext` | `com.day.cq.wcm.api.components.EditContext` |  |
| `log` | `org.slf4j.Logger` |  |
| `out` | `java.io.PrintWriter` |  |
| `pageManager` | `com.day.cq.wcm.api.PageManager` |  |
| `reader` | `java.io.BufferedReader` |  |
| `request` | `org.apache.sling.api.SlingHttpServletRequest` |  |
| `resolver` | `org.apache.sling.api.resource.ResourceResolver` |  |
| `resource` | `org.apache.sling.api.resource.Resource` |  |
| `resourceDesign` | `com.day.cq.wcm.api.designer.Design` |  |
| `resourcePage` | `com.day.cq.wcm.api.Page` |  |
| `response` | `org.apache.sling.api.SlingHttpServletResponse` |  |
| `sling` | `org.apache.sling.api.scripting.SlingScriptHelper` |  |
| `slyWcmHelper` | `com.adobe.cq.sightly.WCMScriptHelper` |  |
| `wcmmode` | `com.adobe.cq.sightly.SightlyWCMMode` |  |
| `xssAPI` | `com.adobe.granite.xss.XSSAPI` |  |

## JavaScript-unterstützte Objekte {#javascript-backed-objects}

Es sind auch Objekte verfügbar, die durch JavaScript unterstützt werden. Diese Objekte sind jedoch ab der Version AEM 6.2 weiterhin experimentell und es ist besser, die Java-unterstützten Objekte zu verwenden, die dieselben Aktionen ermöglichen.

<!-- 

Comment Type: draft

<p> </p> 
<p>JS-specific context variables: These supply access to asynchronous implementions of all the Java objects listed below). To write HTL code that is protable to granite.js, you must use the variables provided by aem and sly, not the native Java variables.</p> 
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
<p>JS specific non-HTL related variables. Present due to JS-implementaion. Generally not used in templating:</p> 
<ul> 
 <li>console: JS Object</li> 
 <li>exports: JS Object</li> 
 <li>module: JS Object</li> 
 <li>setImmediate: JS Function</li> 
 <li>setTimeout: JS Function</li> 
 <li>use: JS Function</li> 
</ul>
-->
