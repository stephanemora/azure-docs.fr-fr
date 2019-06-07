---
title: 'Référence des API Azure Status Monitor v2 : Activer le moteur d’instrumentation | Microsoft Docs'
description: Référence d’API v2 de moniteur d’état. Enable-InstrumentationEngine. Surveiller les performances de site Web sans avoir à redéployer le site Web. Fonctionne avec les applications web ASP.NET hébergées en local, dans les machines virtuelles, ou sur Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 1e30490dbd51f541afd0b7036769cfc638a75877
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514381"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine-v021-alpha"></a>État du moniteur v2 API : Enable-InstrumentationEngine (v0.2.1-alpha)

Cet article décrit une applet de commande qui est un membre de la [Az.ApplicationMonitor PowerShell module](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> État du moniteur v2 est actuellement en version préliminaire publique.
> Cette version préliminaire est fournie sans contrat de niveau de service, et nous n’est pas recommandé pour les charges de travail de production. Certaines fonctionnalités ne peuvent pas être pris en charge, et certains peuvent avoir des fonctionnalités limitées.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Description

Permet au moteur d’instrumentation en définissant certaines clés de Registre.
Redémarrez IIS pour que les modifications prennent effet.

Le moteur d’instrumentation de compléter les données collectées par les kits SDK .NET.
Il collecte les événements et les messages qui décrivent l’exécution d’un processus managé. Ces événements et les messages incluent les codes de résultat de dépendance, les verbes HTTP et texte de commande SQL.

Activer le moteur d’instrumentation si :
- Vous avez déjà activé la surveillance avec l’applet de commande Enable mais que vous n’avez pas activé le moteur d’instrumentation.
- Vous avez instrumenté manuellement votre application avec les kits de développement .NET et que vous souhaitez collecter des données de télémétrie supplémentaires.

> [!IMPORTANT] 
> Cette applet de commande requiert une session PowerShell avec des autorisations d’administrateur.

> [!NOTE] 
> - Cette applet de commande nécessite que vous passez en revue et acceptez notre licence et déclaration de confidentialité.
> - Le moteur d’instrumentation ajoute une charge supplémentaire et est désactivée par défaut.

## <a name="examples"></a>Exemples

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>parameters

### <a name="-acceptlicense"></a>-AcceptLicense
**Facultatif.** Utilisez ce commutateur pour accepter la licence et déclaration de confidentialité dans les installations sans périphérique de contrôle.

### <a name="-verbose"></a>-Verbose
**Paramètre commun.** Utilisez ce commutateur pour générer des journaux détaillés.

## <a name="output"></a>Sortie


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Exemple de sortie d’avoir activé le moteur d’instrumentation

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="next-steps"></a>Étapes suivantes

  Affichez vos données de télémétrie :
 - [Exploration des mesures](../../azure-monitor/app/metrics-explorer.md) pour surveiller les performances et l’utilisation.
- [Recherchez les événements et journaux](../../azure-monitor/app/diagnostic-search.md) pour diagnostiquer les problèmes.
- Utilisez [analytique](../../azure-monitor/app/analytics.md) pour plus d’informations de requêtes avancées.
- [Créer des tableaux de bord](../../azure-monitor/app/overview-dashboard.md).
 
 Ajoutez des données de télémétrie :
 - [Créez des tests web](monitor-web-app-availability.md) s’assurer que votre site reste actif.
- [Ajoutez la télémétrie de client web](../../azure-monitor/app/javascript.md) pour afficher les exceptions à partir du code de page web et pour activer les appels de trace.
- [Ajouter le SDK Application Insights à votre code](../../azure-monitor/app/asp-net.md) afin de pouvoir insérer un suivi et enregistrer les appels.
 
 Faire plus avec Status Monitor v2 :
 - Utilisez notre guide [dépanner](status-monitor-v2-troubleshoot.md) Status Monitor v2.
 - [Obtenir la configuration](status-monitor-v2-api-get-config.md) pour confirmer que vos paramètres ont été enregistrés correctement.
 - [Obtenir l’état](status-monitor-v2-api-get-status.md) pour inspecter la surveillance.
