---
title: Configurer un environnement Gen2 à l'aide du portail Azure - Azure Time Series Insights Gen2 | Microsoft Docs
description: Apprenez à configurer un environnement dans Azure Time Series Insights Gen2 à l'aide du portail Azure.
author: riserrad
ms.author: riserrad
manager: edett
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: e10685b1b64e8e3cd636245625c13df12f177fbb
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107951819"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-portal"></a>Créer un environnement Azure Time Series Insights Gen2 à l'aide du portail Azure

Cet article explique comment créer un environnement Azure Time Series Insights Gen2 via le [portail Azure](https://portal.azure.com/).

Le tutoriel d’approvisionnement de l’environnement vous guidera à travers le processus. Vous apprendrez à sélectionner l’ID de la série chronologique correct et à afficher des exemples de deux charges utiles JSON.</br>

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWzk3P]

## <a name="overview"></a>Vue d’ensemble

Lorsque vous provisionnez un environnement Azure Time Series Insights Gen2, vous créez ces ressources Azure :

* Un environnement Azure Time Series Insights Gen2 qui suit le modèle de tarification avec paiement à l’accès
* Un compte Azure Storage
* Un magasin chaud facultatif pour des requêtes plus rapides et illimitées

> [!TIP]
>
> * Découvrez [comment planifier votre environnement](./how-to-plan-your-environment.md).
> * Lisez les articles expliquant comment [Ajouter une source de hub d’événements](./how-to-ingest-data-event-hub.md) et comment [Ajouter une source de hub IoT](./how-to-ingest-data-iot-hub.md).

Vous apprendrez à :

1. Associez chaque environnement Azure Time Series Insights Gen2 à une source d’événement. Vous devez également fournir une propriété ID d’horodatage et un groupe de consommateurs unique pour garantir que l’environnement a accès aux événements appropriés.

1. Une fois l’approvisionnement terminé, vous pouvez modifier vos stratégies d’accès et d’autres attributs de l’environnement pour qu’ils correspondent à vos besoins.

   > [!NOTE]
   > La première étape est facultative lors du provisionnement d’un environnement. Si vous ignorez cette étape, vous devez attacher une source d’événement à l’environnement ultérieurement afin que les données puissent circuler dans l’environnement et être accessibles par des requêtes.

## <a name="create-the-environment"></a>Créer l’environnement

Pour créer un environnement Azure Time Series Insights Gen2 :

1. Créez une ressource Azure Time Series Insights sous *Internet des objets* sur le [portail Azure](https://portal.azure.com/).

1. Sélectionnez **Gen2 (L1)** comme **Niveau**. Fournissez un nom d’environnement et choisissez le groupe d’abonnements et le groupe de ressources à utiliser. Sélectionnez ensuite un emplacement pris en charge pour héberger l’environnement.

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration.png" alt-text="Créez une instance Azure Time Series Insights." lightbox="media/how-to-create-environment-using-portal/environment-configuration.png":::

1. Entrez un ID Time Series.

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration-2.png" alt-text="Créer une configuration d'environnement Azure Time Series Insights (suite)" lightbox="media/how-to-create-environment-using-portal/environment-configuration-2.png":::

   > [!NOTE]
   >
   > * L’ID de série chronologique *respecte la casse* et est *immuable*. (il ne peut pas être modifié une fois défini).
   > * Les ID de série chronologique peuvent comprendre jusqu’à *trois* clés. Considérez cela comme une clé primaire dans une base de données, qui représente de façon unique chaque capteur d’appareil qui envoie des données à votre environnement. Il peut s'agir d'une seule propriété ou d'une combinaison de trois propriétés maximum.
   > * En savoir plus sur la façon de [choisir un ID de série chronologique](./how-to-select-tsid.md)

1. Créez un compte Stockage Azure en sélectionnant un nom et un type de compte de stockage, et en spécifiant un choix de [réplication](../storage/common/redundancy-migration.md?tabs=portal). Un compte Stockage Azure est alors automatiquement créé. Par défaut, un [compte à usage général v2](../storage/common/storage-account-overview.md) sera créé. Ce compte est créé dans la même région que l’environnement Azure Time Series Insights Gen2 que vous avez sélectionné précédemment.
Vous pouvez également apporter votre propre stockage (BYOS) via un [modèle Azure Resource Manager](./time-series-insights-manage-resources-using-azure-resource-manager-template.md) lorsque vous créez un nouvel environnement Azure Time Series Gen2.

1. **(Facultatif)** Activez le magasin chaud pour votre environnement si vous voulez des requêtes plus rapides et illimitées sur les données les plus récentes de votre environnement. Vous pouvez également créer ou supprimer un magasin chaud par le biais de l’option **Configuration du stockage** dans le volet de navigation gauche, après avoir créé un environnement Azure Time Series Insights Gen2.

1. **(Facultatif)** Vous pouvez ajouter une source d’événement maintenant. Vous pouvez également attendre que l’instance soit provisionnée.

   * Azure Time Series Insights prend en charge [Azure IoT Hub](./how-to-ingest-data-iot-hub.md) et [Azure Event Hubs](./how-to-ingest-data-event-hub.md) en tant que source d’événement. Si vous ne pouvez ajouter qu’une seule source d’événement lors de la création d’un environnement, vous pouvez en ajouter une autre ultérieurement.

     Vous pouvez sélectionner un groupe de consommateurs existant ou bien en créer un nouveau lors de l’ajout de la source d’événement. Veillez à ce que la source de l'événement utilise un groupe de consommateurs unique pour permettre à votre environnement d'y lire les données.

   * Choisissez quand commencer à collecter des données à partir de la source de l'événement. Par défaut, la collecte commence au moment où la source d'événement est créée.

     > [!TIP]
     > Si vous choisissez l'option **Toutes mes données** pour collecter des données préexistantes à partir de votre source d'événements, la latence initiale risque d'être élevée, car votre environnement Azure Time Series Insights Gen2 traitera toutes vos données. Cette latence devrait finir par s'estomper à mesure que vos données seront indexées.

   * Choisissez la propriété Timestamp appropriée. Par défaut, Azure Time Series Insights utilise l’heure de placement du message dans la file d’attente pour chaque source d’événement.

     > [!TIP]
     > Il est possible que l’heure de placement du message dans la file d’attente ne soit pas le meilleur paramètre configuré à utiliser dans les scénarios de traitement par lots des événements ou de chargement des données historiques. Le cas échéant, veillez à vérifier votre choix d’utiliser ou non une propriété Timestamp.

   :::image type="content" source="media/how-to-create-environment-using-portal/configure-event-source.png" alt-text="Onglet de configuration de la source d’événement" lightbox="media/how-to-create-environment-using-portal/configure-event-source.png":::

1. Sélectionnez **Vérifier + créer** pour vérifier que votre environnement a été approvisionné et configuré comme vous le souhaitez.

    :::image type="content" source="media/how-to-create-environment-using-portal/environment-confirmation.png" alt-text="Onglet Vérifier + créer" lightbox="media/how-to-create-environment-using-portal/environment-confirmation.png":::

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les environnements Azure Time Series Insights en disponibilité générale et Gen2, consultez [Planifier votre environnement](./how-to-plan-your-environment.md).
* Découvrez les [Sources d'événements d'ingestion de streaming](./concepts-streaming-ingestion-event-sources.md) pour votre environnement Azure Time Series Insights Gen2.
* Découvrez-en plus sur la [gestion de votre environnement](./how-to-provision-manage.md).
