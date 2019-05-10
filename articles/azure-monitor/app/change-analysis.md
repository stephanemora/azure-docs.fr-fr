---
title: Analyser l’application Azure modifier analysis - Découvrez les modifications qui peuvent affecter les problèmes de site actif/pannes avec application d’Azure Monitor analysis | Microsoft Docs
description: Résoudre les problèmes de site actif d’application sur Azure App Services avec l’analyse de modification d’application Azure Monitor
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: b04bd57c66b52e9a2c14d43c9e89d7c54fc48ae2
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415844"
---
# <a name="azure-monitor-application-change-analysis-public-preview"></a>Azure analyse des modifications application Monitor (préversion publique)

Lorsqu’un problème / l’arrêt du site en direct se produit, il est essentiel de déterminer de cause rapidement. Norme de solutions de surveillance peut-être vous aider à identifier rapidement qu’il existe un problème, et souvent même le composant en cause. Mais cela ne sont pas toujours entraîner une explication immédiate pour la raison pour laquelle l’échec se produit. Votre site a travaillé à cinq minutes auparavant, maintenant il a été endommagé. Ce qui a changé au cours des cinq dernières minutes ? Il s’agit de la question de la nouvelle fonctionnalité Azure Monitor analysis de modification d’application est conçue pour répondre. En s’appuyant sur la puissance de la [graphique des ressources Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview) analysis de modification d’application fournit un aperçu de vos modifications d’application Azure App Service pour augmenter la capacité d’observation et réduire MTTR (temps moyen de réparation).

> [!IMPORTANT]
> Analyse des modifications Azure Monitor application est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-do-i-use-application-change-analysis"></a>Utilisation de l’analyse de modification d’application

Analyse des modifications Azure Monitor application actuellement intègre le libre-service **diagnostiquer et résoudre les problèmes** expérience, qui est accessible à partir de la **vue d’ensemble** section de votre Azure App Service application :

![Vue d’ensemble de la capture d’écran d’Azure App Service page avec zones rouges autour de bouton de la vue d’ensemble et diagnostiquer et résoudre les bouton de problèmes](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis"></a>Activer l’analyse de modification

1. Sélectionnez **disponibilité et performances**

    ![capture d’écran de la disponibilité et les options de résolution des problèmes de performances](./media/change-analysis/availability-and-performance.png)

2. Cliquez sur le **Application subit un incident** vignette.

   ![Capture d’écran avec l’application bloque en mosaïque](./media/change-analysis/application-crashes-tile.png)

3. Pour activer **modification Analysis** sélectionnez **activer maintenant**.

   ![capture d’écran de la disponibilité et les options de résolution des problèmes de performances](./media/change-analysis/application-crashes.png)

4. Pour tirer parti de la version complète modifier ensemble de fonctionnalités d’analyse **Analysis modifier**, **rechercher les modifications de code**, et **Always on** à **sur** Sélectionnez **enregistrer**.

    ![Capture d’écran de l’interface utilisateur analyse de modification d’enable Azure App Service](./media/change-analysis/change-analysis-on.png)

    Si **modification Analysis** est activé, vous serez en mesure de détecter des modifications au niveau des ressources. Si **rechercher les modifications de code** est activé, vous allez également voir les fichiers de déploiement et les modifications de configuration du site. L’activation de **Always on** permettra d’optimiser la modification de l’analyse des performances, mais peut entraîner des frais supplémentaires à partir d’une perspective de facturation.

5.  Une fois que tout est activé, en sélectionnant **diagnostiquer et résoudre les problèmes** > **disponibilité et performances** > **Application subit un incident** vous permettra pour accéder à l’expérience d’analyse de modification. Le graphique sera summerize le type de modifications qui se sont produits au fil du temps, ainsi que des détails sur ces modifications :

     ![Capture d’écran de modifier l’affichage diff](./media/change-analysis/change-view.png)

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="unable-to-fetch-change-analysis-information"></a>Impossible de récupérer les informations d’analyse de la modification.

Il s’agit d’un problème temporaire avec l’expérience de l’intégration de version préliminaire actuelle. La solution de contournement se compose d’une balise masquée sur votre application web et actualisez la page de :

   ![Capture d’écran de la balise de la modification masquée](./media/change-analysis/hidden-tag.png)

Pour définir la balise masquée à l’aide de PowerShell :

1. Ouvrez Azure Cloud Shell :

    ![Capture d’écran de modification Azure Cloud Shell](./media/change-analysis/cloud-shell.png)

2. Modifiez le type de l’interpréteur de commandes PowerShell :

   ![Capture d’écran de modification Azure Cloud Shell](./media/change-analysis/choose-powershell.png)

3. Exécutez la commande suivante :

```powershell
$webapp=Get-AzWebApp -Name <name_of_your_webapp>
$tags = $webapp.Tags
$tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
```

> [!NOTE]
> Une fois que la balise masquée est ajoutée, vous devrez peut-être toujours initialement attendez jusqu'à 4 heures pour être en mesure d’afficher tout d’abord les modifications. Cela est dû le freqeuncy 4 heures par le service d’analyse de modification pour analyser votre application web tout en limitant l’impact sur les performances de l’analyse.

## <a name="next-steps"></a>Étapes suivantes

- Améliorer votre analyse des Services d’application Azure [en activant les fonctionnalités d’Application Insights](azure-web-apps.md) d’Azure Monitor.
- Améliorer vos connaissances de la [graphique des ressources Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview) qui permet d’application d’Azure Monitor power modifier analysis. 
