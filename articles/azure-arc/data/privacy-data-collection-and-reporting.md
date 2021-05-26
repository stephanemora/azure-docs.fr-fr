---
title: Collecte de données et création de rapports | Services de données avec Azure Arc
description: Décrit le type de données transmises par les services de données avec Azure Arc à Microsoft.
author: MikeRayMSFT
ms.author: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: conceptual
ms.date: 04/27/2021
ms.custom: template-concept
ms.openlocfilehash: dcaf900164e3d9d4b9b3b54e27a79fdae937abc5
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110472766"
---
# <a name="azure-arc-data-services-data-collection-and-reporting"></a>Collecte de données et création de rapports des services de données avec Azure Arc

Cet article décrit les données que les services de données avec Azure Arc transmettent à Microsoft. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="related-products"></a>Produits connexes

Les services de données avec Azure Arc peuvent utiliser la totalité ou une partie des produits suivants :

- SQL MI – Azure Arc 
- PostgreSQL Hyperscale – Azure Arc
- Azure Data Studio
- Azure CLI (az)
- Azure Data CLI (`azdata`) 

## <a name="directly-connected"></a>Connecté directement

Lorsqu’un cluster est configuré pour être connecté directement à Azure, certaines données sont transmises automatiquement à Microsoft. 

Le tableau suivant décrit le type de données, la manière dont elles sont envoyées et le caractère obligatoire.  

