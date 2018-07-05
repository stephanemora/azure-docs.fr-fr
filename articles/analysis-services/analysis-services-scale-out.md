---
title: Montée en charge d’Azure Analysis Services | Microsoft Docs
description: Répliquer les serveurs Azure Analysis Services avec montée en charge
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 4cb7b165311f57fadd63770646907ddfc0378844
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445018"
---
# <a name="azure-analysis-services-scale-out"></a>Montée en charge d’Azure Analysis Services

Avec la montée en charge, les requêtes des clients peuvent être distribuées entre plusieurs *réplicas de requête* dans un pool de requêtes, ce qui diminue les temps de réponse quand les charges de travail de requêtes sont importantes. Vous pouvez aussi séparer le traitement du pool de requêtes, ce qui garantit que les requêtes des clients ne sont pas affectées par les opérations de traitement. La montée en charge peut être configurée dans le portail Azure ou avec l’API REST d’Analysis Services.

## <a name="how-it-works"></a>Fonctionnement

Dans un déploiement de serveur classique, un même serveur est utilisé comme serveur de traitement et comme serveur de requêtes. Si le nombre de requêtes de clients sur des modèles présents sur votre serveur dépasse les unités de traitement des requêtes du plan de votre serveur, ou que le traitement du modèle est effectué en même temps que des charges de travail de requêtes importantes, les performances peuvent diminuer. 

Avec la montée en charge, vous pouvez créer un pool de requêtes comportant jusqu’à sept réplicas de requête supplémentaires (pour un total de huit, en y incluant votre serveur). Vous pouvez faire évoluer le nombre de réplicas de requête pour répondre aux demandes en unités de traitement des requêtes aux moments critiques et séparer à tout moment un serveur de traitement du pool de requêtes. Tous les réplicas de la requête sont créés dans la même région que votre serveur.

Quel que soit le nombre de réplicas de requête dont vous disposez dans un pool de requêtes, le traitement des charges de travail n’est pas distribué entre les réplicas de requête. Un seul serveur est utilisé comme serveur de traitement. Les réplicas de requête servent uniquement les requêtes sur les modèles synchronisés entre chaque réplica dans le pool de requêtes. 

Quand les opérations de traitement sont terminées, une synchronisation doit être effectuée entre le serveur de traitement et les serveurs réplica de requête. Lors de l’automatisation des opérations de traitement, il est important de configurer une opération de synchronisation après la réussite des opérations de transformation. La synchronisation peut être effectuée manuellement dans le portail, ou à l’aide de PowerShell ou de l’API REST.

> [!NOTE]
> La montée en charge est disponible pour les serveurs du niveau tarifaire Standard. Chaque réplica de requête est facturé au même prix que votre serveur.

> [!NOTE]
> La montée en charge n’augmente pas la quantité de mémoire disponible pour votre serveur. Pour augmenter la mémoire, vous devez passer à un forfait supérieur.

## <a name="region-limits"></a>Limites de région

Le nombre de réplicas de requête que vous pouvez configurer est limité par la région de votre serveur. Les limites suivantes s’appliquent :

|Région  |Nombre maximum de réplicas  |
|---------|---------|
|Est des États-Unis 2    |    7     |
|Centre-Ouest des États-Unis     |    7     |
|Europe de l'Ouest     |    7     |
|États-Unis de l’Ouest     |     7    |
|Centre des États-Unis     |     3    |
|Asie du Sud-Est    |     3    |
|Toutes les autres régions  |   1    |



## <a name="monitor-qpu-usage"></a>Surveiller l’utilisation des unités de traitement des requêtes

 Pour déterminer si une montée en charge est nécessaire pour votre serveur, surveillez votre serveur dans le portail Azure en utilisant des métriques. Si vos unités de traitement des requêtes atteignent régulièrement le maximum, cela signifie que le nombre de requêtes sur vos modèles dépasse la limite d’unités de traitement des requêtes pour votre forfait. La métrique Longueur de la file d’attente des travaux du pool de requêtes augmente aussi quand le nombre de requêtes dans la file d’attente du pool de threads de requêtes dépasse les unités de traitement des requêtes disponibles. Pour plus d’informations, consultez [Surveiller les métriques du serveur](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Configurer la montée en charge

### <a name="in-azure-portal"></a>Dans le portail Azure

1. Dans le portail, cliquez sur **Montée en charge**. Utilisez le curseur pour sélectionner le nombre de serveurs réplica de requête. Le nombre de réplicas que vous choisissez s’ajoute à votre serveur existant.

2. Dans **Séparer le serveur de traitement du pool de requêtes**, sélectionnez Oui pour exclure votre serveur de traitement des serveurs de requêtes.

   ![Curseur de montée en charge](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Cliquez sur **Enregistrer** pour provisionner vos nouveaux serveurs réplica de requête. 

Les modèles tabulaires sur votre serveur principal sont synchronisés avec les serveurs réplica. Quand la synchronisation est terminée, le pool de requêtes commence la distribution des requêtes entrantes entre les serveurs réplica. 


## <a name="synchronization"></a>Synchronisation 

Quand vous provisionnez de nouveaux réplicas de requête, Azure Analysis Services réplique automatiquement vos modèles sur tous les réplicas. Vous pouvez également effectuer une synchronisation manuelle à l’aide du portail ou de l’API REST. Quand vous traitez vos modèles, vous devez effectuer une synchronisation pour que les mises à jour soient synchronisées entre vos réplicas de requête.

### <a name="in-azure-portal"></a>Dans le portail Azure

Dans **Vue d’ensemble** > Modèle > **Synchroniser le modèle**.

![Curseur de montée en charge](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>de l’API REST
Utilisez l’opération de **synchronisation**.

#### <a name="synchronize-a-model"></a>Synchroniser un modèle   
`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Obtenir l’état de la synchronisation  
`GET https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

### <a name="powershell"></a>PowerShell
Avant d’utiliser PowerShell, [installez ou mettez à jour le dernier module AzureRM](https://github.com/Azure/azure-powershell/releases). 

Pour définir le nombre de réplicas de la requête, utilisez [Set-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver). Spécifiez le paramètre `-ReadonlyReplicaCount` facultatif.

Pour exécuter la synchronisation, utilisez [Sync-AzureAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/sync-azureanalysisservicesinstance).



## <a name="connections"></a>connexions

Dans la page Vue d’ensemble de votre serveur, vous voyez deux noms de serveur. Si vous n’avez pas encore configuré la montée en charge pour un serveur, les deux noms de serveur fonctionnent de la même façon. Une fois le scale-out configuré pour un serveur, vous devez spécifier le nom du serveur approprié en fonction du type de connexion. 

Pour les connexions de clients d’utilisateur finaux, comme Power BI Desktop, Excel et des applications personnalisées, utilisez **Nom du serveur**. 

Pour SSMS, SSDT et les chaînes de connexion dans PowerShell, les applications de fonction Azure et AMO, utilisez **Nom du serveur d’administration**. Le nom du serveur d’administration inclut un qualificateur `:rw` (lecture-écriture) spécial. Toutes les opérations de traitement se produisent sur le serveur d’administration.

![Noms de serveur](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="related-information"></a>Informations connexes

[Surveiller les métriques du serveur](analysis-services-monitor.md)   
[Gérer Azure Analysis Services](analysis-services-manage.md) 

