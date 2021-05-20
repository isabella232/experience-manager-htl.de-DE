---
title: HTL-Anwendungs-API
description: Für HTL sind zwei APIs verfügbar – Java-Anwendungs-API und Javascript-Anwendungs-API.
exl-id: 8f95707e-952c-4efe-a44e-9a1ad501605e
source-git-commit: 8e70ee4921a7ea071ab7e06947824c371f4013d8
workflow-type: tm+mt
source-wordcount: '183'
ht-degree: 55%

---

# HTL-Anwendungs-API {#htl-use-api}

HTL fördert die Trennung von Bedenken, indem es nicht gestattet, Geschäftslogik mit Markup zu mischen. Geschäftslogik kann über die Anwendungs-API implementiert werden.

Die folgende Tabelle gibt einen Überblick über die Vor- und Nachteile jeder API.

|  | **Java-Anwendungs-API** | **JavaScript-Anwendungs-API** |
|--- |--- |--- |
| **Vorteile** | <ul><li>Schneller</li><li>Kann mit einem Debugger überprüft werden</li><li>Einfache Komponententests</li></ul> | <ul><li>Kann von Frontend-Entwicklern geändert werden</li><li>Befindet sich in der Komponente und behält die Ansichtslogik einer Komponente in der Nähe der zugehörigen Vorlage bei</li></ul> |
| **Nachteile** | <ul><li>Kann nicht von Frontend-Entwicklern geändert werden</li></ul> | <ul><li>Langsamer</li><li>Kein Debugger (noch)</li><li>Härte zum Unit-Test</li></ul> |

Für Seitenkomponenten sollte ein kombiniertes Modell verwendet werden, in dem sich die gesamte Modelllogik in Java befindet, wodurch eindeutige APIs bereitgestellt werden, die von den Aktionen in der Ansicht unabhängig sind (d. h. in den Komponenten). AEM verfügt über großartige Standardmodelle wie die Seiten- oder die Ressourcen-API, die die meisten Fälle abdecken sollten.

Die gesamte für eine Komponente spezifische Ansichtslogik sollte in derselben Komponente wie JavaScript platziert werden, da sie zu dieser Komponente gehört.
