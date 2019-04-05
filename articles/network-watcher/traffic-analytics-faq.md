---
title: Forum aux questions pour l’analyse de trafic Azure | Microsoft Docs
description: Découvrez les réponses aux questions les plus fréquemment posées sur l’analyse de trafic.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: jdial
ms.openlocfilehash: 65948b1de3a972687e738b011acf3542073db277
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59046977"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Forum aux questions pour Traffic Analytics

Cet article regroupe les questions fréquemment posées sur l’analytique du trafic dans Azure Network Watcher.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Quels sont les prérequis pour utiliser l’analytique du trafic ?

Traffic Analytics nécessite les prérequis suivants :

- Un abonnement actif à Network Watcher.
- Des journaux de flux NSG (groupe de sécurité réseau) activés pour les groupes de sécurité réseau à surveiller.
- Un compte de stockage Azure pour stocker les journaux de flux bruts.
- Un espace de travail Azure Log Analytics, avec accès en lecture et écriture.

Votre compte doit répondre à l’une des descriptions suivantes pour activer l’analytique du trafic :

- Votre compte doit avoir l’un des rôles RBAC (contrôle d’accès en fonction du rôle) suivants au niveau de l’étendue de l’abonnement : propriétaire, contributeur, lecteur ou contributeur réseau.
- Si votre compte n’a pas l’un des rôles précédemment répertoriés, il doit avoir un rôle personnalisé auquel sont affectées les actions suivantes au niveau de l’abonnement.
            
    - Microsoft.Network/applicationGateways/read
    - Microsoft.Network/connections/read
    - Microsoft.Network/loadBalancers/read 
    - Microsoft.Network/localNetworkGateways/read 
    - Microsoft.Network/networkInterfaces/read 
    - Microsoft.Network/networkSecurityGroups/read 
    - Microsoft.Network/publicIPAddresses/read
    - Microsoft.Network/routeTables/read
    - Microsoft.Network/virtualNetworkGateways/read 
    - Microsoft.Network/virtualNetworks/read
        
Pour vérifier les rôles attribués à un utilisateur pour un abonnement :

1. Connectez-vous à Azure à l’aide de **AzAccount de connexion**. 

2. Sélectionnez l’abonnement nécessaire à l’aide de **sélectionnez-AzSubscription**. 

3. Pour répertorier tous les rôles qui sont attribués à un utilisateur spécifié, utilisez **AzRoleAssignment de Get - SignInName [e-mail de l’utilisateur] - IncludeClassicAdministrators**. 

Si vous ne voyez aucune sortie, contactez l’administrateur de l’abonnement pour qu’il vous donne les droits d’accès nécessaires pour exécuter les commandes. Pour plus d’informations, consultez [Gérer le contrôle d’accès en fonction du rôle avec Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).


## <a name="in-which-azure-regions-is-traffic-analytics-available"></a>Dans quelles régions Traffic Analytics est disponible ?

Vous pouvez utiliser l’analytique du trafic pour les régions prises en charge suivantes :
- Centre du Canada
- USA Centre-Ouest
- USA Est
- USA Est 2
- USA Centre Nord
- USA Centre Sud
- USA Centre
- USA Ouest
- USA Ouest 2
- France Centre
- Europe Ouest
- Europe Nord
- Brésil Sud
- Ouest du Royaume-Uni
- Sud du Royaume-Uni
- Australie Est
- Australie Sud-Est 
- Asie Est
- Asie Sud-Est
- Centre de la Corée
- Inde Centre
- Inde Sud
- Japon Est
- Japon Ouest
- Gouvernement américain - Virginie

L’espace de travail Log Analytics doit exister dans les régions suivantes :
- Centre du Canada
- USA Centre-Ouest
- USA Ouest 2
- USA Est
- France Centre
- Europe Ouest
- Sud du Royaume-Uni
- Australie Sud-Est
- Asie Sud-Est 
- Centre de la Corée
- Inde Centre
- Japon Est
- Gouvernement américain - Virginie

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Puis-je activer des journaux de flux pour des NSG qui se trouvent dans des régions différentes de mon espace de travail ?

Oui, ces NSG peuvent être dans des régions différentes de celles de votre espace de travail Log Analytics.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Est-il possible de configurer plusieurs groupes de sécurité réseau dans un seul espace de travail ?

Oui.

## <a name="can-i-use-an-existing-workspace"></a>Puis-je utiliser un espace de travail existant ?

