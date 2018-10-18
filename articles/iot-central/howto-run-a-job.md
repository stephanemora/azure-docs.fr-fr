---
title: Créer et exécuter des travaux dans votre application Azure IoT Central | Microsoft Docs
description: Les travaux Azure IoT Central prennent en charge les fonctionnalités de gestion des appareils en bloc, telles que la mise à jour d’une propriété d’appareil ou d’un paramètre, ou bien l’exécution d’une commande.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 09/15/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 35db7bf87c7b72fc31d820c9058b1df8415bd553
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031303"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Créer et exécuter un travail dans votre application Azure IoT Central

Vous pouvez utiliser Microsoft Azure IoT Central pour gérer vos appareils connectés à grande échelle à l’aide de travaux. La fonctionnalité Travaux vous permet d’effectuer des mises à jour en bloc des paramètres, des commandes et des propriétés d’appareil. Cet article vous initie à l’utilisation de travaux dans votre propre application.

## <a name="create-and-run-a-job"></a>Créer et exécuter un travail

Cette section vous montre comment créer et exécuter un travail. Chaque étape parcourt un exemple qui montre comment exécuter un travail pour des distributeurs automatiques réfrigérés dont la vitesse du ventilateur doit être augmentée.

1. Accédez à Travaux à partir du volet de navigation.

1. Cliquez sur **+ Nouveau** pour commencer à créer un travail.

    ![Créer un travail](./media/howto-run-a-job/createnewjob.png)

1. Entrez un nom et une description qui vous aident à identifier le travail en cours de création.

1. Sélectionnez l’ensemble d’appareils auquel vous souhaitez appliquer votre travail. Les appareils appartenant à cet ensemble d’appareils apparaissent alors sur le côté droit. Si vous sélectionnez un ensemble d’appareils rompu, aucun appareil n’apparaît et vous voyez un message expliquant que votre ensemble d’appareils est rompu.

1. Ensuite, choisissez le type de travail à définir (paramètre, propriété ou commande). Cliquez sur **+** en regard du type de travail sélectionné et ajoutez les opérations de votre choix.

    ![Configurer le travail](./media/howto-run-a-job/configurejob.png)

1. Sur le côté droit, choisissez les appareils sur lesquels vous souhaitez exécuter le travail. Si vous cochez la case supérieure, tous les appareils de l’ensemble d’appareils sont sélectionnés. Si vous cochez la case près de Nom, tous les appareils sur la page actuelle sont sélectionnés.

1. Une fois les appareils souhaités sélectionnés, choisissez **Exécuter**. Le travail s’affiche maintenant dans la page **Travaux** principale. Dans cette vue, vous pouvez voir le travail en cours d’exécution et l’historique des travaux déjà exécutés. Le travail en cours d’exécution apparaît toujours en haut de la liste.

    ![Exécuter le travail](./media/howto-run-a-job/runjob.png)

    ![Afficher le travail](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Vous pouvez voir l’historique des derniers travaux exécutés sur une période maximale de 30 jours.

1. Pour obtenir une vue d’ensemble d’un travail, cliquez sur son nom dans la liste. Cette vue d’ensemble contient les détails du travail, ainsi que les noms et états des appareils.

    ![Afficher l’état de l’appareil](./media/howto-run-a-job/viewdevicestatus.png)

### <a name="stop-a-running-job"></a>Arrêter un travail en cours d’exécution

Si vous souhaitez arrêter un travail en cours d’exécution, cliquez sur son nom. Choisissez le bouton **Arrêter** sur le panneau. L’état du travail change alors pour refléter l’arrêt de ce dernier.

> [!NOTE]
> Une fois qu’un travail a été arrêté, il ne peut pas être redémarré. Vous devez créer un autre travail avec les opérations et appareils souhaités.

## <a name="view-the-job-status"></a>Afficher l’état d’un travail

Une fois qu’un travail a été créé, la colonne **État** est mise à jour avec le dernier message d’état relatif au travail. Les messages d’état ont les significations suivantes :

| Message d’état       | Signification de l’état                                          |
| -------------------- | ------------------------------------------------------- |
| Completed            | Ce travail a été exécuté sur tous les appareils.              |
| Échec               | Ce travail a échoué et n’a pas été entièrement exécuté sur les appareils.  |
| Pending              | Ce travail n’a pas encore commencé à s’exécuter sur les appareils.        |
| Exécution              | Ce travail est en cours d’exécution sur les appareils.             |
| Arrêté              | Ce travail a été arrêté manuellement par un utilisateur.           |

Le message d’état est suivi d’une vue d’ensemble des appareils au sein du travail. Ces états d’appareil ont les significations suivantes :

| Message d’état       | Signification de l’état                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Réussi            | Nombre d’appareils sur lesquels le travail s’est exécuté correctement.  |
| Échec               | Nombre d’appareils sur lesquels le travail n’a pas pu être exécuté.      |

### <a name="view-the-device-status"></a>Afficher l’état des appareils

Pour afficher l’état de chaque appareil dans le travail, cliquez sur le nom du travail. Vous pouvez alors voir les détails du travail et tous les appareils qui en font partie. À côté de chaque nom d’appareil apparaît un des messages d’état suivants :

| Message d’état       | Signification de l’état                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Terminé            | Le travail a été exécuté sur cet appareil.                                     |
| Échec               | Le travail n’a pas pu être exécuté sur cet appareil. Le message d’erreur annexe affiche plus d’informations.  |
| En attente              | Le travail n’a pas encore été exécuté sur cet appareil.                                  |

> [!NOTE]
> Si un appareil a été supprimé, vous ne pouvez pas le sélectionner et il apparaît comme étant supprimé avec mention de son ID.

## <a name="next-steps"></a>Étapes suivantes

La création de travaux dans votre application Azure IoT Central n’ayant plus de secret pour vous, voici les étapes suivantes :

- [Utiliser des ensembles d’appareils](howto-use-device-sets.md)
- [Gestion de vos appareils](howto-manage-devices.md)
- [Gérer les versions de votre modèle d’appareil](howto-version-devicetemplate.md)
