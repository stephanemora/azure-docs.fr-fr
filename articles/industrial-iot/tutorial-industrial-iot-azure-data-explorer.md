---
title: Extraire des données Azure Industrial IoT dans ADX
description: Dans ce tutoriel, vous allez découvrir comment extraire des données IIoT dans ADX.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 9a18609ab0dc41031a22db6d8b21c1fff83920f2
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108137307"
---
# <a name="tutorial-pull-azure-industrial-iot-data-into-adx"></a>Tutoriel : Extraire des données Azure Industrial IoT dans ADX

La plateforme IIoT (Azure Industrial IoT) combine des modules de périphérie et des microservices cloud avec un certain nombre de services PaaS Azure afin de fournir des fonctionnalités de découverte de ressources industrielles et de collecter des données à partir de ces ressources à l’aide d’OPC UA. [ADX (Azure Data Explorer)](/azure/data-explorer) est une destination naturelle pour les données IIoT, car il propose des fonctionnalités d’analytique données qui permettent d’exécuter des requêtes flexibles sur les données ingérées à partir des serveurs OPC UA connectés au hub IoT par le biais du serveur de publication OPC. Bien qu’un cluster ADX puisse ingérer des données directement à partir du hub IoT, la plateforme IIoT effectue un traitement supplémentaire des données afin de les rendre plus utiles avant de les placer sur le hub d’événements fourni lors de l’utilisation d’un déploiement complet des microservices (voir l’architecture de la plateforme IIoT).

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une table dans ADX.
> * Connecter le hub d’événements au cluster ADX.
> * Analyser les données dans ADX.

## <a name="how-to-make-the-data-available-in-the-adx-cluster-to-query-it-effectively"></a>Comment rendre les données disponibles dans le cluster ADX afin de les interroger efficacement ? 

Si nous examinons le format du message à partir du hub d’événements (comme défini par la classe Microsoft.Azure.IIoT.OpcUa.Subscriber.Models.MonitoredItemMessageModel), nous pouvons voir un indicateur de la structure dont nous avons besoin pour le schéma de table ADX.

![Structure](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-1.png)

