---
title: Exécution de requêtes avec Azure Data Explorer
titleSuffix: Azure Digital Twins
description: Comprendre le plug-in de requête Azure Digital Twins pour Azure Data Explorer
author: baanders
ms.author: baanders
ms.date: 5/19/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 8b0c6558be0022d8cb72ede5b665023f2b3f138d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438707"
---
# <a name="azure-digital-twins-query-plugin-for-azure-data-explorer"></a>Plug-in de requête Azure Digital Twins pour Azure Data Explorer

Le plug-in Azure Digital Twins pour [Azure Data Explorer (ADX)](/azure/data-explorer/data-explorer-overview) vous permet d’exécuter des requêtes ADX qui accèdent aux données dans le graphe Azure Digital Twins et les bases de données de série chronologique ADX, et les combinent. Utilisez le plug-in pour contextualiser des données de série chronologique disparates en raisonnant sur des jumeaux numériques et leurs relations pour obtenir des insights sur le comportement d’environnements modélisés.

Par exemple, avec ce plug-in, vous pouvez écrire une requête Kusto qui...
1. sélectionne les jumeaux numériques présentant un intérêt par le biais du plug-in de requête Azure Digital Twins ;
2. joint ces jumeaux à la série chronologique correspondante dans Azure Data Explorer ; puis 
3. puis procède à une analytique avancée de la série chronologique sur ces jumeaux.  

La combinaison des données d’un graphe de jumeaux dans Azure Digital Twins avec des données de série chronologique dans Azure Data Explorer peut vous aider à comprendre le comportement opérationnel des différentes parties de votre solution. 

## <a name="using-the-plugin"></a>Utilisation du plug-in

Vous pouvez appeler le plug-in dans une requête Kusto à l’aide de la commande suivante. Notez la présence de deux espaces réservés : `<Azure-Digital-Twins-endpoint>` et `<Azure-Digital-Twins-query>`. Ces chaînes représentent respectivement le point de terminaison de l’instance Azure Digital Twins et la requête Azure Digital Twins. 

```kusto
evaluate azure_digital_twins_query_request(<Azure-Digital-Twins-endpoint>, <Azure-Digital-Twins-query>) 
```

Le plug-in fonctionne en appelant l’[API de requête Azure Digital Twins](/rest/api/digital-twins/dataplane/query). La [structure du langage de requête](concepts-query-language.md) est la même que lors de l’utilisation de l’API, avec deux exceptions : 
* Le `*`caractère générique dans la clause `SELECT` n’est pas pris en charge. Au lieu de cela, les requêtes Azure Digital Twin qui sont exécutées à l’aide du plug-in doivent utiliser des alias dans la clause `SELECT`.

    Par exemple, considérez la requête Azure Digital Twins qui est exécutée à l’aide de l’API :
    
    ```SQL
    SELECT * FROM DIGITALTWINS
    ```
    
    Pour exécuter cette requête lors de l’utilisation du plug-in, elle doit être réécrite comme suit :
    
    ```SQL
    SELECT T FROM DIGITALTWINS T
    ```
* Les noms de colonnes retournés par le plug-in ne peuvent pas commencer par un `$`. L’utilisation d’alias dans la clause `SELECT` permettra également d’éviter ce scénario.

    Par exemple, considérez la requête Azure Digital Twins qui est exécutée à l’aide de l’API :
    
    ```SQL
    SELECT T.$dtId, T.Temperature FROM DIGITALTWINS T
    ```
    
    Pour exécuter cette requête lors de l’utilisation du plug-in, elle doit être réécrite comme suit :
    
    ```SQL
    SELECT T.$dtId as tid, T.Temperature FROM DIGITALTWINS T
    ```


