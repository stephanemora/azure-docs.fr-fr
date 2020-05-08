---
title: Configuration DNS des points de terminaison privés Azure
description: En savoir plus sur la configuration DNS des points de terminaison privés Azure
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: 477a5ffa971120d1a98c09ac4ae8ebda1c82b770
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82209024"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Configuration DNS des points de terminaison privés Azure


Lors de la connexion à une ressource Private Link à l’aide d’un nom de domaine complet (FQDN) dans la chaîne de connexion, il est important de configurer correctement vos paramètres DNS pour résoudre l’adresse IP privée allouée. Les services Azure existants peuvent déjà avoir une configuration DNS à utiliser lors de la connexion à un point de terminaison public. Cette valeur doit être remplacée pour la connexion à l’aide de votre instance Private Endpoint. 
 
L’interface réseau associée à l’instance Private Endpoint contient l’ensemble complet des informations requises pour configurer votre DNS, y compris le nom de domaine complet et les adresses IP privées allouées pour une ressource Private Link donnée. 
 
Vous pouvez utiliser les options suivantes pour configurer vos paramètres DNS pour Private Endpoint : 
- **Utilisez le fichier d’hôte (recommandé uniquement pour les tests)** . Vous pouvez utiliser le fichier d’hôte sur une machine virtuelle pour remplacer le DNS.  
- **Utilisez une zone DNS privée**. Vous pouvez utiliser des [zones DNS privées](../dns/private-dns-privatednszone.md) pour remplacer la résolution DNS pour un point de terminaison privé donné. Une zone DNS privée peut être liée à votre réseau virtuel pour résoudre des domaines spécifiques.
- **Utilisez votre serveur DNS personnalisé**. Vous pouvez utiliser votre propre serveur DNS pour remplacer la résolution DNS pour une ressource Private Link donnée. Si votre [serveur DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) est hébergé sur un réseau virtuel, vous pouvez créer une règle de transfert DNS pour utiliser une zone DNS privée afin de simplifier la configuration de toutes les ressources Private Link.
 
> [!IMPORTANT]
> Il n’est pas recommandé de remplacer une zone utilisée activement pour résoudre des points de terminaison publics. Les connexions aux ressources ne peuvent pas être résolues correctement sans transfert DNS vers le DNS public. Pour éviter les problèmes, créez un autre nom de domaine ou suivez le nom suggéré pour chaque service ci-dessous. 

## <a name="azure-services-dns-zone-configuration"></a>Configuration de la zone DNS des services Azure
Les services Azure créent un enregistrement DNS de nom canonique (CNAME) sur le DNS public pour rediriger la résolution vers les noms de domaine privés suggérés. Vous serez en mesure de remplacer la résolution par l’adresse IP privée de vos instances Private Endpoint. 
 
Vos applications n’ont pas besoin de modifier l’URL de connexion. Lors d’une tentative de résolution à l’aide d’un DNS public, le serveur DNS est maintenant résolu sur vos instances Private Endpoint. Le processus n’a pas d’impact sur vos applications existantes. 

Pour les services Azure, utilisez les noms de zone recommandés comme indiqué dans le tableau suivant :

