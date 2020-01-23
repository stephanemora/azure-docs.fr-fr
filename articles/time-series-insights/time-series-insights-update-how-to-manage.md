---
title: Provisionner et gérer un environnement en préversion - Azure Time Series | Microsoft Docs
description: Apprenez à provisionner et gérer un environnement Azure Time Series Insights en préversion.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/19/2019
ms.custom: seodec18
ms.openlocfilehash: b70604c62ae21f9c433b3cd7d9e59f4ccebb61bd
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861725"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Approvisionner et gérer la préversion d’Azure Time Series Insights

Cet article explique comment créer et gérer un environnement Azure Time Series Insights en préversion via le [portail Azure](https://portal.azure.com/).

## <a name="overview"></a>Vue d’ensemble

Les environnements Azure Time Series Insights en préversion sont des environnements avec *paiement à l’utilisation* (PAYG).

Lorsque vous provisionnez un environnement Azure Time Series Insights en préversion, vous créez ces ressources Azure :

* Un environnement Azure Time Series Insights en préversion  
* Un compte Stockage Azure à usage général v1
* Un magasin chaud facultatif pour des requêtes plus rapides et illimitées

> [!TIP]
> * Découvrez [comment planifier votre environnement](./time-series-insights-update-plan.md).
> * Lisez les articles expliquant comment [Ajouter une source de hub d’événements](./time-series-insights-how-to-add-an-event-source-eventhub.md) et comment [Ajouter une source de hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).

Vous apprendrez à :

1. **(Facultatif)**  Associez chaque environnement Azure Time Series Insights en préversion à une source d’événement. Vous devez également fournir une propriété ID d’horodatage et un groupe de consommateurs unique pour garantir que l’environnement a accès aux événements appropriés.

   > [!NOTE]
   > L’étape précédente est facultative lors du provisionnement d’un environnement. Si vous ignorez cette étape, vous devez attacher une source d’événement à l’environnement ultérieurement afin que les données puissent être accessibles dans l’environnement.

1. Une fois l’approvisionnement terminé, vous pouvez modifier vos stratégies d’accès et d’autres attributs de l’environnement pour qu’ils correspondent à vos besoins.

## <a name="create-the-environment"></a>Créer l’environnement

Pour créer un environnement Azure Time Series Insights en préversion :

1. Sélectionnez **PAYG** comme **Niveau**. Fournissez un nom d’environnement et choisissez le groupe d’abonnements et le groupe de ressources à utiliser. Sélectionnez ensuite un emplacement pris en charge pour héberger l’environnement.

   [![Créez une instance Azure Time Series Insights.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Entrez un ID Time Series.

    > [!NOTE]
    > * L’ID de série chronologique *respecte la casse* et est *immuable*. (il ne peut pas être modifié une fois défini).
    > * Les ID de série chronologique peuvent comprendre jusqu’à *trois* clés.
    > * En savoir plus sur la façon de [choisir un ID de série chronologique](time-series-insights-update-how-to-id.md)

1. Créez un compte de Stockage Azure en sélectionnant un nom de compte de stockage et en spécifiant un choix de réplication. Un compte Stockage Azure à usage général v1 est alors automatiquement créé. Ce compte est créé dans la même région que l’environnement Azure Time Series Insights en préversion que vous avez sélectionné précédemment.

    [![Configuration d’un stockage froid](media/v2-update-manage/create-and-manage-cold-store.png)](media/v2-update-manage/create-and-manage-cold-store.png#lightbox)

1. **(Facultatif)** Activez le magasin chaud pour votre environnement si vous voulez des requêtes plus rapides et illimitées sur les données les plus récentes de votre environnement. Vous pouvez également créer ou supprimer un magasin chaud par le biais de l’option **Configuration du stockage** dans le volet de navigation gauche, après avoir créé un environnement Time Series Insights en préversion.

    [![Configuration d’un stockage chaud](media/v2-update-manage/create-and-manage-warm-storage.png)](media/v2-update-manage/create-and-manage-warm-storage.png#lightbox)

1. **(Facultatif)** Vous pouvez ajouter une source d’événement maintenant. Vous pouvez également attendre que l’instance soit provisionnée.

   * Time Series Insights prend en charge [Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) et [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) en tant que source d’événement. Si vous ne pouvez ajouter qu’une seule source d’événement lors de la création d’un environnement, vous pouvez en ajouter une autre ultérieurement. 
   
     Vous pouvez sélectionner un groupe de consommateurs existant ou bien en créer un nouveau lors de l’ajout de la source d’événement. Il est préférable de créer un groupe de consommateurs unique pour vous assurer que tous les événements sont visibles par votre environnement Azure Time Series Insights en préversion.

   * Choisissez la propriété Timestamp appropriée. Par défaut, Azure Time Series Insights utilise l’heure de placement du message dans la file d’attente pour chaque source d’événement.

     > [!TIP]
     > Il est possible que l’heure de placement du message dans la file d’attente ne soit pas le meilleur paramètre configuré à utiliser dans les scénarios de traitement par lots des événements ou de chargement des données historiques. Le cas échéant, veillez à vérifier votre choix d’utiliser ou non une propriété Timestamp.

     [![Onglet de configuration de la source d’événement](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Vérifiez que votre environnement a été provisionné et configuré comme vous le souhaitez.

    [![Onglet Vérifier + créer](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>Gérer l’environnement

Vous pouvez gérer votre environnement Azure Time Series Insights en préversion à l’aide du portail Azure. Il y a quelques différences essentielles entre un environnement Azure Time Series Insights PAYG (paiement à l’utilisation) en préversion et les environnements S1 ou S2 en disponibilité générale à ne pas oublier lorsque vous gérez votre environnement par le biais du portail Azure :

* Le panneau **Vue d’ensemble** de la préversion du portail Azure présente les changements suivants :

  * La capacité est supprimée car elle ne s’applique pas aux environnements PAYG (paiement à l’utilisation).
  * La propriété d’**ID de série chronologique** est ajoutée. Elle détermine la façon dont vos données sont partitionnées.
  * Les jeux de données de référence sont supprimés.
  * L’URL affichée vous dirige vers [l’Explorateur Azure Time Series Insights en préversion](./time-series-insights-update-explorer.md).
  * Le nom de votre compte de stockage Azure est répertorié.

* Le panneau **Configurer** du portail Azure est supprimé dans Azure Time Series Insights en préversion parce que les environnements de paiement à l’utilisation ne sont pas configurables. Toutefois, vous pouvez utiliser **Configuration du stockage** pour configurer le magasin chaud récemment introduit.

* Le panneau **Données de référence** du portail Azure est supprimé dans Azure Time Series Insights en préversion parce que les données de référence ne font pas partie des environnements PAYG (paiement à l’utilisation).

[![Environnement Time Series Insights en préversion dans le portail Azure](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les environnements Time Series Insights en disponibilité générale et en préversion, consultez [Planifier votre environnement](./time-series-insights-update-plan.md).

- Découvrez comment [ajouter une source Event Hub](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Configurez une [source de hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).
