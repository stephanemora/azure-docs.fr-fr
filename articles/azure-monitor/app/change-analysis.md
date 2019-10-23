---
title: Utiliser l’analyse des changements applicatifs dans Azure Monitor pour rechercher les problèmes liés aux applications web | Microsoft Docs
description: Utilisez l’analyse des changements applicatifs dans Azure Monitor pour résoudre les problèmes liés aux applications sur des sites actifs sur Azure App Service.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: 3805d7b39c25bcb213a1d4f110161dcd00eb3630
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678256"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Utilise l’analyse des changements applicatifs (préversion) dans Azure Monitor

Lorsqu’un problème ou une panne se produit sur un site actif, il est essentiel de déterminer rapidement la cause racine. Les solutions de contrôle standard peuvent vous signaler un problème. Elles peuvent même indiquer le composant en cause. Mais cette alerte ne vous expliquera pas toujours immédiatement la cause du problème. Vous savez que votre site fonctionnait il y a cinq minutes et que maintenant il ne marche plus. Qu’est-ce qui a changé au cours des cinq dernières minutes ? C’est la question que l’analyse des changements applicatifs est conçue pour répondre dans Azure Monitor.

Reposant sur la puissance [d’Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), l’analyse des changements applicatifs fournit des informations sur vos modifications à l’application Azure pour améliorer la capacité d’observation et réduire le temps moyen de réparation.

> [!IMPORTANT]
> L’analyse des changements applicatifs est actuellement en préversion. Cette préversion est fournie sans contrat de niveau de service. Cette version n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Vue d'ensemble

L’analyse des changements détecte les différents types de modifications, de la couche d’infrastructure au déploiement des applications. C’est un fournisseur de ressources Azure de niveau d’abonnement qui vérifie les modifications des ressources dans l’abonnement. L’analyse des changements fournit les données de divers outils de diagnostic pour aider les utilisateurs à comprendre les modifications susceptibles d’avoir entraîné des problèmes.

Le diagramme suivant illustre l’architecture de l’analyse des changements :

![Diagramme d’architecture de la façon dont l’analyse des changements obtient les données modifiées et les fournit aux outils clients](./media/change-analysis/overview.png)

Actuellement, l’analyse des changements est intégrée à l’expérience **Diagnostiquer et résoudre les problèmes** dans l’application web App Service. Pour activer la détection des changements et afficher les modifications dans l’application web, consultez *Analyse des changements pour la fonctionnalité Web Apps* plus loin dans cet article.

### <a name="azure-resource-manager-deployment-changes"></a>Changements de déploiement Azure Resource Manager

À l’aide [d’Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), l’analyse des changements fournit un historique de la façon dont les ressources Azure qui hébergent votre application ont été modifiées au fil du temps. L’analyse des changements peut détecter, par exemple, les changements dans les règles de configuration IP, les identités gérées et les paramètres SSL. Par conséquent, si une balise est ajoutée à une application web, l’analyse des changements reflète la modification. Ces informations sont disponibles tant que le fournisseur de ressources `Microsoft.ChangeAnalysis` est activé dans l’abonnement Azure.

### <a name="changes-in-web-app-deployment-and-configuration"></a>Modifications de configuration et de déploiement de l’application web

L’analyse des changements capture l’état de déploiement et la configuration d’une application toutes les 4 heures. Elle peut détecter, par exemple, les modifications dans les variables d’environnement de l’application. L’outil calcule les différences et présente ce qui a changé. Contrairement aux modifications de Resource Manager, les informations de modification de déploiement de code peuvent ne pas être disponibles immédiatement dans l’outil. Pour afficher les dernières modifications dans l’analyse des changements, cliquez sur le bouton **Scan changes now** (« Analyser les changements »).

![Capture d’écran du bouton « Analyser les changements »](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Changements de dépendances

Les modifications apportées aux dépendances de ressources peuvent également entraîner des problèmes dans une application web. Par exemple, si une application web appelle un cache Redis, la référence SKU du cache Redis peut affecter les performances de l’application web. Pour détecter les modifications dans les dépendances, l’analyse des changements vérifie les enregistrements DNS de l’application web. De cette façon, elle identifie les modifications dans tous les composants d’application qui peuvent entraîner des problèmes.
Actuellement, les dépendances suivantes sont prises en charge :
- Web Apps
- Stockage Azure
- Azure SQL


## <a name="change-analysis-for-the-web-apps-feature"></a>Analyse des changements pour la fonctionnalité Web Apps

Dans Azure Monitor, l’analyse des changements est actuellement intégrée dans l’expérience en libre-service **Diagnostiquer et résoudre les problèmes**. Accédez à cette expérience à partir de la page **Vue d’ensemble** de votre application App Service.

![Capture d’écran du bouton « Vue d’ensemble » et du bouton « Diagnostiquer et résoudre les problèmes »](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Activer l’analyse des changements dans l’outil Diagnostiquer et résoudre les problèmes

1. Sélectionnez **Disponibilité et performances**.

    ![Capture d’écran des options de résolution des problèmes « Disponibilité et performances »](./media/change-analysis/availability-and-performance.png)

1. Cliquez sur **Changements d’application**. Notez que cette fonctionnalité est également disponible dans **Blocage de l’application**.

   ![Capture d’écran du bouton « Blocage de l’application »](./media/change-analysis/application-changes.png)

1. Pour activer l’analyse des changements, sélectionnez **Activer maintenant**.

   ![Capture d’écran des options de « Blocage de l’application »](./media/change-analysis/enable-changeanalysis.png)

1. Activez **Analyse des changements** et sélectionnez **Enregistrer**.

    ![Capture d’écran de l’interface utilisateur « Activer l’analyse des changements »](./media/change-analysis/change-analysis-on.png)


1. Pour accéder à l’analyse des changements, sélectionnez **Diagnostiquer et résoudre les problèmes** > **Disponibilité et performances** > **Blocage de l’application**. Vous verrez un graphique qui résume les types de modifications au fil du temps, ainsi que des détails sur ces modifications :

     ![Capture d’écran de la vue de comparaison des modifications](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Activez l’analyse des changements à l’échelle

Si votre abonnement inclut de nombreuses applications web, l’activation du service pour chaque application web serait inefficace. Exécutez le script suivant pour activer toutes les applications web dans votre abonnement.

Conditions préalables :
* Module PowerShell Az. Suivez les instructions dans la rubrique[Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0)

Exécutez le script qui suit :

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"


# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```



## <a name="next-steps"></a>Étapes suivantes

- Activez application Insights pour les [applications Azure App Services](azure-web-apps.md).
- Activez Application Insights pour les [applications hébergées par IIS sur une machine virtuelle Azure et un groupe de machines virtuelles identiques Azure](azure-vm-vmss-apps.md).
- Apprenez-en davantage sur [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), qui vous aide à exécuter l’analyse des changements.
