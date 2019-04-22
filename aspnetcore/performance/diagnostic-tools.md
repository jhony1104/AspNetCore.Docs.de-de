---
title: Diagnose von Leistungstools
author: mjrousos
description: Hilfreiche Tools zum Diagnostizieren von Leistungsproblemen in ASP.NET Core-apps.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
uid: performance/diagnostic-tools
ms.openlocfilehash: 66676b5a2b95b87bfbbd50022e279e35a12b9793
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59516221"
---
# <a name="performance-diagnostic-tools"></a>Leistung-Diagnosetools

Durch [Mike Rousos](https://github.com/mjrousos)

Dieser Artikel beschreibt die Tools zum Diagnostizieren von Leistungsproblemen in ASP.NET Core.

## <a name="visual-studio-diagnostic-tools"></a>Visual Studio-Diagnosetools

Die [profilerstellung und Diagnosetools](/visualstudio/profiling) in Visual Studio integriert sind ein guter Ausgangspunkt für die Untersuchung Leistungsprobleme auftreten. Diese Tools sind leistungsstark und praktisch, in der Visual Studio-Entwicklungsumgebung zu verwenden. Das Tool ermöglicht die Analyse der CPU-Auslastung, speicherauslastung und Leistungsereignisse in ASP.NET Core-apps. Integrierte wird wird einfach die profilerstellung, zum Zeitpunkt der Entwicklung.

Weitere Informationen finden Sie in [Visual Studio-Dokumentation](/visualstudio/profiling/profiling-overview).

## <a name="application-insights"></a>Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) bietet ausführlichere Leistungsdaten für Ihre app. Application Insights erfasst automatisch Daten, auf die Antwortrate, Fehlerraten, Reaktionszeiten von Abhängigkeiten und vieles mehr. Application Insights unterstützt benutzerdefinierte Ereignisse und Metriken, die bestimmte zu Ihrer app anmelden.

Azure Application Insights bietet mehrere Möglichkeiten, die Einblicke für überwachte apps zu gewähren:

- [Anwendungszuordnung](/azure/application-insights/app-insights-app-map) – können Sie erkennen von Leistungsengpässen oder Fehler Hotspots in allen Komponenten der verteilten apps.
- [Azure-Metrik-Explorer](/azure/azure-monitor/platform/metrics-getting-started) ist eine Komponente von Microsoft Azure-Portal, die ermöglicht, Zeichnen von Diagrammen, visuell Korrelieren von Trends, und Untersuchen von Spitzen und Tiefen in Metrikwerten.
- [Blatt "Leistung" in Application Insights-Portal](/azure/application-insights/app-insights-tutorial-performance):

  - Zeigt Details zu der Leistung für verschiedene Vorgänge in der überwachten-app.
  - Können Drilldowns in einem einzigen Vorgang, um alle Teile/Abhängigkeiten zu überprüfen, die zu einem langen beitragen.
  - Profiler kann hier zum Sammeln von Leistung ablaufverfolgungen bei Bedarf aufgerufen werden.

- [Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) können reguläre und on-Demand-profilerstellung von .NET-Apps.  Azure-Portal zeigt erfasst leistungsnachverfolgungen mit Aufruflisten und langsamsten Pfade. Die Ablaufverfolgungsdateien können für eine umfassende Analyse mit PerfView auch heruntergeladen werden.

Application Insights können in einer Vielzahl-Umgebungen verwendet werden:

- Für die Zusammenarbeit in Azure optimiert.
- Funktioniert in Staging, Entwicklung und Produktion.
- Lokal funktioniert [Visual Studio](/azure/application-insights/app-insights-visual-studio) oder in anderen Hostumgebungen.

Weitere Informationen finden Sie unter [Application Insights for ASP.NET Core (Application Insights für ASP.NET Core)](/azure/application-insights/app-insights-asp-net-core).

## <a name="perfview"></a>PerfView

[PerfView](https://github.com/Microsoft/perfview) ist ein Performance Analysetool durch das .NET Team speziell für das Diagnostizieren von Leistungsproblemen .NET erstellt. PerfView ermöglicht die Analyse der CPU-Auslastung, Arbeitsspeicher und GC Verhalten, Leistungsereignisse und gesamtbetrachtungszeit.

Erfahren Sie mehr über PerfView und erste Schritte mit [Videotutorials für PerfView](http://channel9.msdn.com/Series/PerfView-Tutorial) oder durch Lesen das Benutzerhandbuch zur Verfügung, in das Tool oder [auf GitHub](https://github.com/Microsoft/perfview).

## <a name="windows-performance-toolkit"></a>Windows Performance Toolkit

[Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) besteht aus zwei Komponenten: Windows Performance Recorder (WPR) und Windows Performance Analyzer (WPA). Die Tools erzeugen ausführliche Leistungsprofile von Windows-Betriebssystemen und apps. WPT hat umfassendere Möglichkeiten zur Visualisierung von Daten, aber das Sammeln von Daten ist weniger leistungsfähig als die PerfView.

## <a name="perfcollect"></a>PerfCollect

Während PerfView eine nützliche leistungsanalysetools für Szenarien mit .NET ist, wird er nur auf Windows, sodass Sie es verwenden können, um die Ablaufverfolgung von ASP.NET Core-apps unter Linux-Umgebungen zu sammeln.

[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) ist ein Bash-Skript, das native Linux Profilerstellungstools verwendet ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) und [LTTng](https://lttng.org/)) um die Ablaufverfolgung unter Linux zu sammeln, die mit PerfView analysiert werden kann. PerfCollect ist nützlich, wenn Probleme mit der Leistung in Linux-Umgebungen angezeigt, in denen PerfView direkt verwendet werden kann. Stattdessen kann PerfCollect Sammeln von ablaufverfolgungen von .NET Core-apps, die dann analysiert werden auf einem Windows-Computer, die mithilfe von PerfView.

Weitere Informationen zum Installieren und erste Schritte mit PerfCollect [auf GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).

## <a name="other-third-party-performance-tools"></a>Andere Leistungstools von Drittanbietern

Die folgende Liste enthält einige Drittanbieter-Leistungstools, die in die Untersuchung der Leistung von .NET Core-Anwendungen nützlich sind.

- [MiniProfiler](https://miniprofiler.com/)
- DotTrace und DotMemory von JetBrains
- VTune von Intel
