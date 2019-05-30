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
ms.openlocfilehash: 5bd3816e65398283de85b4551a137b3f97db4cc7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226338"
---
# <a name="application-change-analysis-public-preview"></a>Analyse de modification d’application (version préliminaire publique)

Lorsqu’un problème / l’arrêt du site en direct se produit, il est essentiel de déterminer de cause rapidement. Norme de solutions de surveillance peut-être vous aider à identifier rapidement qu’il existe un problème, et souvent même le composant en cause. Mais cela ne sont pas toujours entraîner une explication immédiate pour la raison pour laquelle l’échec se produit. Votre site a travaillé à cinq minutes auparavant, maintenant il a été endommagé. Ce qui a changé au cours des cinq dernières minutes ? Il s’agit de la question de la nouvelle fonctionnalité Azure Monitor analysis de modification d’application est conçue pour répondre. En s’appuyant sur la puissance de la [graphique des ressources Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview) analysis de modification d’application fournit un aperçu de vos modifications de l’application Windows Azure pour augmenter la capacité d’observation et réduire MTTR (temps moyen de réparation).

> [!IMPORTANT]
> Analyse des modifications Azure Monitor application est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview-of-change-analysis-service"></a>Vue d’ensemble du service d’analyse de modification
Le service d’analyse de modification détecte les différents types de modifications à partir de la couche d’infrastructure jusqu’au déploiement d’applications. Il est un fournisseur de ressources Azure au niveau d’abonnement qui examine les modifications des ressources dans l’abonnement et fournit des données pour les différents outils de diagnostic aider les utilisateurs à comprendre les modifications susceptibles d’avoir entraîné des problèmes.

Le diagramme suivant illustre l’architecture du service d’analyse de modification : ![Diagramme d’architecture pour l’obtention du service d’analyse des modifications modifier des données et fournir des données à des outils clients](./media/change-analysis/overview.png)

Actuellement, l’outil est intégré dans les Services d’application web app diagnostiquer et résoudre les problèmes expérience. Consultez *service d’analyse des modifications pour une application Web application Services* section sur la façon d’activer et afficher les modifications apportées à une application web.

### <a name="azure-resource-manager-deployment-changes"></a>Modifications de déploiement Azure Resource Manager
En tirant parti [graphique des ressources Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview) l’outil d’analyse de modification fournit un enregistrement d’historique de la façon dont les ressources Azure qui hébergent votre application ont été modifiés au fil du temps. Par exemple, si une application web a une balise ajoutée, la modification sera répercutée dans l’outil d’analyse de modification.
Ces informations sont toujours disponibles tant que le `Microsoft.ChangeAnalysis` fournisseur de ressources est intégrée à l’abonnement Azure.

### <a name="web-application-deployment-and-configuration-changes"></a>Modifications de configuration et de déploiement Application Web
Outil d’analyse de modification capture l’état de déploiement et la configuration d’une application toutes les 4 heures pour calculer les différences et présenter ce qui a changé. Modifications de variable d’environnement application, modifications de règle de configuration IP, les modifications de Managed Service Identity, modification des paramètres SSL et ainsi de suite constituent des exemples de telles modifications.
Contrairement aux modifications du Gestionnaire de ressources, ce type d’information de modification n’est peut-être pas disponible immédiatement dans l’outil. Pour afficher les dernières modifications, utilisez le bouton « Analyse modifie maintenant » dans l’outil.

