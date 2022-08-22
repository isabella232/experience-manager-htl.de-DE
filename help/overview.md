---
title: HTL-Überblick
description: Erfahren Sie, wie AEM die HTML-Vorlagensprache (HTL) unterstützt, um ein produktives Webframework auf Unternehmensebene anzubieten, das die Sicherheit erhöht und es HTML-Entwicklern ohne Java-Kenntnissen ermöglicht, besser an AEM Projekten teilhaben zu können.
exl-id: 5d06ff25-d681-4b95-8375-c28a8364eb7e
source-git-commit: 5ab1275c984135fe946f36905bbc979cf19edd80
workflow-type: tm+mt
source-wordcount: '711'
ht-degree: 69%

---


# Übersicht {#overview}

Die HTML-Vorlagensprache (HTL), die durch Adobe Experience Manager (AEM) unterstützt wird, bietet ein hoch produktives Webframework auf Unternehmensebene, das die Sicherheit erhöht und es HTML-Entwicklern ohne Java-Kenntnissen ermöglicht, besser an AEM-Projekten teilhaben zu können.

[eingeführt in AEM 6.0,](history.md) Die HTML-Vorlagensprache ist das bevorzugte und empfohlene serverseitige Vorlagensystem für das HTML in AEM. Die HTML-Vorlagensprache unterstützt Web-Entwickler, die zuverlässige Unternehmens-Websites erstellen müssen, dabei, die Sicherheit und die Entwicklungseffizienz zu erhöhen.

## Erhöhte Sicherheit {#increased-security}

Die HTML-Vorlagensprache erhöht die Sicherheit von Sites, die sie in ihrer Implementierung verwenden, im Vergleich zu den meisten anderen Vorlagensystemen, da HTL automatisch die richtige kontextbezogene Maskierung auf alle Variablen anwenden kann, die auf die Präsentationsebene ausgegeben werden. HTL ermöglicht dies, da sie die HTML-Syntax versteht, und verwendet dieses Wissen zum Anpassen der erforderlichen Maskierung für Ausdrücke auf der Grundlage ihrer Position im Markup. Dies führt beispielsweise zu Ausdrücken, die in `href` oder `src` -Attribute anders maskiert werden als Ausdrücke, die in anderen Attributen oder an anderer Stelle platziert werden.

Auch wenn dasselbe Ergebnis mit Vorlagensprachen wie JSP erzielt werden kann, muss dort der Entwickler manuell sicherstellen, dass jede Variable entsprechend maskiert wird. Da ein einzelner Wegfall oder Fehler hinsichtlich der angewendeten Maskierung potenziell ausreichen kann, um eine Sicherheitslücke beim Cross-Site-Scripting (XSS) zu verursachen, haben wir uns dafür entschieden, diese Aufgabe mit HTL zu automatisieren. Bei Bedarf können Entwickler weiterhin eine andere Maskierung für die Ausdrücke angeben. Mit HTL entspricht das Standardverhalten mit höherer Wahrscheinlichkeit dem gewünschten Verhalten, wobei die Fehlerwahrscheinlichkeit reduziert wird.

## Vereinfachte Entwicklung {#simplified-development}

Die HTML-Vorlagensprache lässt sich einfach erlernen und ihre Funktionen sind zweckgebunden, um sicherzustellen, dass sie einfach und gezielt bleibt. Sie verfügt über leistungsstarke Mechanismen für das Strukturieren von Markup und das Aufrufen der Logik. Zugleich erzwingt sie immer die strenge Trennung von Belangen zwischen Markup und Logik. Bei der HTL an sich handelt es sich um den Standard HTML5, da sie Ausdrücke und Datenattribute verwendet, um das Markup mit dem gewünschten dynamischen Verhalten mit Anmerkungen zu versehen. Sie unterbricht demnach nicht die Gültigkeit des Markups und sorgt dafür, dass es weiterhin lesbar bleibt. Beachten Sie, dass die Auswertung der Ausdrücke und Datenattribute vollständig Server-seitig erfolgt und auf der Client-Seite nicht sichtbar ist, wo jedes gewünschte JavaScript-Framework ohne Störung verwendet werden kann. 

Durch diese Funktionen können HTML-Entwickler ohne Java-Kenntnisse und nur mit geringen produktspezifischen Kenntnissen HTL-Vorlagen bearbeiten, wodurch sie Teil des Entwicklungsteams sein und die Zusammenarbeit mit den Java-Entwicklerexperten optimieren können. Zudem können sich Java-Entwickler wiederum auf den Back-End-Code konzentrieren und müssen sich nicht um HTML kümmern.

## Reduzierte Kosten {#reduced-costs}

Erhöhte Sicherheit, vereinfachte Entwicklung und verbesserte Teamzusammenarbeit, bedeuten weniger Aufwand für AEM Projekte, schnellere Markteinführungszeiten (TTM) und niedrigere Gesamtbetriebskosten (TCO).

Konkret wurde bei der Neuimplementierung der Website Adobe.com mit der HTML-Vorlagensprache beobachtet, dass Kosten und Dauer des Projekts um etwa 25 % reduziert werden konnten.

![Effiziente Steigerung und Kostensenkung](assets/chlimage_1.png)

Das obige Diagramm zeigt die folgenden, potenziell durch HTL ermöglichten Verbesserungen hinsichtlich der Effizienz:

* **HTML/CSS/JS:** Da die HTML-Entwickler HTL-Vorlagen direkt bearbeiten können, müssen die Front-End-Designs nicht mehr getrennt vom AEM-Projekt implementiert werden, sondern können direkt in den tatsächlichen AEM-Komponenten implementiert werden. Dies reduziert mühevolle wiederholte Durchgänge mit den Java-Entwicklerexperten.
* **JSP/HTL:** Da für HTL an sich keinerlei Java-Kenntnisse erforderlich sind und darin zu schreiben unkompliziert ist, kann jeder Entwickler mit HTML-Fachwissen die Vorlagen bearbeiten.
* **Java:** Dank der eindeutigen und einfach zu verwendenden durch HTL bereitgestellten Anwendungs-API ist die Schnittstelle mit der Geschäftslogik klarer gestaltet, wovon die Java-Entwicklung insgesamt profitiert.

## Videoeinführung {#video}

Das folgende Video aus einem [AEM Gems-Sitzung,](https://experienceleague.adobe.com/docs/experience-manager-gems-events/gems/gems2014/aem-introduction-to-htl.html) gibt einen Überblick über den Zweck von HTL sowie Implementierungsbeispiele.

>[!VIDEO](https://video.tv.adobe.com/v/19504/?quality=9)

Beachten Sie, dass das Video auf HTL verweist von [seinen früheren Namen Sightly.](history.md)

## Nächste Schritte {#next-steps}

Jetzt kennen Sie die Ziele und Vorteile von HTL, beginnen Sie mit der Sprache, indem Sie das Dokument lesen [Erste Schritte mit der HTML-Vorlagensprache.](getting-started.md)
