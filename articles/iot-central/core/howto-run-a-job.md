---
title: Créer et exécuter des travaux dans votre application Azure IoT Central | Microsoft Docs
description: Les travaux Azure IoT Central prennent en charge les fonctionnalités de gestion des appareils en bloc, telles que la mise à jour des propriétés ou l’exécution d’une commande.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 06/08/2020
ms.topic: how-to
ms.openlocfilehash: 406881a9131aae35b91dcab248745bb426cecf0e
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797834"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Créer et exécuter un travail dans votre application Azure IoT Central

Vous pouvez utiliser Microsoft Azure IoT Central pour gérer vos appareils connectés à grande échelle à l’aide de travaux. Les travaux vous permettent d’effectuer des mises à jour en bloc de propriétés d’appareils et d’exécuter des commandes. Cet article explique comment commencer à utiliser des travaux dans votre propre application.

## <a name="create-and-run-a-job"></a>Créer et exécuter un travail

Cette section vous montre comment créer et exécuter un travail sous la forme de la définition du seuil de lumière pour un groupe d’appareils de passerelle logistique.

1. Dans le volet gauche, sélectionnez **Travaux**.

2. Sélectionnez **+Nouveau**.

   ![Capture d’écran montrant les sélections pour la création d’un travail.](./media/howto-run-a-job/create-new-job.png)

3. Entrez un nom et une description pour identifier le travail que vous créez.

4. Sélectionnez le groupe d’appareils cible auquel doit s’appliquer votre travail. Vous pouvez voir le nombre d’appareils auxquels votre configuration de travail s’applique dans la section **Récapitulatif**.

5. Choisissez ensuite **Propriété cloud**, **Propriété** ou **Commande** en tant que type de travail à configurer. 

   Pour définir une configuration de travail de **Propriété**, sélectionnez une propriété et définissez sa nouvelle valeur. Pour effectuer une configuration de travail **Commande**, choisissez la commande à exécuter. Un travail de propriété peut définir plusieurs propriétés.

   ![Capture d’écran montrant des sélections pour la création d’un travail de propriété appelé Seuil de lumière.](./media/howto-run-a-job/configure-job.png)

6. Sélectionnez **Exécuter** ou **Enregistrer**. Le travail apparaît maintenant dans la page **Travaux** principale. Dans cette page, vous pouvez voir le travail en cours d’exécution et l’historique des travaux déjà exécutés ou enregistrés. Vous pouvez rouvrir votre travail enregistré à tout moment afin de le modifier ou de l’exécuter.

   ![Capture d’écran montrant le nom, l’état et la description d’un travail créé.](./media/howto-run-a-job/view-job.png)

   > [!NOTE]
   > Vous pouvez afficher jusqu’à 30 jours d’historique pour vos travaux exécutés.

7. Sélectionnez le travail enregistré et exécutez-le en sélectionnant le bouton **Exécuter**. 

   La boîte de dialogue **Exécuter votre travail ?** s’affiche. Confirmez en sélectionnant le bouton **Exécuter le travail**. 

   ![Capture d’écran de la boîte de dialogue confirmant que vous souhaitez exécuter un travail.](./media/howto-run-a-job/run-job.png)

8. Le travail passe par différentes phases : en attente, en cours d’exécution et terminé. Les informations sur l’exécution du travail contiennent des métriques de résultats, des détails de durée et une grille de liste d’appareils. 

   Dans cette vue d’ensemble, vous pouvez également sélectionner **Journal des résultats** pour télécharger un fichier CSV contenant les détails de votre travail, notamment les appareils et leurs valeurs d’état. Ces informations peuvent être utiles pour le dépannage.

   ![Capture d’écran montrant l’état d’un appareil.](./media/howto-run-a-job/download-details.png)

## <a name="manage-jobs"></a>Gestion des travaux

Pour arrêter un travail en cours d’exécution, ouvrez-le et sélectionnez **Arrêter**. L’état du travail change pour indiquer qu’il est arrêté. La section **Résumé** indique les appareils qui ont terminé, qui ont échoué ou qui sont en attente.

![Capture d’écran montrant un travail en cours d’exécution et le bouton permettant de l’arrêter.](./media/howto-run-a-job/manage-job.png)

Quand un travail est à l’état arrêté, vous pouvez sélectionner **Continuer** pour reprendre son exécution. L’état du travail change pour indiquer qu’il est à nouveau en cours d’exécution. L’actualisation de la section **Résumé** continue avec la progression la plus récente.

![Capture d’écran montrant un travail arrêté et le bouton permettant de le poursuivre.](./media/howto-run-a-job/stopped-job.png)

## <a name="copy-a-job"></a>Copier un travail

Pour copier un travail, sélectionnez-le dans la page **Travaux**, puis choisissez **Détails du travail**. La page **Détails du travail** s’affiche. 

![Capture d’écran montrant la page des détails du travail.](./media/howto-run-a-job/job-details.png)

Sélectionnez **Copier**.

![Capture d’écran montrant le bouton Annuler.](./media/howto-run-a-job/job-details-copy.png)

