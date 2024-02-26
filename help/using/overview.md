---
title: HTL-Übersicht
description: Erfahren Sie, wie AEM die HTML-Vorlagensprache (HTL) unterstützt, um ein produktives Web-Framework auf Unternehmensebene anzubieten, das die Sicherheit erhöht und Personen ohne Java-Kenntnisse, die HTML entwickeln, eine bessere Beteiligung an AEM-Projekten ermöglicht.
exl-id: 5d06ff25-d681-4b95-8375-c28a8364eb7e
source-git-commit: 88edbd2fd66de960460df5928a3b42846d32066b
workflow-type: ht
source-wordcount: '707'
ht-degree: 100%

---


# Übersicht {#overview}

Die HTML-Vorlagensprache (HTL), die durch Adobe Experience Manager (AEM) unterstützt wird, bietet ein hoch produktives Webframework auf Unternehmensebene, das die Sicherheit erhöht und es HTML-Entwicklern ohne Java-Kenntnissen ermöglicht, besser an AEM-Projekten teilhaben zu können.

Die [in AEM 6.0 eingeführte](history.md) HTML Template Language ist das bevorzugte und empfohlene Server-seitige Vorlagensystem für HTML in AEM. Die HTML-Vorlagensprache unterstützt Web-Entwickler, die zuverlässige Unternehmens-Websites erstellen müssen, dabei, die Sicherheit und die Entwicklungseffizienz zu erhöhen.

## Erhöhte Sicherheit {#increased-security}

Die HTML Template Language erhöht die Sicherheit von Websites, die sie in ihrer Implementierung verwenden, im Vergleich zu den meisten anderen Template-Systemen, da HTL in der Lage ist, automatisch das richtige kontextbezogene Escaping auf alle Variablen anzuwenden, die an die Präsentationsebene ausgegeben werden. HTL ermöglicht dies, da sie die HTML-Syntax versteht, und verwendet dieses Wissen zum Anpassen des erforderlichen Escapings für Ausdrücke auf der Grundlage ihrer Position im Markup. Dies führt beispielsweise dazu, dass Ausdrücke, die in `href`- oder `src`-Attributen stehen, anders behandelt werden als Ausdrücke, die in anderen Attributen oder anderswo stehen.

Das gleiche Ergebnis kann mit Vorlagensprachen wie JSP erreicht werden, aber in diesem Fall muss die Entwicklerin oder der Entwickler manuell sicherstellen, dass jede Variable mit der richtigen Maskierung versehen ist. Da eine einzige Auslassung oder ein einziger Fehler beim Maskieren ausreichen kann, um eine Cross-Site-Scripting-Schwachstelle (XSS) zu verursachen, haben wir uns entschieden, diese Aufgabe mit HTL zu automatisieren. Bei Bedarf können Entwicklerinnen und Entwickler immer noch ein anderes Maskieren für Ausdrücke definieren, aber mit HTL entspricht das Standardverhalten viel eher dem gewünschten Verhalten, was die Wahrscheinlichkeit von Fehlern verringert.

## Vereinfachte Entwicklung {#simplified-development}

Die HTML-Vorlagensprache ist einfach zu erlernen und ihre Funktionen sind absichtlich begrenzt, um sicherzustellen, dass sie einfach und unkompliziert bleibt. Sie verfügt über leistungsstarke Mechanismen für das Strukturieren von Markup und das Aufrufen der Logik. Zugleich erzwingt sie immer die strenge Trennung von Belangen zwischen Markup und Logik. HTL selbst ist Standard-HTML5, da es Ausdrücke und Datenattribute verwendet, um das Markup mit dem gewünschten dynamischen Verhalten zu versehen, was bedeutet, dass es die Gültigkeit des Markups nicht beeinträchtigt und es lesbar bleibt. Beachten Sie, dass die Auswertung der Ausdrücke und Datenattribute vollständig Server-seitig erfolgt und auf der Client-Seite nicht sichtbar ist, wo jedes gewünschte JavaScript-Framework ohne Störung verwendet werden kann. 

Diese Funktionen ermöglichen es HTML-Entwicklerinnen und -Entwicklern ohne Java-Kenntnisse und mit wenig produktspezifischem Wissen, HTL-Vorlagen zu bearbeiten, sodass sie Teil des Entwicklungsteams werden können und die Zusammenarbeit mit den Java-Entwicklerinnen und -Entwicklern vereinfacht wird. Umgekehrt können sich Java-Entwicklerinnen und -Entwicklern auf den Back-End-Code konzentrieren, ohne sich um HTML kümmern zu müssen

## Reduzierte Kosten {#reduced-costs}

Erhöhte Sicherheit, vereinfachte Entwicklung und verbesserte Team-Zusammenarbeit führen bei AEM-Projekten zu geringerem Aufwand, schnellerer Markteinführung (TTM) und niedrigeren Gesamtbetriebskosten (TCO).

Konkret wurde bei der Neuimplementierung der Website Adobe.com mit der HTML-Vorlagensprache beobachtet, dass Kosten und Dauer des Projekts um etwa 25 % reduziert werden konnten.

![Effizienzsteigerung und Kostensenkung](assets/chlimage_1.png)

Das obige Diagramm zeigt die folgenden, potenziell durch HTL ermöglichten Verbesserungen hinsichtlich der Effizienz:

* **HTML/CSS/JS:** Da die HTML-Entwicklerinnen und -Entwickler die HTL-Vorlagen direkt bearbeiten können, ist es nicht mehr notwendig, die Front-End-Designs getrennt vom AEM-Projekt zu implementieren, sondern sie können direkt in die eigentlichen AEM-Komponenten implementiert werden. Dies reduziert schmerzhafte Iterationen mit den Full-Stack-Java-Entwicklerinnen und -Entwicklern.
* **JSP/HTL:** Da HTL selbst keine Java-Kenntnisse erfordert und einfach zu schreiben ist, ist jede Entwicklerin oder jeder Entwickler mit HTML-Kenntnissen in der Lage, die Vorlagen zu bearbeiten.
* **Java:** Dank der klaren und einfach zu verwendenden Use-API von HTL wird die Schnittstelle zur Geschäftslogik klarer, was auch der Java-Entwicklung insgesamt zugutekommt.

## Einführungsvideo {#video}

Das folgende Video aus einer [AEM Gems-Sitzung](https://experienceleague.adobe.com/docs/experience-manager-gems-events/gems/gems2014/aem-introduction-to-htl.html?lang=de) gibt einen Überblick über den Zweck von HTL sowie Implementierungsbeispiele.

>[!VIDEO](https://video.tv.adobe.com/v/19504/?quality=9)

Bitte beachten Sie, dass sich das Video auf HTL unter [seinem früheren Namen, Sightly](history.md), bezieht.

## Nächste Schritte {#next-steps}

Nachdem Sie nun die Ziele und Vorteile von HTL kennen, können Sie sich mit der Sprache vertraut machen, indem Sie das Dokument [Erste Schritte mit der HTML-Vorlagensprache](getting-started.md) lesen.
