---
title: Migrer vers un accès en fonction du rôle granulaire pour les configurations de cluster - Azure HDInsight
description: Découvrez les modifications requises dans le cadre de la migration vers un accès en fonction du rôle granulaire pour les configurations de cluster HDInsight.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/26/2019
ms.openlocfilehash: 8bcb20ec5c85c3cfa2e481a4a5848f404a2fb4eb
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64685457"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrer vers un accès en fonction du rôle granulaire pour les configurations de cluster

Nous vous présentons quelques modifications importantes pour prendre en charge plus précise en fonction du rôle de l’accès pour obtenir des informations sensibles. Comme partie de ces modifications, certaines **action peut être nécessaire** si vous utilisez l’une de la [affectées entités/scénarios](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>Qu’est-ce qui change ?

Auparavant, les secrets pu être obtenus via l’API de HDInsight par les utilisateurs de cluster possédant le propriétaire, collaborateur ou lecteur [rôles RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles).
À l’avenir, ces secrets ne sera plus accessibles aux utilisateurs avec le rôle de lecteur. Les secrets sont définis comme doivent autoriser les valeurs qui peuvent être utilisés pour obtenir un accès plus élevé que le rôle d’un utilisateur. Ceux-ci incluent des valeurs telles que les informations d’identification de cluster passerelle HTTP, les clés de compte de stockage et les informations d’identification de la base de données.

Nous avons également introduit un nouveau [opérateur de Cluster HDInisght](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) rôle qui sera en mesure de récupérer les secrets sans disposer des autorisations d’administration de collaborateur ou propriétaire. Pour résumer :

| Rôle                                  | Précédemment                                                                                       | À l’avenir       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Lecteur                                | -Un accès en lecture, y compris les secrets                                                                   | -Accès en lecture, **à l’exclusion** secrets |           |   |   |
| Opérateur de Cluster de HDInsight<br>(Nouveau rôle) | N/A                                                                                              | -Accès lecture/écriture, y compris les secrets         |   |   |
| Contributeur                           | -Accès lecture/écriture, y compris les secrets<br>-Créer et gérer tous les types de ressources Azure.     | Aucune modification |
| Propriétaire                                 | -Accès en lecture/écriture y compris les secrets<br>-Accès complet à toutes les ressources<br>-Déléguer l’accès à d’autres personnes | Aucune modification |

Pour plus d’informations sur l’ajout de l’attribution de rôle d’opérateur de Cluster HDInsight à un utilisateur pour leur accorder l’accès en lecture/écriture aux clés secrètes de cluster, consultez la section ci-dessous, [ajouter l’attribution de rôle d’opérateur de Cluster HDInsight à un utilisateur](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Suis-je concerné par ces modifications ?

Les entités et les scénarios suivants sont concernés :

- [API](#api) : Les utilisateurs à l’aide de la `/configurations` ou `/configurations/{configurationName}` points de terminaison.
- [Azure HDInsight Tools pour Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) version 1.1.1 ou ci-dessous.
- [Kit de ressources Azure pour IntelliJ](#azure-toolkit-for-intellij) version 3.20.0 ou ci-dessous.
- [Kit de développement logiciel pour .NET](#sdk-for-net)
    - [les versions 1.x ou 2.x](#versions-1x-and-2x): Les utilisateurs à l’aide de la `GetClusterConfigurations`, `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` ou `DisableHttp` méthodes à partir de la classe ConfigurationsOperationsExtensions.
    - [versions 3.x et jusqu'à](#versions-3x-and-up): Les utilisateurs à l’aide de la `Get`, `Update`, `EnableHttp`, ou `DisableHttp` méthodes à partir de la `ConfigurationsOperationsExtensions` classe.
- [Kit SDK pour Python](#sdk-for-python): Les utilisateurs à l’aide de la `get` ou `update` méthodes à partir de la `ConfigurationsOperations` classe.
- [SDK pour Java](#sdk-for-java): Les utilisateurs à l’aide de la `update` ou `get` méthodes à partir de la `ConfigurationsInner` classe.
- [SDK pour Go](#sdk-for-go): Les utilisateurs à l’aide de la `Get` ou `Update` méthodes à partir de la `ConfigurationsClient` struct.

Consultez les sections qui suivent (ou utilisez les liens mentionnés plus haut) pour connaître les étapes de migration de votre scénario.

### <a name="api"></a>API

Les API suivantes seront modifiés ou obsolètes :

- [**GET /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (les informations sensibles)
    - Précédemment utilisé pour obtenir les types de configuration individuels (y compris les secrets).
    - Cet appel d’API renvoie les types de configuration individuels avec des secrets omis. Pour obtenir toutes les configurations, notamment les secrets, utilisez le nouvel appel /configurations POST. Pour obtenir uniquement les paramètres de passerelle, utilisez le nouvel appel /getGatewaySettings POST.
- [**GET /configurations** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configurations) (déconseillée)
    - Précédemment utilisé pour obtenir toutes les configurations (y compris les secrets)
    - Cet appel d’API n’est plus prises en charge. Pour obtenir toutes les configurations à l’avenir, utilisez le nouvel appel /configurations POST. Pour obtenir des configurations avec des paramètres sensibles omis, utilisez l’appel de /configurations/ {configurationName} GET.
- [**POST /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#change-connectivity-settings) (déconseillée)
    - Précédemment utilisé pour mettre à jour les informations d’identification de la passerelle.
    - Cet appel d’API est déconseillé et n’est plus pris en charge. Utilisez la nouvelle /updateGatewaySettings POST à la place.

Le remplacement suivant Qu'api ont été ajoutées :</span>

- [**POST /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Utilisez cette API pour obtenir toutes les configurations, notamment les secrets.
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Utilisez cette API pour obtenir des paramètres de la passerelle.
- [**POST /updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Utilisez cette API pour mettre à jour les paramètres de la passerelle (nom d’utilisateur et/ou mot de passe).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Outils Azure HDInsight pour Visual Studio Code

Si vous utilisez la version 1.1.1 ou ci-dessous, la mise à jour vers la [version la plus récente d’Azure HDInsight Tools pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) d’éviter les interruptions.

### <a name="azure-toolkit-for-intellij"></a>Kit de ressources Azure pour IntelliJ

Si vous utilisez la version 3.20.0 ou ci-dessous, la mise à jour vers la [version la plus récente du Kit de ressources Azure pour le plug-in IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) d’éviter les interruptions.

### <a name="sdk-for-net"></a>Kit SDK pour .NET

#### <a name="versions-1x-and-2x"></a>Les versions 1.x et 2.x

Mettre à jour vers [version 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) du SDK HDInsight pour .NET. Modifications de code minimal peuvent être nécessaire si vous utilisez une méthode affectée par ces modifications :

- `ClusterOperationsExtensions.GetClusterConfigurations` sera **retournent plus de paramètres sensibles** comme clés de stockage (core-site) ou les informations d’identification HTTP (passerelle).
    - Pour récupérer toutes les configurations, notamment les paramètres sensibles, utilisez `ClusterOperationsExtensions.ListConfigurations` à l’avenir.  Notez que les utilisateurs dotés du rôle « Lecteur » ne sera pas en mesure d’utiliser cette méthode. Cela permet un contrôle granulaire quels utilisateurs peuvent accéder à des informations sensibles pour un cluster.
    - Pour récupérer des données d’identification de passerelle HTTP, utilisez `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.GetConnectivitySettings` est maintenant déconseillé et a été remplacé par `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings` est maintenant déconseillé et a été remplacé par `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp` et `DisableHttp` sont désormais déconseillés. HTTP est désormais toujours activée, donc ces méthodes ne sont plus nécessaires.

#### <a name="versions-3x-and-up"></a>Versions 3.x et jusqu'à

Mettre à jour vers [version 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) ou version ultérieure du SDK HDInsight pour .NET. Modifications de code minimal peuvent être nécessaire si vous utilisez une méthode affectée par ces modifications :

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) sera **retournent plus de paramètres sensibles** comme clés de stockage (core-site) ou les informations d’identification HTTP (passerelle).
    - Pour récupérer toutes les configurations, notamment les paramètres sensibles, utilisez [ `ConfigurationOperationsExtensions.List` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) à l’avenir.  Notez que les utilisateurs dotés du rôle « Lecteur » ne sera pas en mesure d’utiliser cette méthode. Cela permet un contrôle granulaire quels utilisateurs peuvent accéder à des informations sensibles pour un cluster. 
    - Pour récupérer des données d’identification de passerelle HTTP, utilisez [ `ClusterOperationsExtensions.GetGatewaySettings` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) est maintenant déconseillé et a été remplacé par [ `ClusterOperationsExtensions.UpdateGatewaySettings` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) et [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) sont désormais déconseillés. HTTP est désormais toujours activée, donc ces méthodes ne sont plus nécessaires.

### <a name="sdk-for-python"></a>Kit SDK pour Python

Mettre à jour vers [version 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) ou version ultérieure du SDK HDInsight pour Python. Modifications de code minimal peuvent être nécessaire si vous utilisez une méthode affectée par ces modifications :

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) sera **retournent plus de paramètres sensibles** comme clés de stockage (core-site) ou les informations d’identification HTTP (passerelle).
    - Pour récupérer toutes les configurations, notamment les paramètres sensibles, utilisez [ `ConfigurationsOperations.list` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) à l’avenir.  Notez que les utilisateurs dotés du rôle « Lecteur » ne sera pas en mesure d’utiliser cette méthode. Cela permet un contrôle granulaire quels utilisateurs peuvent accéder à des informations sensibles pour un cluster. 
    - Pour récupérer des données d’identification de passerelle HTTP, utilisez [ `ConfigurationsOperations.get_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-).
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-resource-group-name--cluster-name--tags-none--custom-headers-none--raw-false----operation-config-) est maintenant déconseillé et a été remplacé par [ `ClusterOperationsExtensions.update_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>SDK pour Java

Mettre à jour vers [version 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) ou version ultérieure du SDK pour Java HDInsight. Modifications de code minimal peuvent être nécessaire si vous utilisez une méthode affectée par ces modifications :

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) sera **retournent plus de paramètres sensibles** comme clés de stockage (core-site) ou les informations d’identification HTTP (passerelle).
    - Pour récupérer toutes les configurations, notamment les paramètres sensibles, utilisez [ `ConfigurationsInner.list` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.configurationsinner.list?view=azure-java-stable) à l’avenir.  Notez que les utilisateurs dotés du rôle « Lecteur » ne sera pas en mesure d’utiliser cette méthode. Cela permet un contrôle granulaire quels utilisateurs peuvent accéder à des informations sensibles pour un cluster. 
    - Pour récupérer des données d’identification de passerelle HTTP, utilisez [ `ConfigurationsOperations.get_gateway_settings` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.getgatewaysettings?view=azure-java-stable).
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) est maintenant déconseillé et a été remplacé par [ `ClusterOperationsExtensions.update_gateway_settings` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.updategatewaysettings?view=azure-java-stable).

### <a name="sdk-for-go"></a>SDK pour Go

Mettre à jour vers [version 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) ou version ultérieure du SDK pour Go HDInsight. Modifications de code minimal peuvent être nécessaire si vous utilisez une méthode affectée par ces modifications :

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) sera **retournent plus de paramètres sensibles** comme clés de stockage (core-site) ou les informations d’identification HTTP (passerelle).
    - Pour récupérer toutes les configurations, notamment les paramètres sensibles, utilisez [ `ConfigurationsClient.list` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) à l’avenir.  Notez que les utilisateurs dotés du rôle « Lecteur » ne sera pas en mesure d’utiliser cette méthode. Cela permet un contrôle granulaire quels utilisateurs peuvent accéder à des informations sensibles pour un cluster. 
    - Pour récupérer des données d’identification de passerelle HTTP, utilisez [ `ClustersClient.get_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings).
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) est maintenant déconseillé et a été remplacé par [ `ClustersClient.update_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Ajouter l’attribution de rôle d’opérateur de Cluster HDInsight à un utilisateur

Un utilisateur avec le [contributeur](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) ou [propriétaire](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) rôle peut affecter la [opérateur de Cluster HDInisght](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) rôle aux utilisateurs qui ont accès en lecture/écriture aux données souhaitées Valeurs de configuration HDInsight cluster (par exemple, les informations d’identification de passerelle de cluster et les clés de compte de stockage).

### <a name="using-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure (CLI)

La façon la plus simple d’ajouter cette attribution de rôle est à l’aide de la `az role assignemnt create` commande dans Azure CLI.

> [!NOTE]
> Cette commande doit être exécutée par un utilisateur avec les rôles de contributeur ou propriétaire, car seulement ils peuvent accorder ces autorisations. Le `--assignee` est l’adresse de messagerie de l’utilisateur auquel vous souhaitez affecter le rôle opérateur de Cluster HDInsight.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Attribuer un rôle au niveau des ressources (cluster)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Attribuer un rôle au niveau du groupe de ressources

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Attribuer un rôle au niveau de l’abonnement

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Utilisation du portail Azure

Vous pouvez également utiliser le portail Azure pour ajouter l’attribution de rôle d’opérateur de Cluster HDInsight à un utilisateur. Consultez la documentation, [gérer l’accès aux ressources Azure à l’aide de RBAC et le portail Azure - ajouter une attribution de rôle](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).