Vous trouverez ci-dessous les étapes nécessaires pour rendre les données disponibles dans le cluster ADX et pour interroger les données efficacement.  
1. Créez un cluster ADX. Si vous ne disposez pas déjà d’un cluster ADX provisionné avec la plateforme IIoT, ou si vous souhaitez utiliser un autre cluster, suivez les étapes fournies [ici](/azure/data-explorer/create-cluster-database-portal#create-a-cluster). 
2. Activez l’ingestion en streaming sur le cluster ADX, comme expliqué [ici](/azure/data-explorer/ingest-data-streaming#enable-streaming-ingestion-on-your-cluster). 
3. Créez une base de données ADX en suivant les étapes fournies [ici](/azure/data-explorer/create-cluster-database-portal#create-a-database).

Pour l’étape suivante, nous allons utiliser l’[interface web ADX](/azure/data-explorer/web-query-data) pour exécuter les requêtes nécessaires. Veillez à ajouter votre cluster à l’interface web comme expliqué dans le lien.  
 
4. Créez une table dans ADX où placer les données ingérées.  Bien que la classe MonitoredItemMessageModel puisse être utilisée pour définir le schéma de la table ADX, nous vous recommandons d’ingérer d’abord les données dans une table de mise en lots avec une colonne de type [Dynamique](/azure/data-explorer/kusto/query/scalar-data-types/dynamic). Cela procure davantage de flexibilité dans la gestion des données et le traitement dans d’autres tables (éventuellement en les combinant avec d’autres sources de données) qui répondent aux besoins de plusieurs cas d’usage. La requête ADX suivante crée la table de mise en lots « iiot_stage » avec une colonne « payload ».

```
.create table ['iiot_stage']  (['payload']:dynamic)
```

Nous devons également ajouter un mappage d’ingestion JSON pour faire en sorte que le cluster place l’intégralité du message JSON provenant du hub d’événements dans la table de mise en lots.

```
.create table ['iiot_stage'] ingestion json mapping 'iiot_stage_mapping' '[{"column":"payload","path":"$","datatype":"dynamic"}]'
```

5. Notre table est maintenant prête à recevoir des données à partir du hub d’événements. 
6. Utilisez [ces instructions](/azure/data-explorer/ingest-data-event-hub#connect-to-the-event-hub) pour connecter le hub d’événements au cluster ADX et commencer à ingérer les données dans notre table de mise en lots. Nous avons simplement besoin de créer la connexion, car un hub d’événements a été provisionné par la plateforme IIoT.  
7. Une fois la connexion vérifiée, les données commencent à circuler vers notre table et, après un bref délai, nous pouvons commencer à examiner les données qui s’y trouvent. Utilisez la requête suivante dans l’interface web ADX pour examiner un échantillon de données de 10 lignes. Nous voyons ici comment les données de la charge utile sont similaires à la classe MonitoredItemMessageModel mentionnée plus haut.

![Requête](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-2.png)

8. Nous allons maintenant exécuter une analytique sur ces données en analysant directement les données dynamiques dans la colonne « payload ». Dans cet exemple, nous allons calculer la moyenne de la télémétrie identifiée par « DisplayName » : « PositiveTrendData », sur des fenêtres de temps de dix minutes, sur tous les enregistrements ingérés depuis un point spécifique (défini par la variable min_t) let min_t = datetime(2020-10-23); iiot_stage | where todatetime(payload.Timestamp) > min_t | where tostring(payload.DisplayName)== ’PositiveTrendData’ | summarize event_avg = avg(todouble(payload.Value)) by bin(todatetime(payload.Timestamp), 10 m)
 
Dans la mesure où notre colonne « payload » contient un type de données dynamique, nous devons effectuer la conversion des données au moment de la requête afin que nos calculs soient effectués sur les types de données corrects.

![Horodatage de charge utile](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-3.png)

Comme mentionné plus haut, l’ingestion des données OPC UA dans une table de mise en lots avec une colonne « dynamique » procure une certaine flexibilité. Toutefois, l’exécution de conversions de types de données au moment de la requête peut entraîner des retards dans l’exécution des requêtes, en particulier si le volume de données est important et s’il existe de nombreuses requêtes simultanées. À ce stade, nous pouvons créer une autre table avec les types de données déjà déterminés, afin d’éviter les conversions de types de données au moment de la requête.
 
9. Créez une table pour les données analysées, qui se compose d’une sélection limitée parmi le contenu de la charge utile (« payload ») dynamique dans la table de mise en lots. Notez que nous avons créé une colonne de valeur pour chacun des types de données attendus dans notre télémétrie.

```
.create table ['iiot_parsed']  
    (['Tag_Timestamp']: datetime ,  
    ['Tag_PublisherId']:string ,  
    ['Tag']:string ,
    ['Tag_Datatype']:string ,  
    ['Tag_NodeId']:string,  
    ['Tag_value_long']:long ,  
    ['Tag_value_double']:double,  
    ['Tag_value_boolean']:bool)
```

10. Créez une fonction (au niveau de la base de données) pour projeter les données requises à partir de la table de mise en lots. Ici, nous sélectionnons les éléments « Timestamp », « PublisherId », « DisplayName », « Datatype » et « NodeId » à partir de la colonne « payload », puis nous les projetons comme « Tag_Timestamp », « Tag_PublisherId », « Tag », « Tag_Datatype », « Tag_NodeId ». L’élément « Value » est projeté en tant que trois parties différentes en fonction du « DataType ».

```
.create-or-alter function fn_InflightParseIIoTEvent()
{
iiot_stage
| extend Tag_Timestamp =  todatetime(payload.Timestamp)
| extend Tag_PublisherId = tostring(payload.PublisherId)
| extend Tag = tostring(payload.DisplayName)
| extend Tag_Datatype = tostring(payload.DataType)
| extend Tag_NodeId = tostring(payload.NodeId)
| extend Tag_value_long = case(Tag_Datatype == "Int64", tolong(payload.Value), long(null))
| extend Tag_value_double = case(Tag_Datatype == "Double", todouble(payload.Value), double(null))
| extend Tag_value_boolean = case(Tag_Datatype == "Boolean", tobool(payload.Value), bool(null))
| project Tag_Timestamp, Tag_PublisherId, Tag, Tag_Datatype, Tag_NodeId, Tag_value_long, Tag_value_double, Tag_value_boolean
}
```

Pour plus d’informations sur le mappage des types de données dans ADX, consultez [cette page](/azure/data-explorer/kusto/query/scalar-data-types/dynamic). Pour en savoir plus sur les fonctions dans ADX, vous pouvez commencer [ici](/azure/data-explorer/kusto/query/schema-entities/stored-functions).
 
11. Appliquez la fonction de l’étape précédente à la table analysée à l’aide d’une stratégie de mise à jour. Une [stratégie](/azure/data-explorer/kusto/management/updatepolicy) de mise à jour fait en sorte qu’ADX ajoute automatiquement des données à une table cible chaque fois que de nouvelles données sont insérées dans la table source, en fonction d’une requête de transformation qui s’exécute sur les données insérées dans la table source. Nous pouvons utiliser la requête suivante pour affecter la table analysée comme destination et la table de mise en lots comme source de la stratégie de mise à jour définie par la fonction que nous avons créée à l’étape précédente.

```
.alter table iiot_parsed policy update
@'[{"IsEnabled": true, "Source": "iiot_stage", "Query": "fn_InflightParseIIoTEvent()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
```

Dès que la requête ci-dessus est exécutée, les données commencent à circuler et à remplir la table de destination « iiot_parsed ». Nous pouvons examiner les données de « iiot_parsed » comme suit.

![Table analysée](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-4.png)

12. Examinons maintenant comment nous pouvons répéter l’analytique que nous avons effectuée à l’étape précédente. Calculez la moyenne de la télémétrie identifiée par « DisplayName » : « PositiveTrendData », sur des fenêtres de temps de dix minutes, sur tous les enregistrements ingérés depuis un point spécifique (défini par la variable min_t). Les valeurs de l’étiquette « PositveTrendData » étant maintenant stockées dans une colonne de type de données double, nous pouvons nous attendre à une amélioration des performances des requêtes.

![Répéter l’analytique](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-5.png)

13. Pour finir, nous allons comparer les performances des requêtes dans les deux cas. Nous pouvons connaître la durée nécessaire à l’exécution de la requête à l’aide des « statistiques » dans l’interface utilisateur ADX (qui se trouvent au-dessus des résultats de la requête).  

![Performances des requêtes 1](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-6.png)

![Performances des requêtes 2](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-7.png)

Nous pouvons constater que la requête qui utilise la table analysée est environ deux fois plus rapide que celle de la table de mise en lots. Dans cet exemple, nous avons un petit jeu de données et il n’y a pas de requêtes simultanées en cours d’exécution, si bien que l’effet sur la durée d’exécution de la requête n’est pas très remarquable. Cependant, pour une charge de travail réaliste, il y aurait un impact important sur les performances. C’est pourquoi il est important de séparer les différents types de données en différentes colonnes.

> [!NOTE] 
> La stratégie de mise à jour fonctionne uniquement sur les données qui sont ingérées dans la table de mise en lots une fois que la stratégie a été configurée, et ne s’applique à aucune donnée préexistante. Cela doit être pris en considération quand, par exemple, nous devons modifier la stratégie de mise à jour. Vous trouverez des informations détaillées dans la documentation ADX.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris à modifier les valeurs par défaut de la configuration, vous pouvez 

> [!div class="nextstepaction"]
> [Configurer des composants Industrial IoT](tutorial-configure-industrial-iot-components.md)

> [!div class="nextstepaction"]
> [Visualiser et analyser les données à l’aide de Time Series Insights](tutorial-visualize-data-time-series-insights.md)