![Capture d’écran de rechercher les modifications maintenant bouton de diagnostiquer et résoudre l’outil de problèmes avec l’intégration d’analyse de modification pour l’application web app service](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Dépendance a été modifiée
Ressources de dépendances peut également être la cause des problèmes. Par exemple, si une application web appelle un cache Redis, les performances d’application web peuvent être affectée par le cache Redis référence (SKU). En recherchant dans l’enregistrement DNS d’application web modifier analysis service présente également les dépendances des informations de modification pour identifier les modifications dans tous les composants d’une application qui a pu causer des problèmes.


## <a name="change-analysis-service-for-app-services-web-app"></a>Modifier le service d’analyse pour l’application Web App Services

Analyse des modifications Azure Monitor application actuellement intègre le libre-service **diagnostiquer et résoudre les problèmes** expérience, qui est accessible à partir de la **vue d’ensemble** section de votre Azure App Service application :

![Vue d’ensemble de la capture d’écran d’Azure App Service page avec zones rouges autour de bouton de la vue d’ensemble et diagnostiquer et résoudre les bouton de problèmes](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-diagnose-and-solve-problems-tool"></a>Activer l’analyse de modification de diagnostiquer et résoudre l’outil de problèmes

1. Sélectionnez **disponibilité et performances**

    ![capture d’écran de la disponibilité et les options de résolution des problèmes de performances](./media/change-analysis/availability-and-performance.png)

2. Cliquez sur le **Application subit un incident** vignette.

   ![Capture d’écran avec l’application bloque en mosaïque](./media/change-analysis/application-crashes-tile.png)

3. Pour activer **modification Analysis** sélectionnez **activer maintenant**.

   ![capture d’écran de la disponibilité et les options de résolution des problèmes de performances](./media/change-analysis/application-crashes.png)

4. Pour tirer parti de la version complète modifier ensemble de fonctionnalités d’analyse **Analysis modifier**, **rechercher les modifications de code**, et **Always on** à **sur** Sélectionnez **enregistrer**.

    ![Capture d’écran de l’interface utilisateur analyse de modification d’enable Azure App Service](./media/change-analysis/change-analysis-on.png)

    Si **modification Analysis** est activé, vous serez en mesure de détecter des modifications au niveau des ressources. Si **rechercher les modifications de code** est activé, vous allez également voir les fichiers de déploiement et les modifications de configuration du site. L’activation de **Always on** permettra d’optimiser la modification de l’analyse des performances, mais peut entraîner des frais supplémentaires à partir d’une perspective de facturation.

5.  Une fois que tout est activé, en sélectionnant **diagnostiquer et résoudre les problèmes** > **disponibilité et performances** > **Application subit un incident** vous permettra pour accéder à l’expérience d’analyse de modification. Le graphique résume le type de modifications qui se sont produits au fil du temps, ainsi que des détails sur ces modifications :

     ![Capture d’écran de modifier l’affichage diff](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-service-at-scale"></a>Activer le service d’analyse des modifications à grande échelle
Si vous avez un grand nombre d’applications web dans votre abonnement, activer le service au niveau d’application web par sera inefficace. Voici des instructions d’intégration de remplacement.

#### <a name="registering-change-analysis-resource-provider-for-your-subscription"></a>L’inscription du fournisseur de ressources d’analyse des modifications de votre abonnement

1. Inscrire l’indicateur de fonctionnalité d’aperçu analyse des modifications

    Étant donné que cette fonctionnalité est disponible en version préliminaire, vous devez tout d’abord inscrire l’indicateur de fonctionnalité pour qu’elle soit visible pour votre abonnement.
    - Ouvrez [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/).

    ![Capture d’écran de modification Azure Cloud Shell](./media/change-analysis/cloud-shell.png)

    - Modifiez le type de l’interpréteur de commandes PowerShell :

    ![Capture d’écran de modification Azure Cloud Shell](./media/change-analysis/choose-powershell.png)

    - Exécutez la commande PowerShell suivante :

    ``` PowerShell

    Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to onboard
    Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
    Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag

    ```

2. Inscrire le fournisseur de ressources d’analyse de modification pour l’abonnement

    - Accédez aux abonnements, sélectionnez l’abonnement que vous souhaitez intégrer le service de modification, puis cliquez sur les fournisseurs de ressources :

        ![Capture d’écran pour l’inscription de la partie de confiance modification analyse à partir de panneau abonnements](./media/change-analysis/register-rp.png)

    - Sélectionnez *Microsoft.ChangeAnalysis* et cliquez sur *inscrire* en haut de la page.

    - Une fois que le fournisseur de ressources est intégré, suivez les instructions de *Impossible de récupérer les informations d’analyse modifier* ci-dessous pour définir la balise masqué sur l’application web pour activer le déploiement au niveau de détection des modifications sur l’application web.

3. À l’étape 2 ci-dessus, vous pouvez également enregistrer pour le fournisseur de ressources par le biais de script PowerShell :

    ```PowerShell
    Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
    ```

4. Pour définir une balise masquée sur une application web à l’aide de PowerShell, exécutez la commande suivante :

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
