---
title: Ingestion des données dans l’Explorateur de données Azure
description: Découvrez les différentes manières d’ingérer (charger) des données dans l’Explorateur de données Azure.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 6c7d4d8d4a16e0679722f9de007870a7ec7554b0
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51635997"
---
# <a name="azure-data-explorer-data-ingestion"></a>Ingestion des données dans l’Explorateur de données Azure

L’ingestion des données est le processus qui consiste à charger des enregistrements de données à partir d’une ou plusieurs sources pour créer ou mettre à jour une table dans l’Explorateur de données Azure. Une fois ingérées, les données sont disponibles pour les requêtes. Le diagramme ci-dessous illustre le flux de bout en bout d’utilisation de l’Explorateur de données Azure, notamment l’ingestion des données.

![Flux de données](media/ingest-data-overview/data-flow.png)

Le service de gestion des données Explorateur de données Azure, qui est responsable de l’ingestion des données, offre les fonctionnalités suivantes :

1. **Extraction de données** : extraire des données provenant de sources externes (hubs d’événements) ou lire des requêtes d’ingestion à partir d’une file d’attente Azure.

1. **Traitement par lot** : traiter par lot des données à destination de la même base de données et de la même table, afin optimiser le débit d’ingestion.

1. **Validation** : validation et conversion de format préliminaires si nécessaire.

1. **Manipulation de données** : mise en correspondance de schéma, organisation, indexation, encodage et compression des données.

1. **Point de persistance dans le flux d’ingestion** : gérer la charge d’ingestion sur le moteur et traiter les nouvelles tentatives en cas de défaillance temporaire.

1. **Valider l’ingestion des données** : rend les données disponibles pour les requêtes.

## <a name="ingestion-methods"></a>Méthodes d’ingestion

L’Explorateur de données Azure prend en charge plusieurs méthodes d’ingestion, chacune avec ses propres scénarios cibles, avantages et inconvénients. L’Explorateur de données Azure offre des pipelines et des connecteurs pour les services les plus courants, l’ingestion par programmation à l’aide de SDK et un accès direct au moteur de fins d’exploration.

### <a name="ingestion-using-pipelines"></a>Ingestion à l’aide de pipelines

L’Explorateur de données Azure prend actuellement en charge le pipeline Event Hub, qui peut être géré à l’aide de l’Assistant de gestion dans le portail Azure. Pour plus d’informations, consultez [Démarrage rapide : Ingérer des données Event Hub dans l’Explorateur de données Azure](ingest-data-event-hub.md).

