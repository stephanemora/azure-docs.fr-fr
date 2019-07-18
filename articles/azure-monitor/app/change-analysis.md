---
title: Utiliser l’analyse des changements applicatifs dans Azure Monitor pour rechercher les problèmes liés aux applications web | Microsoft Docs
description: Utilisez l’analyse des changements applicatifs dans Azure Monitor pour résoudre les problèmes liés aux applications sur des sites actifs sur Azure App Service.
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 45df8f9e57223ea60a11c6af2187d362184cae2b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443376"
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

Si votre abonnement inclut de nombreuses applications web, l’activation du service pour chaque application web serait inefficace. Dans ce cas, suivez ces instructions alternatives.

### <a name="register-the-change-analysis-resource-provider-for-your-subscription"></a>Inscrire le fournisseur de ressources d’analyse des changements pour votre abonnement

1. Inscrire l’indicateur de fonctionnalité d’analyse des changements (préversion). Étant donné que l’indicateur de fonctionnalité est disponible en préversion, vous devez l’inscrire pour le rendre visible dans votre abonnement :

   1. Ouvrez [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/).

      ![Capture d’écran du Cloud Shell changé](./media/change-analysis/cloud-shell.png)

   1. Modifiez le type de shell sur **PowerShell**.

      ![Capture d’écran du Cloud Shell changé](./media/change-analysis/choose-powershell.png)

   1. Exécutez la commande PowerShell suivante :

        ``` PowerShell
        Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to enable
        Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
        Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag
        ```

1. Inscrivez le fournisseur de ressources d’analyse des changements pour l’abonnement.

   - Accédez à **Abonnements**, puis sélectionnez l’abonnement que vous souhaitez activer dans le service de changements. Puis sélectionnez les fournisseurs de ressources :

        ![Capture d’écran montrant comment inscrire le fournisseur de ressources d’analyse des changements](./media/change-analysis/register-rp.png)

       - Sélectionnez **Microsoft.ChangeAnalysis**. Puis, en haut de la page, sélectionnez **S’inscrire**.

       - Une fois que le fournisseur de ressources est activé, vous pouvez définir une balise cachée sur l’application web pour détecter les modifications au niveau du déploiement. Pour définir une balise cachée, suivez les instructions sous **Impossible de récupérer les informations d’analyse des changements**.

   - Vous pouvez également utiliser un script PowerShell pour inscrire le fournisseur de ressources :

        ```PowerShell
        Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
        ```

        Pour utiliser PowerShell pour définir une balise cachée sur une application web, exécutez la commande suivante :

        ```powershell
        $webapp=Get-AzWebApp -Name <name_of_your_webapp>
        $tags = $webapp.Tags
        $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
        Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
        ```

     > [!NOTE]
     > Après avoir ajouté la balise cachée, vous devrez peut-être encore attendre jusqu'à 4 heures avant de commencer à voir les modifications. Les résultats sont différés, car l’analyse des modifications analyse votre application web uniquement toutes les 4 heures. La planification de 4 heures limite l’impact sur les performances de l’analyse.

## <a name="next-steps"></a>Étapes suivantes

- Surveillez App Service plus efficacement en [activant les fonctionnalités d’Application Insights](azure-web-apps.md) dans Azure Monitor.
- Apprenez-en davantage sur [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), qui vous aide à exécuter l’analyse des changements.
