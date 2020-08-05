---
title: Créer et exécuter des travaux dans votre application Azure IoT Central | Microsoft Docs
description: Les travaux Azure IoT Central prennent en charge les fonctionnalités de gestion des appareils en bloc, telles que la mise à jour des propriétés ou l’exécution d’une commande.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 06/08/2020
ms.topic: how-to
ms.openlocfilehash: 6073f71eb21ba4a6739647964d4888044d6ee59a
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87283619"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Créer et exécuter un travail dans votre application Azure IoT Central

Vous pouvez utiliser Microsoft Azure IoT Central pour gérer vos appareils connectés à grande échelle à l’aide de travaux. Les travaux vous permettent d’effectuer des mises à jour en bloc de propriétés d’appareils et d’exécuter des commandes. Cet article vous initie à l’utilisation de travaux dans votre propre application.

## <a name="create-and-run-a-job"></a>Créer et exécuter un travail

Cette section vous montre comment créer et exécuter un travail. Elle montre comment définir le seuil de lumière pour un groupe d’appareils de passerelle logistique.

1. Accédez à **Travaux** dans le volet gauche.

2. Sélectionnez **+ Nouveau** pour créer un travail :

    ![Créer un travail](./media/howto-run-a-job/create-new-job.png)

3. Entrez un nom et une description pour identifier le travail que vous créez.

4. Sélectionnez le groupe d’appareils cible auquel doit s’appliquer votre travail. Vous pouvez voir le nombre d’appareils auxquels votre configuration de travail s’applique dans la section **Récapitulatif**.

5. Choisissez ensuite **Propriété cloud**, **Propriété** ou **Commande** en tant que type de travail à configurer. Pour définir une configuration de travail de **Propriété**, sélectionnez une propriété et définissez sa nouvelle valeur. Pour configurer une **Commande**, choisissez la commande à exécuter. Un travail de propriété peut définir plusieurs propriétés :

    ![Configurer le travail](./media/howto-run-a-job/configure-job.png)

6. Après avoir créé votre travail, choisissez **Exécuter** ou **Enregistrer**. Le travail apparaît maintenant dans la page **Travaux** principale. Dans cette page, vous pouvez voir le travail en cours d’exécution et l’historique des travaux déjà exécutés ou enregistrés. Vos pouvez rouvrir votre travail enregistré à tout moment afin de le modifier ou de l’exécuter :

    ![Afficher le travail](./media/howto-run-a-job/view-job.png)

    > [!NOTE]
    > Vous pouvez afficher jusqu’à 30 jours d’historique pour vos travaux exécutés.

7. Cliquez sur le travail enregistré et exécutez-le en cliquant sur le bouton Exécuter. La fenêtre contextuelle Exécuter le travail s’affiche. Confirmez en cliquant sur le bouton Exécuter le travail. 

    ![Exécution d’un travail](./media/howto-run-a-job/run-job.png)

8. Le travail passe par différentes phases : En attente, En cours d’exécution et Terminé. Parmi ses informations d’exécution figurent Métriques des résultats, Détails de durée et la liste grille des appareils. Dans cette vue d’ensemble, vous pouvez également sélectionner **Journal des résultats** pour télécharger un fichier CSV contenant les détails de votre travail, notamment les appareils et leurs valeurs d’état. Ces informations peuvent être utiles pour le dépannage.

    ![Afficher l’état de l’appareil](./media/howto-run-a-job/download-details.png)

## <a name="manage-jobs"></a>Gestion des travaux

Pour arrêter l’un de vos travaux en cours d’exécution, ouvrez-le et sélectionnez **Arrêter**. L’état du travail change pour indiquer qu’il est arrêté. La section **Récapitulatif** indique les appareils dont l’état est terminé, en échec ou en attente.

![Gérer un travail](./media/howto-run-a-job/manage-job.png)

Une fois le travail à l’état Arrêté, vous pouvez cliquer sur **Continuer** pour reprendre son exécution. L’état du travail change pour indiquer qu’il est à nouveau en cours d’exécution. La section **Récapitulatif** continue à être mise à jour avec la progression la plus récente.

![Travail arrêté](./media/howto-run-a-job/stopped-job.png)

