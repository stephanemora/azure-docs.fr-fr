---
title: Utiliser l’analyse des changements applicatifs dans Azure Monitor pour rechercher les problèmes liés aux applications web | Microsoft Docs
description: Utilisez l’analyse des changements applicatifs dans Azure Monitor pour résoudre les problèmes liés aux applications sur des sites actifs sur Azure App Service.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: 143f55a02a856b536172bd5fc2bac15903a228b9
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77655682"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Utilise l’analyse des changements applicatifs (préversion) dans Azure Monitor

Lorsqu’un problème ou une panne se produit sur un site actif, il est essentiel de déterminer rapidement la cause racine. Les solutions de contrôle standard peuvent vous signaler un problème. Elles peuvent même indiquer le composant en cause. Mais cette alerte ne vous expliquera pas toujours immédiatement la cause du problème. Vous savez que votre site fonctionnait il y a cinq minutes et que maintenant il ne marche plus. Qu’est-ce qui a changé au cours des cinq dernières minutes ? C’est la question que l’analyse des changements applicatifs est conçue pour répondre dans Azure Monitor.

Reposant sur la puissance [d’Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), l’analyse des changements applicatifs fournit des informations sur vos modifications à l’application Azure pour améliorer la capacité d’observation et réduire le temps moyen de réparation.

> [!IMPORTANT]
> L’analyse des changements applicatifs est actuellement en préversion. Cette préversion est fournie sans contrat de niveau de service. Cette version n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Vue d’ensemble

L’analyse des changements détecte les différents types de modifications, de la couche d’infrastructure au déploiement des applications. C’est un fournisseur de ressources Azure de niveau d’abonnement qui vérifie les modifications des ressources dans l’abonnement. L’analyse des changements fournit les données de divers outils de diagnostic pour aider les utilisateurs à comprendre les modifications susceptibles d’avoir entraîné des problèmes.

Le diagramme suivant illustre l’architecture de l’analyse des changements :

![Diagramme d’architecture de la façon dont l’analyse des changements obtient les données modifiées et les fournit aux outils clients](./media/change-analysis/overview.png)

L’analyse des changements est intégrée à l’expérience **Diagnostiquer et résoudre les problèmes** dans l’application web App Service et est également disponible en tant qu’onglet autonome dans le portail Azure.
Plus loin dans cet article, consultez la section *Afficher les modifications de toutes les ressources dans Azure*, pour savoir comment accéder au panneau Analyse des changements, et à la section *Analyse des changements pour la fonctionnalité Web Apps*, pour savoir comment l’utiliser dans le portail d’applications web.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Modifications des propriétés suivies Azure Resource Manager

À l’aide [d’Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), l’analyse des changements fournit un historique de la façon dont les ressources Azure qui hébergent votre application ont été modifiées au fil du temps. Les paramètres suivis tels que les identités managées, la mise à niveau du système d’exploitation de la plateforme et les noms d’hôte peuvent être détectés.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Modifications des paramètres de proxy Azure Resource Manager
Les paramètres tels que la règle Configuration IP, les paramètres SSL et les versions d’extension ne sont pas encore disponibles dans Azure Resource Graph. Ainsi, l’analyse des changements interroge et calcule ces modifications de manière sécurisée pour fournir plus de détails sur ce qui a changé dans l’application. Ces informations ne sont pas encore disponibles dans Azure Resource Graph, mais le seront bientôt.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Modifications dans le déploiement et la configuration des applications web (dans l’invité)

L’analyse des changements capture l’état de déploiement et la configuration d’une application toutes les 4 heures. Elle peut détecter, par exemple, les modifications dans les variables d’environnement de l’application. L’outil calcule les différences et présente ce qui a changé. Contrairement aux modifications de Resource Manager, les informations de modification de déploiement de code peuvent ne pas être disponibles immédiatement dans l’outil. Pour afficher les dernières modifications dans l’analyse des changements, cliquez sur le bouton **Scan changes now** (« Analyser les changements »).

