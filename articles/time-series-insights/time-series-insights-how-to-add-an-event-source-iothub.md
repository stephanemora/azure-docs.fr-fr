---
title: Guide pratique pour ajouter une source d’événement de hub IoT à Azure Time Series Insights | Microsoft Docs
description: Cet article décrit comment ajouter une source d’événement connectée à un IoT Hub à votre environnement Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/01/2019
ms.custom: seodec18
ms.openlocfilehash: 79d2062f0e174eeb7550205f90c1650185e1623a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236981"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>Ajouter une source d’événement IoT Hub à votre environnement Time Series Insights

Cet article décrit comment utiliser le portail Azure pour ajouter une source d’événement qui lit des données à partir d’un IoT Hub Azure dans votre environnement Azure Time Series Insights.

> [!NOTE]
> Les instructions dans cet article s’appliquent à la fois aux environnements Azure Time Series Insights GA et Time Series Insights Préversion.

## <a name="prerequisites"></a>Conditions préalables

* Créez un [environnement Azure Time Series Insights](time-series-insights-update-create-environment.md).
* Créez un [IoT Hub à l’aide du portail Azure](../iot-hub/iot-hub-create-through-portal.md).
* L’IoT Hub doit avoir des événements de message actifs envoyés.
* Créez un groupe de consommateurs dédié dans IoT Hub pour l’environnement Time Series Insights à utiliser. Chaque source d’événement Time Series Insights doit avoir son propre groupe de consommateurs dédié, qui n’est pas partagé avec un autre consommateur. Si plusieurs lecteurs consomment des événements du même groupe de consommateurs, tous les lecteurs sont susceptibles d’obtenir des erreurs. Pour plus d’informations, reportez-vous au [Guide du développeur Azure IoT Hub](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Ajouter un groupe de consommateurs à votre instance IoT Hub

Les applications utilisent des groupes de consommateurs pour extraire des données d’Azure IoT Hub. Indiquez un groupe de consommateurs dédiés, qui sera utilisé par cet environnement Time Series Insights uniquement, pour lire les données de manière fiable à partir de votre IoT Hub.

Pour ajouter un nouveau groupe de consommateurs à votre IoT Hub :

1. Dans le portail Azure, recherchez et ouvrez votre IoT Hub.

1. Dans le menu, sous **Paramètres**, sélectionnez **Points de terminaison intégrés**, puis sélectionnez le point de terminaison **Événements**.

   [![Dans la page de Build dans les points de terminaison, sélectionnez le bouton événements](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_one.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_one.png#lightbox)

1. Sous **Groupes de consommateurs**, entrez un nom unique pour le groupe de consommateurs. Utilisez le même nom dans votre environnement Time Series Insights lors de la création d’une source d’événement.

1. Sélectionnez **Enregistrer**.

## <a name="add-a-new-event-source"></a>Ajouter une nouvelle source d’événement

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Dans le menu de gauche, sélectionnez **Toutes les ressources**. Sélectionnez votre environnement Time Series Insights.

1. Sous **Topologie de l’environnement**, sélectionnez **Sources d’événements**, puis sélectionnez **Ajouter**.

   [![Sélectionnez les Sources d’événements, puis sélectionnez le bouton Ajouter](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_two.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_two.png#lightbox)

1. Dans le volet **Nouvelle source d’événements**, pour **Nom de source d’événement**, entrez un nom unique pour cet environnement Time Series Insights. Par exemple, entrez **event-stream**.

1. Pour **Source**, sélectionnez **IoT Hub**.

1. Sélectionnez une valeur pour **Option d’importation** :

   * Si vous avez déjà un IoT Hub dans l’un de vos abonnements, sélectionnez **Utiliser IoT Hub à partir des abonnements disponibles**. Il s’agit de l’approche la plus simple.
   * Sélectionnez **Fournir les paramètres d’IoT Hub manuellement** si l’IoT Hub est externe à vos abonnements ou si vous souhaitez choisir des options avancées.

   [![Sélectionnez les options dans le nouveau volet de source d’événement](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_three.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_three.png#lightbox)

1. Le tableau suivant décrit les propriétés requises pour l’option **Utiliser IoT Hub à partir des abonnements disponibles** :

   [![Nouveau volet de source des événements - propriétés à définir dans l’utilisation de IoT Hub à partir de l’option d’abonnements disponibles](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_four.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_four.png#lightbox)

   | Propriété | Description |
   | --- | --- |
   | Identifiant d’abonnement | Sélectionnez l’abonnement dans lequel l’IoT Hub a été créé.
   | Nom de l’IoT Hub | Sélectionnez le nom de l’IoT Hub.
   | Nom de la stratégie IoT Hub | Sélectionnez la stratégie d’accès partagé. Vous trouverez la stratégie d’accès partagé dans l’onglet Paramètres IoT Hub. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. La stratégie d’accès partagé pour votre source d’événements *doit* avoir des autorisations de **connexion au service**.
   | Clé de stratégie IoT Hub | La clé est déjà renseignée.
   | Groupe de consommateurs IoT Hub | Groupe de consommateurs qui lit les événements de l’IoT Hub. Il est vivement recommandé d’utiliser un groupe de consommateurs dédié pour votre source d’événements.
   | Format de sérialisation de l’événement | Actuellement, JSON est le seul format de sérialisation disponible. Les messages d’événement doivent respecter ce format, sans quoi aucune donnée ne peut être lue. |
   | Nom de la propriété d’horodatage | Pour déterminer cette valeur, vous devez comprendre le format de message des données de message envoyées dans IoT Hub. Cette valeur est le **nom** de la propriété d’événement spécifique dans les données de message à utiliser comme horodateur de l’événement. Cette valeur respecte la casse. Lorsque ce champ est vide, **l’heure de mise en file d’attente de l’événement** dans la source d’événement est utilisée comme horodateur de l’événement. |

1. Le tableau suivant décrit les propriétés requises pour l’option **Fournir des paramètres IoT Hub manuellement** :

   | Propriété | Description |
   | --- | --- |
   | Identifiant d’abonnement | Abonnement dans lequel l’IoT hub a été créé.
   | Groupe de ressources | Nom du groupe de ressources dans lequel l’IoT Hub a été créé.
   | Nom de l’IoT Hub | Nom de votre IoT Hub. Lorsque vous avez créé votre IoT Hub, vous lui avez donné un nom.
   | Nom de la stratégie IoT Hub | Stratégie d’accès partagé. Vous pouvez créer la stratégie d’accès partagé dans l’onglet Paramètres IoT Hub. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. La stratégie d’accès partagé pour votre source d’événements *doit* avoir des autorisations de **connexion au service**.
   | Clé de stratégie IoT Hub | Clé d’accès partagé utilisée pour authentifier l’accès à l’espace de noms Azure Service Bus. Entrez la clé primaire ou secondaire ici.
   | Groupe de consommateurs IoT Hub | Groupe de consommateurs qui lit les événements de l’IoT Hub. Il est vivement recommandé d’utiliser un groupe de consommateurs dédié pour votre source d’événements.
   | Format de sérialisation de l’événement | Actuellement, JSON est le seul format de sérialisation disponible. Les messages d’événement doivent respecter ce format, sans quoi aucune donnée ne peut être lue. |
   | Nom de la propriété d’horodatage | Pour déterminer cette valeur, vous devez comprendre le format de message des données de message envoyées dans IoT Hub. Cette valeur est le **nom** de la propriété d’événement spécifique dans les données de message à utiliser comme horodateur de l’événement. Cette valeur respecte la casse. Lorsque ce champ est vide, **l’heure de mise en file d’attente de l’événement** dans la source d’événement est utilisée comme horodateur de l’événement. |

1. Ajoutez le nom du groupe de consommateurs Time Series Insights dédié que vous avez ajouté à votre IoT Hub.

1. Sélectionnez **Créer**.

   [![Le bouton Créer](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_five.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_five.png#lightbox)

1. Après la création de la source d’événement, Time Series Insights démarre automatiquement la diffusion de données dans votre environnement.

## <a name="next-steps"></a>Étapes suivantes

* [Définissez les stratégies d’accès aux données](time-series-insights-data-access.md) pour sécuriser les données.

* [Envoyez des événements](time-series-insights-send-events.md) à la source d’événement.

* Accédez à votre environnement dans [l’explorateur Time Series Insights](https://insights.timeseries.azure.com).