## <a name="copy-a-job"></a>Copier un travail

Pour copier l’un de vos travaux, sélectionnez-le sur la page **Travaux** et sélectionnez **Détails du travail**. La page Détails du travail s’affiche. 

![Détails du travail](./media/howto-run-a-job/job-details.png)

Cliquez sur **Copier**.

![Détails du travail](./media/howto-run-a-job/job-details-copy.png)

Une copie de la configuration du travail s’ouvre afin de vous permettre de le modifier, et **Copie** est ajouté au nom du travail. Vous pouvez enregistrer ou d’exécuter le nouveau travail :

![Copier un travail](./media/howto-run-a-job/copy-job.png)

## <a name="view-job-status"></a>Afficher le statut de la tâche

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

### <a name="view-the-device-status-values"></a>Voir les valeurs d’état de l’appareil

Pour voir l’état du travail et tous les appareils affectés, ouvrez le travail. En regard du nom de chaque appareil, vous voyez un des messages d’état suivants :

| Message d’état       | Signification de l’état                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Completed            | Le travail a été exécuté sur cet appareil.                                     |
| Échec               | L’exécution du travail a été un échec sur cet appareil. Le message d’erreur montre plus d’informations.  |
| Pending              | Le travail n’a pas encore été exécuté sur cet appareil.                                   |

Pour télécharger un fichier CSV qui comprend les détails du travail ainsi que la liste des appareils et leurs valeurs d’état, sélectionnez **Télécharger**.

### <a name="filter-the-list-of-devices"></a>Filtrer la liste des appareils

Vous pouvez filtrer la liste des appareils dans la page des détails du travail en sélectionnant l’icône de filtre. Vous pouvez filtrer les champs **ID d’appareil** ou **État** :

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Filtrer la liste des appareils":::

### <a name="customize-columns-in-the-device-list"></a>Personnaliser les colonnes de la liste des appareils

Vous pouvez choisir des colonnes supplémentaires à afficher dans la liste des appareils en sélectionnant l’icône d’options de colonne :

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Options de colonne":::

Vous voyez une boîte de dialogue qui vous permet de choisir les colonnes à afficher dans la liste des appareils. Sélectionnez les colonnes à afficher, sélectionnez l’icône représentant une flèche vers la droite, puis sélectionnez **OK**. Pour sélectionner toutes les colonnes disponibles, cochez **Tout sélectionner** :

:::image type="content" source="media/howto-run-a-job/column-picker-popup.png" alt-text="Boîte de dialogue du sélecteur de colonnes":::

Les colonnes sélectionnées s’affichent dans la liste des appareils :

:::image type="content" source="media/howto-run-a-job/column-picker-column-selected.png" alt-text="Sélectionner les colonnes":::

Les colonnes sélectionnées sont conservées tout au long d’une session utilisateur ou des sessions utilisateur ayant accès à l’application.

## <a name="rerun-jobs"></a>Réexécuter les travaux

Vous pouvez réexécuter un travail pour lequel des actions n’ont pas abouti sur certains appareils. Sélectionnez **Réexécuter** :

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Réexécuter un travail":::

Entrez le nom et la description du travail, puis sélectionnez **Réexécuter le travail**. Un nouveau travail est soumis pour que l’action soit retentée sur les appareils où un échec s’est produit :

:::image type="content" source="media/howto-run-a-job/rerun-failed.png" alt-text="Réexécuter le travail sur les appareils où un échec s’est produit":::

> [!NOTE]
> Vous ne pouvez pas exécuter plus de cinq travaux à la fois à partir d’une application IoT Central.

> [!NOTE]
> Quand un travail a été effectué et que vous supprimez un appareil qui figure dans la liste d’appareils de ce travail, l’entrée de l’appareil s’affiche comme étant supprimée dans le nom de l’appareil. De plus, le lien des détails de l’appareil n’est pas disponible pour l’appareil supprimé.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment créer des travaux dans votre application Azure IoT Central, voici les étapes suivantes :

- [Gestion de vos appareils](howto-manage-devices.md)
- [Gérer les versions de votre modèle d’appareil](howto-version-device-template.md)
