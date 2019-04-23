---
title: Migrer vers un accès en fonction du rôle granulaire pour les configurations de cluster - Azure HDInsight
description: En savoir plus sur les modifications nécessaires pour migrer vers un accès en fonction du rôle granulaire pour les configurations de cluster.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 0422d848ccdf9ba82e68813de64eec863ee4ad29
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006231"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrer vers un accès en fonction du rôle granulaire pour les configurations de cluster

Nous vous présentons quelques modifications importantes pour prendre en charge plus précise en fonction du rôle de l’accès pour obtenir des informations sensibles. Comme partie de ces modifications, certaines **action peut être nécessaire** si vous utilisez l’une de la [affectées entités/scénarios](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>Qu’est-ce qui change ?

Auparavant, les secrets pu être obtenus via l’API de HDInsight par les utilisateurs de cluster possédant le propriétaire, collaborateur ou lecteur [rôles RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles).
À l’avenir, ces secrets ne sera plus accessibles aux utilisateurs avec le rôle de lecteur. Nous sommes également présenter un nouveau rôle « Opérateur de Cluster HDInisght » qui est en mesure de récupérer des secrets sans disposer des autorisations d’administration de collaborateur ou propriétaire. Pour résumer :

| Rôle                                  | Précédemment                                                                                       | Now       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Lecteur                                | -Un accès en lecture, y compris les secrets                                                                   | -Accès en lecture, **à l’exclusion** secrets |           |   |   |
| Opérateur de Cluster de HDInsight<br>(Nouveau rôle) | S.O.                                                                                              | -Accès lecture/écriture, y compris les secrets         |   |   |
| Contributeur                           | -Accès lecture/écriture, y compris les secrets<br>-Créer et gérer tous les types de ressources Azure.     | Aucune modification |
| Propriétaire                                 | -Accès en lecture/écriture y compris les secrets<br>-Accès complet à toutes les ressources<br>-Déléguer l’accès à d’autres personnes | Aucune modification |

Pour plus d’informations sur l’ajout de l’attribution de rôle d’opérateur de Cluster HDInsight à un utilisateur pour leur accorder l’accès en lecture/écriture aux clés secrètes de cluster, consultez la section ci-dessous, [ajouter l’attribution de rôle d’opérateur de Cluster HDInsight à un utilisateur](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Suis-je concerné par ces modifications ?

Les entités et les scénarios suivants sont concernés :

- [API](#api) : Les utilisateurs à l’aide de la `/configurations` ou `/configurations/{configurationName}` points de terminaison.
- [Azure HDInsight Tools pour Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) version ___ et versions antérieures.
- [Kit de ressources Azure pour IntelliJ](#azure-toolkit-for-intellij) version __ et versions antérieures.
- [Kit de ressources Azure pour Eclipse](#azure-toolkit-for-eclipse) version __ et versions antérieures.
- [Kit de développement logiciel pour .NET](#sdk-for-net)
    - [les versions 1.x ou 2.x](#versions-1x-and-2x): Les utilisateurs à l’aide de la `GetClusterConfigurations`, `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` ou `DisableHttp` méthodes à partir de la classe ConfigurationsOperationsExtensions.
    - [versions 3.x et jusqu'à](#versions-3x-and-up): Les utilisateurs à l’aide de la `EnableHttp`, `DisableHttp`, `Update`, ou `Get` méthodes à partir de la `ConfigurationsOperationsExtensions` classe.
- [Kit SDK pour Python](#sdk-for-python): Les utilisateurs à l’aide de la `get` ou `update` méthodes à partir de la classe ConfigurationsOperations.
- [SDK pour Java](#sdk-for-java): Les utilisateurs à l’aide de la `update` ou `get` méthodes à partir de la classe ConfigurationsInner.
- [SDK pour Go](#sdk-for-go): Les utilisateurs à l’aide de la `Get` ou `Update` méthodes à partir de la structure ConfigurationsClient.

Consultez les sections ci-dessous (ou utilisez les liens ci-dessus) pour la migration de voir les étapes pour votre scénario.

### <a name="api"></a>API

Les API suivantes seront modifiés ou obsolètes :

- [**GET /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (les informations sensibles) https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations/{configurationName}?api-version={api-version}
    - Précédemment utilisé pour obtenir les types de configuration individuels (y compris les secrets).
    - Cet appel d’API renvoie les types de configuration individuels avec des secrets omis. Pour obtenir toutes les configurations, notamment les secrets, utilisez la nouvelle [POST /configurations]() appeler. Pour obtenir uniquement les paramètres de passerelle, utilisez la nouvelle [POST /getGatewaySettings]() appeler.
- [**GET /configurations** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configurations) (déconseillée) https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations?api-version={api-version}
    - Précédemment utilisé pour obtenir toutes les configurations (y compris les secrets)
    - Cet appel d’API n’est plus prises en charge. Pour obtenir toutes les configurations à l’avenir, utilisez la nouvelle [POST /configurations]() appeler. Pour obtenir des configurations avec des paramètres sensibles omis, utilisez le [/configurations/ GET {configurationName}]() appeler.
- [**POST /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#change-connectivity-settings) (déconseillée) https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations/{configurationName}?api-version={api-version}
    - Précédemment utilisé pour mettre à jour les informations d’identification de la passerelle.
    - Cet appel d’API est déconseillé et n’est plus pris en charge. Utilisez la nouvelle [POST /updateGatewaySettings]() à la place.

Le remplacement suivant Qu'api ont été ajoutées :</span>

- **POST /configurations** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations?api-version={api-version}
    - Utilisez cette API pour obtenir toutes les configurations, notamment les secrets.
- **POST /getGatewaySettings** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/getGatewaySettings?api-version={api-version}
    - Utilisez cette API pour obtenir des paramètres de la passerelle.
- **POST /updateGatewaySettings** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/updateGatewaySettings?api-version={api-version}
    - Utilisez cette API pour mettre à jour les paramètres de la passerelle (nom d’utilisateur et/ou mot de passe).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Outils Azure HDInsight pour Visual Studio Code

Si vous utilisez la version 1.1.1 ou une version antérieure, mettez à jour vers la [version la plus récente d’Azure HDInsight Tools pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) d’éviter les interruptions.

### <a name="azure-toolkit-for-intellij"></a>Kit de ressources Azure pour IntelliJ

Si vous utilisez la version 3.21.0 ou une version antérieure, mettez à jour vers la [version la plus récente du Kit de ressources Azure pour le plug-in IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) d’éviter les interruptions.

### <a name="azure-toolkit-for-eclipse"></a>Kit de ressources Azure pour Eclipse

Si vous utilisez la version 2019-03-29 ou une version antérieure, mettez à jour vers la dernière version du Kit de ressources Azure pour Eclipse afin d’éviter les interruptions.

### <a name="sdk-for-net"></a>Kit SDK pour .NET

#### <a name="versions-1x-and-2x"></a>Les versions 1.x et 2.x

Mettez à jour vers [version 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) du SDK HDInsight pour .NET. Modifications de code minimal peuvent être nécessaire si vous utilisez une méthode affectée par ces modifications :

- `ClusterOperationsExtensions.GetClusterConfigurations` sera **retournent plus de paramètres sensibles** comme clés de stockage (core-site) ou les informations d’identification HTTP (passerelle).
    - Pour récupérer toutes les configurations, notamment les paramètres sensibles, utilisez `ClusterOperationsExtensions.ListConfigurations` à l’avenir.  Notez que les utilisateurs dotés du rôle « Lecteur » ne sera pas en mesure d’utiliser cette méthode. Cela permet un contrôle granulaire quels utilisateurs peuvent accéder à des informations sensibles pour un cluster.
    - Pour récupérer des données d’identification de passerelle HTTP, utilisez `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.GetConnectivitySettings` est maintenant déconseillé et a été remplacé par `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings` est maintenant déconseillé et a été remplacé par `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp` et `DisableHttp` sont désormais déconseillés. HTTP est désormais toujours activée, donc ces méthodes ne sont plus nécessaires.

#### <a name="versions-3x-and-up"></a>Versions 3.x et jusqu'à

Mettez à jour vers [version 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) du SDK HDInsight pour .NET. Modifications de code minimal peuvent être nécessaire si vous utilisez une méthode affectée par ces modifications :

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) sera **retournent plus de paramètres sensibles** comme clés de stockage (core-site) ou les informations d’identification HTTP (passerelle).
    - Pour récupérer toutes les configurations, notamment les paramètres sensibles, utilisez `ConfigurationOperationsExtensions.List` à l’avenir.  Notez que les utilisateurs dotés du rôle « Lecteur » ne sera pas en mesure d’utiliser cette méthode. Cela permet un contrôle granulaire quels utilisateurs peuvent accéder à des informations sensibles pour un cluster. 
    - Pour récupérer des données d’identification de passerelle HTTP, utilisez `ClusterOperationsExtensions.GetGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) est maintenant déconseillé et a été remplacé par `ClusterOperationsExtensions.UpdateGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) et [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) sont désormais déconseillés. HTTP est désormais toujours activée, donc ces méthodes ne sont plus nécessaires.

### <a name="sdk-for-python"></a>Kit SDK pour Python

Mettez à jour vers [version 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) du SDK HDInsight pour Python. Modifications de code minimal peuvent être nécessaire si vous utilisez une méthode affectée par ces modifications :

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) sera **retournent plus de paramètres sensibles** comme clés de stockage (core-site) ou les informations d’identification HTTP (passerelle).
    - Pour récupérer toutes les configurations, notamment les paramètres sensibles, utilisez [ `ConfigurationsOperations.list` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) à l’avenir.  Notez que les utilisateurs dotés du rôle « Lecteur » ne sera pas en mesure d’utiliser cette méthode. Cela permet un contrôle granulaire quels utilisateurs peuvent accéder à des informations sensibles pour un cluster. 
    - Pour récupérer des données d’identification de passerelle HTTP, utilisez [ `ConfigurationsOperations.get_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-).
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-resource-group-name--cluster-name--tags-none--custom-headers-none--raw-false----operation-config-) est maintenant déconseillé et a été remplacé par [ `ClusterOperationsExtensions.update_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>SDK pour Java

Mettez à jour vers [version 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) du SDK pour Java HDInsight. Modifications de code minimal peuvent être nécessaire si vous utilisez une méthode affectée par ces modifications :

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) sera **retournent plus de paramètres sensibles** comme clés de stockage (core-site) ou les informations d’identification HTTP (passerelle).
    - Pour récupérer toutes les configurations, notamment les paramètres sensibles, utilisez `ConfigurationsInner.list` à l’avenir.  Notez que les utilisateurs dotés du rôle « Lecteur » ne sera pas en mesure d’utiliser cette méthode. Cela permet un contrôle granulaire quels utilisateurs peuvent accéder à des informations sensibles pour un cluster. 
    - Pour récupérer des données d’identification de passerelle HTTP, utilisez `ConfigurationsOperations.get_gateway_settings`.
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) est maintenant déconseillé et a été remplacé par `ClusterOperationsExtensions.update_gateway_settings`.

### <a name="sdk-for-go"></a>SDK pour Go

Mettez à jour vers [version 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) du SDK pour Go HDInsight. Modifications de code minimal peuvent être nécessaire si vous utilisez une méthode affectée par ces modifications :

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) sera **retournent plus de paramètres sensibles** comme clés de stockage (core-site) ou les informations d’identification HTTP (passerelle).
    - Pour récupérer toutes les configurations, notamment les paramètres sensibles, utilisez [ `ConfigurationsClient.list` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) à l’avenir.  Notez que les utilisateurs dotés du rôle « Lecteur » ne sera pas en mesure d’utiliser cette méthode. Cela permet un contrôle granulaire quels utilisateurs peuvent accéder à des informations sensibles pour un cluster. 
    - Pour récupérer des données d’identification de passerelle HTTP, utilisez [ `ClustersClient.get_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings).
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) est maintenant déconseillé et a été remplacé par [ `ClustersClient.update_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Ajouter l’attribution de rôle d’opérateur de Cluster HDInsight à un utilisateur

Un utilisateur avec le [contributeur](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) ou [propriétaire](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) rôle peut accorder le rôle d’opérateur de Cluster HDInsight pour les utilisateurs que vous souhaitez avoir accès en lecture/écriture aux secrets du cluster HDInsight comme informations d’identification de la passerelle de cluster et les clés de compte de stockage.

### <a name="using-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure (CLI)

Pour ajouter cette attribution de rôle, le plus simple consiste à l’aide de la commande suivante dans l’interface CLI Azure :

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

> [!NOTE]
> Cette commande doit être exécutée par un utilisateur avec les rôles de contributeur ou propriétaire, car seulement ils peuvent accorder ces autorisations. Le `--assignee` est l’adresse de messagerie de l’utilisateur auquel vous souhaitez affecter le rôle opérateur de Cluster HDInsight.

La commande ci-dessus attribue à ce rôle au niveau de l’abonnement. Pour à la place simplement accorder ce rôle au niveau du groupe de ressources, vous pouvez modifier la commande comme suit :

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

### <a name="using-the-azure-portal"></a>Utilisation du portail Azure

Vous pouvez également utiliser le portail Azure pour ajouter l’attribution de rôle d’opérateur de Cluster HDInsight à un utilisateur. Consultez la documentation, [gérer l’accès aux ressources Azure à l’aide de RBAC et le portail Azure - ajouter une attribution de rôle](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).