|Type de ressource Private Link   |Sous-ressource  |Nom de la zone  |
|---------|---------|---------|
|SQL DB (Microsoft.Sql/servers)    |  Sql Server (sqlServer)        |   privatelink.database.windows.net       |
|Azure Synapse Analytics (Microsoft.Sql/servers)    |  Sql Server (sqlServer)        | privatelink.database.windows.net |
|Compte de stockage (Microsoft.Storage/storageAccounts)    |  Blob (blob, blob_secondary)        |    privatelink.blob.core.windows.net      |
|Compte de stockage (Microsoft.Storage/storageAccounts)    |    Table (table, table_secondary)      |   privatelink.table.core.windows.net       |
|Compte de stockage (Microsoft.Storage/storageAccounts)    |    File d'attente (queue, queue_secondary)     |   privatelink.queue.core.windows.net       |
|Compte de stockage (Microsoft.Storage/storageAccounts)   |    Fichier (file, file_secondary)      |    privatelink.file.core.windows.net      |
|Compte de stockage (Microsoft.Storage/storageAccounts)     |  Web (web, web_secondary)        |    privatelink.web.core.windows.net      |
|Système de fichiers Data Lake Gen2 (Microsoft.Storage/storageAccounts)  |  Système de fichiers Data Lake Gen2 (dfs, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|SQL    |privatelink.documents.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|MongoDB    |privatelink.mongo.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Gremlin    |privatelink.gremlin.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Table de charge de travail|privatelink.table.cosmos.azure.com|
|Azure Database pour PostgreSQL – Serveur unique (Microsoft.DBforPostgreSQL/servers)|postgresqlServer|privatelink.postgres.database.azure.com|
|Azure Database pour MySQL (Microsoft.DBforMySQL/servers)|mysqlServer|privatelink.mysql.database.azure.com|
|Azure Database pour MariaDB (Microsoft.DBforMariaDB/servers)|mariadbServer|privatelink.mariadb.database.azure.com|
|Azure Key Vault (Microsoft.KeyVault/vaults)|coffre|privatelink.vaultcore.azure.net|
|Azure Kubernetes Service – API Kubernetes (Microsoft.ContainerService/managedClusters)    | managedCluster | {guid}.privatelink.{région}.azmk8s.io|
|Recherche Azure (Microsoft.Search/searchServices)|searchService|privatelink.search.windows.net|   
|Azure Container Registry (Microsoft.ContainerRegistry/registries) | Registre | privatelink.azurecr.io |
|Azure App Configuration (Microsoft.Appconfiguration/configurationStores)| configurationStore | privatelink.azconfig.io|
|Sauvegarde Azure (Microsoft.RecoveryServices/vaults)| coffre |privatelink.{region}.backup.windowsazure.com|
|Azure Event Hub (Microsoft.EventHub/namespaces)| espace de noms |privatelink.servicebus.windows.net|
|Azure Service Bus (Microsoft.ServiceBus/namespaces) | espace de noms |privatelink.servicebus.windows.net|
|Azure Relay (Microsoft.Relay/namespaces) | espace de noms |privatelink.servicebus.windows.net|
|Azure Event Grid (Microsoft.EventGrid/topics)     | topic | topic.{region}.privatelink.eventgrid.azure.net|
|Azure Event Grid (Microsoft.EventGrid/domains) | domaine | domain.{region}.privatelink.eventgrid.azure.net |
|Azure WebApps (Microsoft.Web/sites)    | site | privatelink.azurewebsites.net |
|Azure Machine Learning(Microsoft.MachineLearningServices/workspaces)    | espace de travail | privatelink.api.azureml.ms |
 


## <a name="dns-configuration-scenarios"></a>Scénarios de configuration DNS

Le nom de domaine complet des services résout une adresse IP publique, vous devez modifier votre configuration DNS pour résoudre l’adresse IP privée du point de terminaison privé.

Le DNS est un composant essentiel qui permet à l’application de fonctionner correctement en la résolvant de manière appropriée à l’adresse IP du point de terminaison privé.

Selon vos préférences, les scénarios suivants sont disponibles pour la résolution DNS intégrée :

- [Charges de travail de Réseau virtuel sans serveur DNS personnalisé](#virtual-network-workloads-without-custom-dns-server)


## <a name="virtual-network-workloads-without-custom-dns-server"></a>Charges de travail de Réseau virtuel sans serveur DNS personnalisé

Cette configuration est appropriée pour les charges de travail de réseau virtuel sans serveur DNS personnalisé. Dans ce scénario, le client recherche l’adresse IP du point de terminaison privé sur le DNS fourni par Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). Azure DNS est responsable de la résolution DNS des zones DNS privées.


 > [!NOTE]
> Ce scénario utilise la zone DNS privée recommandée pour la base de données Azure SQL. Pour les autres services, vous pouvez ajuster le modèle à l’aide de la référence suivante : [Configuration de la zone DNS des services Azure](#azure-services-dns-zone-configuration).

Pour la configurer correctement, vous avez besoin des ressources suivantes :

- Réseau virtuel client

- Zone DNS privée [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) avec [Type d’enregistrement A](../dns/dns-zones-records.md#record-types)

- Informations sur le point de terminaison privé (nom d’enregistrement FQDN et adresse IP privée)

Le diagramme suivant illustre la séquence de résolution DNS des charges de travail de réseau virtuel à l’aide d’une zone DNS privée

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="réseau virtuel unique et DNS fourni par Azure":::

Ce modèle peut être étendu à plusieurs réseaux virtuels homologués qui sont associés au même point de terminaison privé. Pour ce faire, vous pouvez [ajouter de nouveaux liens de réseau virtuel](../dns/private-dns-virtual-network-links.md) à la zone DNS privée pour tous les réseaux virtuels homologués.

 > [!IMPORTANT]
>  Une seule zone DNS privée est requise pour cette configuration ; la création de plusieurs zones portant le même nom pour différents réseaux virtuels nécessiterait des opérations manuelles pour fusionner les enregistrements DNS.

Dans ce scénario, il existe une topologie de mise en réseau [Hub and Spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) avec les réseaux spoke partageant un point de terminaison privé commun, et tout le réseau virtuel spoke est lié à la même zone DNS privée. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="hub and spoke avec DNS fourni par Azure":::


## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus sur les points de terminaison privés](private-endpoint-overview.md)
