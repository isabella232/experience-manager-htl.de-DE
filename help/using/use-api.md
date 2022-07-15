---
title: HTL-Anwendungs-API
description: Für HTL sind zwei APIs verfügbar – Java-Anwendungs-API und Javascript-Anwendungs-API.
exl-id: 8f95707e-952c-4efe-a44e-9a1ad501605e
source-git-commit: 8e70ee4921a7ea071ab7e06947824c371f4013d8
workflow-type: ht
source-wordcount: '183'
ht-degree: 100%

---

# HTL-Anwendungs-API {#htl-use-api}

HTL fördert die Trennung von Belangen, indem es nicht zulässt, dass sich Business-Logik mit Markup vermischt. Business-Logik kann über die Use-API implementiert werden.

In der folgenden Tabelle sind die Vor- und Nachteile jeder API in einer Übersicht aufgeführt.

|  | **Java-Anwendungs-API** | **JavaScript-Anwendungs-API** |
|--- |--- |--- |
| **Vorteile** | <ul><li>Schneller</li><li>Kann mit einem Debugger geprüft werden</li><li>Einfacher Unit-Test möglich</li></ul> | <ul><li>Kann durch Frontend-Entwickelnde geändert werden</li><li>Befindet sich in der Komponente, wodurch die Ansichtslogik einer Komponente nahe an der entsprechenden Vorlage gehalten wird</li></ul> |
| **Nachteile** | <ul><li>Kann nicht durch Frontend-Entwickelnde geändert werden</li></ul> | <ul><li>Langsamer</li><li>(Bisher) kein Debugger</li><li>Unit-Test schwieriger</li></ul> |

Für Seitenkomponenten sollte ein kombiniertes Modell verwendet werden, in dem sich die gesamte Modelllogik in Java befindet, wodurch eindeutige APIs bereitgestellt werden, die von den Aktionen in der Ansicht unabhängig sind (d. h. in den Komponenten). AEM verfügt über großartige Standardmodelle wie die Seiten- oder die Ressourcen-API, die die meisten Fälle abdecken sollten.

Die gesamte für eine Komponente spezifische Ansichtslogik sollte in derselben Komponente wie JavaScript platziert werden, da sie zu dieser Komponente gehört.
