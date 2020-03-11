---
title: Créer et exécuter des travaux dans votre application Azure IoT Central | Microsoft Docs
description: Les travaux Azure IoT Central prennent en charge les fonctionnalités de gestion des appareils en bloc, telles que la mise à jour des propriétés ou l’exécution d’une commande.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 03/03/2020
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 8f982dbb10a15a1e02a62a97431cdd1b7015472c
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252267"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Créer et exécuter un travail dans votre application Azure IoT Central

Vous pouvez utiliser Microsoft Azure IoT Central pour gérer vos appareils connectés à grande échelle à l’aide de travaux. Les travaux vous permettent d’effectuer des mises à jour en bloc de propriétés d’appareils et d’exécuter des commandes. Cet article vous initie à l’utilisation de travaux dans votre propre application.

## <a name="create-and-run-a-job"></a>Créer et exécuter un travail

Cette section vous montre comment créer et exécuter un travail. Elle montre comment définir le seuil de lumière pour un groupe d’appareils de passerelle logistique.

1. Accédez à **Travaux** dans le volet gauche.

2. Sélectionnez **+ Nouveau** pour créer un travail :

    ![Créer un travail](./media/howto-run-a-job/createnewjob.png)

3. Entrez un nom et une description pour identifier le travail que vous créez.

4. Sélectionnez le groupe d’appareils cible auquel doit s’appliquer votre travail. Vous pouvez voir le nombre d’appareils auxquels votre configuration de travail s’applique dans la section **Récapitulatif**.

5. Ensuite, choisissez **Propriété cloud**, **Propriété** ou **Commande** comme type de travail à configurer. Pour définir une configuration de travail de **Propriété**, sélectionnez une propriété et définissez sa nouvelle valeur. Pour configurer une **Commande**, choisissez la commande à exécuter. Un travail de propriété peut définir plusieurs propriétés :

    ![Configurer le travail](./media/howto-run-a-job/configurejob.png)

6. Après avoir créé votre travail, choisissez **Exécuter** ou **Enregistrer**. Le travail apparaît maintenant dans la page **Travaux** principale. Dans cette page, vous pouvez voir le travail en cours d’exécution et l’historique des travaux déjà exécutés ou enregistrés. Vos pouvez rouvrir votre travail enregistré à tout moment afin de le modifier ou de l’exécuter :

    ![Afficher le travail](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Vous pouvez afficher jusqu’à 30 jours d’historique pour vos travaux exécutés.

7. Pour obtenir une vue d’ensemble de votre travail, sélectionnez le travail à afficher dans la liste. Cette vue d’ensemble contient les détails du travail, ainsi que les noms et les états des appareils. Dans cette vue d’ensemble, vous pouvez également sélectionner **Détails du travail de téléchargement** pour télécharger un fichier CSV des détails de votre travail, notamment les appareils et leurs valeurs d’état. Ces informations peuvent être utiles pour le dépannage :

    ![Afficher l’état de l’appareil](./media/howto-run-a-job/downloaddetails.png)

### <a name="manage-a-job"></a>Gérer un travail

Pour arrêter l’un de vos travaux en cours d’exécution, ouvrez-le et sélectionnez **Arrêter**. L’état du travail change pour indiquer qu’il est arrêté. La section **Récapitulatif** indique les appareils dont l’état est terminé, en échec ou en attente.

Pour exécuter un travail qui est actuellement arrêté, sélectionnez-le, puis sélectionnez **Exécuter**. L’état du travail change pour indiquer qu’il est à nouveau en cours d’exécution. La section **Récapitulatif** continue à être mise à jour avec la progression la plus récente.

![Gérer un travail](./media/howto-run-a-job/managejob.png)

## <a name="copy-a-job"></a>Copier un travail

Pour copier l’un de vos travaux existants, sélectionnez-le dans la page **Travaux** et sélectionnez **Copier**. Une copie de la configuration du travail s’ouvre afin de vous permettre de le modifier, et **Copie** est ajouté au nom du travail. Vous pouvez enregistrer ou d’exécuter le nouveau travail :

![Copier un travail](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Afficher l’état d’un travail

Une fois qu’un travail a été créé, la colonne **État** est mise à jour avec le dernier message d’état du travail. Le tableau suivant liste les valeurs d’état possibles :

| Message d’état       | Signification de l’état                                          |
| -------------------- | ------------------------------------------------------- |
| Completed            | Ce travail a été exécuté sur tous les appareils.              |
| Échec               | Ce travail a échoué et n’a pas été entièrement exécuté sur les appareils.  |
| Pending              | Ce travail n’a pas encore commencé à s’exécuter sur les appareils.         |
| Exécution en cours              | Ce travail est en cours d’exécution sur les appareils.             |
| Arrêté              | Ce travail a été arrêté manuellement par un utilisateur.           |

Le message d’état est suivi d’une vue d’ensemble des appareils au sein du travail. Le tableau suivant liste les valeurs d’état possibles des appareils :

| Message d’état       | Signification de l’état                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Opération réussie            | Nombre d’appareils sur lesquels le travail s’est exécuté correctement.       |
| Échec               | Nombre d’appareils sur lesquels l’exécution du travail a échoué.       |

### <a name="view-the-device-status"></a>Afficher l’état des appareils

Pour voir l’état du travail et tous les appareils affectés, ouvrez le travail. Pour télécharger un fichier CSV des détails de votre travail, avec la liste des appareils et leurs valeurs d’état, sélectionnez **Détails du travail de téléchargement**. En regard du nom de chaque appareil, vous voyez un des messages d’état suivants :

| Message d’état       | Signification de l’état                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Completed            | Le travail a été exécuté sur cet appareil.                                     |
| Échec               | Le travail n’a pas pu être exécuté sur cet appareil. Le message d’erreur montre plus d’informations.  |
| Pending              | Le travail n’a pas encore été exécuté sur cet appareil.                                   |

> [!NOTE]
> Si un appareil a été supprimé, vous ne pouvez pas le sélectionner. Il s’affiche comme étant supprimé, avec mention de l’ID d’appareil.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment créer des travaux dans votre application Azure IoT Central, voici les étapes suivantes :

- [Gestion de vos appareils](howto-manage-devices.md)
- [Gérer les versions de votre modèle d’appareil](howto-version-device-template.md)