![Capture d’écran du bouton « Analyser les changements »](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Changements de dépendances

Les modifications apportées aux dépendances de ressources peuvent également entraîner des problèmes dans une application web. Par exemple, si une application web appelle un cache Redis, la référence SKU du cache Redis peut affecter les performances de l’application web. Pour détecter les modifications dans les dépendances, l’analyse des changements vérifie les enregistrements DNS de l’application web. De cette façon, elle identifie les modifications dans tous les composants d’application qui peuvent entraîner des problèmes.
Actuellement, les dépendances suivantes sont prises en charge :
- Web Apps
- Stockage Azure
- Azure SQL

### <a name="enablement"></a>Activation
Le fournisseur de ressources « Microsoft.ChangeAnalysis » doit être inscrit avec un abonnement pour que les données sur les modifications des propriétés suivies et des paramètres de proxy Azure Resource Manager soient disponibles. Quand vous entrez dans l’outil Diagnostiquer et résoudre les problèmes de l’application web ou que vous affichez l’onglet autonome Analyse des changements, ce fournisseur de ressources est automatiquement inscrit. Il est sans incidence sur votre abonnement concernant les performances et les coûts. Quand vous activez Analyse des changements pour les applications web (ou que vous procédez à l’activation dans l’outil Diagnostiquer et résoudre les problèmes), l’opération a un impact négligeable sur les performances de l’application web et n’entraîne aucun coût de facturation.
Pour les modifications d’une application web dans l’invité, une activation distincte est requise pour l’analyse des fichiers de code au sein de l’application web. Pour plus d’informations, consultez [Activer l’analyse des changements dans l’outil Diagnostiquer et résoudre les problèmes](https://docs.microsoft.com/azure/azure-monitor/app/change-analysis#enable-change-analysis-in-the-diagnose-and-solve-problems-tool) plus loin dans cet article.


## <a name="viewing-changes-for-all-resources-in-azure"></a>Afficher les modifications de toutes les ressources dans Azure
Il existe dans Azure Monitor un panneau autonome pour l’Analyse des changements qui permet d’afficher toutes les modifications avec des insights et les ressources des dépendances de l’application.

Recherchez Analyse des changements dans la barre de recherche du Portail Azure pour lancer le panneau.

![Capture d’écran de la recherche de l’Analyse des changements sur le Portail Azure](./media/change-analysis/search-change-analysis.png)

Sélectionnez Groupe de ressources et les ressources pour afficher les modifications.

![Capture d’écran du panneau Analyse des changements sur le Portail Azure](./media/change-analysis/change-analysis-standalone-blade.png)

Vous pouvez y voir des insights et les ressources des dépendances associées qui hébergent votre application. Cette vue est conçue pour être centrée sur l’application et permettre ainsi aux développeurs de résoudre les problèmes.

Les ressources actuellement prises en charge sont les suivantes :
- Virtual Machines
- Groupe de machines virtuelles identiques
- Ressources réseau Azure
- Application web avec suivi des fichiers dans l’invité et modification des variables d’environnement

Pour tout commentaire, utilisez le bouton Envoyer des commentaires dans le panneau ou envoyez un e-mail à l’adresse changeanalysisteam@microsoft.com.

![Capture d’écran du bouton Commentaires dans le panneau Analyse des changements](./media/change-analysis/change-analysis-feedback.png)

## <a name="change-analysis-for-the-web-apps-feature"></a>Analyse des changements pour la fonctionnalité Web Apps

Dans Azure Monitor, l’Analyse des changements est également intégrée à l’expérience en libre-service **Diagnostiquer et résoudre les problèmes**. Accédez à cette expérience à partir de la page **Vue d’ensemble** de votre application App Service.

![Capture d’écran du bouton « Vue d’ensemble » et du bouton « Diagnostiquer et résoudre les problèmes »](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Activer l’analyse des changements dans l’outil Diagnostiquer et résoudre les problèmes

1. Sélectionnez **Disponibilité et performances**.

    ![Capture d’écran des options de résolution des problèmes « Disponibilité et performances »](./media/change-analysis/availability-and-performance.png)

1. Cliquez sur **Changements d’application**. Notez que cette fonctionnalité est également disponible dans **Blocage de l’application**.

   ![Capture d’écran du bouton « Blocage de l’application »](./media/change-analysis/application-changes.png)

1. Pour activer l’analyse des changements, sélectionnez **Activer maintenant**.

   ![Capture d’écran des options de « Blocage de l’application »](./media/change-analysis/enable-changeanalysis.png)

1. Activez **Analyse des changements** et sélectionnez **Enregistrer**. L’outil affiche toutes les applications web dans le cadre d’un plan App Service. Vous pouvez utiliser le commutateur de niveau de plan afin d’activer l’analyse des modifications pour toutes les applications web dans le cadre d’un plan.

    ![Capture d’écran de l’interface utilisateur « Activer l’analyse des changements »](./media/change-analysis/change-analysis-on.png)


1. Pour accéder à l’analyse des changements, sélectionnez **Diagnostiquer et résoudre les problèmes** > **Disponibilité et performances** > **Blocage de l’application**. Vous verrez un graphique qui résume les types de modifications au fil du temps ainsi que des détails sur ces modifications. Par défaut, les modifications effectuées au cours des dernières 24 heures s’affichent pour vous aider à résoudre des problèmes immédiats.

     ![Capture d’écran de la vue de comparaison des modifications](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Activez l’analyse des changements à l’échelle

Si votre abonnement inclut de nombreuses applications web, l’activation du service pour chaque application web serait inefficace. Exécutez le script suivant pour activer toutes les applications web dans votre abonnement.

Conditions préalables :
* Module PowerShell Az. Suivez les instructions dans la rubrique[Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0)

Exécutez le script suivant :

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
