---
title: Créer et exécuter des travaux dans votre application Azure IoT Central | Microsoft Docs
description: Les travaux Azure IoT Central prennent en charge les fonctionnalités de gestion des appareils en bloc, telles que la mise à jour d’une propriété d’appareil ou d’un paramètre, ou bien l’exécution d’une commande.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 03/18/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: ec7033719316bb186408ea78f6dabac43c383491
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60519359"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Créer et exécuter un travail dans votre application Azure IoT Central

Vous pouvez utiliser Microsoft Azure IoT Central pour gérer vos appareils connectés à grande échelle à l’aide de travaux. Tâches vous permettent d’effectuer des mises à jour des propriétés de l’appareil, les paramètres et les commandes en bloc. Cet article vous guide tout au long de la prise en main à l’aide de travaux dans votre propre application.

## <a name="create-and-run-a-job"></a>Créer et exécuter un travail

Cette section vous montre comment créer et exécuter un travail. Il vous montre comment augmenter la vitesse de ventilateur pour plusieurs distributeurs réfrigérés.

1. Accédez à Travaux à partir du volet de navigation.

1. Sélectionnez **+ nouveau** pour créer une nouvelle tâche.

    ![Créer un travail](./media/howto-run-a-job/createnewjob.png)

1. Entrez un nom et une description pour identifier la tâche que vous créez.

1. Sélectionnez l’ensemble de l’appareil que vous souhaitez appliquer à votre travail. Une fois que la valeur en sélectionnant l’appareil, consultez la partie droite remplir avec les appareils dans l’ensemble de l’appareil. Si vous sélectionnez un ensemble de périphériques rompue, aucun appareil n’affiche et vous voyez un message que votre ensemble de l’appareil est rompue.

1. Ensuite, choisissez le type de travail à définir (un paramètre, la propriété ou la commande). Sélectionnez **+** en regard du type de travail sélectionné et ajoutez vos opérations.

    ![Configurer le travail](./media/howto-run-a-job/configurejob.png)

1. Sur le côté droit, choisissez les appareils que vous souhaitez exécuter le travail. En sélectionnant la case à cocher top, tous les appareils sont sélectionnés dans l’ensemble de l’ensemble de l’appareil. En sélectionnant la case à cocher près de **nom**, tous les appareils sur la page actuelle sont sélectionnés.

1. Après avoir sélectionné vos appareils, choisissez **exécuter** ou **enregistrer**. Le travail apparaît maintenant sur votre main **travaux** page. Sur cette vue, vous pouvez voir votre travail en cours d’exécution et l’historique de n’importe quel précédemment exécuter des tâches. Votre travail en cours d’exécution apparaît toujours en haut de la liste. Votre travail enregistré peut être ouvert à nouveau à tout moment pour continuer à modifier ou à exécuter.

    ![Afficher le travail](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Vous pouvez afficher l’historique de vos travaux exécutés précédemment pendant 30 jours.

1. Pour obtenir une vue d’ensemble de votre travail, sélectionnez la tâche à afficher dans la liste. Cette vue d’ensemble contient les détails du travail, les appareils et les valeurs d’état de périphérique. Cette vue d’ensemble, vous pouvez également sélectionner **détails du travail de téléchargement** pour télécharger un fichier .csv des détails de votre travail, y compris les appareils et leurs valeurs d’état. Ces informations peuvent être utiles pour le dépannage.

    ![Afficher l’état de l’appareil](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Arrêter un travail en cours d’exécution

Pour arrêter une tâche en cours d’exécution, sélectionnez-la et choisissez **arrêter** sur le panneau de configuration. Les changements d’état de travail afin de refléter que la tâche est arrêtée.

   ![Arrêter une tâche](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Exécuter une tâche arrêtée

Pour exécuter une tâche qui est actuellement arrêtée, sélectionnez le travail arrêté. Choisissez **exécuter** sur le panneau de configuration. Les changements d’état de travail pour refléter le travail s’exécute désormais à nouveau.

   ![Tâche reprise](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Une tâche de copie

Pour copier un travail existant que vous avez créé, sélectionnez-le dans la page principale de travaux et sélectionnez **copie**. Une nouvelle copie de la configuration du travail s’ouvre pour vous permettre de modifier. Vous pouvez enregistrer ou d’exécuter la nouvelle tâche. Si des modifications ont été apportées dans votre jeu de périphérique sélectionné, ils sont reflétés dans ce travail copié, vous pouvez modifier.

   ![travail de copie](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Afficher l’état d’un travail

Après la création d’un travail, le **état** mises à jour de la colonne avec le dernier message d’état du travail. Le tableau suivant répertorie les valeurs d’état possibles :

| Message d’état       | Signification de l’état                                          |
| -------------------- | ------------------------------------------------------- |
| Completed            | Ce travail a été exécuté sur tous les appareils.              |
| Échec               | Ce travail a échoué et n’a pas été entièrement exécuté sur les appareils.  |
| Pending              | Ce travail n’a pas encore commencé l’exécution sur les appareils.         |
| Exécution              | Ce travail est en cours d’exécution sur les appareils.             |
| Arrêté              | Ce travail a été arrêté manuellement par un utilisateur.           |

Le message d’état est suivi d’une vue d’ensemble des appareils dans le travail. Le tableau suivant répertorie les valeurs d’état possibles de l’appareil :

| Message d’état       | Signification de l’état                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Succeeded            | Le nombre d’appareils qui la tâche exécutée avec succès sur.       |
| Échec               | Le nombre d’appareils qui la tâche a échoué à s’exécuter sur.       |

### <a name="view-the-device-status"></a>Afficher l’état des appareils

Pour afficher l’état du travail et tous les périphériques affectés, sélectionnez la tâche. Pour télécharger un fichier .csv qui contient les détails du travail, y compris la liste des appareils et leurs valeurs d’état, sélectionnez **télécharger les détails d’une tâche**. En regard de chaque nom de l’appareil, vous constatez l’un des messages d’état suivants :

| Message d’état       | Signification de l’état                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Completed            | Le travail a été exécuté sur cet appareil.                                     |
| Échec               | Le travail n’a pas pu être exécuté sur cet appareil. Le message d’erreur affiche plus d’informations.  |
| Pending              | Le travail n’a pas encore été exécutée sur cet appareil.                                   |

> [!NOTE]
> Si un appareil a été supprimé, vous ne pouvez pas sélectionner l’appareil et il s’affiche comme étant supprimé avec l’ID de périphérique.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à créer des tâches dans votre application Azure IoT Central, voici quelques étapes suivantes :

- [Utiliser des ensembles d’appareils](howto-use-device-sets.md)
- [Gestion de vos appareils](howto-manage-devices.md)
- [Gérer les versions de votre modèle d’appareil](howto-version-devicetemplate.md)
