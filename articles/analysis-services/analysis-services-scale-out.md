---
title: Montée en charge d’Azure Analysis Services | Microsoft Docs
description: Répliquer les serveurs Azure Analysis Services avec montée en charge
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6a69d8d60b2e588ded9ccca20521195ae11ff136
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449419"
---
# <a name="azure-analysis-services-scale-out"></a>Montée en charge d’Azure Analysis Services

Avec montée en puissance, les requêtes des clients peuvent être distribuées entre plusieurs *réplicas de requête* dans un *interroger pool*, réduction des délais de réponse au cours des charges de travail élevées pour les requêtes. Vous pouvez aussi séparer le traitement du pool de requêtes, ce qui garantit que les requêtes des clients ne sont pas affectées par les opérations de traitement. La montée en charge peut être configurée dans le portail Azure ou avec l’API REST d’Analysis Services.

La montée en charge est disponible pour les serveurs du niveau tarifaire Standard. Chaque réplica de requête est facturé au même prix que votre serveur. Tous les réplicas de la requête sont créés dans la même région que votre serveur. Le nombre de réplicas de requête que vous pouvez configurer est limité par la région de votre serveur. Pour plus d’informations, consultez [Disponibilité par région](analysis-services-overview.md#availability-by-region). La montée en charge n’augmente pas la quantité de mémoire disponible pour votre serveur. Pour augmenter la mémoire, vous devez passer à un forfait supérieur. 

## <a name="why-scale-out"></a>Pourquoi montée en puissance ?

Dans un déploiement de serveur classique, un même serveur est utilisé comme serveur de traitement et comme serveur de requêtes. Si le nombre de requêtes de clients sur des modèles présents sur votre serveur dépasse les unités de traitement des requêtes du plan de votre serveur, ou que le traitement du modèle est effectué en même temps que des charges de travail de requêtes importantes, les performances peuvent diminuer. 

Avec montée en puissance, vous pouvez créer un pool de requêtes avec les ressources de réplica de requête supplémentaire jusqu'à sept (huit total, y compris votre *principal* server). Vous pouvez faire évoluer le nombre de réplicas dans le pool de requêtes pour répondre aux exigences QPU aux moments critiques, et vous pouvez séparer un serveur de traitement du pool de requêtes à tout moment. 

Quel que soit le nombre de réplicas de requête dont vous disposez dans un pool de requêtes, le traitement des charges de travail n’est pas distribué entre les réplicas de requête. Le serveur principal sert le serveur de traitement. Réplicas de requête servent uniquement les requêtes sur les bases de données de modèle de synchronisation entre le serveur principal et chaque réplica dans le pool de requêtes. 

Lors de la montée en charge, il peut prendre jusqu'à cinq minutes pour les nouveaux réplicas de requête à ajouter de façon incrémentielle pour le pool de requêtes. Lorsque tous les nouveaux réplicas de requête sont en hausse et en cours d’exécution, les nouvelles connexions clientes sont à charge équilibrée entre les ressources dans le pool de requêtes. Les connexions client existantes ne sont pas modifiées à partir de la ressource à laquelle ils sont actuellement connectés. Lors d’une mise à l’échelle à la baisse, toutes les connexions client existantes à une ressource de pool de requêtes en cours de suppression du pool de requêtes sont arrêtées. Les clients peuvent se reconnecter à une ressource de pool de requêtes restantes.

## <a name="how-it-works"></a>Fonctionnement

Lorsque vous configurez la montée en puissance la première fois, les bases de données model sur votre serveur principal sont *automatiquement* synchronisé avec les nouveaux réplicas dans un nouveau pool de requêtes. La synchronisation automatique se produit qu’une seule fois. Pendant la synchronisation automatique, les fichiers de données du serveur principal (chiffrées au repos dans le stockage d’objets blob) sont copiés vers un second emplacement, également chiffré au repos dans le stockage d’objets blob. Réplicas dans le pool de requêtes sont ensuite *alimenté* avec des données à partir de la deuxième ensemble de fichiers. 

Pendant une synchronisation automatique est effectuée uniquement quand vous montée en puissance un serveur pour la première fois, vous pouvez également effectuer une synchronisation manuelle. Synchronisation garantit que les données sur les réplicas dans le pool de requêtes correspond à celui du serveur principal. Lors du traitement (actualisation) des modèles sur le serveur principal, une synchronisation doit être effectuée *après* les opérations de traitement sont terminées. Cette synchronisation copie données mises à jour à partir de fichiers du serveur principal dans le stockage d’objets blob dans le deuxième ensemble de fichiers. Réplicas dans le pool de requêtes sont ensuite alimentés avec les données mises à jour à partir de la deuxième ensemble de fichiers dans le stockage blob. 

Lorsque vous effectuez une opération de montée en puissance ultérieure, par exemple, augmentation du nombre de réplicas dans le pool de requêtes de deux à cinq, les nouveaux réplicas sont alimentés avec les données à partir de la deuxième ensemble de fichiers dans le stockage blob. Il n’existe aucune synchronisation. Si vous deviez effectuer une synchronisation une fois que la montée en charge, les nouveaux réplicas dans le pool de requêtes serait alimenté deux fois : une alimentation redondante. Lorsque vous effectuez une opération de montée en puissance ultérieure, il est important de garder à l’esprit :

* Effectuer une synchronisation *avant l’opération de montée en puissance* afin d’éviter d’alimentation redondante des réplicas ajoutés.

* Lors de l’automatisation de ces deux traitement *et* opérations de montée en puissance, il est important de traiter tout d’abord les données sur le serveur principal, puis effectuer une synchronisation et puis effectuer l’opération de montée en puissance. Cette séquence garantit un impact minimal sur les ressources QPU et de la mémoire.

* La synchronisation est autorisée même lorsqu’il n’existe aucun réplica dans le pool de requêtes. Si vous montent en puissance à partir de zéro à un ou plusieurs réplicas avec de nouvelles données à partir d’une opération de traitement sur le serveur principal, effectuez tout d’abord de la synchronisation sans aucun réplica dans le pool de requêtes et puis montée en puissance. Synchronisation avant la montée en puissance évite alimentation redondante des réplicas nouvellement ajoutés.

* Lorsque vous supprimez une base de données model à partir du serveur principal, il ne pas automatiquement supprimé de réplicas dans le pool de requêtes. Vous devez effectuer une opération de synchronisation à l’aide de la [AzAnalysisServicesInstance de synchronisation](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) commande PowerShell qui supprime le fichier/s pour cette base de données d’emplacement de stockage d’objets blob partagé du réplica, puis supprime le modèle base de données sur les réplicas dans le pool de requêtes.

* Lorsque vous renommez une base de données sur le serveur principal, il existe une étape supplémentaire nécessaire pour garantir que la base de données est correctement synchronisé avec tous les réplicas. Après avoir renommé, effectuer une synchronisation à l’aide de la [AzAnalysisServicesInstance de synchronisation](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) commande en spécifiant le `-Database` paramètre avec l’ancien nom de base de données. Cette synchronisation supprime la base de données et les fichiers portant l’ancien nom de tous les réplicas. Puis effectuez une autre synchronisation spécification le `-Database` paramètre avec le nouveau nom de base de données. La synchronisation deuxième copie de la base de données qui vient d’être nommé dans le deuxième ensemble de fichiers et l’alimente tous les réplicas. Ces synchronisations ne peut pas être effectuées à l’aide de la commande de modèle de synchronisation dans le portail.

### <a name="separate-processing-from-query-pool"></a>Traitement distinct à partir du pool de requêtes

Pour optimiser les performances des opérations de traitement et de requête, vous pouvez choisir de séparer votre serveur de traitement du pool de requêtes. Lorsqu’ils sont séparés, les connexions client existantes et nouvelles sont attribuées aux réplicas de requête dans le pool de requêtes uniquement. Si les opérations de traitement ne prennent qu’un court laps de temps, vous pouvez choisir de séparer votre serveur de traitement du pool de requêtes uniquement pendant le temps nécessaire à la réalisation des opérations de traitement et de synchronisation, puis de le réinclure dans le pool de requêtes. 

## <a name="monitor-qpu-usage"></a>Surveiller l’utilisation des unités de traitement des requêtes

Pour déterminer si une montée en charge est nécessaire pour votre serveur, surveillez votre serveur dans le portail Azure en utilisant des métriques. Si vos unités de traitement des requêtes atteignent régulièrement le maximum, cela signifie que le nombre de requêtes sur vos modèles dépasse la limite d’unités de traitement des requêtes pour votre forfait. La métrique Longueur de la file d’attente des travaux du pool de requêtes augmente aussi quand le nombre de requêtes dans la file d’attente du pool de threads de requêtes dépasse les unités de traitement des requêtes disponibles. 

Une autre bonne métrique à surveiller est moyenne QPU par ServerResourceType. Cette mesure compare QPU moyenne pour le serveur principal avec qui le pool de requêtes. 

### <a name="to-configure-qpu-by-serverresourcetype"></a>Pour configurer des QPU par ServerResourceType
1. Dans un graphique en courbes mesures, cliquez sur **ajouter une métrique**. 
2. Dans **ressource**, sélectionnez votre serveur, puis dans **espace de noms métrique**, sélectionnez **métriques standard Analysis Services**, puis dans **métrique**, Sélectionnez **QPU**, puis dans **agrégation**, sélectionnez **Avg**. 
3. Cliquez sur **appliquer fractionnement**. 
4. Dans **valeurs**, sélectionnez **ServerResourceType**.  

Pour plus d’informations, consultez [Surveiller les métriques du serveur](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Configurer la montée en charge

### <a name="in-azure-portal"></a>Dans le portail Azure

1. Dans le portail, cliquez sur **Montée en charge**. Utilisez le curseur pour sélectionner le nombre de serveurs réplica de requête. Le nombre de réplicas que vous choisissez s’ajoute à votre serveur existant.

2. Dans **Séparer le serveur de traitement du pool de requêtes**, sélectionnez Oui pour exclure votre serveur de traitement des serveurs de requêtes. Client [connexions](#connections) à l’aide de la chaîne de connexion par défaut (sans `:rw`) sont redirigés vers des réplicas dans le pool de requêtes. 

   ![Curseur de montée en charge](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Cliquez sur **Enregistrer** pour provisionner vos nouveaux serveurs réplica de requête. 

Lors de la configuration de montée en puissance pour un serveur la première fois, les modèles sur votre serveur principal sont automatiquement synchronisés avec des réplicas dans le pool de requêtes. La synchronisation automatique se produit uniquement une fois, lorsque vous configurez tout d’abord montée en puissance à un ou plusieurs réplicas. Les modifications ultérieures apportées au nombre de réplicas sur le même serveur *ne déclenche pas une autre synchronisation automatique*. La synchronisation automatique n’a pas lieu à nouveau même si vous définissez le serveur à zéro les réplicas et puis à nouveau montée à n’importe quel nombre de réplicas. 

## <a name="synchronize"></a>Synchroniser 

Opérations de synchronisation doivent être effectuées manuellement ou à l’aide de l’API REST.

### <a name="in-azure-portal"></a>Dans le portail Azure

Dans **Vue d’ensemble** > Modèle > **Synchroniser le modèle**.

![Curseur de montée en charge](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>API REST

Utilisez l’opération de **synchronisation**.

#### <a name="synchronize-a-model"></a>Synchroniser un modèle   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Obtenir l’état de la synchronisation  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Codes d’état de retour :


|Code  |Description  |
|---------|---------|
|-1     |  Non valide       |
|0     | Réplication en cours        |
|1     |  Réactivation       |
|2     |   Completed       |
|3     |   Échec      |
|4     |    Finalisation     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Avant d’utiliser PowerShell, [installer ou mettre à jour le dernier module Azure PowerShell](/powershell/azure/install-az-ps). 

Pour exécuter la synchronisation, utilisez [AzAnalysisServicesInstance de synchronisation](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

Pour définir le nombre de réplicas de requête, utilisez [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Spécifiez le paramètre `-ReadonlyReplicaCount` facultatif.

Pour séparer le serveur de traitement du pool de requêtes, utilisez [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Spécifiez le paramètre facultatif `-DefaultConnectionMode` paramètre à utiliser `Readonly`.

## <a name="connections"></a>connexions

Dans la page Vue d’ensemble de votre serveur, vous voyez deux noms de serveur. Si vous n’avez pas encore configuré la montée en charge pour un serveur, les deux noms de serveur fonctionnent de la même façon. Une fois le scale-out configuré pour un serveur, vous devez spécifier le nom du serveur approprié en fonction du type de connexion. 

Pour les connexions de clients d’utilisateur finaux, comme Power BI Desktop, Excel et des applications personnalisées, utilisez **Nom du serveur**. 

Pour SSMS, SSDT et les chaînes de connexion dans PowerShell, les applications de fonction Azure et AMO, utilisez **Nom du serveur d’administration**. Le nom du serveur d’administration inclut un qualificateur `:rw` (lecture-écriture) spécial. Toutes les opérations de traitement se produisent sur le serveur d’administration (principal).

![Noms de serveur](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="troubleshoot"></a>Résolution des problèmes

**Problème :** les utilisateurs obtiennent l’erreur **Cannot find server ’\<Name of the server>’ instance in connection mode ’ReadOnly’** (Impossible de trouver l’instance de serveur ’<Nom du serveur>’ en mode de connexion ’Lecture seule’).

**Solution :** Lorsque vous sélectionnez le **séparer le serveur de traitement du pool de requêtes** option, les connexions client à l’aide de la chaîne de connexion par défaut (sans `:rw`) sont redirigés vers des réplicas de pool de requête. Si les réplicas du pool de requêtes ne sont pas encore en ligne, car la synchronisation n’est pas terminée, les connexions client redirigées peuvent échouer. Pour empêcher l’échec des connexions, vous devez avoir au moins deux serveurs dans le pool de requêtes quand vous effectuez une synchronisation. Chaque serveur est synchronisé individuellement pendant que les autres serveurs restent en ligne. Si vous choisissez de ne pas mettre le serveur de traitement dans le pool de requêtes durant le traitement, vous pouvez l’enlever du pool avant le traitement, puis le réintégrer au pool une fois le traitement terminé, mais avant la synchronisation. Utilisez les métriques Mémoire et QPU pour superviser l’état de synchronisation.

## <a name="related-information"></a>Informations connexes

[Surveiller les métriques du serveur](analysis-services-monitor.md)   
[Gérer Azure Analysis Services](analysis-services-manage.md) 
