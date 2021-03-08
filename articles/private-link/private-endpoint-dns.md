---
title: Configuration DNS des points de terminaison privés Azure
description: En savoir plus sur la configuration DNS des points de terminaison privés Azure
services: private-link
author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: allensu
ms.openlocfilehash: d0085dc1cd7afa1fd8f557db27d30fd76ca05fac
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732732"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Configuration DNS des points de terminaison privés Azure

Il est important de configurer correctement vos paramètres DNS pour résoudre l’adresse IP du point de terminaison privé en nom de domaine complet (FQDN) de la chaîne de connexion.

Les services Microsoft Azure existants sont susceptibles de disposer d’une configuration DNS pour un point de terminaison public. Cette configuration doit être remplacée pour la connexion à l’aide de votre point de terminaison privé. 
 
L’interface réseau associée au point de terminaison privé contient les informations permettant de configurer votre DNS. Les informations de l’interface réseau incluent le nom de domaine complet et les adresses IP privées de votre ressource de liaison privée. 
 
Vous pouvez utiliser les options suivantes pour configurer vos paramètres DNS pour Private Endpoint : 
- **Utilisez le fichier d’hôte (recommandé uniquement pour les tests)** . Vous pouvez utiliser le fichier d’hôte sur une machine virtuelle pour remplacer le DNS.  
- **Utilisez une zone DNS privée**. Vous pouvez utiliser des [zones DNS privées](../dns/private-dns-privatednszone.md) pour remplacer la résolution DNS pour un point de terminaison privé. Une zone DNS privée peut être liée à votre réseau virtuel pour résoudre des domaines spécifiques.
- **Utilisez votre redirecteur DNS (facultatif)** . Vous pouvez utiliser votre redirecteur DNS pour remplacer la résolution DNS par une ressource de liaison privée. Créez une règle de transfert DNS pour utiliser une zone DNS privée sur votre [serveur DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) hébergé dans un réseau virtuel.

> [!IMPORTANT]
> Il n'est pas recommandé de remplacer une zone utilisée activement pour résoudre des points de terminaison publics. Les connexions aux ressources ne peuvent pas être résolues correctement sans transfert DNS vers le DNS public. Pour éviter les problèmes, créez un autre nom de domaine ou suivez le nom suggéré pour chaque service ci-dessous. 

## <a name="azure-services-dns-zone-configuration"></a>Configuration de la zone DNS des services Azure
Azure crée un enregistrement de nom canonique (CNAME) DNS sur le DNS public. L’enregistrement CNAME redirige la résolution vers le nom de domaine privé. Vous pouvez remplacer la résolution par l’adresse IP privée de vos points de terminaison privés. 
 
Vos applications n’ont pas besoin de modifier l’URL de connexion. Lors de la résolution sur un service DNS public, le serveur DNS est résolu sur vos points de terminaison privés. Le processus n’a pas d’impact sur vos applications existantes. 

> [!IMPORTANT]
> Les réseaux privés utilisant déjà la zone DNS privée pour un type donné ne peuvent se connecter aux ressources publiques que s'ils ne disposent d'aucune connexion à un point de terminaison privé. Sinon, une configuration DNS correspondante est nécessaire sur la zone DNS privée afin de terminer la séquence de résolution DNS. 

Pour les services Azure, utilisez les noms de zone recommandés comme indiqué dans le tableau suivant :

