---
title: Qu’est-ce qu’Azure Private Endpoint ?
description: En savoir plus sur Azure Private Endpoint
services: private-link
author: asudbring
ms.service: private-link
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: allensu
ms.openlocfilehash: 485cd0d3d7ce2c64bfb0f37e07d785dee8808e3a
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130062668"
---
# <a name="what-is-azure-private-endpoint"></a>Qu’est-ce qu’Azure Private Endpoint ?

Un point de terminaison privé est une interface réseau qui utilise une adresse IP privée de votre réseau virtuel. Cette interface réseau vous connecte de manière privée et sécurisée à un service fonctionnant avec Azure Private Link. En activant un point de terminaison privé, vous intégrez le service à votre réseau virtuel.

Le service peut être l’un des services Azure suivants :

* Stockage Azure
* Azure Cosmos DB
* Azure SQL Database
* Votre propre service utilisant un [Service de liaison privée](private-link-service-overview.md).
  
## <a name="private-endpoint-properties"></a>Propriétés de Private Endpoint 
 Private Endpoint spécifie les propriétés suivantes : 

|Propriété  |Description |
|---------|---------|
|Nom    |    Nom unique dans le groupe de ressources.      |
|Subnet    |  Sous-réseau à déployer et emplacement où l’adresse IP privée est attribuée. Pour connaître les impératifs liés au sous-réseau, consultez la section Limitations de cet article.         |
|Ressource Private Link    |   Ressource Private Link à connecter avec l’ID de ressource ou l’alias dans la liste des types disponibles. Un identificateur réseau unique sera généré pour tout le trafic envoyé à cette ressource.       |
|Sous-ressource cible   |      Sous-ressource à connecter. Chaque type de ressource Private Link a différentes options à sélectionner en fonction de vos préférences.    |
|Méthode d’approbation de la connexion    |  Automatique ou manuel. Selon les autorisations de contrôle d’accès en fonction du rôle Azure, votre point de terminaison privé peut être approuvé automatiquement. Si vous essayez de vous connecter à une ressource de liaison privée sans contrôle d’accès en fonction du rôle Azure, utilisez la méthode manuelle pour permettre au propriétaire de la ressource d’approuver la connexion.        |
|Message de requête     |  Vous pouvez spécifier un message pour que les connexions demandées soient approuvées manuellement. Ce message peut être utilisé pour identifier une demande spécifique.        |
|État de la connexion   |   Propriété en lecture seule qui spécifie si Private Endpoint est actif. Seules les instances Private Endpoint dans un état approuvé peuvent être utilisées pour envoyer du trafic. Autres états disponibles : <br>-**Approuvé** : La connexion a été approuvée automatiquement ou manuellement et est prête à être utilisée.</br><br>-**En attente** : La connexion a été créée manuellement et est en attente d’approbation par le propriétaire de la ressource Private Link.</br><br>-**Rejeté** : La connexion a été rejetée par le propriétaire de la ressource Private Link.</br><br>-**Déconnecté** : La connexion a été supprimée par le propriétaire de la ressource Private Link. Private Endpoint devient informatif et doit être supprimé pour le nettoyage. </br>|

Voici quelques détails clés sur les points de terminaison privés : 

