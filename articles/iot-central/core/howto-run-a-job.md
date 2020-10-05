---
title: Créer et exécuter des travaux dans votre application Azure IoT Central | Microsoft Docs
description: Les travaux Azure IoT Central prennent en charge les fonctionnalités de gestion des appareils en bloc, telles que la mise à jour des propriétés ou l’exécution d’une commande.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 09/10/2020
ms.topic: how-to
ms.openlocfilehash: ae8b830469a9b52ae68310dde2e65dcffdf4e3be
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90060813"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Créer et exécuter un travail dans votre application Azure IoT Central

Vous pouvez utiliser Azure IoT Central pour gérer vos appareils connectés à grande échelle à l’aide de travaux. Les travaux vous permettent d’effectuer des mises à jour en bloc de propriétés de clouds et d’appareils et d’exécuter des commandes. Cet article explique comment commencer à utiliser des travaux dans votre propre application.

## <a name="create-and-run-a-job"></a>Créer et exécuter un travail

L’exemple suivant montre comment créer et exécuter un travail pour définir le seuil de lumière pour un groupe d’appareils de passerelle logistique. Vous utilisez l’Assistant Travail pour créer et exécuter des travaux. Vous pouvez enregistrer un travail à exécuter ultérieurement.

1. Dans le volet gauche, sélectionnez **Travaux**.

1. Sélectionnez **+ Nouveau travail**.

1. Dans la page **Configurer votre travail**, entrez un nom et une description pour identifier le travail que vous créez.

1. Sélectionnez le groupe d’appareils cible auquel doit s’appliquer votre travail. Vous pouvez voir le nombre d’appareils auxquels votre configuration de travail s’applique sous votre sélection **Groupe d’appareils**.

1. Choisissez ensuite **Propriété cloud**, **Propriété** ou **Commande** pour **Type de travail** :

    Pour définir une configuration de travail de **Propriété**, sélectionnez une propriété et définissez sa nouvelle valeur. Pour effectuer une configuration de travail **Commande**, choisissez la commande à exécuter. Un travail de propriété peut définir plusieurs propriétés.

    :::image type="content" source="media/howto-run-a-job/configure-job.png" alt-text="Capture d’écran montrant des sélections pour la création d’un travail de propriété appelé Seuil de lumière":::

    Sélectionnez **Enregistrer et quitter** pour ajouter le travail à la liste des travaux enregistrés sur la page**Travaux**. Vous pouvez revenir ultérieurement à un travail à partir de la liste des travaux enregistrés.

    Sélectionnez **Suivant** pour accéder à la page **Vérifier**. La page **Vérification** affiche les détails de la configuration du travail. Sélectionnez **Exécuter** pour envoyer le travail.

    :::image type="content" source="media/howto-run-a-job/job-wizard-review.png" alt-text="Capture d’écran montrant des sélections pour la création d’un travail de propriété appelé Seuil de lumière":::

1. Le travail passe par différentes phases : *en attente*, *en cours d’exécution* et *terminé*. Les informations sur l’exécution du travail contiennent des métriques de résultats, des détails de durée et une grille de liste d’appareils.

    Une fois le travail terminé, vous pouvez sélectionner **Journal des résultats** pour télécharger un fichier CSV contenant les détails de votre travail, notamment les appareils et leurs valeurs d’état. Ces informations peuvent être utiles pour le dépannage.

    :::image type="content" source="media/howto-run-a-job/download-details.png" alt-text="Capture d’écran montrant des sélections pour la création d’un travail de propriété appelé Seuil de lumière":::

1. Le travail s’affiche à présent dans la liste des **30 derniers jours** sur la page **Travaux**. Cette page affiche les travaux en cours d’exécution et l’historique des travaux déjà exécutés ou enregistrés.

    > [!NOTE]
    > Vous pouvez afficher jusqu’à 30 jours d’historique pour vos travaux exécutés.

## <a name="manage-jobs"></a>Gestion des travaux

Pour arrêter un travail en cours d’exécution, ouvrez-le et sélectionnez **Arrêter**. L’état du travail change pour indiquer qu’il est arrêté. La section **Résumé** indique les appareils qui ont terminé, qui ont échoué ou qui sont en attente.

:::image type="content" source="media/howto-run-a-job/manage-job.png" alt-text="Capture d’écran montrant des sélections pour la création d’un travail de propriété appelé Seuil de lumière":::

