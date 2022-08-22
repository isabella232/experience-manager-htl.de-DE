---
title: HTL-Verlauf
description: Lange Benutzer von AEM erhalten in diesem Dokument einen Hintergrund für HTL, wie JSP ersetzt wird und wie der Name von Sightly geändert wird.
exl-id: 00985b35-2130-4946-959a-0a09a34a0f05
source-git-commit: 5ab1275c984135fe946f36905bbc979cf19edd80
workflow-type: tm+mt
source-wordcount: '542'
ht-degree: 50%

---


# HTL-Verlauf {#history-of-htl}

Lange Benutzer von AEM erhalten in diesem Dokument einen Hintergrund für HTL, wie JSP ersetzt wird und wie der Name von Sightly geändert wird.

## Zuvor als Sightly bezeichnet {#sightly}

HTML Template Language (HTL) ist das bevorzugte und empfohlene serverseitige Vorlagensystem zum HTML in Adobe Experience Manager. Sie ersetzt JSP (JavaServer Pages), das in früheren Versionen von AEM verwendet wurde.

## HTL über JSP {#htl-over-jsp}

Für neue AEM-Projekte sollte die HTML-Vorlagensprache verwendet werden, da sie gegenüber JSP mehrere Vorteile hat. Bei bestehenden Projekten macht eine Migration jedoch nur Sinn, wenn abschätzbar ist, dass dies weniger aufwändig ist, als die vorhandenen JSPs für die kommenden Jahre zu verwalten.

Der Wechsel zu HTL ist nicht zwangsläufig eine Alles-oder-Nichts-Entscheidung, da in HTL geschriebene Komponenten mit in JSP oder ESP geschriebenen Komponenten kompatibel sind. Vorhandene Projekte können demnach problemlos HTL für neue Komponenten und gleichzeitig JSP für vorhandene Komponenten verwenden.

HTL-Dateien können sogar in derselben Komponente zusammen mit JSPs und ESPs verwendet werden. Im folgenden Beispiel wird in **Zeile 1** gezeigt, wie eine HTL-Datei aus einer JSP-Datei eingeschlossen wird, und in **Zeile 2**, wie eine JSP-Datei aus einer HTL-Datei eingeschlossen werden kann:

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

## Häufig gestellte Fragen {#frequently-asked-questions}

Dies sind einige Fragen, die häufig von erfahrenen AEM-Entwicklern gestellt werden, die neu bei HTL sind.

### Weist HTL Einschränkungen auf, die bei JSP nicht vorhanden sind? {#limitations}

HTL weist im Vergleich zu JSP keine wirklichen Einschränkungen auf. Was mit JSP erledigt werden kann, sollte auch mit HTL erreichbar sein. HTL ist in verschiedener Hinsicht jedoch grundsätzlich strenger als JSP. Was in einer einzelnen JSP-Datei erreicht werden kann, muss möglicherweise in eine Java-Klasse oder eine JavaScript-Datei unterteilt werden, damit es in HTL erreichbar ist. Dies ist jedoch generell erwünscht, um eine entsprechende Trennung von Belangen zwischen der Logik und dem Markup sicherzustellen.

### Werden JSP-Tag-Bibliotheken durch HTL unterstützt? {#tag-libraries}

Nein, aber wie im [Laden von Client-Bibliotheken](getting-started.md#loading-client-libraries) -Abschnitt des Dokuments &quot;Erste Schritte&quot;bieten die Anweisungen template &amp; call ein ähnliches Muster.

### Können die HTL-Funktionen für ein AEM-Projekt erweitert werden? {#extended}

Nein, das können sie nicht. HTL verfügt über leistungsstarke Erweiterungsmechanismen für die Wiederverwendung der Logik (die [Anwendungs-API](#use-api-for-accessing-logic)) und des Markups (die Anweisungen template &amp; call ), die zur Modularisierung des Projektcodes verwendet werden können.

### Was sind die Hauptvorteile von HTL im Vergleich zu JSP? {#benefits}

Sicherheit und Projekteffizienz sind die wichtigsten Vorteile, die im Abschnitt [Überblick.](overview.md)

### Wird JSP schließlich verschwinden? {#go-away}

Es gibt keine Pläne in dieser Richtung.

## Was ist in einem Namen? {#what-is-in-a-name}

In AEM 6.0 und 6.1 wurde HTL als **Sightly**. Adobe umbenannt in **HTML-Vorlagensprache** oder **HTL** , um klarzustellen, wozu die Spezifikation dient, und um sie an die Benennungsrichtlinien der Adobe im Allgemeinen anzupassen. Diese Namensänderung wurde im August 2016 wirksam und gilt für AEM Version 6.0 und höher.

>[!NOTE]
>
>Die Namensänderung wirkt sich weder auf den Code noch auf die API aus. Daher ist die Kompatibilität nicht betroffen. Weitere Informationen finden Sie unter [Sehen Sie sich hierzu das Ankündigungsvideo an.](https://helpx.adobe.com/de/experience-manager/how-to/announce-htl.html)

Um mehr über HTL zu erfahren und einen guten Ausgangspunkt zu finden, ist unser offizieller [Erste Schritte mit dem Handbuch zum HTML Templating Language (HTL).](overview.md)
