---
title: Diffuser en continu des données en tant qu’entrées dans Azure Stream Analytics
description: Apprenez en davantage sur la configuration d’une connexion de données dans Azure Stream Analytics. Les données incluent un flux de données de partir d’événements et également des données de référence.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/19/2018
ms.openlocfilehash: 5ebf2d1025c8f9469a83a408cb79e3d944a601bc
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2018
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Diffuser en continu des données en tant qu’entrées dans Stream Analytics

Stream Analytics accepte des données provenant de divers types de sources d’événements. La connexion de données fournie en tant qu’entrée dans un travail Stream Analytics est appelée *entrée* du travail. 

Stream Analytics possède une intégration de première classe avec des flux de données Azure en tant qu’entrées provenant de trois types de ressources :
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [stockage d’objets blob Azure](https://azure.microsoft.com/services/storage/blobs/) 

Ces ressources d’entrée peuvent résider dans le même abonnement Azure que votre travail Stream Analytics ou provenir d’un autre abonnement.

## <a name="compare-stream-and-reference-inputs"></a>Comparer les entrées de flux de données et les entrées de données de référence
Lorsque les données sont transmises à une source de données, elles sont utilisées par le travail Stream Analytics et traitées en temps réel. Les entrées sont réparties en deux types : les entrées de flux de données et les entrées de données de référence.

### <a name="data-stream-input"></a>Entrée de flux de données
Un flux de données est une séquence illimitée d’événements au fil du temps. Les travaux Stream Analytics doivent contenir au moins une entrée de flux de données. Le stockage d’objets blob, Event Hubs et IoT Hub sont pris en charge en tant que sources d’entrée de flux de données. Les concentrateurs Event Hubs permettent de collecter des flux d’événements à partir de plusieurs appareils et services. Il peut s’agir de flux d’activités de réseaux sociaux, d’informations boursières ou de données provenant de capteurs. Les conteneurs IoT Hub sont optimisés pour collecter des données à partir d’appareils connectés dans les scénarios Internet des objets (IoT).  Le stockage d’objets blob peut être utilisé comme source d’entrée pour la réception de données en bloc en tant que flux, par exemple des fichiers journaux.  

### <a name="reference-data-input"></a>Entrée de données de référence
Stream Analytics prend également en charge des entrées appelées *données de référence*. Ces données auxiliaires sont statiques ou sont modifiées lentement. Les données de référence sont généralement utilisées pour la corrélation et les recherches. Par exemple, vous pouvez joindre des données de l’entrée de flux de données à des données de référence, comme vous effectueriez une jointure SQL pour rechercher des valeurs statiques. Le stockage d’objets blob Azure est la seule source d’entrée prise en charge pour les données de référence. Les objets blob de source de données de référence sont limités à une taille de 100 Mo.

Pour découvrir comment créer des entrées de données de référence, voir [Utiliser les données de référence](stream-analytics-use-reference-data.md).  

### <a name="compression"></a>Compression
Stream Analytics prend en charge la compression pour toutes les sources d’entrée de flux de données. Les types de référence actuellement pris en charge pour la compression sont : Aucune, GZip et Deflate. La prise en charge de la compression n’est pas disponible pour les données de référence. Si le format d’entrée est des données Avro compressées, celles-ci sont gérées de façon transparente. Vous n’avez pas besoin de spécifier le type de compression avec la sérialisation Avro. 

## <a name="create-or-edit-inputs"></a>Créer ou modifier des entrées
Pour créer des entrées et répertorier ou modifier des entrées existantes de votre travail de diffusion en continu, vous pouvez utiliser le portail Azure :
1. Ouvrez le [portail Azure](https://portal.azure.com) pour rechercher et sélectionner votre travail Stream Analytics.
2. Sélectionnez l’option **Entrées** sous le titre **PARAMÈTRES**. 
4. La page **Entrées** répertorie toutes les entrées existantes. 
5. Sur la page **Entrées**, sélectionnez **Ajouter une entrée de flux** ou **Ajouter une entrée de référence** pour ouvrir la page d’informations.
6. Sélectionnez une entrée existante pour modifier les informations et cliquez sur **Enregistrer** pour modifier une entrée existante.
7. Sélectionnez **Tester** sur la page d’informations de l’entrée pour vérifier que les options de connexion sont valides et qu’elles fonctionnent. 
8. Cliquez avec le bouton droit sur le nom d’une entrée existante et sélectionnez **Exemple de données de l’entrée** si nécessaire pour des tests ultérieurs.

Vous pouvez également utiliser [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.streamanalytics/New-AzureRmStreamAnalyticsInput), [API .Net](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), [API REST](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-input) et [Visual Studio](stream-analytics-tools-for-visual-studio.md) pour créer, modifier et tester les entrées du travail Stream Analytics.

## <a name="stream-data-from-event-hubs"></a>Diffuser en continu des données depuis Event Hubs

Azure Event Hubs fournit des services de réception d’événements de publication/d’abonnement hautement évolutifs. Un concentrateur Event Hub peut collecter des millions d’événements par seconde afin que vous puissiez traiter et analyser les grandes quantités de données générées par vos appareils et applications connectés. Event Hubs et Stream Analytics vous fournissent une solution complète pour des analyses en temps réel. Event Hubs vous permet d’alimenter des événements dans Azure en temps réel, et les travaux Stream Analytics peuvent traiter ces événements en temps réel. Par exemple, vous pouvez envoyer à Event Hubs des clics web, des lectures de capteurs ou des événements de journaux en ligne. Vous pouvez alors créer des travaux Stream Analytics pour utiliser Event Hubs en tant que flux de données d’entrée pour le filtrage, l’agrégation et la corrélation en temps réel.

L’horodatage par défaut des événements provenant d’Event Hubs dans Stream Analytics est l’horodatage de l’arrivée de l’événement dans le concentrateur Event Hub, `EventEnqueuedUtcTime`. Pour traiter les données en tant que flux à l’aide d’un horodatage dans la charge utile d’événement, vous devez utiliser le mot-clé [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

### <a name="consumer-groups"></a>Groupes de consommateurs
Vous devez configurer chaque entrée Event Hub de Stream Analytics afin qu’elle dispose de son propre groupe de consommateurs. Lorsqu’un travail contient une jointure réflexive ou s’il comporte plusieurs entrées, une entrée peut être lue par plusieurs lecteurs en aval. Cette situation a une incidence sur le nombre de lecteurs dans un groupe de consommateurs unique. Pour éviter de dépasser la limite des Event Hubs de cinq lecteurs par groupe de consommateurs par partition, il est recommandé de désigner un groupe de consommateurs pour chaque travail Stream Analytics. Il existe également une limite de 20 groupes de consommateurs par concentrateur Event Hub. Pour plus d’informations, consultez l’article [Guide de programmation de concentrateurs d’événements](../event-hubs/event-hubs-programming-guide.md).

### <a name="stream-data-from-event-hubs"></a>Diffuser en continu des données depuis Event Hubs
Le tableau suivant décrit chaque propriété de la page **Nouvelle entrée** du portail Azure pour diffuser en continu des entrées de données depuis un concentrateur Event Hub :

| Propriété | Description |
| --- | --- |
| **Alias d’entrée** |Nom convivial que vous utilisez dans la requête du travail pour faire référence à cette entrée. |
| **Abonnement** | Sélectionnez l’abonnement dans lequel se trouve la ressource du concentrateur Event Hub. | 
| **Espace de noms Event Hub** | L’espace de noms Event Hub est un conteneur pour un jeu d’entités de messagerie. Lorsque vous créez un concentrateur Event Hub, vous créez également l’espace de noms. |
| **Nom de l’Event Hub** | Nom du concentrateur Event Hub à utiliser comme entrée. |
| **Nom de la stratégie du hub d’événements** | La stratégie d’accès partagé qui fournit l’accès au concentrateur Event Hub. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. Cette option est automatiquement renseignée, sauf si vous sélectionnez l’option pour indiquer manuellement les paramètres du concentrateur Event Hub.|
| **Groupe de consommateurs du hub d’événements** (recommandé) | Il est vivement recommandé d’utiliser un groupe de consommateurs différent pour chaque travail Stream Analytics. Cette chaîne identifie le groupe de consommateurs à utiliser pour ingérer les données du concentrateur Event Hub. Si aucun groupe de consommateurs n’est spécifié, le travail Stream Analytics utilise le groupe de consommateurs $Default.  |
| **Format de sérialisation de l’événement** | Format de sérialisation (JSON, CSV ou Avro) du flux de données entrant. |
| **Encodage** | Pour le moment, UTF-8 est le seul format d’encodage pris en charge. |
| **Type de compression d’événement** | Le type de compression utilisé pour lire le flux de données entrant, tel que Aucune (valeur par défaut), GZip ou Deflate. |

Si vos données proviennent d’une entrée de données d’un concentrateur Event Hub, vous avez accès aux champs de métadonnées suivants dans votre requête Stream Analytics :

| Propriété | Description |
| --- | --- |
| **EventProcessedUtcTime** |Date et heure du traitement de l’événement par Stream Analytics |
| **EventEnqueuedUtcTime** |Date et heure de la réception de l’événement par le hub d’événements. |
| **PartitionId** |ID de partition de base zéro de l'adaptateur d'entrée. |

Par exemple, en utilisant ces champs, vous pouvez écrire une requête semblable à l’exemple suivant :

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Lorsque vous utilisez le concentrateur d’événements comme un point de terminaison pour les itinéraires IoT Hub, vous pouvez accéder à la métadonnée IoT Hub en utilisant la [fonction GetMetadataPropertyValue](https://msdn.microsoft.com/en-us/library/azure/mt793845.aspx).
> 

## <a name="stream-data-from-iot-hub"></a>Diffuser en continu des données depuis IoT Hub
Azure Iot Hub est un service de réception d’événements de publication/d’abonnement hautement évolutif optimisé pour les scénarios IoT.

Le timestamp par défaut des événements provenant d’un concentrateur IoT Hub dans Stream Analytics est le timestamp de l’arrivée de l’événement dans le concentrateur IoT Hub, `EventEnqueuedUtcTime`. Pour traiter les données en tant que flux à l’aide d’un horodatage dans la charge utile d’événement, vous devez utiliser le mot-clé [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

> [!NOTE]
> Seuls les messages envoyés avec une propriété `DeviceClient` peuvent être traités.
> 

### <a name="consumer-groups"></a>Groupes de consommateurs
Vous devez configurer chaque entrée IoT Hub de Stream Analytics afin qu’elle dispose de son propre groupe de consommateurs. Lorsqu’un travail contient une jointure réflexive ou s’il comporte plusieurs entrées, une entrée peut être lue par plusieurs lecteurs en aval. Cette situation a une incidence sur le nombre de lecteurs dans un groupe de consommateurs unique. Pour éviter de dépasser la limite Azure IoT Hub de cinq lecteurs par groupe de consommateurs par partition, il est recommandé de désigner un groupe de consommateurs pour chaque travail Stream Analytics.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Configurer un concentrateur IoT Hub en tant qu’entrée de flux de données
Le tableau suivant décrit chaque propriété de la page **Nouvelle entrée** du portail Azure lorsque vous configurez un concentrateur IoT Hub en tant qu’entrée de flux de données.

| Propriété | Description |
| --- | --- |
| **Alias d’entrée** | Nom convivial que vous utilisez dans la requête du travail pour faire référence à cette entrée.|
| **Abonnement** | Sélectionnez l’abonnement dans lequel se trouve la ressource du concentrateur IoT Hub. | 
| **IoT Hub** | Le nom du concentrateur IoT Hub à utiliser comme entrée. |
| **Point de terminaison** | Le point de terminaison du concentrateur IoT Hub.|
| **Nom de la stratégie d’accès partagé** | La stratégie d’accès partagé qui fournit l’accès au concentrateur IoT Hub. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. |
| **Clé de la stratégie d’accès partagé** | La clé d’accès partagé utilisée pour autoriser l’accès au concentrateur IoT Hub.  Cette option est automatiquement renseignée, sauf si vous sélectionnez l’option pour indiquer manuellement les paramètres du concentrateur IoT Hub. |
| **Groupe de consommateurs** | Il est vivement recommandé d’utiliser un groupe de consommateurs différent pour chaque travail Stream Analytics. Le groupe de consommateurs à utiliser pour ingérer les données du concentrateur IoT Hub. Stream Analytics utilise le groupe de consommateurs $Default, sauf si vous spécifiez autre chose.  |
| **Format de sérialisation de l’événement** | Format de sérialisation (JSON, CSV ou Avro) du flux de données entrant. |
| **Encodage** | Pour le moment, UTF-8 est le seul format d’encodage pris en charge. |
| **Type de compression d’événement** | Le type de compression utilisé pour lire le flux de données entrant, tel que Aucune (valeur par défaut), GZip ou Deflate. |


Si vous utilisez des données de flux provenant d’un concentrateur IoT Hub, vous avez accès aux champs de métadonnées suivants dans votre requête Stream Analytics :

| Propriété | Description |
| --- | --- |
| **EventProcessedUtcTime** | Date et heure du traitement de l'événement. |
| **EventEnqueuedUtcTime** | Date et heure de la réception de l’événement par IoT Hub |
| **PartitionId** | ID de partition de base zéro de l'adaptateur d'entrée. |
| **IoTHub.MessageId** | Un ID utilisé pour corréler une communication bidirectionnelle dans un concentrateur IoT Hub. |
| **IoTHub.CorrelationId** | Un ID utilisé dans les réponses de message et les commentaires dans un concentrateur IoT Hub. |
| **IoTHub.ConnectionDeviceId** | ID d’authentification utilisé pour envoyer ce message. Cette valeur est marquée sur les messages liés aux services par le concentrateur IoT Hub. |
| **IoTHub.ConnectionDeviceGenerationId** | ID de génération de l’appareil authentifié qui a été utilisé pour envoyer ce message. Cette valeur est marquée sur les messages liés aux services par le concentrateur IoT Hub. |
| **IoTHub.EnqueuedTime** | L’heure de réception du message par le concentrateur IoT Hub. |
| **IoTHub.StreamId** | Propriété d’événement personnalisée, ajoutée par l’appareil de l’expéditeur. |


## <a name="stream-data-from-blob-storage"></a>Diffuser en continu des données depuis le stockage d’objets blob
Quand il est nécessaire de stocker de grandes quantités de données non structurées dans le cloud, le stockage Blob Azure offre une solution peu coûteuse et évolutive. Les données du stockage d’objets blob sont généralement considérées comme des données au repos. Toutefois, les données d’objets blob peuvent être traitées comme un flux de données par Stream Analytics. 

Le traitement des journaux est un scénario couramment utilisé pour se servir des entrées de stockage d’objets blob avec Stream Analytics. Dans ce scénario, des fichiers de données de télémétrie ont été capturés à partir d’un système, et doivent être analysés et traités pour extraire des données significatives.

L’horodatage par défaut des événements de stockage d’objets blob dans Stream Analytics est l’horodatage de la dernière modification de l’objet blob, soit `BlobLastModifiedUtcTime`. Pour traiter les données en tant que flux à l’aide d’un horodatage dans la charge utile d’événement, vous devez utiliser le mot-clé [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

Les entrées au format CSV *nécessitent* une ligne d’en-tête pour définir les champs du jeu de données. En outre, tous les champs de ligne d’en-tête doivent être uniques.

Actuellement, Stream Analytics ne prend pas en charge la désérialisation des messages AVRO générés par la capture du concentrateur Event Hub ou le point de terminaison personnalisé du conteneur Stockage Azure IoT Hub.

> [!NOTE]
> Stream Analytics ne prend pas en charge l’ajout de contenu à un objet blob existant. Stream Analytics n’affiche chaque fichier qu’une seule fois, et toutes les modifications qui sont apportées à celui-ci, une fois que le travail a lu les données, ne sont pas traitées. Une meilleure pratique consiste à télécharger toutes les données d’un objet blob en une seule fois, puis d’ajouter les événements récents supplémentaires dans un fichier blob nouveau et différent.
> 

### <a name="configure-blob-storage-as-a-stream-input"></a>Configurer un stockage d’objets blob en tant qu’entrée de flux de données 

Le tableau suivant décrit chaque propriété de la page **Nouvelle entrée** du portail Azure lorsque vous configurez un stockage d’objets blob en tant qu’entrée de flux de données.

| Propriété | Description |
| --- | --- |
| **Alias d’entrée** | Nom convivial que vous utilisez dans la requête du travail pour faire référence à cette entrée. |
| **Abonnement** | Sélectionnez l’abonnement dans lequel se trouve la ressource du concentrateur IoT Hub. | 
| **Compte de stockage** | Nom du compte de stockage dans lequel se trouvent les fichiers d’objets blob. |
| **Clé du compte de stockage** | Clé secrète associée au compte de stockage. Cette option est automatiquement renseignée, sauf si vous sélectionnez l’option pour indiquer manuellement les paramètres du stockage d’objets blob. |
| **Conteneur** | Conteneur pour les entrées d’objets blob. Les conteneurs fournissent un regroupement logique des objets blob stockés dans le service d’objets blob Microsoft Azure. Lorsque vous chargez un objet blob dans le service de stockage Blob Azure, vous devez spécifier un conteneur pour cet objet blob. Vous pouvez choisir l’option **Use existing container** (Utiliser un conteneur existant) ou **Créer nouveau**.|
| **Modèle de chemin d’accès** (facultatif) | Chemin d’accès au fichier utilisé pour localiser les objets blob dans le conteneur spécifié. Dans le chemin d’accès, vous pouvez spécifier une ou plusieurs instances de l’une des trois variables suivantes : `{date}`, `{time}` ou `{partition}`<br/><br/>Exemple 1 : `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Exemple 2 : `cluster1/logs/{date}`<br/><br/>Le caractère `*` n’est pas une valeur autorisée pour le préfixe du chemin d’accès. Seuls les <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">caractères d’objet blob Azure</a> valides sont autorisés. |
| **Format de date** (facultatif) | Format de date suivant lequel les fichiers sont organisés si vous utilisez la variable de date dans le chemin d’accès. Exemple : `YYYY/MM/DD` |
| **Format d’heure** (facultatif) |  Format d’heure suivant lequel les fichiers sont organisés si vous utilisez la variable d’heure dans le chemin d’accès. Actuellement, la seule valeur possible est `HH` pour les heures. |
| **Format de sérialisation de l’événement** | Format de sérialisation (JSON, CSV ou Avro) des flux de données entrants. |
| **Encodage** | Pour CSV et JSON, UTF-8 est le seul format d’encodage actuellement pris en charge. |
| **Compression** | Le type de compression utilisé pour lire le flux de données entrant, tel que Aucune (valeur par défaut), GZip ou Deflate. |

Si vos données proviennent d’une source de stockage d’objets blob, vous avez accès aux champs de métadonnées suivants dans votre requête Stream Analytics :

| Propriété | Description |
| --- | --- |
| **BlobName** |Nom de l’objet blob d’entrée d’où provient l’événement. |
| **EventProcessedUtcTime** |Date et heure du traitement de l’événement par Stream Analytics |
| **BlobLastModifiedUtcTime** |Date et heure de la dernière modification apportée à l’objet blob. |
| **PartitionId** |ID de partition de base zéro de l'adaptateur d'entrée. |

Par exemple, en utilisant ces champs, vous pouvez écrire une requête semblable à l’exemple suivant :

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Étapes suivantes
Vous avez appris à connaître les options de connexion de données dans Azure pour vos travaux Stream Analytics. Pour en savoir plus sur Stream Analytics, consultez :

* [Prise en main d'Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
