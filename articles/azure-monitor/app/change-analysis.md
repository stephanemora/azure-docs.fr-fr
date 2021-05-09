---
title: Utiliser l’analyse des changements applicatifs dans Azure Monitor pour rechercher les problèmes liés aux applications web | Microsoft Docs
description: Utilisez l’analyse des changements applicatifs dans Azure Monitor pour résoudre les problèmes liés aux applications sur des sites actifs sur Azure App Service.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9366b268ca394228a63ecfd18ac6c2f4576ba21a
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108315684"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Utilise l’analyse des changements applicatifs (préversion) dans Azure Monitor

Lorsqu’un problème ou une panne se produit sur un site actif, il est essentiel de déterminer rapidement la cause racine. Les solutions de contrôle standard peuvent vous signaler un problème. Elles peuvent même indiquer le composant en cause. Mais cette alerte ne vous expliquera pas toujours immédiatement la cause du problème. Vous savez que votre site fonctionnait il y a cinq minutes et que maintenant il ne marche plus. Qu’est-ce qui a changé au cours des cinq dernières minutes ? C’est la question que l’analyse des changements applicatifs est conçue pour répondre dans Azure Monitor.

Reposant sur la puissance [d’Azure Resource Graph](../../governance/resource-graph/overview.md), l’analyse des changements applicatifs fournit des informations sur vos modifications à l’application Azure pour améliorer la capacité d’observation et réduire le temps moyen de réparation.

> [!IMPORTANT]
> L’analyse des changements applicatifs est actuellement en préversion. Cette préversion est fournie sans contrat de niveau de service. Cette version n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Vue d’ensemble

L’analyse des changements détecte les différents types de modifications, de la couche d’infrastructure au déploiement des applications. C’est un fournisseur de ressources Azure de niveau d’abonnement qui vérifie les modifications des ressources dans l’abonnement. L’analyse des changements fournit les données de divers outils de diagnostic pour aider les utilisateurs à comprendre les modifications susceptibles d’avoir entraîné des problèmes.

Le diagramme suivant illustre l’architecture de l’analyse des changements :

![Diagramme d’architecture de la façon dont l’analyse des changements obtient les données modifiées et les fournit aux outils clients](./media/change-analysis/overview.png)

## <a name="supported-resource-types"></a>Types de ressources pris en charge

Le service Analyse des changements d'application prend en charge les modifications de niveau de propriété de ressources dans tous les types de ressources Azure, y compris des ressources courantes telles que les suivantes :
- Machine virtuelle
- Jeu de mise à l’échelle de machine virtuelle
- App Service
- Azure Kubernetes Service
- Fonction Azure
- Ressources réseau : groupe de sécurité réseau, réseau virtuel, passerelle applicative, etc.
- Services de données : Stockage, SQL, Cache Redis, Cosmos DB, etc.

## <a name="data-sources"></a>Sources de données

L’analyse des changements d’application interroge les propriétés suivies d’Azure Resource Manager, les configurations proxysées et les modifications apportées à une application web dans l’invité. De plus, le service effectue le suivi des modifications sur les dépendances de ressource pour diagnostiquer et superviser une application de bout en bout.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Modifications des propriétés suivies Azure Resource Manager

À l’aide [d’Azure Resource Graph](../../governance/resource-graph/overview.md), l’analyse des changements fournit un historique de la façon dont les ressources Azure qui hébergent votre application ont été modifiées au fil du temps. Les paramètres suivis tels que les identités managées, la mise à niveau du système d’exploitation de la plateforme et les noms d’hôte peuvent être détectés.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Modifications des paramètres de proxy Azure Resource Manager

Les paramètres, tels que la règle Configuration IP, les paramètres TLS et les versions d’extension ne sont pas encore disponibles dans Azure Resource Graph. Ainsi, l’analyse des changements interroge et calcule ces modifications de manière sécurisée pour fournir plus de détails sur ce qui a changé dans l’application.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Modifications dans le déploiement et la configuration des applications web (dans l’invité)

L’analyse des changements capture l’état de déploiement et la configuration d’une application toutes les 4 heures. Elle peut détecter, par exemple, les modifications dans les variables d’environnement de l’application. L’outil calcule les différences et présente ce qui a changé. Contrairement aux modifications de Resource Manager, les informations de modification de déploiement de code peuvent ne pas être disponibles immédiatement dans l’outil. Pour afficher les toutes dernières modifications dans l’analyse des changements, sélectionnez **Actualiser**.

![Capture d’écran du bouton « Analyser les changements »](./media/change-analysis/scan-changes.png)