Oui. Si vous sélectionnez un espace de travail existant, vérifiez qu’il a été migré vers le nouveau langage de requête. Si vous ne souhaitez pas mettre à niveau l’espace de travail, vous devez en créer un autre. Pour plus d’informations sur le nouveau langage de requête, consultez [Azure Monitor enregistre la mise à niveau vers la nouvelle recherche de journal](../log-analytics/log-analytics-log-search-upgrade.md).

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>Mon compte Stockage Azure peut-il être dans un abonnement spécifique et mon espace de travail Log Analytics dans un autre abonnement ?

Oui, votre compte Stockage Azure peut être dans un abonnement spécifique et votre espace de travail Log Analytics dans un autre abonnement.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Puis-je stocker les journaux bruts dans un autre abonnement ?

Non. Vous pouvez stocker des journaux bruts dans n’importe quel compte de stockage dans lequel un NSG est activé pour les journaux de flux. Toutefois, le compte de stockage et les journaux bruts doivent se trouver dans le même abonnement et la même région.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Que faire si je ne peux pas configurer un NSG pour l’analytique du trafic en raison d’une erreur « Introuvable » ?

Sélectionnez une région prise en charge. Si vous sélectionnez une région non prise en charge, vous recevez une erreur « Introuvable ». Les régions prises en charge sont répertoriées plus haut dans cet article.

## <a name="why-am-i-getting-the-error-failed-to-update-flow-logs-settings-for--internalservererror-when-enabling-nsgs-in-us-gov-virginia"></a>Pourquoi l’erreur « Impossible de mettre à jour les paramètres des journaux de flux pour... InternalServerError... » Lorsque vous activez les groupes de sécurité réseau dans Virginie ?

Il s’agit d’un bogue où le fournisseur de ressources 'Microsoft.Network' n’est pas nouveau inscrit pour un abonnement en Virginie. L’équipe travaille sur la solution à ce problème. Pour résoudre ce problème, vous devez [réinscrire manuellement 'Microsoft.Network' RP](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-register-provider-errors). 

Contactez le support technique si le problème persiste. 

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Que faire si j’obtiens l’état « Échec de chargement » sous la page de journaux de flux NSG ?

Pour que la journalisation du flux fonctionne correctement, le fournisseur Microsoft.Insights doit être inscrit. Si vous ne savez pas si le fournisseur Microsoft.Insights est inscrit ou non pour votre abonnement, remplacez *xxxxx-xxxxx-xxxxxx-xxxx* dans la commande suivante, puis exécutez les commandes ci-dessous à partir de PowerShell :

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>J’ai configuré la solution. Pourquoi ne vois-je rien sur le tableau de bord ?

L’affichage du tableau de bord peut prendre jusqu’à 30 minutes la première fois. La solution doit d’abord agréger suffisamment de données pour en dériver des insights utiles. Ensuite, elle génère des rapports. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Que faire si je reçois le message « Impossible de trouver des données dans cet espace de travail pour l’intervalle de temps sélectionné. Essayez de changer l’intervalle de temps ou de sélectionner un autre espace de travail. » ?

Tentez les opérations suivantes :
- Changez l’intervalle de temps dans la barre supérieure.
- Sélectionnez un autre espace de travail Log Analytics dans la barre supérieure.
- Essayez d’accéder à l’analytique du trafic après 30 minutes, si elle a été récemment activée.
    
Si les problèmes persistent, expliquez votre problème dans le [forum des utilisateurs](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Que faire si je reçois le message « Nous analysons peut-être vos journaux de flux NSG pour la première fois. Ce processus peut prendre de 20 à 30 minutes. Vérifiez ultérieurement. 2) Si l’étape ci-dessus ne fonctionne pas et que votre espace de travail se trouve sous la référence SKU libre, vérifiez l’utilisation de votre espace de travail pour valider le quota. Sinon, reportez-vous à la FAQ pour plus d’informations. » ?

Ce message peut s’afficher pour les raisons suivantes :
- Traffic Analytics a été récemment activé et peut ne pas avoir agrégé suffisamment de données pour qu’en ressortent des insights significatifs.
- Vous utilisez la version gratuite de l’espace de travail Log Analytics, et les limites de quota ont été dépassées. Vous devrez peut-être utiliser un espace de travail d’une capacité supérieure.
    