|Catégorie de données|Quelles données sont envoyées ?|Comment sont-elles envoyées ?|Est-ce obligatoire ?
|:----|:----|:----|:----|
|Données opérationnelles|Métriques et journaux|Automatique, lorsqu’elles sont configurées pour cela|Non
Facturation et données d’inventaire|Inventaire, comme le nombre d’instances, et utilisation, comme le nombre de vCores consommés|Automatique |Oui
Diagnostics|Informations de diagnostic à des fins de résolution des problèmes|Exportées manuellement et fournies à Support Microsoft|Uniquement dans le cadre de la résolution des problèmes et conformément aux [politiques de confidentialité](https://privacy.microsoft.com/privacystatement) standard
Customer Experience Improvement Program (CEIP)|[Résumé du CEIP](/sql/sql-server/usage-and-diagnostic-data-configuration-for-sql-server)|Automatique, si autorisé|Non

## <a name="indirectly-connected"></a>Connecté indirectement

Lorsqu’un cluster n’est pas configuré pour être connecté directement à Azure, il ne transmet pas automatiquement les données opérationnelles ou de facturation et d’inventaire à Microsoft. Pour transmettre des données à Microsoft, vous devez configurer l’exportation. 

Le tableau suivant décrit le type de données, la manière dont elles sont envoyées et le caractère obligatoire.  

|Catégorie de données|Quelles données sont envoyées ?|Comment sont-elles envoyées ?|Est-ce obligatoire ?
|:----|:----|:----|:----|
|Données opérationnelles|Métriques et journaux|Manuel|Non
Facturation et données d’inventaire|Inventaire, comme le nombre d’instances, et utilisation, comme le nombre de vCores consommés|Manuel |Oui
Diagnostics|Informations de diagnostic à des fins de résolution des problèmes|Exportées manuellement et fournies à Support Microsoft|Uniquement dans le cadre de la résolution des problèmes et conformément aux [politiques de confidentialité](https://privacy.microsoft.com/privacystatement) standard
Customer Experience Improvement Program (CEIP)|[Résumé du CEIP](/sql/sql-server/usage-and-diagnostic-data-configuration-for-sql-server)|Automatique, si autorisé|Non

## <a name="detailed-description-of-data"></a>Description détaillée des données

Cette section fournit plus de détails sur les informations incluses dans les services de données avec Azure Arc et transmises à Microsoft.

### <a name="operational-data"></a>Données opérationnelles

Les données opérationnelles sont collectées pour toutes les instances de base de données et pour la plateforme des services de données avec Azure Arc elle-même. Il existe deux types de données opérationnelles : 

- Métriques : Métriques liées aux performances et à la capacité, qui sont collectées dans une base de données InfluxDB fournie dans le cadre des services de données avec Azure Arc. Vous pouvez consulter ces métriques dans le tableau de bord Grafana fourni. 

- Journaux : Les journaux émis par tous les composants, y compris les événements d’échec, d’avertissement et d’information, sont collectés dans une base de données Elasticsearch fournie dans le cadre des services de données avec Azure Arc. Vous pouvez consulter les journaux dans le tableau de bord Kibana fourni. 

Les données opérationnelles stockées localement nécessitent des privilèges administratifs intégrés pour être visualisées dans Grafana/Kibana. 

Les données opérationnelles ne quittent pas votre environnement, sauf si vous choisissez d’exporter/de charger (mode connecté indirectement) ou d’envoyer automatiquement (mode connecté directement) les données vers Azure Monitor/Log Analytics. Les données sont placées dans un espace de travail Log Analytics, que vous contrôlez. 

Si les données sont envoyées à Azure Monitor ou Log Analytics, vous pouvez choisir la région ou le centre de données Azure dans lequel l’espace de travail Log Analytics réside. Après cela, vous pouvez contrôler l’accès pour les visualiser ou les copier à partir d’autres emplacements. 

### <a name="billing-and-inventory-data"></a>Données de facturation et d’inventaire 

Les données de facturation sont utilisées dans le cadre du suivi de l’utilisation facturable. Ces données sont essentielles au fonctionnement du service et doivent être transmises manuellement ou automatiquement dans tous les modes. 

Chaque instance de base de données et le contrôleur de données proprement dit seront reflétés dans Azure en tant que ressource Azure dans Azure Resource Manager. 

Il existe trois types de ressources : 

- SQL Managed Instance avec Azure Arc 
- Groupe de serveurs PostgreSQL Hyperscale avec Azure Arc 
- SQL Server avec Azure Arc 
- Contrôleur de données 

Les sections suivantes présentent les propriétés, les types et les descriptions qui sont collectés et stockés pour chaque type de ressource : 

### <a name="arc-enabled-sql-server"></a>SQL Server avec Azure Arc 
- Édition de SQL Server. 
   - `string: Edition` 
- ID de ressource de la ressource de conteneur (Azure Arc pour les serveurs). 
   - `string: ContainerResourceId` 
- Heure de création de la ressource. 
   - `string: CreateTime` 
- Nombre de processeurs logiques utilisés par l’instance SQL.
   - `string: VCore` 
- État de la connectivité au cloud. 
   - `string: Status` 
- Niveau de mise à jour de SQL Server. 
   - `string: PatchLevel` 
- Classement SQL Server. 
   - `string: Collation`
- Version actuelle de SQL Server.
   - `string: CurrentVersion`
- Nom de l’instance SQL. 
   - `string: InstanceName`
- Ports TCP dynamiques utilisés par SQL Server. 
   - `string: TcpDynamicPorts`
- Ports TCP statiques utilisés par SQL Server.
   - `string: TcpStaticPorts` 
- ID du produit SQL Server.
   - `string: ProductId`
- État d’approvisionnement de SQL Server.
   - `string: ProvisioningState`

### <a name="data-controller"></a>Contrôleur de données 

- Informations sur l’emplacement
   - `public OnPremiseProperty OnPremiseProperty` 
- Informations Kubernetes brutes (`kubectl get datacontroller`) 
   - `object: K8sRaw` 
- Date du dernier chargement à partir du cluster local
   - `System.DateTime: LastUploadedDate` 
- État du contrôleur de données
   - `string: ProvisioningState` 

### <a name="postgresql-hyperscale-server-group"></a>Groupe de serveurs PostgreSQL Hyperscale 

- ID du contrôleur de données
   - `string: DataControllerId`
- Nom de l’administrateur de l’instance
   - `string: Admin`
- Nom d’utilisateur et mot de passe pour l’authentification de base
   - `public: BasicLoginInformation BasicLoginInformation` : Informations Kubernetes brutes (`kubectl get postgres12`) 
   - `object: K8sRaw` : Date du dernier chargement à partir du cluster local 
   - `System.DateTime: LastUploadedDate` 
- État d’approvisionnement du groupe
   - `string: ProvisioningState` 

### <a name="sql-managed-instance"></a>Instance managée SQL 

- ID de l’instance gérée
   - `public string: DataControllerId` 
- Nom d’utilisateur de l’administrateur de l’instance 
   - `string: Admin` 
- Heure de début de l’instance 
   - `string: StartTime`
- Heure de fin de l’instance 
   - `string: EndTime` 
- Informations Kubernetes brutes (`kubectl get sqlmi`) 
   - `object: K8sRaw` 
- Nom d’utilisateur et mot de passe pour l’authentification de base 
   - `public: BasicLoginInformation BasicLoginInformation`
- Date du dernier chargement à partir du cluster local 
   - `public: System.DateTime LastUploadedDate` 
- État d’approvisionnement de l’instance gérée SQL
   - `public string: ProvisioningState` 

### <a name="examples"></a>Exemples

Exemple de document JSON des données d’inventaire des ressources qui est envoyé à Azure pour créer des ressources Azure dans votre abonnement. 

```json
{ 

        "customObjectName": "<resource type>-2020-29-5-23-13-17-164711", 

        "uid": "4bc3dc6b-9148-4c7a-b7dc-01afc1ef5373", 

        "instanceName": "sqlInstance001", 

        "instanceNamespace": "arc", 

        "instanceType": "<resource>", 

        "location": "eastus", 

        "resourceGroupName": "production-resources", 

        "subscriptionId": "<subscription_id>", 

        "isDeleted": false, 

        "externalEndpoint": "32.191.39.83:1433", 

        "vCores": "2", 

        "createTimestamp": "05/29/2020 23:13:17", 

        "updateTimestamp": "05/29/2020 23:13:17" 

    } 
```

 

Les données de facturation capturent l’heure de début (« créée ») et l’heure de fin (« supprimée ») d’une instance donnée, ainsi que tout début et toute heure où se produit un changement dans le nombre de cœurs disponibles pour une instance donnée (« limite de cœur »). 

```json
{ 

          "requestType": "usageUpload", 

          "clusterId": "4b0917dd-e003-480e-ae74-1a8bb5e36b5d", 

          "name": "DataControllerTestName", 

          "subscriptionId": "<subscription_id>", 

          "resourceGroup": "production-resources", 

          "location": "eastus", 

          "uploadRequest": { 

            "exportType": "usages", 

            "dataTimestamp": "2020-06-17T22:32:24Z", 

            "data": "[{\"name\":\"sqlInstance001\", 

                       \"namespace\":\"arc\", 

                       \"type\":\"<resource type>\", 

                       \"eventSequence\":1,  

                       \"eventId\":\"50DF90E8-FC2C-4BBF-B245-CB20DC97FF24\", 

                       \"startTime\":\"2020-06-17T19:11:47.7533333\", 

                       \"endTime\":\"2020-06-17T19:59:00\", 

                       \"quantity\":1, 

                       \"id\":\"<subscription_id>\"}]", 

           "signature":"MIIE7gYJKoZIhvcNAQ...2xXqkK" 

```

### <a name="diagnostic-data"></a>Données de diagnostic

Dans les situations de support, il peut vous être demandé de fournir des journaux d’instance de base de données, des journaux Kubernetes et d’autres journaux de diagnostic. L’équipe de support vous fournira un emplacement sécurisé vers lequel vous pourrez les charger. Les vues de gestion dynamique (DMV) peuvent également fournir des données de diagnostic. Les DMV ou les requêtes utilisées peuvent contenir des détails sur les métadonnées du schéma de la base de données, mais généralement pas de données client. Les données de diagnostic ne contiennent aucun mot de passe, aucune adresse IP de cluster ni aucune donnée identifiable individuellement. Elles sont nettoyées, et les journaux sont rendus anonymes pour le stockage dans la mesure du possible. Ils ne sont pas transmis automatiquement et l’administrateur doit les charger manuellement. 

|Nom du champ  |Notes  |
|---------|---------|
|Journaux des erreurs |Les fichiers journaux capturant des erreurs pouvant contenir des données clients ou personnelles (voir ci-dessous) sont restreints et partagés par l’utilisateur |
|DMV      |Les vues de gestion dynamique peuvent contenir des requêtes et des plans de requêtes, mais sont restreintes et partagées par l’utilisateur     |
|Les vues    |Les vues peuvent contenir des données client, mais sont restreintes et partagées uniquement par l’utilisateur     |
|Vidages sur incident – données client | Rétention maximale de 30 jours pour les vidages sur incident ; peut contenir des données de contrôle d’accès <br/><br/> Les objets de statistiques, les valeurs de données dans les lignes et les textes des requêtes peuvent se trouver dans les vidages sur incident des clients    |
|Vidages sur incident – données personnelles | Ordinateur, noms de connexion/d’utilisateur, e-mails, informations de localisation, identification du client : nécessitent le consentement de l’utilisateur pour être inclus  |

### <a name="customer-experience-improvement-program-ceip-telemetry"></a>Programme d’amélioration de l’expérience utilisateur (CEIP) [Télémétrie] 

La télémétrie est utilisée pour suivre les métriques d’utilisation et les informations sur l’environnement relatives au produit. Voir [Avenant à la déclaration de confidentialité de SQL Server](/sql/sql-server/sql-server-privacy/). 

## <a name="next-steps"></a>Étapes suivantes
[Charger des données d’utilisation sur Azure Monitor](upload-usage-data.md)
