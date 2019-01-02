---
title: Gestion de la préversion d’Azure Time Series - Comment approvisionner et gérer la préversion d’Azure Times Series. | Microsoft Docs
description: Comprendre comment approvisionner et gérer la préversion d’Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: 1834a59ff675f109267a406e3c2b03411e3bb50b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273498"
---
# <a name="how-to-provision-and-manage-azure-time-series-insights-preview"></a>Comment approvisionner et gérer la préversion d’Azure Time Series Insights

Ce document explique comment créer un environnement et gérer Azure Time Series Insights (préversion) via le [portail Azure](https://portal.azure.com/).

## <a name="overview"></a>Vue d’ensemble

Une vue d’ensemble sur la façon d’approvisionner un environnement Azure Time Series Insights en préversion est décrite ci-dessous :

* Les environnements d’Azure Time Series Insights en préversion sont des environnements **PAYG** (paiement à l’utilisation).
  * Dans le cadre du processus de création, vous devez fournir un ID Time Series. Les ID Time Series peuvent comprendre jusqu’à trois clés. Apprenez-en plus sur la sélection d’un ID Time Series en lisant [Comment choisir un ID Time Series](./time-series-insights-update-how-to-id.md).
  * Lorsque vous approvisionnez un environnement Azure Time Series Insights en préversion, vous créez deux ressources Azure :

    * Un environnement Azure Time Series Insights en préversion  
    * Un compte Stockage Azure à usage général V1
  
    Apprenez à [planifier votre environnement](./time-series-insights-update-plan.md).

    >[!IMPORTANT]
    > Pour les clients utilisant des comptes V2, n’activez pas les propriétés de stockage d’archivage/à froid sur le compte de stockage que vous souhaitez utiliser.

* Chaque environnement Azure Time Series Insights en préversion peut être associé à une source d’événement (le cas échéant). Lisez les articles [Comment ajouter une source Event Hub](./time-series-insights-how-to-add-an-event-source-eventhub.md) et [Comment ajouter une source IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) pour plus d’informations.
  * Pendant cette étape, vous fournissez une propriété d’ID d’horodatage et un groupe de consommateurs unique. Ainsi, cela garantit l’accès de l’environnement aux événements appropriés.

* Une fois l’approvisionnement terminé, vous pouvez modifier vos stratégies d’accès et d’autres attributs de l’environnement pour qu’ils correspondent à vos besoins.

## <a name="new-environment-creation"></a>Création d’un nouvel environnement

Les étapes suivantes permettent de créer un environnement Azure Time Series Insights en préversion :

1. Sélectionnez le bouton **PAYG** sous le menu **référence SKU**. Fournissez un nom d’environnement, désignez le groupe d’abonnement et le groupe de ressources à utiliser. Ensuite, sélectionnez un emplacement pris en charge pour héberger l’environnement.

1. Entrer un ID Time Series

    >[!NOTE]
    > * L’ID Time Series respecte la casse et il est immuable (il ne peut pas être modifié après sa définition).
    > * Les ID Time Series peuvent comprendre jusqu’à trois clés.
    > * Apprenez-en plus sur la sélection d’un ID Time Series en lisant [Comment choisir un ID Time Series](./time-series-insights-update-how-to-id.md).

1. Créez un compte de stockage Azure en sélectionnant un nom de compte de stockage Azure et en spécifiant un choix de réplication. Un compte Stockage Azure à usage général V1 est alors automatiquement créé. Il sera créé dans la même région que l’environnement Azure Time Series Insights en préversion que vous avez sélectionné précédemment.

    ![Créez une instance Azure Time Series Insight.][1]

1. Si vous le souhaitez, vous pouvez ajouter une source d’événement.

   * Time Series Insights prend en charge [Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) et les [Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md). Vous ne pouvez ajouter qu’une seule source d’événement lors de la création d’un environnement, mais vous pouvez en ajouter une plus tard. Il est préférable de créer un groupe de consommateurs unique pour vous assurer que tous les événements sont visibles par votre instance Azure Time Series Insights en préversion. Vous pouvez sélectionner un groupe de consommateurs existant ou bien en créer un nouveau lors de l’ajout de la source d’événement.

   * Vous pouvez également désigner la propriété Timestamp appropriée. Par défaut, Azure Time Series Insights utilise l’heure de placement du message dans la file d’attente pour chaque source d’événement.

     > [!TIP]
     > Il est possible que l’heure de placement du message dans la file d’attente ne soit pas le paramètre le mieux configuré à utiliser pour un l’événement de traitement par lots ou des scénarios de chargement des données historiques. Veillez à vérifier votre choix d’utiliser ou non une propriété Timestamp dans de tels cas.

    ![Ajoutez une source d’événement à votre instance.][2]

1. Examiner et créer

    ![Ajoutez une source d’événement à votre instance.][3]

Vérifiez que votre environnement a été approvisionné avec les paramètres souhaités.

## <a name="management"></a>gestion

Vous avez la possibilité de gérer votre environnement Azure Time Series Insights en préversion à l’aide du portail Azure. Voici les principales différences dans la gestion d’un environnement **PAYG** Azure Time Series Insights en préversion comparé à un environnement S1 ou S2 utilisant le portail Azure :

* Le panneau *Vue d’ensemble* du portail Azure reste inchangé dans Azure Time Series Insights à quelques exceptions près :
  * La capacité est supprimée, car ce concept ne s’applique pas aux environnements **PAYG**.
  * La propriété d’ID Time Series a été ajoutée. Elle détermine la façon dont vos données sont partitionnées.
  * Les jeux de données de référence sont supprimés.
  * L’URL affichée vous dirige vers [l’Explorateur Azure Time Series Insights en préversion](./time-series-insights-update-explorer.md).
  * Le nom de votre compte de stockage Azure est répertorié.

* Le panneau *Configurer* du portail Azure a été supprimé dans Azure Time Series Insights en préversion dans la mesure où les environnements **PAYG** ne sont pas configurables.

* Le panneau de données de *référence* du portail Azure a été supprimé dans Azure Time Series Insights en préversion dans la mesure où les données de référence ne sont pas un composant des environnements **PAYG**.

![Vérifiez votre instance.][4]

## <a name="next-steps"></a>Étapes suivantes

Lisez [Comment planifier votre environnement](./time-series-insights-update-plan.md).

Lisez [Comment ajouter une source Event Hub](./time-series-insights-how-to-add-an-event-source-eventhub.md).

Lisez [Comment ajouter une source IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

<!-- Images -->
[1]: media/v2-update-manage/manage_one.png
[2]: media/v2-update-manage/manage_two.png
[3]: media/v2-update-manage/manage_three.png
[4]: media/v2-update-manage/manage_four.png
