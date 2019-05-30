---
title: Ajouter une source d’événement Event Hubs à Azure Time Series Insights | Microsoft Docs
description: Cet article explique comment ajouter une source d’événement qui est connectée à Azure Event Hubs dans votre environnement Time Series Insights.
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
ms.openlocfilehash: 8040368f4cbd6d264070aa3db0a8e6b07a866480
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239025"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Ajouter une source d’événement Event Hub à votre environnement Time Series Insights

Cet article explique comment utiliser le Portail Azure pour ajouter une source d’événement qui lit des données à partir d’Azure Event Hubs dans votre environnement Azure Time Series Insights.

> [!NOTE]
> Les étapes décrites dans cet article s’appliquent à la fois aux environnements Time Series Insights - GA et Time Series Insights - Préversion.

## <a name="prerequisites"></a>Conditions préalables

- Créer un environnement Time Series Insights comme décrit dans [créer un environnement Azure Time Series Insights](./time-series-insights-update-create-environment.md).
- Créez un hub d’événements. Consultez [créer un espace de noms Event Hubs et un concentrateur d’événements à l’aide du portail Azure](../event-hubs/event-hubs-create.md).
- L’Event Hub doit avoir reçu des événements de message actifs. Découvrez comment [envoyer des événements vers Azure Event Hubs à l’aide de .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Créez un groupe de consommateurs dédié dans l’Event Hub que l’environnement Time Series Insights peut utiliser. Chaque source d’événement Time Series Insights doit avoir son propre groupe de consommateurs dédié, qui n’est pas partagé avec un autre consommateur. Si plusieurs lecteurs consomment des événements du même groupe de consommateurs, tous les lecteurs sont susceptibles d’obtenir des erreurs. Il existe également une limite de 20 groupes de consommateurs par Event Hub. Pour plus d’informations, consultez le [Guide de programmation Event Hubs](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Ajouter un groupe de consommateurs à votre Event Hub

Les applications utilisent des groupes de consommateurs pour extraire des données d’Azure Event Hubs. Fournissez un groupe de consommateurs dédié, qui sera utilisé par cet environnement Time Series Insights uniquement, pour lire les données de manière fiable à partir de votre Event Hub.

Pour ajouter un groupe de consommateurs dans votre Event Hub :

1. Dans le Portail Azure, recherchez et ouvrez votre Event Hub.

1. Sous **Entités**, sélectionnez **Groupes de consommateurs**, puis **Groupe de consommateurs**.

   [![Hub d’événements - ajouter un groupe de consommateurs](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png#lightbox)

1. Sur la page **Groupes de consommateurs**, indiquez une valeur unique pour **Nom**.  Utilisez le même nom lorsque vous créez une source d’événement dans l’environnement Time Series Insights.

1. Sélectionnez **Créer**.

## <a name="add-a-new-event-source"></a>Ajouter une nouvelle source d’événement

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Recherchez votre environnement Time Series Insights existant. Dans le menu de gauche, sélectionnez **Toutes les ressources**, puis sélectionnez votre environnement Time Series Insights.

1. Sous **Topologie de l’environnement**, sélectionnez **Sources d’événements**, puis sélectionnez **Ajouter**.

   [![Sous les Sources d’événements, sélectionnez le bouton Ajouter](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png#lightbox)

1. Sous **Nom de la source de l’événement**, indiquez une valeur unique pour cet environnement Time Series Insights, comme **event-stream**.

1. Pour **Source**, sélectionnez **Event Hub**.

1. Sélectionnez les valeurs appropriées sous **Option d’importation** :
   - Si un Event Hub est déjà présent dans l’un de vos abonnements, sélectionnez **Utiliser un Event Hub à partir des abonnements disponibles**. Il s’agit de l’approche la plus simple.
   - Sélectionnez **Indiquez manuellement les paramètres Event Hub** si l’Event Hub est externe à vos abonnements ou si vous souhaitez choisir des options avancées.

   [![Dans le nouveau volet de source de l’événement, entrez des valeurs pour les trois premiers paramètres](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png#lightbox)

1. Le tableau suivant décrit les propriétés requises pour l’option **Utiliser un Event Hub à partir des abonnements disponibles** :

   [![Détails du hub abonnement et d’événement](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png#lightbox)

   | Propriété | Description |
   | --- | --- |
   | ID d’abonnement | Sélectionnez l’abonnement dans lequel ce Event Hub a été créé.
   | Espace de noms Service Bus | Sélectionnez l’espace de noms Azure Service Bus qui contient l’Event Hub.
   | Nom du hub d’événements | Sélectionnez le nom de l’Event Hub.
   | Nom de la stratégie du hub d’événements | Sélectionnez la stratégie d’accès partagé. Vous pouvez créer la stratégie d’accès partagé dans l’onglet **Configurer** de l’Event Hub. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. La stratégie d’accès partagé pour votre source d’événements *doit* avoir des autorisations de **lecture**.
   | Clé de la stratégie du Event Hub | La valeur de clé peut être prédéfinie.
   | Groupe de consommateurs du Event Hub | Groupe de consommateurs qui lit les événements de l’Event Hub. Il est vivement recommandé d’utiliser un groupe de consommateurs dédié pour votre source de l’événement. |
   | Format de sérialisation de l’événement | Actuellement, JSON est le seul format de sérialisation disponible. Les messages d’événement doivent respecter ce format, sans quoi aucune donnée ne peut être lue. |
   | Nom de la propriété d’horodatage | Pour déterminer cette valeur, vous devez comprendre le format de message des données de message envoyées dans l’Event Hub. Cette valeur est le **nom** de la propriété d’événement spécifique dans les données de message à utiliser comme horodateur de l’événement. Cette valeur respecte la casse. Lorsque ce champ est vide, l’**heure de mise en file d’attente de l’événement** dans la source de l’événement est utilisée comme timestamp de l’événement. |

1. Le tableau suivant décrit les propriétés requises pour l’option **Indiquez manuellement les paramètres Event Hub** :

   | Propriété | Description |
   | --- | --- |
   | ID d’abonnement | Abonnement dans lequel ce Event Hub a été créé.
   | Groupe de ressources | Le groupe de ressources dans lequel ce Event Hub a été créé.
   | Espace de noms Service Bus | Un espace de noms Service Bus est un conteneur pour un jeu d’entités de messagerie. En créant un Event Hub, vous avez créé un espace de noms Service Bus.
   | Nom du hub d’événements | Nom de votre Event Hub. Lorsque vous avez créé votre Event Hub, vous lui avez également donné un nom spécifique.
   | Nom de la stratégie du hub d’événements | Stratégie d’accès partagé. Vous pouvez créer une stratégie d’accès partagé dans l’onglet **Configurer** de l’Event Hub. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. La stratégie d’accès partagé pour votre source d’événements *doit* avoir des autorisations de **lecture**.
   | Clé de la stratégie du Event Hub | Clé d’accès partagé qui permet d’authentifier l’accès à l’espace de noms Service Bus. Entrez la clé primaire ou secondaire ici.
   | Groupe de consommateurs du Event Hub | Groupe de consommateurs qui lit les événements de l’Event Hub. Il est vivement recommandé d’utiliser un groupe de consommateurs dédié pour votre source de l’événement.
   | Format de sérialisation de l’événement | Actuellement, JSON est le seul format de sérialisation disponible. Les messages d’événement doivent respecter ce format, sans quoi aucune donnée ne peut être lue. |
   | Nom de la propriété d’horodatage | Pour déterminer cette valeur, vous devez comprendre le format de message des données de message envoyées dans l’Event Hub. Cette valeur est le **nom** de la propriété d’événement spécifique dans les données de message à utiliser comme horodateur de l’événement. Cette valeur respecte la casse. Lorsque ce champ est vide, l’**heure de mise en file d’attente de l’événement** dans la source de l’événement est utilisée comme timestamp de l’événement. |

1. Ajoutez le nom du groupe de consommateurs Time Series Insights dédié que vous avez ajouté à votre Event Hub.

1. Sélectionnez **Créer**.

   [![Sélectionnez Créer](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png#lightbox)

   Après la création de la source de l’événement, Time Series Insights démarre automatiquement la diffusion en continu des données dans votre environnement.

## <a name="next-steps"></a>Étapes suivantes

* [Définissez les stratégies d’accès aux données](time-series-insights-data-access.md) pour sécuriser les données.

* [Envoyez des événements](time-series-insights-send-events.md) à la source d’événement.

* Accédez à votre environnement dans [l’explorateur Time Series Insights](https://insights.timeseries.azure.com).
