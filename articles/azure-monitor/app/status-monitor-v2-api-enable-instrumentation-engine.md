---
title: Informations de référence sur l’API de l’agent Azure Application Insights
description: Référence API d’Application Insights Agent. Enable-InstrumentationEngine. Surveillez les performances de site web sans avoir à redéployer le site web. Fonctionne avec les applications web ASP.NET hébergées en local, dans des machines virtuelles ou sur Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b3f298ac31cc584cd16553186359c87f69f27aad
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671352"
---
# <a name="application-insights-agent-api-enable-instrumentationengine"></a>API d’Application Insights Agent : Enable-InstrumentationEngine

Cet article décrit une cmdlet appartenant au [module PowerShell Az.ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Description

Active le moteur d’instrumentation en définissant certaines clés de Registre.
Redémarrez IIS pour que les modifications soient prises en compte.

Le moteur d’instrumentation peut compléter les données collectées par les kits de développement logiciel (SDK) .NET.
Il collecte les événements et les messages qui décrivent l’exécution d’un processus managé. Ces événements et messages incluent les codes de résultat de dépendance, les verbes HTTP et le [texte de commande SQL](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Activez le moteur d’instrumentation si :
- Vous avez déjà activé la supervision avec la cmdlet Enable mais n’avez pas activé le moteur d’instrumentation.
- Vous avez instrumenté manuellement votre application avec les kits de développement (SDK) .NET, et souhaitez collecter des données de télémétrie supplémentaires.

> [!IMPORTANT] 
> Cette cmdlet requiert une session PowerShell avec des autorisations d’administrateur.

> [!NOTE] 
> - Cette cmdlet nécessite que vous lisiez et acceptiez nos licence et déclaration de confidentialité.
> - Le moteur d’instrumentation ajoute une surcharge et est désactivé par défaut.

## <a name="examples"></a>Exemples

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Paramètres

### <a name="-acceptlicense"></a>-AcceptLicense
**Facultatif.** Utilisez cette instruction switch pour accepter la licence et la déclaration de confidentialité dans les installations sans affichage.

### <a name="-verbose"></a>-Verbose
**Paramètre commun.** Utilisez ce commutateur pour générer des journaux détaillés.

## <a name="output"></a>Output


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Exemple de sortie résultant d’une activation réussie du moteur d’instrumentation

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="next-steps"></a>Étapes suivantes

  Affichez vos données de télémétrie :
 - [Explorez les métriques](../../azure-monitor/app/metrics-explorer.md) pour surveiller les performances et l’utilisation.
- [Effectuez des recherches dans les événements et les journaux](../../azure-monitor/app/diagnostic-search.md) pour diagnostiquer les problèmes.
- Utilisez la fonctionnalité [Analytics](../../azure-monitor/app/analytics.md) pour des requêtes plus élaborées.
- [Créez des tableaux de bord](../../azure-monitor/app/overview-dashboard.md).
 
 Ajoutez des données de télémétrie :
 - [Créez des tests web](monitor-web-app-availability.md) pour vous assurer que votre site reste actif.
- [Ajoutez la télémétrie de client web](../../azure-monitor/app/javascript.md) pour afficher les exceptions à partir du code de la page web et activer le suivi des appels.
- [Ajoutez le kit de développement logiciel (SDK) Application Insights à votre code](../../azure-monitor/app/asp-net.md) afin de pouvoir insérer un suivi et journaliser les appels.
 
 En faire plus avec Application Insights Agent :
 - Utilisez notre guide pour [résoudre les problèmes](status-monitor-v2-troubleshoot.md) d’Application Insights Agent.
 - [Obtenez la configuration](status-monitor-v2-api-get-config.md) pour confirmer que vos paramètres ont été enregistrés correctement.
 - [Obtenez l’état](status-monitor-v2-api-get-status.md) pour inspecter la surveillance.