| Type de ressource/Sous-ressource Private Link |Nom de la zone DNS privée | Redirecteurs de la zone DNS publique |
|---|---|---|
| Azure Automation / (Microsoft.Automation/automationAccounts) / Webhook, DSCAndHybridWorker | privatelink.azure-automation.net | azure-automation.net |
| Azure SQL Database (Microsoft.Sql/servers)/SQL Server | privatelink.database.windows.net | database.windows.net |
| Azure Synapse Analytics (Microsoft.Sql/servers)/SQL Server  | privatelink.database.windows.net | database.windows.net |
| Compte de stockage (Microsoft.Storage/storageAccounts) / Blob (blob, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| Compte de stockage (Microsoft.Storage/storageAccounts) / Table (table, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| Compte de stockage (Microsoft.Storage/storageAccounts) / File d’attente (file d’attente, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| Compte de stockage (Microsoft.Storage/storageAccounts) / Fichier (file, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| Compte de stockage (Microsoft.Storage/storageAccounts) / Web (web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Système de fichiers Azure Data Lake Gen2 (Microsoft.Storage/storageAccounts) / Système de fichiers Data Lake Gen2 (dfs, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Table | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Azure Database pour PostgreSQL - Serveur unique (Microsoft.DBforPostgreSQL/servers) / postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Azure Database pour MySQL (Microsoft.DBforMySQL/servers) / mysqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Azure Database pour MariaDB (Microsoft.DBforMariaDB/servers) / mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (Microsoft.KeyVault/vaults) / coffre | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Azure Kubernetes Service - Kubernetes API (Microsoft.ContainerService/managedClusters) / management | privatelink.{region}.azmk8s.io | {region}.azmk8s.io |
| Azure Search (Microsoft.Search/searchServices) / searchService | privatelink.search.windows.net | search.windows.net |
| Azure Container Registry (Microsoft.ContainerRegistry/registries) / registre | privatelink.azurecr.io | azurecr.io |
| Azure App Configuration (Microsoft.AppConfiguration/configurationStores) / configurationStore | privatelink.azconfig.io | azconfig.io |
| Sauvegarde Azure (Microsoft.RecoveryServices/vaults) / coffre | privatelink.{region}.backup.windowsazure.com | {region}.backup.windowsazure.com |
| Azure Site Recovery (Microsoft.RecoveryServices/vaults) / coffre | {region}.privatelink.siterecovery.windowsazure.com | {region}.hypervrecoverymanager.windowsazure.com |
| Azure Event Hubs (Microsoft.EventHub/namespaces) / espace de noms | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Service Bus (Microsoft.ServiceBus/namespaces) / espace de noms | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure IoT Hub (Microsoft.Devices/IotHubs) / iotHub | privatelink.azure-devices.net<br/>privatelink.servicebus.windows.net<sup>1</sup> | azure-devices.net<br/>servicebus.windows.net |
| Azure Relay (Microsoft.Relay/namespaces) / espace de noms | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Event Grid (Microsoft.EventGrid/topics) / rubrique | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Event Grid (Microsoft.EventGrid/domains) / domaine | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Web Apps (Microsoft.Web/sites) / sites | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning (Microsoft.MachineLearningServices/workspaces) / amlworkspace | privatelink.api.azureml.ms<br/>privatelink.notebooks.azure.net | api.azureml.ms<br/>notebooks.azure.net<br/>aznbcontent.net |
| SignalR (Microsoft.SignalRService/SignalR) / signalR | privatelink.service.signalr.net | service.signalr.net |
| Azure Monitor (Microsoft.Insights/privateLinkScopes) / azuremonitor | privatelink.monitor.azure.com<br/> privatelink.oms.opinsights.azure.com <br/> privatelink.ods.opinsights.azure.com <br/> privatelink.agentsvc.azure-automation.net | monitor.azure.com<br/> oms.opinsights.azure.com<br/> ods.opinsights.azure.com<br/> agentsvc.azure-automation.net |
| Cognitive Services (Microsoft.CognitiveServices/accounts) / account | privatelink.cognitiveservices.azure.com  | cognitiveservices.azure.com  |
| Azure File Sync (Microsoft.StorageSync/storageSyncServices)/afs |  privatelink.afs.azure.net  |  afs.azure.net  |
| Azure Data Factory (Microsoft.DataFactory/factories) / dataFactory |  privatelink.datafactory.azure.net  |  datafactory.azure.net  |
| Azure Data Factory (Microsoft.DataFactory/factories) / portal |  privatelink.azure.com  |  azure.com  |
| Azure Cache pour Redis (Microsoft.Cache/Redis) / redisCache | privatelink.redis.cache.windows.net | redis.cache.windows.net |

<sup>1</sup> A utiliser avec un point de terminaison compatible avec l’Event Hub intégré d’IoT Hub. Pour plus d’informations, consultez [Prise en charge de la liaison privée pour le point de terminaison intégré IoT Hub](../iot-hub/virtual-network-support.md#built-in-event-hub-compatible-endpoint)

### <a name="china"></a>Chine

| Type de ressource/Sous-ressource Private Link |Nom de la zone DNS privée | Redirecteurs de la zone DNS publique |
|---|---|---|
| Azure SQL Database (Microsoft.Sql/servers)/SQL Server | privatelink.database.chinacloudapi.cn | database.chinacloudapi.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / SQL | privatelink.documents.azure.cn | documents.azure.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / MongoDB | privatelink.mongo.cosmos.azure.cn | mongo.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Cassandra | privatelink.cassandra.cosmos.azure.cn | cassandra.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Gremlin | privatelink.gremlin.cosmos.azure.cn | gremlin.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Table | privatelink.table.cosmos.azure.cn | table.cosmos.azure.cn |
| Azure Database pour PostgreSQL - Serveur unique (Microsoft.DBforPostgreSQL/servers) / postgresqlServer | privatelink.postgres.database.chinacloudapi.cn | postgres.database.chinacloudapi.cn |
| Azure Database pour MySQL (Microsoft.DBforMySQL/servers) / mysqlServer | privatelink.mysql.database.chinacloudapi.cn  | mysql.database.chinacloudapi.cn  |
| Azure Database pour MariaDB (Microsoft.DBforMariaDB/servers) / mariadbServer | privatelink.mariadb.database.chinacloudapi.cn | mariadb.database.chinacloudapi.cn |


## <a name="dns-configuration-scenarios"></a>Scénarios de configuration DNS

Le nom de domaine complet des services se résout automatiquement en adresse IP publique. Pour résoudre l’adresse IP privée du point de terminaison privé, vous devez modifier votre configuration DNS.

Le DNS est un composant essentiel qui permet à l’application de fonctionner correctement en la résolvant correctement l’adresse IP du point de terminaison privé.

Selon vos préférences, les scénarios suivants sont disponibles avec la résolution DNS intégrée :

- [Charges de travail de réseau virtuel sans serveur DNS personnalisé](#virtual-network-workloads-without-custom-dns-server)
- [Charges de travail locales à l’aide d’un redirecteur DNS](#on-premises-workloads-using-a-dns-forwarder)
- [Réseau virtuel et charges de travail locales utilisant un redirecteur DNS](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder)

> [!NOTE]
> Le [proxy DNS de Pare-feu Azure](../firewall/dns-settings.md#dns-proxy) peut être utilisé en tant que redirecteur DNS pour des [charges de travail locales](#on-premises-workloads-using-a-dns-forwarder) et des [charges de travail de réseau virtuel à l’aide d’un redirecteur DNS](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder).

## <a name="virtual-network-workloads-without-custom-dns-server"></a>Charges de travail de réseau virtuel sans serveur DNS personnalisé

Cette configuration est appropriée pour les charges de travail de réseau virtuel sans serveur DNS personnalisé. Dans ce scénario, le client recherche l’adresse IP du point de terminaison privé sur le service DNS fourni par Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). Azure DNS est responsable de la résolution DNS des zones DNS privées.

> [!NOTE]
> Ce scénario utilise la zone DNS privée recommandée d’Azure SQL Database. Pour les autres services, vous pouvez ajuster le modèle à l’aide de la référence suivante : [Configuration de la zone DNS des services Azure](#azure-services-dns-zone-configuration).

Pour la configurer correctement, vous avez besoin des ressources suivantes :

- Réseau virtuel client

- Zone DNS privée [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) avec [Type d’enregistrement A](../dns/dns-zones-records.md#record-types)

- Informations sur le point de terminaison privé (nom d’enregistrement FQDN et adresse IP privée)

La capture d’écran suivante illustre la séquence de résolution DNS des charges de travail de réseau virtuel à l’aide d’une zone DNS privée :

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Réseau virtuel unique et DNS fourni par Azure":::

Vous pouvez étendre ce modèle à plusieurs réseaux virtuels appairés associés au même point de terminaison privé. [Ajouter de nouveaux liens de réseau virtuel](../dns/private-dns-virtual-network-links.md) à la zone DNS privée pour tous les réseaux virtuels appairés.

> [!IMPORTANT]
> Une seule zone DNS privée est requise pour cette configuration. La création de plusieurs zones portant le même nom pour différents réseaux virtuels nécessiterait des opérations manuelles pour fusionner les enregistrements DNS.

> [!IMPORTANT]
> Si vous utilisez un point de terminaison privé dans un modèle hub-and-spoke d’un autre abonnement, réutilisez la même zone DNS privée sur le hub.

Dans ce scénario, il existe une topologie de réseau [hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Les réseaux spoke partagent un point de terminaison privé. Les réseaux virtuels spoke sont liés à la même zone DNS privée. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="hub-and-spoke avec DNS fourni par Azure":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>Charges de travail locales à l’aide d’un redirecteur DNS

Pour que les charges de travail locales puissent résoudre le nom de domaine complet (FQDN) d’un point de terminaison privé, utilisez un redirecteur DNS pour résoudre la [zone DNS publique](#azure-services-dns-zone-configuration) du service Azure dans Azure.

Le scénario suivant concerne un réseau local qui a un redirecteur DNS dans Azure. Ce redirecteur résout les requêtes DNS par le biais d’un redirecteur au niveau du serveur vers le DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) fourni par Azure. 

> [!NOTE]
> Ce scénario utilise la zone DNS privée recommandée d’Azure SQL Database. Pour les autres services, vous pouvez ajuster le modèle à l’aide de la référence suivante : [Configuration de la zone DNS des services Azure](#azure-services-dns-zone-configuration).

Pour la configurer correctement, vous avez besoin des ressources suivantes :

- Réseau local
- Réseau virtuel [connecté à un site local](/azure/architecture/reference-architectures/hybrid-networking/)
- Redirecteur DNS déployé dans Azure 
- Zones DNS privées [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) avec [Type d'enregistrement A](../dns/dns-zones-records.md#record-types)
- Informations sur le point de terminaison privé (nom d’enregistrement FQDN et adresse IP privée)

Le diagramme suivant illustre la séquence de résolution DNS à partir d’un réseau local. La configuration utilise un redirecteur DNS déployé dans Azure. La résolution est effectuée par une zone DNS privée [liée à un réseau virtuel](../dns/private-dns-virtual-network-links.md) :

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Localement à l’aide d’Azure DNS":::

Cette configuration peut être étendue à un réseau local qui a déjà une solution DNS en place. La solution DNS locale est configurée pour transférer le trafic DNS vers Azure DNS via un [redirecteur conditionnel](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). Le redirecteur conditionnel référence le redirecteur DNS déployé dans Azure.

> [!NOTE]
> Ce scénario utilise la zone DNS privée recommandée d’Azure SQL Database. Pour les autres services, vous pouvez ajuster le modèle à l’aide de la référence suivante : [Configuration de la zone DNS des services Azure](#azure-services-dns-zone-configuration)

Pour la configurer correctement, vous avez besoin des ressources suivantes :

- Réseau local avec une solution DNS personnalisée en place 
- Réseau virtuel [connecté à un site local](/azure/architecture/reference-architectures/hybrid-networking/)
- Redirecteur DNS déployé dans Azure
- Zones DNS privées [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) avec [Type d'enregistrement A](../dns/dns-zones-records.md#record-types)
- Informations sur le point de terminaison privé (nom d’enregistrement FQDN et adresse IP privée)

Le diagramme suivant illustre la résolution DNS à partir d’un réseau local. La résolution DNS est transférée de manière conditionnelle vers Azure. La résolution est effectuée par une zone DNS privée [liée à un réseau virtuel](../dns/private-dns-virtual-network-links.md).

> [!IMPORTANT]
> Le transfert conditionnel doit être effectué vers le [redirecteur de la zone DNS public recommandé](#azure-services-dns-zone-configuration). Par exemple : `database.windows.net` au lieu de **privatelink**.database.windows.net.

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="Transfert local vers Azure DNS":::

## <a name="virtual-network-and-on-premises-workloads-using-a-dns-forwarder"></a>Réseau virtuel et charges de travail locales utilisant un redirecteur DNS

Pour les charges de travail qui accèdent à un point de terminaison privé à partir de réseaux virtuels et locaux, utilisez un redirecteur DNS pour résoudre la [zone DNS publique](#azure-services-dns-zone-configuration) du service Azure déployée dans Azure.

Le scénario suivant concerne un réseau local avec des réseaux virtuels dans Azure. Les deux réseaux accèdent au point de terminaison privé situé dans un réseau hub partagé.

Ce redirecteur DNS est chargé de résoudre toutes les requêtes DNS via un redirecteur au niveau du serveur vers le service DNS fourni par Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md).

> [!IMPORTANT]
> Une seule zone DNS privée est requise pour cette configuration. Toutes les connexions client effectuées à partir de [réseaux virtuels locaux homologués](../virtual-network/virtual-network-peering-overview.md) et doivent également utiliser la même zone DNS privée.

> [!NOTE]
> Ce scénario utilise la zone DNS privée recommandée d’Azure SQL Database. Pour les autres services, vous pouvez ajuster le modèle à l’aide de la référence suivante : [Configuration de la zone DNS des services Azure](#azure-services-dns-zone-configuration).

Pour la configurer correctement, vous avez besoin des ressources suivantes :

- Réseau local
- Réseau virtuel [connecté à un site local](/azure/architecture/reference-architectures/hybrid-networking/)
- [Réseau virtuel homologue](../virtual-network/virtual-network-peering-overview.md) 
- Redirecteur DNS déployé dans Azure
- Zones DNS privées [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) avec [Type d'enregistrement A](../dns/dns-zones-records.md#record-types)
- Informations sur le point de terminaison privé (nom d’enregistrement FQDN et adresse IP privée)

Le diagramme suivant montre la résolution DNS pour les deux types de réseaux, locaux et virtuels. La résolution utilise un redirecteur DNS. La résolution est effectuée par une zone DNS privée [liée à un réseau virtuel](../dns/private-dns-virtual-network-links.md) :

:::image type="content" source="media/private-endpoint-dns/hybrid-scenario.png" alt-text="Scénario hybride":::

## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus sur les points de terminaison privés](private-endpoint-overview.md)
