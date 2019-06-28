---
title: Migrer vers un accès en fonction du rôle granulaire pour les configurations de cluster - Azure HDInsight
description: Découvrez les modifications nécessaires dans le cadre de la migration vers un accès granulaire en fonction du rôle pour des configurations de cluster HDInsight.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 1ec4786291d6e2e5be6785e52cf3ab5bb5bbc690
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66754539"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrer vers un accès en fonction du rôle granulaire pour les configurations de cluster

Nous ajoutons d’importantes modifications pour pouvoir utiliser des accès basés sur les rôles encore plus détaillés pour obtenir des informations sensibles. Dans le cadre de ces modifications, certaines **actions peuvent être nécessaires** si vous utilisez [une des entités ou un des scénarios affectés](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>Qu’est-ce qui change ?

Auparavant, les secrets pouvaient être obtenus via l’API HDInsight par des utilisateurs du cluster avec les [rôles RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) Propriétaire, Contributeur ou Lecteur, car ils étaient disponibles pour tous et l’autorisation `*/read` était nécessaire.
À partir de maintenant, l’accès à ces secrets nécessitera l’autorisation `Microsoft.HDInsight/clusters/configurations/*`, ce qui veut dire qu’ils ne seront plus accessibles aux utilisateurs disposant du rôle Lecteur. Les secrets sont définis comme des valeurs pouvant être utilisées pour obtenir un accès supérieur à celui d’un rôle d’utilisateur. Ils comprennent des valeurs telles que les informations d’identification HTTP de la passerelle du cluster, les clés de compte de stockage et les informations d'identification de la base de données.

Nous introduisons également un nouveau rôle [Opérateur de cluster HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator), capable de récupérer des secrets sans disposer des autorisations administratives d’un Contributeur ou d’un Propriétaire. Pour résumer :

| Rôle                                  | Précédemment                                                                                       | À l’avenir       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Lecteur                                | - Un accès en lecture, dont aux secrets                                                                   | - Un accès en lecture, **sans** les secrets |           |   |   |
| Opérateur de cluster HDInsight<br>(Nouveau rôle) | N/A                                                                                              | - Accès lecture/écriture, dont aux secrets         |   |   |
| Contributeur                           | - Accès lecture/écriture, dont aux secrets<br>- Création et gestion de tous les types de ressources Azure.     | Aucune modification |
| Propriétaire                                 | - Accès lecture/écriture, dont aux secrets<br>- Accès total à toutes les ressources<br>- Délégation de l’accès à d’autres personnes | Aucune modification |

Pour plus d’informations sur l’attribution du rôle Opérateur de cluster HDInsight à un utilisateur afin de lui autoriser l’accès lecture/écriture aux secrets du cluster, consultez la section ci-dessous, [Ajouter l’attribution de rôle Opérateur de cluster HDInsight à un utilisateur](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Suis-je concerné par ces modifications ?

Les entités et scénarios suivants sont concernés :

- [API](#api) : Utilisateurs ayant recours aux points de terminaison `/configurations` et `/configurations/{configurationName}`.
- [Informations de connexion Azure HDInsight Tools for Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) version 1.1.1 ou inférieure.
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) version 3.20.0 ou inférieure.
- [Azure Data Lake et Stream Analytics Tools pour Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) version 2.3.9000.1 ou inférieure.
- [Azure Toolkit pour Eclipse](#azure-toolkit-for-eclipse) version 3.15.0 ou inférieure.
- [Kit SDK pour .NET](#sdk-for-net)
    - [Versions 1.x ou 2.x](#versions-1x-and-2x) : utilisateurs qui ont recours aux méthodes `GetClusterConfigurations`; `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` ou `DisableHttp` de la classe ConfigurationsOperationsExtensions.
    - [Versions 3.x et versions ultérieures](#versions-3x-and-up) : Utilisateurs ayant recours aux méthodes `Get`, `Update`, `EnableHttp`, ou `DisableHttp` depuis la classe `ConfigurationsOperationsExtensions`.
- [Kit SDK pour Python](#sdk-for-python) : Utilisateurs ayant recours aux méthodes `get` ou `update` depuis la classe `ConfigurationsOperations`.
- [Kit SDK pour Java](#sdk-for-java) : Utilisateurs ayant recours aux méthodes `update` ou `get` depuis la classe `ConfigurationsInner`.
- [Kit SDK pour Go](#sdk-for-go) : Utilisateurs ayant recours aux méthodes `Get` ou `Update` depuis la structure `ConfigurationsClient`.
- [PowerShell Az.HDInsight](#azhdinsight-powershell) version inférieure à 2.0.0.
Consultez les sections qui suivent (ou utilisez les liens mentionnés plus haut) pour connaître les étapes de migration de votre scénario.

### <a name="api"></a>API

Les API suivantes seront modifiées ou déconseillées :

- [**GET /configurations/{configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (informations sensibles supprimées)
    - Précédemment utilisé pour obtenir les types de configuration individuels (y compris les secrets).
    - Cet appel d’API renvoie les types de configuration individuels avec les secrets omis. Pour obtenir toutes les configurations, dont les secrets, utilisez le nouvel appel POST/configurations. Pour obtenir uniquement les paramètres de passerelle, utilisez le nouvel appel POST/getGatewaySettings.
- [**GET /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (déconseillé)
    - Précédemment utilisé pour obtenir toutes les configurations (y compris les secrets)
    - Cet appel d’API n’est plus pris en charge. Pour obtenir toutes les configurations, à l’avenir, utilisez le nouvel appel POST/configurations. Pour obtenir des configurations avec des paramètres sensibles omis, utilisez l’appel GET /configurations/{configurationName}.
- [**POST /configurations/{configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (déconseillé)
    - Précédemment utilisé pour mettre à jour les informations d’identification de la passerelle.
    - Cet appel d’API est déconseillé et n’est plus pris en charge. Utilisez le nouvel appel POST /updateGatewaySettings à la place.

Les API de remplacement suivantes ont été ajoutées :</span>

- [**POST /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Utilisez cette API pour obtenir toutes les configurations, y compris les secrets.
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Utilisez cette API pour obtenir les paramètres de la passerelle.
- [**POST /updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Utilisez cette API pour mettre à jour les paramètres de la passerelle (nom d’utilisateur et/ou mot de passe).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tools pour Visual Studio Code

Si vous utilisez la version 1.1.1 ou une version inférieure, mettez à jour vers la [dernière version d’Azure HDInsight Tools pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) afin d’éviter toute interruption.

### <a name="azure-toolkit-for-intellij"></a>Kit de ressources Azure pour IntelliJ

Si vous utilisez la version 3.20.0 ou une version inférieure, mettez à jour vers la [dernière version du plugin Azure Toolkit pour IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) afin d’éviter toute interruption.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Azure Data Lake et Stream Analytics Tools pour Visual Studio

Mettez à jour vers la version 2.3.9000.1 ou ultérieure d’[Azure Data Lake et Stream Analytics Tools pour Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) afin d’éviter toute interruption.  Pour faciliter la mise à jour, consultez notre documentation, [Update Data Lake Tools for Visual Studio (Mettre à jour Data Lake Tools pour Visual Studio)](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Kit de ressources Azure pour Eclipse

Si vous utilisez la version 3.15.0 ou une version inférieure, mettez à jour vers la [dernière version d’Azure Toolkit pour Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) afin d’éviter toute interruption.

### <a name="sdk-for-net"></a>Kit SDK pour .NET

#### <a name="versions-1x-and-2x"></a>Versions 1.x et 2.x

Mettez à jour vers la [version 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) du SDK HDInsight pour .NET. Des modifications minimes du code peuvent être nécessaires si vous utilisez une méthode affectée par ces changements :

- `ClusterOperationsExtensions.GetClusterConfigurations` **ne retournera plus de paramètres sensibles** comme les clés de stockage (core-site) ou les informations d'identification HTTP (passerelle).
    - Pour récupérer toutes les configurations, y compris les paramètres sensibles, utilisez `ClusterOperationsExtensions.ListConfigurations` à l’avenir.  Notez que les utilisateurs disposant du rôle « Lecteur » ne seront pas en mesure d’utiliser cette méthode. Cela permet un contrôle granulaire sur les utilisateurs pouvant accéder aux informations sensibles d’un cluster.
    - Pour récupérer des données d’identification de passerelle HTTP, utilisez `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.GetConnectivitySettings` est maintenant déconseillé et a été remplacé par `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings` est maintenant déconseillé et a été remplacé par `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp` et `DisableHttp` sont désormais déconseillés. HTTP est désormais toujours activé. Ces méthodes ne sont donc plus nécessaires.

#### <a name="versions-3x-and-up"></a>Versions 3.x et versions ultérieures

Mettez à jour vers la [version 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) ou versions ultérieures du SDK HDInsight pour .NET. Des modifications minimes du code peuvent être nécessaires si vous utilisez une méthode affectée par ces changements :

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) **ne retournera plus de paramètres sensibles** comme les clés de stockage (core-site) ou les informations d'identification HTTP (passerelle).
    - Pour récupérer toutes les configurations, y compris les paramètres sensibles, utilisez [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) à l’avenir.  Notez que les utilisateurs disposant du rôle « Lecteur » ne seront pas en mesure d’utiliser cette méthode. Cela permet un contrôle granulaire sur les utilisateurs pouvant accéder aux informations sensibles d’un cluster. 
    - Pour récupérer des données d’identification de passerelle HTTP, utilisez [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) est maintenant déconseillé et a été remplacé par [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) et [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) sont désormais déconseillés. HTTP est désormais toujours activé. Ces méthodes ne sont donc plus nécessaires.

### <a name="sdk-for-python"></a>Kit SDK pour Python

Mettez à jour vers la [version 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) ou versions ultérieures du SDK HDInsight pour Python. Des modifications minimes du code peuvent être nécessaires si vous utilisez une méthode affectée par ces changements :

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) **ne retournera plus de paramètres sensibles** comme les clés de stockage (core-site) ou les informations d'identification HTTP (passerelle).
    - Pour récupérer toutes les configurations, y compris les paramètres sensibles, utilisez [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) à l’avenir.  Notez que les utilisateurs disposant du rôle « Lecteur » ne seront pas en mesure d’utiliser cette méthode. Cela permet un contrôle granulaire sur les utilisateurs pouvant accéder aux informations sensibles d’un cluster. 
    - Pour récupérer des données d’identification de passerelle HTTP, utilisez [`ConfigurationsOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-).
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-resource-group-name--cluster-name--tags-none--custom-headers-none--raw-false----operation-config-) est maintenant déconseillé et a été remplacé par [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>Kit SDK pour Java

Mettez à jour vers la [version 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) ou versions ultérieures du SDK HDInsight pour Java. Des modifications minimes du code peuvent être nécessaires si vous utilisez une méthode affectée par ces changements :

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) **ne retournera plus de paramètres sensibles** comme les clés de stockage (core-site) ou les informations d'identification HTTP (passerelle).
    - Pour récupérer toutes les configurations, y compris les paramètres sensibles, utilisez [`ConfigurationsInner.list`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.configurationsinner.list?view=azure-java-stable) à l’avenir.  Notez que les utilisateurs disposant du rôle « Lecteur » ne seront pas en mesure d’utiliser cette méthode. Cela permet un contrôle granulaire sur les utilisateurs pouvant accéder aux informations sensibles d’un cluster. 
    - Pour récupérer des données d’identification de passerelle HTTP, utilisez [`ClustersInner.getGatewaySettings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.getgatewaysettings?view=azure-java-stable).
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) est maintenant déconseillé et a été remplacé par [`ClustersInner.updateGatewaySettings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.updategatewaysettings?view=azure-java-stable).

### <a name="sdk-for-go"></a>Kit SDK pour Go

Mettez à jour vers la [version 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) ou versions ultérieures du SDK HDInsight pour Go. Des modifications minimes du code peuvent être nécessaires si vous utilisez une méthode affectée par ces changements :

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) **ne retournera plus de paramètres sensibles** comme les clés de stockage (core-site) ou les informations d'identification HTTP (passerelle).
    - Pour récupérer toutes les configurations, y compris les paramètres sensibles, utilisez [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) à l’avenir.  Notez que les utilisateurs disposant du rôle « Lecteur » ne seront pas en mesure d’utiliser cette méthode. Cela permet un contrôle granulaire sur les utilisateurs pouvant accéder aux informations sensibles d’un cluster. 
    - Pour récupérer des données d’identification de passerelle HTTP, utilisez [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings).
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) est maintenant déconseillé et a été remplacé par [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
Mettez à jour vers la version [2.0.0 d’Az PowerShell](https://www.powershellgallery.com/packages/Az) ou version ultérieure afin d’éviter toute interruption.  Des modifications minimes du code peuvent être nécessaires si vous utilisez une méthode affectée par ces changements.
- `Grant-AzHDInsightHttpServicesAccess` est maintenant déconseillé et a été remplacé par la nouvelle cmdlet `Set-AzHDInsightGatewayCredential`.
- `Get-AzHDInsightJobOutput` a été mise à jour de façon à prendre en charge la précision de l’accès en fonction du rôle à la clé de stockage.
    - Les utilisateurs avec les rôles Opérateur, Contributeur et Propriétaire de cluster HDInsight ne seront pas affectés.
    - Les utilisateurs qui ont seulement le rôle Lecteur doivent spécifier explicitement le paramètre `DefaultStorageAccountKey`.
- `Revoke-AzHDInsightHttpServicesAccess` est désormais déconseillé. HTTP est désormais toujours activé. Cette cmdlet n’est donc plus nécessaire.
 Pour plus de détails, consultez le [guide de migration az.HDInsight](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight).

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Ajouter l’attribution de rôle Opérateur de cluster HDInsight à un utilisateur

Un utilisateur disposant du rôle [Contributeur](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) ou [Propriétaire](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) peut attribuer le rôle [Opérateur de cluster HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) aux utilisateurs auxquels vous voulez donner l’accès lecture/écriture à des valeurs de configuration sensibles de cluster HDInsight (telles que les informations d'identification de la passerelle du cluster et les clés de compte de stockage).

### <a name="using-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure (CLI)

La façon la plus simple pour ajouter cette attribution de rôle consiste à utiliser la commande `az role assignemnt create` dans Azure CLI.

> [!NOTE]
> Cette commande doit être exécutée par un utilisateur avec les rôles Contributeur ou Propriétaire, car ils sont les seuls à pouvoir accorder ces autorisations. Le `--assignee` correspond à l’adresse e-mail de l’utilisateur à qui vous souhaitez attribuer le rôle Opérateur de cluster HDInsight.

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

Vous pouvez également utiliser le portail Azure pour ajouter l’attribution du rôle Opérateur de cluster HDInsight à un utilisateur. Consultez la documentation, [Gérer l’accès aux ressources Azure à l’aide de RBAC et du portail Azure - Ajouter une attribution de rôle](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).
