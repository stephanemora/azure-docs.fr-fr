---
title: Configuration DNS des points de terminaison privés Azure
description: En savoir plus sur la configuration DNS des points de terminaison privés Azure
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: 7c8ff0808ada522dc24ef3c27156f4151832fbcd
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715934"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Configuration DNS des points de terminaison privés Azure


Lors de la connexion à une ressource Private Link à l’aide d’un nom de domaine complet (FQDN) dans la chaîne de connexion, il est important de configurer correctement vos paramètres DNS pour résoudre l’adresse IP privée allouée. Les services Azure existants peuvent déjà avoir une configuration DNS à utiliser lors de la connexion à un point de terminaison public. Cette configuration doit être remplacée pour vous connecter à l’aide de votre point de terminaison privé. 
 
L’interface réseau associée à l’instance Private Endpoint contient l’ensemble complet des informations requises pour configurer votre DNS, y compris le nom de domaine complet et les adresses IP privées allouées pour une ressource Private Link donnée. 
 
Vous pouvez utiliser les options suivantes pour configurer vos paramètres DNS pour Private Endpoint : 
- **Utilisez le fichier d’hôte (recommandé uniquement pour les tests)** . Vous pouvez utiliser le fichier d’hôte sur une machine virtuelle pour remplacer le DNS.  
- **Utilisez une zone DNS privée**. Vous pouvez utiliser des [zones DNS privées](../dns/private-dns-privatednszone.md) pour remplacer la résolution DNS pour un point de terminaison privé donné. Une zone DNS privée peut être liée à votre réseau virtuel pour résoudre des domaines spécifiques.
- **Utiliser votre redirecteur DNS (facultatif)** . Vous pouvez utiliser votre redirecteur DNS pour remplacer la résolution DNS par une ressource de lien privé donnée. Si votre [serveur DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) est hébergé sur un réseau virtuel, vous pouvez créer une règle de transfert DNS pour utiliser une zone DNS privée afin de simplifier la configuration de toutes les ressources Private Link.
 
> [!IMPORTANT]
> Il n’est pas recommandé de remplacer une zone utilisée activement pour résoudre des points de terminaison publics. Les connexions aux ressources ne peuvent pas être résolues correctement sans transfert DNS vers le DNS public. Pour éviter les problèmes, créez un autre nom de domaine ou suivez le nom suggéré pour chaque service ci-dessous. 

## <a name="azure-services-dns-zone-configuration"></a>Configuration de la zone DNS des services Azure
Les services Azure créent un enregistrement DNS de nom canonique (CNAME) sur le DNS public pour rediriger la résolution vers les noms de domaine privés suggérés. Vous pouvez remplacer la résolution par l’adresse IP privée de vos points de terminaison privés. 
 
Vos applications n’ont pas besoin de modifier l’URL de connexion. Lors d’une tentative de résolution à l’aide d’un DNS public, le serveur DNS est maintenant résolu sur vos instances Private Endpoint. Le processus n’a pas d’impact sur vos applications existantes. 

Pour les services Azure, utilisez les noms de zone recommandés comme indiqué dans le tableau suivant :

