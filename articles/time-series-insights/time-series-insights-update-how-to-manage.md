---
title: Approvisionner et gérer la préversion d’Azure Time Series Insights | Microsoft Docs
description: Comprendre comment approvisionner et gérer la préversion d’Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/26/2019
ms.custom: seodec18
ms.openlocfilehash: f626ce2e009a18afcb4d04b7caa6850ea58c7483
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446811"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Approvisionner et gérer la préversion d’Azure Time Series Insights

Cet article explique comment créer et gérer un environnement Azure Time Series Insights en préversion via le [portail Azure](https://portal.azure.com/).

## <a name="overview"></a>Vue d'ensemble

Les environnements Azure Time Series Insights en préversion sont des environnements payants à l’utilisation.

Lorsque vous approvisionnez un environnement Azure Time Series Insights en préversion, vous créez deux ressources Azure :

* Un environnement Azure Time Series Insights en préversion  
* Un compte Stockage Azure à usage général v1
  
Découvrez [comment planifier votre environnement](./time-series-insights-update-plan.md).

>[!IMPORTANT]
> Pour la préversion, assurez-vous que vous utilisez un compte de stockage Azure v1 universel (GPv1).

Vous pouvez également associer chaque environnement Azure Time Series Insights en préversion à une source d’événement. Pour plus d’informations, voir [Ajouter une source de hub d’événements](./time-series-insights-how-to-add-an-event-source-eventhub.md) et [Ajouter une source de hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md). Pendant cette étape, vous fournissez une propriété d’ID d’horodatage et un groupe de consommateurs unique. Cela garantit l’accès de l’environnement aux événements appropriés.

Une fois l’approvisionnement terminé, vous pouvez modifier vos stratégies d’accès et d’autres attributs de l’environnement pour qu’ils correspondent à vos besoins.

## <a name="create-the-environment"></a>Créer l’environnement

Les étapes suivantes permettent de créer un environnement Azure Time Series Insights en préversion :

1. Sélectionnez le bouton **PAYG** sous le menu **référence SKU**. Fournissez un nom d’environnement et choisissez le groupe d’abonnements et le groupe de ressources à utiliser. Ensuite, sélectionnez un emplacement pris en charge pour héberger l’environnement.

   [![Créez une instance Azure Time Series Insights.](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

1. Entrez un ID Time Series.

    >[!NOTE]
    > * L’ID Time Series respecte la casse et est immuable (il ne peut pas être modifié une fois défini).
    > * Les ID Time Series peuvent comprendre jusqu’à trois clés.
    > * Pour plus d’informations sur la sélection d’un ID Time Series, voir [Choisir un ID Time Series](./time-series-insights-update-how-to-id.md).

1. Créez un compte de stockage Azure en sélectionnant un nom de compte de stockage et en spécifiant un choix de réplication. Un compte Stockage Azure à usage général v1 est alors automatiquement créé. Il sera créé dans la même région que l’environnement Azure Time Series Insights en préversion que vous avez sélectionné précédemment.

    [![Créer un compte de stockage Azure pour votre instance](media/v2-update-manage/manage-five.png)](media/v2-update-manage/manage-five.png#lightbox)

1. Si vous le souhaitez, vous pouvez ajouter une source d’événement.

   * Time Series Insights prend en charge [Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) et [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md). Si vous ne pouvez ajouter qu’une seule source d’événement lors de la création d’un environnement, vous pouvez en ajouter une autre ultérieurement. Il est préférable de créer un groupe de consommateurs unique pour vous assurer que tous les événements sont visibles par votre instance Azure Time Series Insights en préversion. Vous pouvez sélectionner un groupe de consommateurs existant ou bien en créer un nouveau lors de l’ajout de la source d’événement.

   * Vous devez également choisir la propriété Timestamp appropriée. Par défaut, Azure Time Series Insights utilise l’heure de placement du message dans la file d’attente pour chaque source d’événement.

     > [!TIP]
     > Il est possible que l’heure de placement du message dans la file d’attente ne soit pas le paramètre le mieux configuré à utiliser pour un l’événement de traitement par lots ou des scénarios de chargement des données historiques. Veillez à vérifier votre choix d’utiliser ou non une propriété Timestamp dans de tels cas.

     [![Onglet Source de l’événement](media/v2-update-manage/manage-two.png)](media/v2-update-manage/manage-two.png#lightbox)

1. Vérifiez que votre environnement a été approvisionné avec les paramètres souhaités.

    [![Onglet Vérifier + créer](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

## <a name="manage-the-environment"></a>Gérer l’environnement

Vous pouvez gérer votre environnement Azure Time Series Insights en préversion à l’aide du portail Azure. Voici les principales différences dans la gestion d’un environnement Azure Time Series Insights en préversion payant à l’utilisation par rapport à un environnement S1 ou S2, en utilisant le portail Azure :

* Le panneau **Vue d’ensemble** du portail Azure est inchangé dans Azure Time Series Insights, à quelques exceptions près :
  * La capacité est supprimée car ce concept ne s’applique pas aux environnements payants à l’utilisation.
  * La propriété d’ID Time Series a été ajoutée. Elle détermine la façon dont vos données sont partitionnées.
  * Les jeux de données de référence sont supprimés.
  * L’URL affichée vous dirige vers [l’Explorateur Azure Time Series Insights en préversion](./time-series-insights-update-explorer.md).
  * Le nom de votre compte de stockage Azure est répertorié.

* Le panneau **Configurer** du portail Azure a été supprimé dans Azure Time Series Insights en préversion parce que les environnements payants à l’utilisation ne sont pas configurables.

* Le panneau **Données de référence** du portail Azure a été supprimé dans Azure Time Series Insights en préversion parce que les données de référence ne sont pas un composant des environnements payants à l’utilisation.

[![Environnement Time Series Insights en préversion dans le portail Azure](media/v2-update-manage/manage-four.png)](media/v2-update-manage/manage-four.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

- Lire [Planifier votre environnement](./time-series-insights-update-plan.md).

- Découvrez comment [ajouter une source Event Hub](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Configurez [une source de hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).