- Le point de terminaison privé permet la connectivité entre les consommateurs :
    
    - Réseau virtuel
    - réseaux virtuels avec peering régional ;
    - réseaux virtuels avec peering mondial ;
    - Accès local par [VPN](https://azure.microsoft.com/services/vpn-gateway/) ou [ExpressRoute](https://azure.microsoft.com/services/expressroute/)
    - Services fonctionnant avec Private Link
 
- Des connexions réseau ne peuvent être établies que par des clients se connectant au point de terminaison privé. Les fournisseurs de services n’ont pas de configuration de routage pour établir des connexions avec des consommateurs du service. Les connexions ne peuvent être établies que dans une seule direction.

- Lors de la création d’un point de terminaison privé, une interface réseau en lecture seule est créée pour le cycle de vie de la ressource. Une adresse IP privée dynamique est attribuée à l’interface à partir du sous-réseau qui est mappé à la ressource de liaison privée. La valeur de l’adresse IP privée reste inchangée pour l’intégralité du cycle de vie du point de terminaison privé.
 
- Le point de terminaison privé doit être déployé dans la même région et dans le même abonnement que le réseau virtuel. 
 
- La ressource Private Link peut être déployée dans une autre région que le réseau virtuel et Private Endpoint.
 
- Vous pouvez créer plusieurs instances Private Endpoint à l’aide de la même ressource Private Link. Dans le cas d’un seul réseau utilisant une configuration de serveur DNS courante, il est recommandé d’utiliser un seul point de terminaison privé pour une ressource Private Link donnée. Utilisez cette bonne pratique pour éviter les entrées en double ou les conflits de résolution DNS. 
 
- Plusieurs instances Private Endpoint privées peuvent être créées sur des sous-réseaux identiques ou différents au sein du même réseau virtuel. Il existe des limites au nombre d’instances Private Endpoint que vous pouvez créer dans un abonnement. Pour plus d’informations, consultez  [Limites Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

- L’abonnement de la ressource de liaison privée doit également être inscrit auprès de Microsoft. Fournisseur de ressources réseau. Pour plus d’informations, consultez  [Fournisseurs de ressources Azure](../azure-resource-manager/management/resource-providers-and-types.md).
 
## <a name="private-link-resource"></a>Ressource Private Link 
Une ressource Private Link est la cible de destination d’une instance Private Endpoint donnée. 

Le tableau ci-dessous liste les ressources disponibles qui prennent en charge un point de terminaison privé : 
 
| Nom de la ressource Private Link | Type de ressource | Sous-ressources |
| ---------------------------| ------------- | ------------- |
| **Azure App Configuration** | Microsoft.Appconfiguration/configurationStores | configurationStores |
| **Azure Automation** | Microsoft.Automation/automationAccounts | Webhook, DSCAndHybridWorker |
| **Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts | Sql, MongoDB, Cassandra, Gremlin, Table |
| **Azure Batch** | Microsoft.Batch/batchAccounts | compte Batch |
| **Cache Azure pour Redis** | Microsoft.Cache/Redis | redisCache |
| **Azure Cache pour Redis Entreprise** | Microsoft.Cache/redisEnterprise | redisEnterprise |
| **Cognitive Services** | Microsoft.CognitiveServices/accounts | account |
| **Azure Disques managés** | Microsoft.Compute/diskAccesses | disque managé |
| **Azure Container Registry** | Microsoft.ContainerRegistry/registries | Registre |
| **Azure Kubernetes Service – API Kubernetes** | Microsoft.ContainerService/managedClusters | gestion |
| **Azure Data Factory**. | Microsoft.DataFactory/factories | fabrique de données |
| **Azure Database for MariaDB** | Microsoft.DBforMariaDB/servers | mariadbServer |
| **Azure Database pour MySQL** | Microsoft.DBforMySQL/servers | mysqlServer |
| **Azure Database pour PostgreSQL – Serveur unique** | Microsoft.DBforPostgreSQL/servers | postgresqlServer |
| **Azure IoT Hub** | Microsoft.Devices/IotHubs | iotHub |
| **Microsoft Digital Twins** | Microsoft.DigitalTwins/digitalTwinsInstances | digitaltwinsinstance |
| **Azure Event Grid** | Microsoft.EventGrid/domains | domaine |
| **Azure Event Grid** | Microsoft.EventGrid/topics  | Rubrique Event Grid |
| **Azure Event Hub** | Microsoft.EventHub/namespaces | espace de noms |
| **Azure HDInsight** | Microsoft.HDInsight/clusters | cluster |
| **Azure API pour FHIR** | Microsoft.HealthcareApis/services | service |
| **HSM Azure Keyvault** | Microsoft.Keyvault/managedHSMs | HSM |
| **Azure Key Vault** | Microsoft.KeyVault/vaults | coffre |
| **Azure Machine Learning** | Microsoft.MachineLearningServices/workspaces | amlworkspace |
| **Azure Migrate** | Microsoft.Migrate/assessmentProjects | project |
| **Application Gateway** | Microsoft.Network/applicationgateways | application gateway |
| **Service Private Link** (votre propre service) |  Microsoft.Network/privateLinkServices | empty |
| **Power BI** | Microsoft.PowerBI/privateLinkServicesForPowerBI | Power BI |
| **Azure Purview** | Microsoft.Purview/accounts | account |
| **Azure Purview** | Microsoft.Purview/accounts | portail |
| **Azure Backup** | Microsoft.RecoveryServices/vaults | coffre |
| **Azure Relay** | Microsoft.Relay/namespaces | espace de noms |
| **Recherche Microsoft** | Microsoft.Search/searchServices | service de recherche |
| **Azure Service Bus** | Microsoft.ServiceBus/namespaces | espace de noms |
| **SignalR** | Microsoft.SignalRService/SignalR | signalr |
| **SignalR** | Microsoft.SignalRService/webPubSub | webpubsub |
| **Azure SQL Database** | Microsoft.Sql/servers | Sql Server (sqlServer) |
| **Stockage Azure** | Microsoft.Storage/storageAccounts | Blob (blob, blob_secondary)<BR> Table (table, table_secondary)<BR> File d'attente (queue, queue_secondary)<BR> Fichier (file, file_secondary)<BR> Web (web, web_secondary) |
| **Azure File Sync** | Microsoft.StorageSync/storageSyncServices | Service File Sync |
| **Azure Synapse** | Microsoft.Synapse/privateLinkHubs | synapse |
| **Azure Synapse Analytics** | Microsoft.Synapse/workspaces | Sql, SqlOnDemand, Dev | 
| **Azure App Service** | Microsoft.Web/hostingEnvironments | environnement d'hébergement |
| **Azure App Service** | Microsoft.Web/sites | sites |
| **Azure App Service** | Microsoft.Web/staticSites | staticSite |

## <a name="network-security-of-private-endpoints"></a>Sécurité réseau de Private Endpoint 

Quand vous utilisez des points de terminaison privés, le trafic est sécurisé vers une ressource Private Link. La plateforme effectue un contrôle d’accès pour valider les connexions réseau qui atteignent uniquement la ressource Private Link spécifiée. Pour accéder à d’autres ressources au sein du même service Azure, des points de terminaison privés supplémentaires sont requis. 
 
Vous pouvez verrouiller complètement vos charges de travail d’accès aux points de terminaison publics pour vous connecter à un service Azure pris en charge. Ce contrôle fournit une couche de sécurité réseau supplémentaire à vos ressources. La sécurité fournit une protection qui empêche l’accès aux autres ressources hébergées sur le même service Azure. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Accès à une ressource Private Link à l’aide du flux de travail d’approbation 
Vous pouvez vous connecter à une ressource Private Link à l’aide des méthodes d’approbation de connexion suivantes :
- Approuvé **automatiquement** lorsque vous êtes propriétaire de la ressource Private Link spécifique ou que vous en avez l’autorisation. L’autorisation requise est basée sur le type de ressource de liaison privée au format suivant : Microsoft.\<Provider>/<resource_type>/privateEndpointConnectionsApproval/action
- Demande **manuelle** lorsque vous n’avez pas l’autorisation requise et que vous souhaitez demander l’accès. Un workflow d’approbation est lancé. Le point de terminaison privé et les connexions de point de terminaison privé ultérieures sont créés dans un état « En attente ». Le propriétaire de la ressource Private Link est responsable de l’approbation de la connexion. Une fois approuvé, Private Endpoint est activé pour envoyer le trafic normalement, comme indiqué dans le diagramme de flux de travail d’approbation suivant.  

![workflow d’approbation](media/private-endpoint-overview/private-link-paas-workflow.png)
 
Le propriétaire de la ressource de liaison privée peut effectuer les actions suivantes sur une connexion de point de terminaison privé : 

- Passez en revue tous les détails des connexions Private Endpoint. 
- Approuvez une connexion Private Endpoint. Le Private Endpoint correspondant sera activé pour envoyer le trafic vers la ressource Private Link. 
- Rejetez une connexion Private Endpoint. L’instance Private Endpoint correspondante sera mise à jour pour refléter cet état.
- Supprimez une connexion Private Endpoint dans n’importe quel état. L’instance Private Endpoint privée correspondante sera mise à jour avec un état Déconnecté pour refléter l’action ; le propriétaire de Private Endpoint peut uniquement supprimer la ressource à ce stade. 
 
> [!NOTE]
> Seule une instance Private Endpoint dans un état approuvé peut envoyer le trafic vers une ressource Private Link donnée. 

### <a name="connect-with-alias"></a>Se connecter avec un alias

Alias est un moniker unique qui est généré lorsque le propriétaire du service crée le service Private Link derrière un Standard Load Balancer. Le propriétaire du service peut partager cet alias avec leurs consommateurs hors connexion. 

Les consommateurs peuvent demander une connexion au service Private Link à l’aide de l’URI de ressource ou de l’alias. Si vous souhaitez vous connecter à l’aide de l’alias, vous devez créer un point de terminaison privé en utilisant la méthode d’approbation de connexion manuelle. Pour utiliser la méthode d’approbation de connexion manuelle, affectez la valeur true au paramètre de demande manuelle au cours de la création de votre Private Endpoint. Pour plus d’informations, consultez [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) et [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create).

## <a name="dns-configuration"></a>Configuration DNS

Les paramètres DNS utilisés pour les connexions à une ressource Private Link sont importants. Vérifiez que vos paramètres DNS sont corrects quand vous utilisez le nom de domaine complet (FQDN) pour la connexion. Les paramètres doivent correspondre à l’adresse IP privée du point de terminaison privé. Les services Azure existants peuvent déjà avoir une configuration DNS à utiliser lors de la connexion à un point de terminaison public. Cette configuration doit être modifiée pour une connexion à l’aide de votre point de terminaison privé. 
 
L’interface réseau associée au point de terminaison privé contient les informations nécessaires à la configuration de votre DNS. Les informations incluent le nom FQDN et l’adresse IP privée d’une ressource Private Link. 

Pour obtenir des informations détaillées complètes sur les recommandations relatives à la configuration du DNS pour les points de terminaison privés, consultez [Configuration DNS des points de terminaison privés](private-endpoint-dns.md).
 
## <a name="limitations"></a>Limites
 
Le tableau suivant répertorie les limitations connues lors de l’utilisation de Private Endpoint : 

| Limitation | Description |Limitation des risques |
| --------- | --------- | --------- |
| Le trafic destiné à un point de terminaison privé, qui utilise une route définie par l’utilisateur peut être asymétrique. | Le trafic de retour provenant d’un point de terminaison privé ignore une appliance virtuelle réseau (NVA) et tente de retourner à la machine virtuelle source. | La fonctionnalité SNAT (traduction d’adresses réseau source) est utilisée pour garantir un routage symétrique. Pour l’ensemble du trafic destiné à un point de terminaison privé via une route définie par l’utilisateur, il est recommandé d’appliquer au trafic la fonctionnalité SNAT (traduction d’adresses réseau source) au niveau de l’appliance virtuelle réseau (NVA). |

> [!IMPORTANT]
> La prise en charge du groupe de sécurité réseau et de l'UDR pour les points de terminaison privés est en préversion publique dans certaines régions. Pour plus d’informations, consultez [Préversion publique de la prise en charge de l’UDR de liaison privée](https://azure.microsoft.com/updates/public-preview-of-private-link-udr-support/) et [Préversion publique de la prise en charge du groupe de sécurité réseau de liaison privée](https://azure.microsoft.com/updates/public-preview-of-private-link-network-security-group-support/).
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="public-preview-limitations"></a>Limitations de la version préliminaire publique

### <a name="nsg"></a>Groupe de sécurité réseau

| Limitation | Description | Limitation des risques |
| ---------- | ----------- | ---------- |
| L’obtention de routes et de règles de sécurité efficaces n’est pas disponible sur une interface réseau de point de terminaison privé. | Vous ne pouvez pas accéder à l’interface réseau pour voir les informations appropriées relatives aux routes et aux règles de sécurité efficaces. | Q4CY21 |
| Journaux de flux NSG non pris en charge. | Les journaux de flux NSG ne fonctionnent pas pour le trafic entrant destiné à un point de terminaison privé. | Aucune information n’est disponible pour le moment. |
| Pertes intermittentes avec les comptes de stockage ZRS. | Les clients qui utilisent un compte de stockage ZRS peuvent constater des pertes intermittentes périodiques, même si le groupe NSG est autorisé sur le sous-réseau du point de terminaison privé du stockage. | Septembre |
| Pertes intermittentes avec Azure Key Vault. | Les clients qui utilisent Azure Key Vault peuvent constater des pertes intermittentes périodiques, même si le groupe NSG est autorisé sur le sous-réseau du point de terminaison privé d’Azure Key Vault. | Septembre |
| Limite du nombre de préfixes d’adresses par groupe NSG. | L’utilisation de plus de 500 préfixes d’adresses dans un groupe NSG au sein d’une seule règle n’est pas prise en charge. | Septembre |
| Indicateur AllowVirtualNetworkAccess | Les clients qui définissent VNET Peering sur leur VNet (VNet A) avec l’indicateur **AllowVirtualNetworkAccess** ayant la valeur false pour le lien de peering vers un autre VNet (VNet B), ne peuvent pas utiliser l’étiquette **VirtualNetwork** afin d’empêcher le trafic du VNet B d’accéder aux ressources du point de terminaison privé. Ils doivent bloquer explicitement le préfixe d’adresse du VNet B pour empêcher le trafic d’accéder au point de terminaison privé. | Septembre |
| Règles NSG à plusieurs plages de ports non prises en charge. | Si plusieurs plages de ports sont utilisées avec des règles NSG, seule la première plage de ports est prise en compte pour les règles d’autorisation et de refus. Par défaut, les règles avec plusieurs plages de ports refusent tout au lieu de refuser l’accès à des ports spécifiques. </br> **Pour plus d’informations, consultez l’exemple de règle ci-dessous.** | Septembre |

| Priority | Port source | Port de destination | Action | Action effective |
| -------- | ----------- | ---------------- | ------ | ---------------- |
| 10 | 10-12 | 10-12 |  Autoriser/Refuser | Une seule plage de ports dans les ports sources/de destination fonctionne comme prévu. |
| 10 | 10-12, 13-14 | 14-15, 16-17 | Autoriser | Seuls les ports sources 10-12 et les ports de destination 14-15 sont autorisés. |
| 10 | 10-12, 13-14 | 120-130, 140-150 | Deny | Le trafic de tous les ports sources est refusé vers tous les ports de destination, car il existe plusieurs plages de ports sources et de destination. |
| 10 | 10-12, 13-14 | 120-130 | Deny | Le trafic de tous les ports sources est refusé vers les ports de destination 120-130 uniquement. Il existe plusieurs plages de ports sources et une seule plage de ports de destination. |

**Table : Exemples de règles à plusieurs plages de ports.**

### <a name="udr"></a>UDR

| Limitation | Description | Limitation des risques |
| ---------- | ----------- | ---------- |
| La fonctionnalité SNAT (traduction d’adresses réseau source) est toujours recommandée. | En raison de la nature variable du plan de données du point de terminaison privé, il est recommandé d’appliquer la fonctionnalité SNAT au trafic destiné à un point de terminaison privé pour garantir la prise en compte du trafic de retour. | Aucune information n’est disponible pour le moment. |
 
## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les points de terminaison privés et les liaisons privées, consultez [Qu’est-ce qu’Azure Private Link ?](private-link-overview.md).
- Pour commencer à créer un point de terminaison privé pour une application web, consultez [Démarrage rapide - Créer un point de terminaison privé en utilisant le portail Azure](create-private-endpoint-portal.md).