| Type de ressource / sous-ressource Liaison privée |Nom de la zone DNS privée | Redirecteurs de la zone DNS publique |
|---|---|---|---|
| SQL DB (Microsoft.Sql/servers) / Sql Server | privatelink.database.windows.net | database.windows.net |
| Azure Synapse Analytics (Microsoft.Sql/servers) / Sql Server  | privatelink.database.windows.net | database.windows.net |
| Compte de stockage (Microsoft.Storage/storageAccounts) / Blob (blob, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| Compte de stockage (Microsoft.Storage/storageAccounts) / Table (table, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| Compte de stockage (Microsoft.Storage/storageAccounts) / File d’attente (file d’attente, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| Compte de stockage (Microsoft.Storage/storageAccounts) / Fichier (fichier, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| Compte de stockage (Microsoft.Storage/storageAccounts) / Web (web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Système de fichiers Data Lake Gen2 (Microsoft. Storage/storageAccounts) / Système de fichiers Data Lake Gen2 (dfs, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Table | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Azure Database pour PostgreSQL - Serveur unique (Microsoft.DBforPostgreSQL/servers) / postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Azure Database pour MySQL (Microsoft.DBforMySQL/servers) / mysqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Azure Database pour MariaDB (Microsoft.DBforMariaDB/servers) / mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (Microsoft.KeyVault/vaults) / coffre | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Azure Kubernetes Service - Kubernetes API (Microsoft.ContainerService/managedClusters) / managedCluster | privatelink.{region}.azmk8s.io | {region}.azmk8s.io |
| Azure Search (Microsoft.Search/searchServices) / searchService | privatelink.search.windows.net | search.windows.net |
| Azure Container Registry (Microsoft.ContainerRegistry/registries) / registre | privatelink.azurecr.io | azurecr.io |
| Azure App Configuration (Microsoft.AppConfiguration/configurationStores) / configurationStore | privatelink.azconfig.io | azconfig.io |
| Sauvegarde Azure (Microsoft.RecoveryServices/vaults) / coffre | privatelink.{region}.backup.windowsazure.com | {region}.backup.windowsazure.com |
| Azure Event Hub (Microsoft.EventHub/namespaces) / espace de noms | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Service Bus (Microsoft.ServiceBus/namespaces) / espace de noms | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Relay (Microsoft.Relay/namespaces) / espace de noms | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Event Grid (Microsoft.EventGrid/topics) / rubrique | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Event Grid (Microsoft.EventGrid/domains) / domaine | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure WebApps (Microsoft.Web/sites) / site | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning(Microsoft.MachineLearningServices/workspaces) / espace de travail | privatelink.api.azureml.ms | api.azureml.ms |

 


## <a name="dns-configuration-scenarios"></a>Scénarios de configuration DNS

Le nom de domaine complet des services effectue automatiquement une résolution vers une adresse IP publique, ainsi, afin d’effectuer une résolution vers l’adresse IP privée du point de terminaison privé vous devez modifier votre configuration DNS en conséquence.

Le DNS est un composant essentiel qui permet à l’application de fonctionner correctement en la résolvant de manière appropriée à l’adresse IP du point de terminaison privé.

Selon vos préférences, les scénarios suivants sont disponibles pour la résolution DNS intégrée :

- [Charges de travail de réseau virtuel sans serveur DNS personnalisé](#virtual-network-workloads-without-custom-dns-server)
- [Charges de travail locales à l’aide d’un redirecteur DNS](#on-premises-workloads-using-a-dns-forwarder)

## <a name="virtual-network-workloads-without-custom-dns-server"></a>Charges de travail de réseau virtuel sans serveur DNS personnalisé

Cette configuration est appropriée pour les charges de travail de réseau virtuel sans serveur DNS personnalisé. Dans ce scénario, le client recherche l’adresse IP du point de terminaison privé sur le DNS fourni par Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). Azure DNS est responsable de la résolution DNS des zones DNS privées.


> [!NOTE]
> Ce scénario utilise la zone DNS privée recommandée pour la base de données Azure SQL. Pour les autres services, vous pouvez ajuster le modèle à l’aide de la référence suivante : [Configuration de la zone DNS des services Azure](#azure-services-dns-zone-configuration).

Pour la configurer correctement, vous avez besoin des ressources suivantes :

- Réseau virtuel client

- Zone DNS privée [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) avec [Type d’enregistrement A](../dns/dns-zones-records.md#record-types)

- Informations sur le point de terminaison privé (nom d’enregistrement FQDN et adresse IP privée)

Le diagramme suivant illustre la séquence de résolution DNS des charges de travail de réseau virtuel à l’aide d’une zone DNS privée

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Réseau virtuel unique et DNS fourni par Azure":::

Ce modèle peut être étendu à plusieurs réseaux virtuels homologués qui sont associés au même point de terminaison privé. Pour ce faire, vous pouvez [ajouter de nouveaux liens de réseau virtuel](../dns/private-dns-virtual-network-links.md) à la zone DNS privée pour tous les réseaux virtuels homologués.

> [!IMPORTANT]
> Une seule zone DNS privée est requise pour cette configuration ; la création de plusieurs zones portant le même nom pour différents réseaux virtuels nécessiterait des opérations manuelles pour fusionner les enregistrements DNS.

Dans ce scénario, il existe une topologie de mise en réseau [hub-and-spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) avec les réseaux spoke partageant un point de terminaison privé commun et tout le réseau virtuel spoke est lié à la même zone DNS privée. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="hub-and-spoke avec DNS fourni par Azure":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>Charges de travail locales à l’aide d’un redirecteur DNS

Pour que les charges de travail locales puissent résoudre le nom de domaine complet d’un point de terminaison privé dans l’adresse IP privée, vous devez utiliser un redirecteur DNS pour que la résolution du service Azure [zone DNS publique](#azure-services-dns-zone-configuration) soit déployée dans Azure.


Le scénario suivant est approprié pour un réseau local qui dispose d’un redirecteur DNS dans Azure, qui à son tour est chargé de résoudre toutes les requêtes DNS via un redirecteur au niveau du serveur vers le DNS fourni par Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) 

> [!NOTE]
> Ce scénario utilise la base de données Azure SQL recommandée pour la zone DNS privée. Pour les autres services, vous pouvez ajuster le modèle à l’aide de la référence suivante :  [Configuration de la zone DNS des services Azure](#azure-services-dns-zone-configuration).

Pour la configurer correctement, vous avez besoin des ressources suivantes :

- Réseau local
- Réseau virtuel  [connecté localement](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Redirecteur DNS déployé dans Azure 
- Zones DNS privées  [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) avec [Type d’enregistrement A](../dns/dns-zones-records.md#record-types)
- Informations sur le point de terminaison privé (nom d’enregistrement FQDN et adresse IP privée)

Le diagramme suivant illustre la séquence de résolution DNS à partir d’un réseau local qui utilise un redirecteur DNS déployé dans Azure, où la résolution est effectuée par une zone DNS privée [liée à un réseau virtuel.](../dns/private-dns-virtual-network-links.md)

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Localement à l’aide d’Azure DNS":::

Cette configuration peut être étendue à un réseau local qui a déjà une solution DNS en place. 
La solution DNS locale doit être configurée pour transférer le trafic DNS vers le DNS Azure via un  [redirecteur conditionnel](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) référençant le redirecteur DNS déployé dans Azure.

> [!NOTE]
> 
Ce scénario utilise la base de données Azure SQL recommandée pour la zone DNS privée. Pour les autres services, vous pouvez ajuster le modèle à l’aide de la référence suivante :  [Configuration de la zone DNS des services Azure](#azure-services-dns-zone-configuration).

Pour la configurer correctement, vous avez besoin des ressources suivantes :

- Réseau local avec une solution DNS personnalisée en place 
- Réseau virtuel  [connecté localement](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Redirecteur DNS déployé dans Azure
- Zones DNS privées  [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)  avec [Type d’enregistrement A](../dns/dns-zones-records.md#record-types)
- Informations sur le point de terminaison privé (nom d’enregistrement FQDN et adresse IP privée)

Le diagramme suivant illustre la séquence de résolution DNS à partir d’un réseau local qui transfère de façon conditionnelle le trafic DNS dans Azure, où la résolution est effectuée par une zone DNS privée  [liée à un réseau virtuel.](../dns/private-dns-virtual-network-links.md)

> [!IMPORTANT]
> Le transfert conditionnel doit être effectué vers le [**redirecteur de la zone DNS public**](#azure-services-dns-zone-configuration) recommandé  Par exemple : `database.windows.net` , au lieu de  **privatelink**.database.windows.net

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="Transfert local vers Azure DNS":::


## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus sur les points de terminaison privés](private-endpoint-overview.md)