Si les problèmes persistent, expliquez votre problème dans le [forum des utilisateurs](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Que faire si je reçois le message « Il semble qu’il existe des données de ressources (topologie), mais aucune information de flux. Cliquez ici pour afficher les données de ressources et reportez-vous à la FAQ pour plus d’informations. » ?

Vous voyez les informations de ressources sur le tableau de bord. Toutefois, aucune statistique de flux n’est présente. Les données sont peut-être manquantes en l’absence de flux de communication entre les ressources. Attendez 60 minutes et revérifiez l’état. Si le problème persiste et que vous êtes sûr que des flux de communication existent entre les ressources, expliquez votre problème dans le [forum des utilisateurs](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Puis-je configurer l’analytique du trafic à l’aide de PowerShell ou d’un modèle/client Azure Resource Manager ?

Vous pouvez configurer l’analytique du trafic à l’aide de Windows PowerShell (versions 6.2.1 et ultérieures). Pour configurer la journalisation des flux et analytique du trafic pour un groupe de sécurité réseau spécifique à l’aide de l’applet de commande Set, consultez [Set-AzNetworkWatcherConfigFlowLog](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkwatcherconfigflowlog). Pour obtenir la journalisation des flux et l’état analytique du trafic pour un groupe de sécurité réseau spécifique, consultez [Get-AzNetworkWatcherFlowLogStatus](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkwatcherflowlogstatus).

Vous ne pouvez pas actuellement utiliser un modèle Azure Resource Manager pour configurer l’analytique du trafic.

Pour configurer l’analytique du trafic à l’aide d’un client Azure Resource Manager, consultez les exemples suivants.

**Exemple d’applet de commande Set :**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<name of NSG>"
$TAstorageId = "/subscriptions/<storage subscription id>/resourcegroups/<storage resource group name> /providers/microsoft.storage/storageaccounts/<storage account name>"
$networkWatcherResourceGroupName = "<network watcher resource group name>"
$networkWatcherName = "<network watcher name>"

$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}',
    'properties': 
    {
        'storageId': '${TAstorageId}',
        'enabled': '<true to enable flow log or false to disable flow log>',
        'retentionPolicy' : 
        {
            days: <enter number of days like to retail flow logs in storage account>,
            enabled: <true to enable retention or false to disable retention>
        }
    },
    'flowAnalyticsConfiguration':
    {
                'networkWatcherFlowAnalyticsConfiguration':
      {
        'enabled':,<true to enable traffic analytics or false to disable traffic analytics>
        'workspaceId':'bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb',
        'workspaceRegion':'<workspace region>',
        'workspaceResourceId':'/subscriptions/<workspace subscription id>/resourcegroups/<workspace resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
        
      }

    }
}
"@
$apiversion = "2016-09-01"

armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/configureFlowlog?api-version=${apiversion}" $requestBody
```
**Obtenir un exemple d’applet de commande :**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<NSG name>"


$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}'
}
“@


armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/queryFlowLogStatus?api-version=${apiversion}" $requestBody
```



## <a name="how-is-traffic-analytics-priced"></a>Quel est le prix de Traffic Analytics ?

Traffic Analytics est mesuré. Les mesures sont basées sur le traitement des données de journal de flux par le service et sur le stockage des journaux améliorés résultants dans un espace de travail Log Analytics. 