Quand un travail est à l’état arrêté, vous pouvez sélectionner **Continuer** pour reprendre son exécution. L’état du travail change pour indiquer qu’il est à nouveau en cours d’exécution. La section **Récapitulatif** continue à être mise à jour avec la progression la plus récente.

:::image type="content" source="media/howto-run-a-job/stopped-job.png" alt-text="Capture d’écran montrant des sélections pour la création d’un travail de propriété appelé Seuil de lumière":::

## <a name="copy-a-job"></a>Copier un travail

Pour copier un travail existant, sélectionnez un travail exécuté. Sélectionnez **Copier** dans la page des résultats du travail ou la page de détails des travaux :

:::image type="content" source="media/howto-run-a-job/job-details-copy.png" alt-text="Capture d’écran montrant des sélections pour la création d’un travail de propriété appelé Seuil de lumière":::

Une copie de la configuration du travail s’ouvre afin de vous permettre de le modifier, et **Copie** est ajouté au nom du travail.

## <a name="view-job-status"></a>Afficher le statut de la tâche

Une fois qu’un travail a été créé, la colonne **État** est mise à jour avec le dernier message d’état du travail. Le tableau suivant répertorie les valeurs d’*état de travail* possibles :

| Message d’état       | Signification de l’état                                          |
| -------------------- | ------------------------------------------------------- |
| Completed            | Ce travail a été exécuté sur tous les appareils.              |
| Échec               | Ce travail a échoué et n’a pas été entièrement exécuté sur les appareils.  |
| Pending              | L’exécution de ce travail n’a pas encore commencé sur les appareils.         |
| Exécution en cours              | Ce travail est en cours d’exécution sur les appareils.             |
| Arrêté              | Un utilisateur a arrêté manuellement ce travail.           |

Le message d’état est suivi d’une vue d’ensemble des appareils au sein du travail. Le tableau suivant répertorie les valeurs d’*état des appareils* possibles :

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

Pour télécharger un fichier CSV qui comprend les détails du travail ainsi que la liste des appareils et leurs valeurs d’état, sélectionnez **Journal des résultats**.

## <a name="filter-the-device-list"></a>Filtrer la liste des appareils

Vous pouvez filtrer la liste des appareils dans la page **Détails du travail** en sélectionnant l’icône de filtre. Vous pouvez filtrer sur les champs **ID d’appareil** ou **État** :

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Capture d’écran montrant des sélections pour la création d’un travail de propriété appelé Seuil de lumière":::

## <a name="customize-columns-in-the-device-list"></a>Personnaliser les colonnes de la liste des appareils

Vous pouvez ajouter des colonnes à la liste des appareils en sélectionnant l’icône des options de la colonne :

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Capture d’écran montrant des sélections pour la création d’un travail de propriété appelé Seuil de lumière":::

Utilisez la boîte de dialogue **Options de colonne** pour choisir les colonnes de la liste des appareils. Sélectionnez les colonnes à afficher, sélectionnez l’icône représentant une flèche orientée vers la droite, puis sélectionnez **OK**. Pour sélectionner toutes les colonnes disponibles, choisissez **Tout sélectionner**. Les colonnes sélectionnées apparaissent dans la liste des appareils.

Les colonnes sélectionnées sont conservées tout au long d’une session utilisateur ou des sessions utilisateur ayant accès à l’application.

## <a name="rerun-jobs"></a>Réexécuter les travaux

Vous pouvez réexécuter un travail pour lequel des actions n’ont pas abouti sur certains appareils. Sélectionnez **Réexécuter en cas d’échec** :

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Capture d’écran montrant des sélections pour la création d’un travail de propriété appelé Seuil de lumière":::

Entrez le nom et la description du travail, puis sélectionnez **Réexécuter le travail**. Un nouveau travail est soumis pour réessayer l’action sur les appareils sur lesquels son exécution a échoué :

> [!NOTE]
> Vous ne pouvez pas exécuter plus de cinq travaux à la fois à partir d’une application Azure IoT Central.
>
> Quand un travail a été effectué et que vous supprimez un appareil qui figure dans la liste des appareils associés à ce travail, l’entrée de l’appareil apparaît supprimée dans le nom de l’appareil. Le lien des détails n’est pas disponible pour l’appareil supprimé.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment créer des travaux dans votre application Azure IoT Central, voici les étapes suivantes :

- [Gestion de vos appareils](howto-manage-devices.md)
- [Gérer les versions de votre modèle d’appareil](howto-version-device-template.md)
