---
title: HTL-Anwendungs-API
description: Für HTL sind zwei APIs verfügbar – Java-Anwendungs-API und Javascript-Anwendungs-API.
translation-type: tm+mt
source-git-commit: f7e46aaac2a4b51d7fa131ef46692ba6be58d878
workflow-type: tm+mt
source-wordcount: '183'
ht-degree: 55%

---


# HTL-Anwendungs-API {#htl-use-api}

HTL fördert die Trennung von Bedenken, indem es nicht erlaubt, Geschäftslogik mit Markup zu mischen. Geschäftslogik kann über die Use-API implementiert werden.

Die folgende Tabelle gibt einen Überblick über die Vor- und Nachteile der einzelnen APIs.

|  | **Java-Anwendungs-API** | **JavaScript-Anwendungs-API** |
|--- |--- |--- |
| **Vorteile** | <ul><li>Schneller</li><li>Kann mit einem Debugger überprüft werden</li><li>Einfache Prüfung</li></ul> | <ul><li>Kann von Frontend-Entwicklern geändert werden</li><li>Befindet sich innerhalb der Komponente und behält die Ansichten-Logik einer Komponente nahe der zugehörigen Vorlage bei</li></ul> |
| **Nachteile** | <ul><li>Kann nicht von Frontend-Entwicklern geändert werden</li></ul> | <ul><li>langsamer</li><li>Kein Debugger (noch)</li><li>Härter-Unit-Test</li></ul> |

Für Seitenkomponenten sollte ein kombiniertes Modell verwendet werden, in dem sich die gesamte Modelllogik in Java befindet, wodurch eindeutige APIs bereitgestellt werden, die von den Aktionen in der Ansicht unabhängig sind (d. h. in den Komponenten). AEM verfügt über großartige Standardmodelle wie die Seiten- oder die Ressourcen-API, die die meisten Fälle abdecken sollten.

Die gesamte für eine Komponente spezifische Ansichtslogik sollte in derselben Komponente wie JavaScript platziert werden, da sie zu dieser Komponente gehört.