Par exemple, conformément au [plan tarifaire](https://azure.microsoft.com/pricing/details/network-watcher/), concernant la région USA Centre-Ouest, si des données de journal de flux stockées dans un compte de stockage traité par Traffic Analytics ont un volume de 10 Go et que les journaux améliorés ingérés dans l’espace de travail Log Analytics ont un volume de 1 Go, les charges applicables sont les suivantes : 10 x 2.3$ + 1 x 2.76$ = 25.76$

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Comment faire pour naviguer dans la vue de la carte géographique à l’aide du clavier ?

La page de la carte géographique contient deux sections principales :
    
- **Bannière** : la bannière en haut de la carte géographique fournit des boutons pour sélectionner les filtres de distribution du trafic (Déploiement, Trafic en provenance de pays, Malveillant, etc.). Quand vous sélectionnez un bouton, le filtre correspondant est appliqué sur la carte. Par exemple, si vous sélectionnez le bouton Actif, la carte met en surbrillance les centres de données actifs dans votre déploiement.
- **Carte** : sous la bannière, la section de la carte montre la distribution du trafic entre les pays et les centres de données Azure.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navigation au clavier sur la bannière
    
- La sélection par défaut dans la bannière de la page de la carte géographique est le filtre « Contrôleurs de domaine Azure ».
- Pour passer à un autre filtre, utilisez la touche `Tab` ou `Right arrow`. Pour vous déplacer vers l’arrière, utilisez la touche `Shift+Tab` ou `Left arrow`. La navigation vers l’avant va de gauche à droite, puis de haut en bas.
- Appuyez sur la touche de direction `Enter` ou `Down` pour appliquer le filtre sélectionné. Selon la sélection de filtre et le déploiement, un ou plusieurs nœuds sous la section de la carte sont mis en surbrillance.
- Pour basculer entre la bannière et la carte, appuyez sur `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-map"></a>Navigation au clavier sur la carte
    
- Une fois que vous avez sélectionné un filtre sur la bannière et appuyé sur `Ctrl+F6`, le focus passe à l’un des nœuds en surbrillance (**Centre de données Azure** ou **Pays/Région**) dans la vue cartographique.
- Pour passer à d’autres nœuds en surbrillance dans la carte, utilisez la touche `Tab` ou `Right arrow` pour vous déplacer vers l’avant. Utilisez la touche `Shift+Tab` ou `Left arrow` pour vous déplacer vers l’arrière.
- Pour sélectionner n’importe quel nœud en surbrillance sur la carte, utilisez la touche `Enter` ou `Down arrow`.
- Lorsque vous sélectionnez ces nœuds, le focus se déplace vers la **boîte à outils Informations** du nœud. Par défaut, le focus se déplace sur le bouton Fermer de la **boîte à outils Informations**. Pour vous déplacer davantage dans la vue de la **boîte**, utilisez les touches `Right arrow` et `Left arrow` pour vous déplacer vers l’avant et vers l’arrière, respectivement. Appuyer sur `Enter` a le même effet que cliquer sur le bouton actif dans la **boîte à outils Informations**.
- Quand vous appuyez sur `Tab` et que le focus est sur la **boîte à outils Informations**, le focus passe aux points de terminaison du même continent que le nœud sélectionné. Utilisez les touches `Right arrow` et `Left arrow` pour parcourir ces points de terminaison.
- Pour passer à d’autres clusters de continents/points de terminaison de flux, utilisez `Tab` pour vous déplacer vers l’avant et `Shift+Tab` pour vous déplacer vers l’arrière.
- Quand le focus est sur des **clusters de continents**, utilisez la touche de direction `Enter` ou `Down` pour mettre en surbrillance les points de terminaison dans le cluster de continents. Pour parcourir les points de terminaison et accéder au bouton Fermer de la boîte d’informations du cluster de continents, utilisez les touches `Right arrow` et `Left arrow` pour vous déplacer vers l’avant et vers l’arrière, respectivement. Sur n’importe quel point de terminaison, vous pouvez utiliser `Shift+L` pour basculer vers la ligne de connexion à partir du nœud sélectionné pour le point de terminaison. Vous pouvez réappuyer sur `Shift+L` pour passer au point de terminaison sélectionné.
        
### <a name="keyboard-navigation-at-any-stage"></a>Navigation au clavier à tout moment
    
- `Esc` réduit la sélection développée.
- La touche `Up arrow` effectue la même action que `Esc`. La touche `Down arrow` effectue la même action que `Enter`.
- Utilisez `Shift+Plus` pour effectuer un zoom avant et `Shift+Minus` pour effectuer un zoom arrière.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Comment faire pour naviguer dans la vue de la topologie des réseaux virtuels à l’aide du clavier ?

La page de la topologie des réseaux virtuels contient deux sections principales :
    
- **Bannière** : la bannière en haut de la topologie des réseaux virtuels fournit des boutons pour sélectionner les filtres de distribution du trafic (réseaux virtuels connectés, réseaux virtuels déconnectés, adresses IP publiques, etc.). Quand vous sélectionnez un bouton, le filtre correspondant est appliqué sur la topologie. Par exemple, si vous sélectionnez le bouton Actif, la topologie met en surbrillance les réseaux virtuels actifs dans votre déploiement.
- **Topologie** : sous la bannière, la section de la topologie montre la distribution du trafic entre les réseaux virtuels.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navigation au clavier sur la bannière
    
- La sélection par défaut dans la bannière de la page de la topologie des réseaux virtuels est le filtre « Réseaux virtuels connectés ».
- Pour passer à un autre filtre, utilisez la touche `Tab` pour vous déplacer vers l’avant. Pour vous déplacer vers l’arrière, utilisez la touche `Shift+Tab`. La navigation vers l’avant va de gauche à droite, puis de haut en bas.
- Appuyez sur `Enter` pour appliquer le filtre sélectionné. Selon la sélection de filtre et le déploiement, un ou plusieurs nœuds (réseau virtuel) sous la section de la topologie sont mis en surbrillance.
- Pour basculer entre la bannière et la topologie, appuyez sur `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Navigation au clavier sur la topologie
    
- Une fois que vous avez sélectionné un filtre sur la bannière et appuyé sur `Ctrl+F6`, le focus passe à l’un des nœuds en surbrillance (**VNet**) dans la vue de la topologie.
- Pour passer à d’autres nœuds en surbrillance dans la vue de la topologie, utilisez la touche `Shift+Right arrow` pour vous déplacer vers l’avant. 
- Sur les nœuds en surbrillance, le focus passe à la **boîte à outils Informations** du nœud. Par défaut, le focus passe au bouton **Plus de détails** de la **boîte à outils Informations**. Pour vous déplacer davantage dans la vue de la **boîte**, utilisez les touches `Right arrow` et `Left arrow` pour vous déplacer vers l’avant et vers l’arrière, respectivement. Appuyer sur `Enter` a le même effet que cliquer sur le bouton actif dans la **boîte à outils Informations**.
- Si vous sélectionnez l’un de ces nœuds, appuyez sur la touche `Shift+Left arrow` pour visiter chacune de ses connexions. Le focus passe à la **boîte à outils Informations** de cette connexion. Vous pouvez à tout moment réappuyer sur `Shift+Right arrow` pour faire revenir le focus sur le nœud.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Comment faire pour naviguer dans la vue de la topologie des sous-réseaux à l’aide du clavier ?

La page de la topologie des sous-réseaux virtuels contient deux sections principales :
    
- **Bannière** : la bannière en haut de la topologie des sous-réseaux virtuels fournit des boutons pour sélectionner les filtres de distribution du trafic (sous-réseaux actifs, moyens, de passerelle, etc.). Quand vous sélectionnez un bouton, le filtre correspondant est appliqué sur la topologie. Par exemple, si vous sélectionnez le bouton Actif, la topologie met en surbrillance le sous-réseau virtuel actif dans votre déploiement.
- **Topologie** : sous la bannière, la section de la topologie montre la distribution du trafic entre les sous-réseaux virtuels.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navigation au clavier sur la bannière
    
- La sélection par défaut dans la bannière de la page de la topologie des sous-réseaux virtuels est le filtre « Sous-réseaux ».
- Pour passer à un autre filtre, utilisez la touche `Tab` pour vous déplacer vers l’avant. Pour vous déplacer vers l’arrière, utilisez la touche `Shift+Tab`. La navigation vers l’avant va de gauche à droite, puis de haut en bas.
- Appuyez sur `Enter` pour appliquer le filtre sélectionné. Selon la sélection de filtre et le déploiement, un ou plusieurs nœuds (sous-réseau) sous la section de la topologie sont mis en surbrillance.
- Pour basculer entre la bannière et la topologie, appuyez sur `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Navigation au clavier sur la topologie
    
- Une fois que vous avez sélectionné un filtre sur la bannière et appuyé sur `Ctrl+F6`, le focus passe à l’un des nœuds en surbrillance (**Sous-réseau**) dans la vue de la topologie.
- Pour passer à d’autres nœuds en surbrillance dans la vue de la topologie, utilisez la touche `Shift+Right arrow` pour vous déplacer vers l’avant. 
- Sur les nœuds en surbrillance, le focus passe à la **boîte à outils Informations** du nœud. Par défaut, le focus passe au bouton **Plus de détails** de la **boîte à outils Informations**. Pour vous déplacer davantage dans la vue de la **boîte**, utilisez les touches `Right arrow` et `Left arrow` pour vous déplacer vers l’avant et vers l’arrière, respectivement. Appuyer sur `Enter` a le même effet que cliquer sur le bouton actif dans la **boîte à outils Informations**.
- Si vous sélectionnez l’un de ces nœuds, vous pouvez appuyer sur la touche `Shift+Left arrow` pour visiter chacune de ses connexions. Le focus passe à la **boîte à outils Informations** de cette connexion. Vous pouvez à tout moment réappuyer sur `Shift+Right arrow` pour faire revenir le focus sur le nœud.    