### <a name="ingestion-using-connectors-and-plugins"></a>Ingestion à l’aide de connecteurs et de plug-ins
L’Explorateur de données Azure prend actuellement en charge le plug-in Logstash. Pour plus d’informations, consultez [Plug-in de sortie Logstash pour l’Explorateur de données Azure](https://github.com/Azure/logstash-output-kusto/blob/master/README.md).

### <a name="programmatic-ingestion"></a>Ingestion par programmation

L’Explorateur de données Azure fournit des SDK qui peuvent être utilisées pour l’ingestion de données et les requêtes. L’ingestion par programmation est optimisée afin de réduire les coûts d’ingestion, grâce à la réduction des transactions de stockage pendant et après le processus d’ingestion.

**SDK et projets open source disponibles** :

Kusto propose des SDK client qui peuvent être utilisés pour ingérer et interroger des données avec :

* [Kit de développement logiciel (SDK) Python](/azure/kusto/api/python/kusto-python-client-library)

* [Kit de développement logiciel (SDK) .NET](/azure/kusto/api/netfx/about-the-sdk)

* [Kit SDK Java](/azure/kusto/api/java/kusto-java-client-library)

* [Node SDK](/azure/kusto/api/node/kusto-node-client-library)

* [API REST](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Techniques d’ingestion par programmation** :

* Ingestion de données par le biais du service de gestion de données Explorateur de données Azure (ingestion à débit élevé et fiable) :

  * [**Ingestion par lot**](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) (fournie par le SDK) : le client charge les données vers le stockage d’objets blob Azure (désigné par le service de gestion des données Explorateur de données Azure) et envoie une notification à une file d’attente Azure. Il s’agit de la technique recommandée pour une ingestion des données à volume élevé, fiable et peu coûteuse.

* Ingestion des données directement dans le moteur de l’Explorateur de données Azure (approche la plus appropriée pour l’exploration et le prototypage) :

  * **Ingestion inline** : commande de contrôle (.ingest inline) contenant des données intrabande et destinée aux tests ad hoc.

  * **Ingestion à partir de requête** : commande de contrôle (.set, .set-or-append, .set-or-replace) qui pointe vers des résultats de requête et qui sert à générer des rapports ou de petites tables temporaires.

  * **Ingestion à partir du stockage** : commande de contrôle (.ingest into) avec données stockées en externe (par exemple, stockage d’objets blob Azure) qui permet d’effectuer une ingestion en bloc efficace des données.

**Latence des différentes méthodes** :

| Méthode | Latence |
| --- | --- |
| **Ingestion inline** | Immédiat |
| **Ingestion à partir de requête** | Temps de requête + temps de traitement |
| **Ingestion à partir du stockage** | Temps de téléchargement + temps de traitement |
| **Ingestion en file d’attente** | Temps de traitement par lot + temps de traitement |
| |

Le temps de traitement dépend de la taille des données (il est généralement inférieur à quelques secondes). Le temps de traitement par lot par défaut est de cinq minutes.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>Choix de la méthode d’ingestion la plus appropriée

Avant de commencer à ingérer les données, vous devez vous poser les questions suivantes.

* Où mes données résident-elles ? 
* Quel est le format des données, et peut-il être modifié ? 
* Quels sont les champs obligatoires à interroger ? 
* Quels sont la vélocité et le volume de données attendus ? 
* Combien de types d’événements sont attendus (correspondant au nombre de tables) ? 
* Quelle est la fréquence de changement attendue du schéma d’événement ? 
* Combien de nœuds les données génèreront-elles ? 
* Quel est le système d’exploitation source ? 
* Quelles sont les exigences de latence ? 
* Peut-on utiliser l’un des pipelines d’ingestion managés existants ? 

Pour les organisations disposant d’une infrastructure existante basée sur un service de messagerie tel qu’Event Hub, l’utilisation d’un connecteur est probablement la solution la plus appropriée. L’ingestion en file d’attente convient aux grands volumes de données.

## <a name="supported-data-formats"></a>Formats de données pris en charge

Pour toutes les méthodes d’ingestion autres que l’ingestion à partir de requête, les données doivent être mises en forme dans l’un des formats de données pris en charge, afin que l’Explorateur de données Azure puisse les analyser.

* CSV, TSV, PSV, SCSV, SOH
* JSON (séparé par une ligne, multiligne), Avro
* ZIP et GZIP 

> [!NOTE]
> Quand des données sont ingérées, les types de données sont déduits d’après les colonnes de la table cible. Si un enregistrement est incomplet ou un champ ne peut pas être analysé en tant que type de données requis, les colonnes correspondantes de la table sont remplies avec des valeurs null.

## <a name="ingestion-recommendations-and-limitations"></a>Restrictions et recommandations d’ingestion
* La stratégie de rétention effective des données ingérées est dérivée de la stratégie de rétention de la base de données. Pour plus d’informations, consultez [Stratégie de rétention](/azure/kusto/concepts/retentionpolicy). L’ingestion des données nécessite l’autorisation **Ingéreur de table** ou **Ingéreur de base de données**.
* L’ingestion prend en charge une taille de fichier maximale de 5 Go. Nous vous recommandons d’ingérer des fichiers entre 100 Mo et 1 Go.

## <a name="schema-mapping"></a>Mappage de schéma

Le mappage de schéma permet de lier de façon déterministe des champs de données sources à des colonnes de table de destination.

* Le [mappage CSV](/azure/kusto/management/mappings?branch=master#csv-mapping) (facultatif) fonctionne avec tous les formats basés sur un ordinal, et peut être passé comme paramètre de commande d’ingestion ou [précréé sur la table](/azure/kusto/management/tables?branch=master#create-ingestion-mapping) et référencé à partir du paramètre de commande d’ingestion.
* Le [mappage JSON](/azure/kusto/management/mappings?branch=master#json-mapping) (obligatoire) et le [mappage Avro](/azure/kusto/management/mappings?branch=master#avro-mapping) (obligatoire) peuvent être passés comme paramètre de commande d’ingestion ou [précréé sur la table](/azure/kusto/management/tables#create-ingestion-mapping) et référencé à partir du paramètre de commande d’ingestion.

## <a name="next-steps"></a>Étapes suivantes

[Démarrage rapide : Ingérer des données Event Hub dans l’Explorateur de données Azure](ingest-data-event-hub.md)

[Démarrage rapide : Ingérer des données à l’aide de la bibliothèque Python de l’Explorateur de données Azure](python-ingest-data.md)

[Démarrage rapide : Ingérer des données à l’aide de la bibliothèque Node de l’Explorateur de données Azure](node-ingest-data.md)