Tous les fichiers texte sous la racine du site **wwwroot** avec les extensions suivantes sont actuellement pris en charge :
- *.json
- *.xml
- *.ini
- *.yml
- *.config
- *.properties
- *.html
- *.cshtml
- *.js
- requirements.txt
- Gemfile
- Gemfile.lock
- config.gemspec

### <a name="dependency-changes"></a>Changements de dépendances

Les modifications apportées aux dépendances de ressources peuvent également occasionner des problèmes dans une ressource. Par exemple, si une application web appelle un cache Redis, la référence SKU du cache Redis peut affecter les performances de l’application web. Autre exemple. La fermeture du port 22 dans un groupe de sécurité réseau de machine virtuelle entraîne des erreurs de connectivité.

#### <a name="web-app-diagnose-and-solve-problems-navigator-preview"></a>Navigateur pour le diagnostic et la résolution de problèmes d’application web (préversion)

Pour détecter les modifications dans les dépendances, l’analyse des changements vérifie les enregistrements DNS de l’application web. De cette façon, elle identifie les modifications dans tous les composants d’application qui peuvent entraîner des problèmes.
Actuellement, les dépendances suivantes sont prises en charge dans le **Navigateur pour le diagnostic et la résolution de problèmes d’application web (préversion)**  :

- Web Apps
- Stockage Azure
- Azure SQL

#### <a name="related-resources"></a>Ressources associées

La fonctionnalité Analyse des changements d'application détecte les ressources associées. Exemples courants : groupe de sécurité réseau, réseau virtuel, passerelle applicative et équilibreur de charge en lien avec une machine virtuelle.
Les ressources réseau étant généralement approvisionnées automatiquement dans le même groupe de ressources que les ressources qui utilisent celui-ci, le filtrage des modifications par groupe de ressources affiche toutes les modifications de la machine virtuelle et des ressources réseau associées.

![Capture d’écran des modifications de réseau](./media/change-analysis/network-changes.png)

## <a name="application-change-analysis-service-enablement"></a>Activation du service Analyse des changements d’application

Le service d’analyse des changements d’application calcule et agrège les données modifiées à partir des sources de données mentionnées ci-dessus. Il fournit un ensemble d’analytiques permettant aux utilisateurs de parcourir facilement toutes les modifications apportées aux ressources, et d’identifier quelle modification est pertinente dans le contexte de dépannage ou de supervision.
Le fournisseur de ressources « Microsoft.ChangeAnalysis » doit être inscrit avec un abonnement pour que les données sur les modifications des propriétés suivies et des paramètres de proxy Azure Resource Manager soient disponibles. Quand vous entrez dans l’outil Diagnostiquer et résoudre les problèmes de l’application web ou que vous affichez l’onglet autonome Analyse des changements, ce fournisseur de ressources est automatiquement inscrit.
Pour les modifications d’une application web dans l’invité, une activation distincte est requise pour l’analyse des fichiers de code au sein de l’application web. Pour plus d’informations, consultez la section [Analyse des changements dans l’outil Diagnostiquer et résoudre les problèmes](change-analysis-visualizations.md#application-change-analysis-in-the-diagnose-and-solve-problems-tool) plus loin dans cet article.

## <a name="cost"></a>Coût
Le service Analyse des changements d'application est gratuit. Il n’entraîne aucun coût de facturation pour les abonnements sur lesquels il est activé. Il n’a pas non plus d’incidence sur les performances d’analyse des modifications de propriétés de ressources Azure. L’activation de l’analyse des changements dans l’invité d’applications web (ou de l’outil de diagnostic et résolution des problèmes) n’entraîne qu’un impact négligeable sur les performances de l’application web et aucun coût de facturation.


## <a name="enable-change-analysis-at-scale-for-web-app-in-guest-file-and-environment-variable-changes"></a>Activer l’Analyse des changements à grande échelle pour les modifications de fichier invité d’application web et de variable d’environnement

Si votre abonnement inclut de nombreuses applications web, l’activation du service pour chaque application web serait inefficace. Exécutez le script suivant pour activer toutes les applications web dans votre abonnement.

Conditions préalables :

- Module PowerShell Az. Suivez les instructions dans la rubrique[Installer le module Azure PowerShell](/powershell/azure/install-az-ps)

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

- Découvrez les [visualisations dans l’Analyse des changements](change-analysis-visualizations.md).
- Découvrez comment [résoudre les problèmes liés à l’Analyse des changements](change-analysis-troubleshoot.md).
- Activez application Insights pour les [applications Azure App Services](azure-web-apps.md).
- Activez Application Insights pour les [applications hébergées par IIS sur une machine virtuelle Azure et un groupe de machines virtuelles identiques Azure](azure-vm-vmss-apps.md).
