---
title: Comprendre les entrées d’Azure Stream Analytics
description: Cet article décrit le concept des entrées d’un travail Azure Stream Analytics, en comparant les entrées de diffusion en continu aux entrées des données de référence.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/25/2018
ms.openlocfilehash: 0475318ce983b98951fb9cd5d24a5bc4452b1f1e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52970108"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Comprendre les entrées d’Azure Stream Analytics

Les travaux Azure Stream Analytics se connectent à une ou plusieurs entrées de données. Chaque entrée définit une connexion à une source de données existante. Stream Analytics accepte des données provenant de divers types de sources d’événements, notamment Event Hubs, IoT Hub et le stockage d’objets blob. Les entrées sont référencées à l’aide du nom de la requête SQL de diffusion en continu que vous écrivez pour chaque travail. Dans la requête, vous pouvez joindre plusieurs entrées pour fusionner des données ou pour comparer des données de diffusion en continu à une recherche pour référencer des données, puis transmettre les résultats aux sorties. 

Stream Analytics possède une intégration de première classe provenant de trois types de ressources en tant qu’entrées :
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [stockage d’objets blob Azure](https://azure.microsoft.com/services/storage/blobs/) 

Ces ressources d’entrée peuvent résider dans le même abonnement Azure que votre travail Stream Analytics ou provenir d’un autre abonnement.

Vous pouvez utiliser le [portail Azure](stream-analytics-quick-create-portal.md#configure-job-input), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/New-AzureRmStreamAnalyticsInput), [API .Net](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), [API REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input) et [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) pour créer, modifier et tester les entrées du travail Stream Analytics.

## <a name="stream-and-reference-inputs"></a>Entrées de flux de données et entrées de données de référence
Lorsque les données sont transmises à une source de données, elles sont utilisées par le travail Stream Analytics et traitées en temps réel. Les entrées sont réparties en deux types : les entrées de flux de données et les entrées de données de référence.

### <a name="data-stream-input"></a>Entrée de flux de données
Un flux de données est une séquence illimitée d’événements au fil du temps. Les travaux Stream Analytics doivent contenir au moins une entrée de flux de données. Le stockage d’objets blob, Event Hubs et IoT Hub sont pris en charge en tant que sources d’entrée de flux de données. Les concentrateurs Event Hubs permettent de collecter des flux d’événements à partir de plusieurs appareils et services. Il peut s’agir de flux d’activités de réseaux sociaux, d’informations boursières ou de données provenant de capteurs. Les conteneurs IoT Hub sont optimisés pour collecter des données à partir d’appareils connectés dans les scénarios Internet des objets (IoT).  Le stockage d’objets blob peut être utilisé comme source d’entrée pour la réception de données en bloc en tant que flux, par exemple des fichiers journaux.  

Pour plus d’informations sur les entrées de données de diffusion en continu, consultez [Connexion de données : en savoir plus sur les entrées de flux de données pour Stream Analytics](stream-analytics-define-inputs.md).

### <a name="reference-data-input"></a>Entrée de données de référence
Stream Analytics prend également en charge des entrées appelées *données de référence*. Les données de référence sont complètement statiques ou elles subissent de lentes modifications. Elles sont généralement utilisées pour la corrélation et les recherches. Par exemple, vous pouvez joindre des données de l’entrée de flux de données à des données de référence, comme vous effectueriez une jointure SQL pour rechercher des valeurs statiques. Le stockage d’objets blob Azure est la seule source d’entrée prise en charge pour les données de référence. Les objets blob des sources de données de référence ont une limite maximale de 300 Mo en taille, en fonction de la complexité des requêtes et des unités de streaming allouées.

Pour plus d’informations sur les entrées de données de référence, consultez [Utiliser des données de référence pour effectuer des recherches dans Stream Analytics](stream-analytics-use-reference-data.md).

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Démarrage rapide : Créer une tâche Stream Analytics à l’aide du Portail Azure](stream-analytics-quick-create-portal.md)
