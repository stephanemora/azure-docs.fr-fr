---
title: 'Référence des API v2 statut Azure Monitor : Activer le moteur d’instrumentation | Microsoft Docs'
description: État du moniteur v2 API référence Enable-InstrumentationEngine. Surveiller les performances de site Web sans avoir à redéployer le site Web. Fonctionne avec les applications web ASP.NET hébergées localement, dans des machines virtuelles ou sur Azure.
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
ms.openlocfilehash: 7d337c9b6b22f3abfcb4aea1c47127706ed9e9d7
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870506"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine-v021-alpha"></a>État du moniteur v2 API : Enable-InstrumentationEngine (v0.2.1-alpha)

Ce document décrit une applet de commande qui est fourni en tant que membre de la [Az.ApplicationMonitor PowerShell module](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> État du moniteur v2 est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [conditions d’utilisation supplémentaires pour les versions préliminaires de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>Description

Cette applet de commande permettra le moteur d’Instrumentation en définissant certaines clés de Registre.
Redémarrez IIS pour que ces modifications prennent effet.

Le moteur d’Instrumentation de compléter les données collectées par les kits SDK .NET.
Événements et les messages seront collectés qui décrivent l’exécution d’un processus managé. Y compris mais pas limité à des Codes de résultat de dépendance, les verbes HTTP et texte de commande SQL. 

Activer le moteur d’Instrumentation si :
- Vous avez déjà activé la surveillance à l’aide d’applet de commande Enable mais que vous n’avez pas activé le InstrumentationEngine.
- Vous avez instrumenté manuellement votre application avec les kits de développement .NET et que vous souhaitez collecter des données de télémétrie supplémentaires.

> [!IMPORTANT] 
> Cette applet de commande requiert une PowerShell Session avec des autorisations d’administrateur.

> [!NOTE] 
> Cette applet de commande vous obligera à Lisez et acceptez notre licence et déclaration de confidentialité.

> [!NOTE] 
> Le moteur d’Instrumentation ajoute une charge supplémentaire et est désactivée par défaut.

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