Une copie de la configuration du travail s’ouvre afin de vous permettre de le modifier, et **Copie** est ajouté au nom du travail. Vous pouvez enregistrer ou d’exécuter le nouveau travail.

![Capture d’écran montrant une copie de la configuration du travail.](./media/howto-run-a-job/copy-job.png)

## <a name="view-job-status"></a>Afficher le statut de la tâche

Une fois qu’un travail a été créé, la colonne **État** est mise à jour avec le dernier message d’état du travail. Le tableau suivant répertorie les valeurs d’état de travail possibles :

| Message d’état       | Signification de l’état                                          |
| -------------------- | ------------------------------------------------------- |
| Completed            | Ce travail a été exécuté sur tous les appareils.              |
| Échec               | Ce travail a échoué et n’a pas été entièrement exécuté sur les appareils.  |
| Pending              | L’exécution de ce travail n’a pas encore commencé sur les appareils.         |
| Exécution en cours              | Ce travail est en cours d’exécution sur les appareils.             |
| Arrêté              | Un utilisateur a arrêté manuellement ce travail.           |

Le message d’état est suivi d’une vue d’ensemble des appareils au sein du travail. Le tableau suivant liste les valeurs d’état possibles des appareils :

| Message d’état       | Signification de l’état                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Opération réussie            | Nombre d’appareils sur lesquels le travail a été correctement exécuté.       |
| Échec               | Nombre d’appareils sur lesquels l’exécution du travail a échoué.       |

Pour voir l’état du travail et tous les appareils affectés, ouvrez le travail. En regard du nom de chaque appareil, vous voyez un des messages d’état suivants :

| Message d’état       | Signification de l’état                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Completed            | Le travail a été exécuté sur cet appareil.                                     |
| Échec               | L’exécution du travail a échoué sur cet appareil. Le message d’erreur montre plus d’informations.  |
| Pending              | Le travail n’a pas encore été exécuté sur cet appareil.                                   |

Pour télécharger un fichier CSV qui comprend les détails du travail ainsi que la liste des appareils et leurs valeurs d’état, sélectionnez **Télécharger**.

## <a name="filter-the-device-list"></a>Filtrer la liste des appareils

Vous pouvez filtrer la liste des appareils dans la page **Détails du travail** en sélectionnant l’icône de filtre. Vous pouvez filtrer sur les champs **ID d’appareil** ou **État**.

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Capture d’écran montrant les sélections pour le filtrage d’une liste d’appareils.":::

## <a name="customize-columns-in-the-device-list"></a>Personnaliser les colonnes de la liste des appareils

Vous pouvez choisir des colonnes supplémentaires à afficher dans la liste des appareils en sélectionnant l’icône des options de la colonne.

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Capture d’écran montrant l’icône des options de la colonne.":::

Une boîte de dialogue vous permet de choisir les colonnes à afficher dans la liste des appareils. Sélectionnez les colonnes à afficher, sélectionnez l’icône représentant une flèche orientée vers la droite, puis sélectionnez **OK**. Pour sélectionner toutes les colonnes disponibles, cochez **Tout sélectionner**.

:::image type="content" source="media/howto-run-a-job/column-picker-popup.png" alt-text="Capture d’écran montrant la boîte de dialogue permettant de choisir les colonnes à afficher.":::

Les colonnes sélectionnées apparaissent dans la liste des appareils.

:::image type="content" source="media/howto-run-a-job/column-picker-column-selected.png" alt-text="Capture d’écran montrant les colonnes sélectionnées dans la liste des appareils.":::

Les colonnes sélectionnées sont conservées tout au long d’une session utilisateur ou des sessions utilisateur ayant accès à l’application.

## <a name="rerun-jobs"></a>Réexécuter les travaux

Vous pouvez réexécuter un travail pour lequel des actions n’ont pas abouti sur certains appareils. Sélectionnez **Réexécuter en cas d’échec**.

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Capture d’écran montrant le bouton de réexécution d’un travail sur les appareils sur lesquels sont exécution a échoué.":::

Entrez le nom et la description du travail, puis sélectionnez **Réexécuter le travail**. Un nouveau travail est soumis pour réessayer l’action sur les appareils sur lesquels son exécution a échoué :

:::image type="content" source="media/howto-run-a-job/rerun-failed.png" alt-text="Capture d’écran montrant la boîte de dialogue pour la réexécution du travail sur les appareils sur lesquels sont exécution a échoué.":::

> [!NOTE]
> Vous ne pouvez pas exécuter plus de cinq travaux à la fois à partir d’une application Azure IoT Central.
>
> Quand un travail a été effectué et que vous supprimez un appareil qui figure dans la liste des appareils associés à ce travail, l’entrée de l’appareil apparaît supprimée dans le nom de l’appareil. Le lien des détails n’est pas disponible pour l’appareil supprimé.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment créer des travaux dans votre application Azure IoT Central, voici les étapes suivantes :

- [Gestion de vos appareils](howto-manage-devices.md)
- [Gérer les versions de votre modèle d’appareil](howto-version-device-template.md)