>[!IMPORTANT]
>L’utilisateur du plug-in doit disposer du rôle **Lecteur des données Azure Digital Twins** ou du rôle **Propriétaire des données Azure Digital Twins**, car le jeton Azure AD de l’utilisateur est utilisé pour l’authentification. Vous trouverez des informations sur la façon d’attribuer ce rôle dans [Sécurité pour les solutions Azure Digital Twins](concepts-security.md#authorization-azure-roles-for-azure-digital-twins).

Pour plus d’informations sur l’utilisation du plug-in, consultez la [documentation Kusto pour le plug-in azure_digital_twins_query_request](/azure/data-explorer/kusto/query/azure-digital-twins-query-request-plugin).

Pour obtenir des exemples de requêtes et effectuer une procédure pas-à-pas avec un exemple de données, consultez [les exemples de requêtes et la procédure pas-à-pas du plug-in de requête Azure Digital Twins pour Azure Data Explorer](https://github.com/Azure-Samples/azure-digital-twins-getting-started/tree/main/adt-adx-queries) dans GitHub.

## <a name="using-azure-data-explorer-iot-data-with-azure-digital-twins"></a>Utilisation des données de l’Explorateur de données IoT Azure avec Azure Digital Twins

Pour ingérer des données IoT dans Azure Data Explorer, plusieurs méthodes s’offrent à vous. En voici deux dont vous pouvez vous servir lors de l’utilisation d’Azure Data Explorer avec Azure Digital Twins :
* Historiser les valeurs des propriétés des jumeaux numériques dans Azure Data Explorer avec une fonction Azure qui gère les événements de changement de jumeau et écrit les données de jumeau dans Azure Data Explorer. Ce processus est similaire à celui utilisé dans [Effectuer l’intégration à Azure Time Series Insights](how-to-integrate-time-series-insights.md). Cette approche convient aux clients qui utilisent des données de télémétrie pour donner vie à leurs jumeaux numériques.
* [Ingérer les données IoT directement dans votre cluster Azure Data Explorer à partir d’IoT Hub](/azure/data-explorer/ingest-data-iot-hub) ou d’autres sources. Ensuite, le graphe Azure Digital Twins est utilisé pour contextualiser les données de série chronologique avec des requêtes Azure Digital Twins/Azure Data Explorer mixtes. Cette approche peut convenir aux charges de travail d’ingestion directe. 

### <a name="mapping-data-across-azure-data-explorer-and-azure-digital-twins"></a>Mappage de données dans Azure Data Explorer et Azure Digital Twins

Si vous ingérez des données de série chronologique directement dans Azure Data Explorer, vous devrez probablement convertir ces données brutes en schéma adapté aux requêtes Azure Digital Twins/Azure Data Explorer mixtes.

Une [stratégie de mise à jour](/azure/data-explorer/kusto/management/updatepolicy)  dans Azure Data Explorer vous permet de transformer et d’ajouter automatiquement des données à une table cible chaque fois que de nouvelles données sont insérées dans une table source. 

Vous pouvez utiliser une stratégie de mise à jour pour enrichir vos données de série chronologique brutes avec l’**ID de jumeau** correspondant à partir d’Azure Digital Twins et le rendre persistant dans une table cible. À l’aide de l’ID de jumeau, la table cible peut ensuite être jointe aux jumeaux numériques sélectionnés par le plug-in Azure Digital Twins. 

Par exemple, imaginons que vous avez créé la table suivante pour stocker les données de série chronologique brutes transmises à votre instance Azure Data Explorer. 

```kusto
.create-merge table rawData (Timestamp:datetime, someId:string, Value:string, ValueType:string)  
```

Vous pouvez créer une table de mappage pour associer les ID de série chronologique aux ID de jumeau et d’autres champs facultatifs. 

```kusto
.create-merge table mappingTable (someId:string, twinId:string, otherMetadata:string) 
```

Ensuite, créez une table cible pour stocker les données de série chronologique enrichies. 

```kusto
.create-merge table timeseriesSilver (twinId:string, Timestamp:datetime, someId:string, otherMetadata:string, ValueNumeric:real, ValueString:string)  
```

Créez à présent une fonction `Update_rawData` pour enrichir les données brutes en les joignant à la table de mappage. Cette opération ajoute l’ID de jumeau à la table cible résultante. 

```kusto
.create-or-alter function with (folder = "Update", skipvalidation = "true") Update_rawData() { 
rawData 
| join kind=leftouter mappingTable on someId 
| project 
    Timestamp, ValueNumeric = toreal(Value), ValueString = Value, ... 
} 
```

Enfin, créez une stratégie de mise à jour pour appeler la fonction et mettre à jour la table cible. 

```kusto
.alter table timeseriesSilver policy update 
@'[{"IsEnabled": true, "Source": "rawData", "Query": "Update_rawData()", "IsTransactional": false, "PropagateIngestionProperties": false}]' 
```

Une fois la table cible créée, vous pouvez utiliser le plug-in Azure Digital Twins pour sélectionner les jumeaux présentant un intérêt, puis les joindre aux données de série chronologique dans la table cible. 

### <a name="example-schema"></a>Exemple de schéma

Voici un exemple de schéma qui peut être utilisé pour représenter des données partagées.

| timestamp | twinId | modelId | name | value | relationshipTarget | relationshipID |
| --- | --- | --- | --- | --- | --- | --- |
| 2021-02-01 17:24 | ConfRoomTempSensor | dtmi:com:example:TemperatureSensor;1 | température | 301.0 |  |  |

Les propriétés des jumeaux numériques sont stockées sous la forme de paires clé-valeur (`name, value`). `name` et `value` sont stockées sous la forme de types de données dynamiques. 

Le schéma prend également en charge le stockage des propriétés pour les relations, grâce aux champs `relationshipTarget` et `relationshipID`. Le schéma clé-valeur vous évite d’avoir à créer une colonne pour chaque propriété de jumeau.

### <a name="representing-properties-with-multiple-fields"></a>Représentation de propriétés avec plusieurs champs 

Vous pouvez être amené à stocker une propriété dans votre schéma avec plusieurs champs. Pour représenter ces propriétés, stockez un objet JSON en tant que `value` dans votre schéma.

Par exemple, si vous souhaitez représenter une propriété avec trois champs (roulis, tangage et lacet), l’objet de valeur ressemble à ceci : `{"roll": 20, "pitch": 15, "yaw": 45}`.

## <a name="next-steps"></a>Étapes suivantes

* Consultez la documentation du plug-in pour le langage Kusto dans Azure Data Explorer : [azure_digital_twins_query_request plugin](/azure/data-explorer/kusto/query/azure-digital-twins-query-request-plugin)

* Examinez des exemples de requêtes utilisant le plug-in, notamment une procédure pas-à-pas qui exécute les requêtes dans un exemple de scénario : [Plug-in de requête Azure Digital Twins pour Azure Data Explorer : exemples de requêtes et procédure pas-à-pas](https://github.com/Azure-Samples/azure-digital-twins-getting-started/tree/main/adt-adx-queries) 

* Découvrez une autre stratégie d’analyse des données d’historique dans Azure Digital Twins : [Intégrer à Azure Time Series Insights](how-to-integrate-time-series-insights.md)
