---
title: HTL-Anwendungs-API
seo-title: Adobe HTL-Anwendungs-API
description: Für HTL sind zwei APIs verfügbar – Java-Anwendungs-API und Javascript-Anwendungs-API.
seo-description: Für Adobe HTL sind zwei APIs verfügbar – Java-Anwendungs-API und
  Javascript-Anwendungs-API.
uuid: ab44aa5c-ce7e-40b9-97fb-e86c6a28405c
contentOwner: Benutzer
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: Referenz
discoiquuid: 89004426-eb59-4b63-913f-51bf98662773
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 271c355ae56e16e309853b02b8ef09f2ff971a2e

---


# HTL-Anwendungs-API {#htl-use-api}

In der folgenden Tabelle sind die Vor- und Nachteile jeder API in einer Übersicht aufgeführt.

|  | **Java-Anwendungs-API** | **JavaScript-Anwendungs-API** |
|--- |--- |--- |
| **Vorteile** | <ul><li>schneller</li><li>kann mit einem Debugger geprüft werden</li><li>einfacher Unit-Test möglich</li></ul> | <ul><li>kann durch Front-End-Entwickler geändert werden</li><li>befindet sich in der Komponente, wodurch die Ansichtslogik einer Komponente in der Nähe der entsprechenden Vorlage beibehalten wird</li></ul> |
| **Nachteile** | <ul><li>kann nicht durch Front-End-Entwickler geändert werden</li></ul> | <ul><li>langsamer</li><li>(bisher) kein Debugger</li><li>Unit-Test schwieriger</li></ul> |


Für Seitenkomponenten sollte ein kombiniertes Modell verwendet werden, in dem sich die gesamte Modelllogik in Java befindet, wodurch eindeutige APIs bereitgestellt werden, die von den Aktionen in der Ansicht unabhängig sind (d. h. in den Komponenten). AEM verfügt über großartige Standardmodelle wie die Seiten- oder die Ressourcen-API, die die meisten Fälle abdecken sollten.

Die gesamte für eine Komponente spezifische Ansichtslogik sollte in derselben Komponente wie JavaScript platziert werden, da sie zu dieser Komponente gehört.
