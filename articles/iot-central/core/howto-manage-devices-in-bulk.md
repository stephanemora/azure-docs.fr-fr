---
title: Créer et exécuter des travaux dans votre application Azure IoT Central | Microsoft Docs
description: Les travaux Azure IoT Central prennent en charge les fonctionnalités de gestion des appareils en bloc, telles que la mise à jour des propriétés ou l’exécution d’une commande.
ms.service: iot-central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/08/2021
ms.topic: how-to
ms.openlocfilehash: c24eac9d7446f983bd4dc1e8573d3c831c7ff7fb
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442715"
---
# <a name="manage-devices-in-bulk-in-your-azure-iot-central-application"></a>Gérer les appareils en bloc dans votre application Azure IoT Central

Vous pouvez utiliser Azure IoT Central pour gérer vos appareils connectés à grande échelle à l’aide de travaux. Les travaux vous permettent d’effectuer des mises à jour en bloc de propriétés de clouds et d’appareils et d’exécuter des commandes. Vous pouvez également utiliser des fichiers CSV pour importer et exporter des appareils en bloc. Cet article vous montre comment bien démarrer avec l’utilisation de tâches dans votre propre application et comment utiliser les fonctionnalités d’importation et d’exportation.

## <a name="create-and-run-a-job"></a>Créer et exécuter un travail

L’exemple suivant montre comment créer et exécuter un travail pour définir le seuil de lumière pour un groupe d’appareils de passerelle logistique. Vous utilisez l’Assistant Travail pour créer et exécuter des travaux. Vous pouvez enregistrer un travail à exécuter ultérieurement.

1. Dans le volet gauche, sélectionnez **Travaux**.

1. Sélectionnez **+ Nouveau travail**.

1. Dans la page **Configurer votre travail**, entrez un nom et une description pour identifier le travail que vous créez.

1. Sélectionnez le groupe d’appareils cible auquel doit s’appliquer votre travail. Vous pouvez voir le nombre d’appareils auxquels votre configuration de travail s’applique sous votre sélection **Groupe d’appareils**.

1. Choisissez ensuite **Propriété cloud**, **Propriété** ou **Commande** pour **Type de travail** :

    Pour configurer un travail **Propriété**, sélectionnez une propriété et définissez sa nouvelle valeur. Pour configurer un travail **Commande**, choisissez la commande à exécuter. Un travail de propriété peut définir plusieurs propriétés.

    :::image type="content" source="media/howto-manage-devices-in-bulk/configure-job.png" alt-text="Capture d’écran montrant des sélections pour la création d’un travail de propriété appelé Seuil de lumière":::

    Sélectionnez **Enregistrer et quitter** pour ajouter le travail à la liste des travaux enregistrés sur la page **Travaux**. Vous pouvez revenir ultérieurement à un travail à partir de la liste des travaux enregistrés.

1. Sélectionnez **Suivant** pour accéder à la page **Options de remise**. La page **Options de remise** vous permet de définir les options de remise pour ce travail : **Lots** et **Seuil d’annulation**.

    Les lots vous permettent d’échelonner les tâches pour un grand nombre d’appareils. La tâche est divisée en plusieurs lots, et chaque lot contient un sous-ensemble des appareils. Les lots sont mis en file d’attente et exécutés dans l’ordre.

    Le seuil d’annulation vous permet d’annuler automatiquement un travail si le nombre d’erreurs dépasse la limite définie. Le seuil peut s’appliquer à tous les appareils du travail ou à des lots individuels.

    :::image type="content" source="media/howto-manage-devices-in-bulk/job-wizard-delivery-options.png" alt-text="Capture d’écran de la page d’options de remise de l’Assistant des travaux":::

1. Sélectionnez **Suivant** pour accéder à la page **Planifier**. La page **Planifier** vous permet d’activer une planification pour exécuter la tâche à l’avenir :

    Choisissez une option de périodicité pour la planification. Vous pouvez configurer une tâche à exécuter :

    * Ponctuelle
    * Quotidien
    * Hebdomadaire

    Définissez une date et une heure de début d’une tâche planifiée. La date et l'heure sont définies sur votre fuseau horaire, et non sur l'heure locale de l’appareil.

    Pour terminer une planification périodique, choisissez :

    * **Ce jour** afin de définir une date de fin pour la planification.
    * **Après** pour définir le nombre d’exécutions de la tâche.

    Les tâches planifiées sont toujours exécutées sur les appareils d'un groupe d'appareils, même si l’appartenance de ce groupe change au fil du temps.

    :::image type="content" source="media/howto-manage-devices-in-bulk/job-wizard-schedule.png" alt-text="Capture d’écran de la page d’options de planification de l’assistant des tâches":::

1. Sélectionnez **Suivant** pour accéder à la page **Vérifier**. La page **Vérification** affiche les détails de la configuration du travail. Sélectionnez **Planifier** pour planifier la tâche :

    :::image type="content" source="media/howto-manage-devices-in-bulk/job-wizard-schedule-review.png" alt-text="Capture d’écran de la page de révision de l’assistant de la tâche planifiée":::

1. La page des détails de la tâche des informations sur les tâches planifiées. Lorsque la tâche planifiée s’exécute, une liste des instances de la tâche s’affiche. L’exécution d’une tâche planifiée fait également partie de la liste des tâches **30 derniers jours**.

    Sur cette page, vous pouvez **déplanifier** la tâche ou **modifier** la tâche planifiée. Vous pouvez revenir à une tâche planifiée à partir de la liste des tâches planifiées.

    :::image type="content" source="media/howto-manage-devices-in-bulk/job-schedule-details.png" alt-text="Capture d’écran de la page des détails d’une tâche planifiée":::

1. Dans l’assistant de la tâche, vous pouvez choisir de ne pas planifier une tâche et l’exécuter immédiatement. La capture d’écran suivante montre une tâche sans planification et prête à être exécutée immédiatement. Sélectionnez **Exécuter** pour exécuter la tâche :

    :::image type="content" source="media/howto-manage-devices-in-bulk/job-wizard-schedule-immediate.png" alt-text="Capture d’écran de la page de révision de l’Assistant Travail":::

1. Le travail passe par différentes phases : *en attente*, *en cours d’exécution* et *terminé*. Les informations sur l’exécution du travail contiennent des métriques de résultats, des détails de durée et une grille de liste d’appareils.

    Une fois le travail terminé, vous pouvez sélectionner **Journal des résultats** pour télécharger un fichier CSV contenant les détails de votre travail, notamment les appareils et leurs valeurs d’état. Ces informations peuvent être utiles pour le dépannage.

    :::image type="content" source="media/howto-manage-devices-in-bulk/download-details.png" alt-text="Capture d’écran montrant l’état d’un appareil":::

1. Le travail s’affiche à présent dans la liste des **30 derniers jours** sur la page **Travaux**. Cette page affiche les travaux en cours d’exécution et l’historique des travaux déjà exécutés ou enregistrés.

    > [!NOTE]
    > Vous pouvez afficher 30 jours d’historique pour vos travaux exécutés.

## <a name="manage-jobs"></a>Gestion des travaux

Pour arrêter un travail en cours d’exécution, ouvrez-le et sélectionnez **Arrêter**. L’état du travail change pour indiquer qu’il est arrêté. La section **Résumé** indique les appareils qui ont terminé, qui ont échoué ou qui sont en attente.

:::image type="content" source="media/howto-manage-devices-in-bulk/manage-job.png" alt-text="Capture d’écran montrant un travail en cours d’exécution et le bouton permettant de l’arrêter":::

Quand un travail est à l’état arrêté, vous pouvez sélectionner **Continuer** pour reprendre son exécution. L’état du travail change pour indiquer qu’il est à nouveau en cours d’exécution. La section **Récapitulatif** continue à être mise à jour avec la progression la plus récente.

:::image type="content" source="media/howto-manage-devices-in-bulk/stopped-job.png" alt-text="Capture d’écran montrant un travail arrêté et le bouton permettant de le poursuivre":::

## <a name="copy-a-job"></a>Copier un travail

Pour copier un travail existant, sélectionnez un travail exécuté. Sélectionnez **Copier** dans la page des résultats du travail ou la page de détails des travaux :

:::image type="content" source="media/howto-manage-devices-in-bulk/job-details-copy.png" alt-text="Capture d’écran montrant le bouton Copier":::

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
| Opération annulée             | Ce travail a été annulé, car le seuil défini sur la page **Options de remise** a été dépassé. |

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

:::image type="content" source="media/howto-manage-devices-in-bulk/filter.png" alt-text="Capture d’écran montrant les sélections pour le filtrage d’une liste d’appareils.":::

## <a name="customize-columns-in-the-device-list"></a>Personnaliser les colonnes de la liste des appareils

Vous pouvez ajouter des colonnes à la liste des appareils en sélectionnant l’icône des options de la colonne :

:::image type="content" source="media/howto-manage-devices-in-bulk/column-options.png" alt-text="Capture d’écran montrant l’icône des options de la colonne.":::

Utilisez la boîte de dialogue **Options de colonne** pour choisir les colonnes de la liste des appareils. Sélectionnez les colonnes à afficher, sélectionnez l’icône représentant une flèche orientée vers la droite, puis sélectionnez **OK**. Pour sélectionner toutes les colonnes disponibles, choisissez **Tout sélectionner**. Les colonnes sélectionnées apparaissent dans la liste des appareils.

Les colonnes sélectionnées sont conservées tout au long d’une session utilisateur ou des sessions utilisateur ayant accès à l’application.

## <a name="rerun-jobs"></a>Réexécuter les travaux

Vous pouvez réexécuter un travail pour lequel des actions n’ont pas abouti sur certains appareils. Sélectionnez **Réexécuter en cas d’échec** :

:::image type="content" source="media/howto-manage-devices-in-bulk/rerun.png" alt-text="Capture d’écran montrant le bouton de réexécution d’un travail sur les appareils sur lesquels sont exécution a échoué.":::

Entrez le nom et la description du travail, puis sélectionnez **Réexécuter le travail**. Un nouveau travail est soumis pour réessayer l’action sur les appareils sur lesquels son exécution a échoué :

> [!NOTE]
> Vous ne pouvez pas exécuter plus de cinq travaux à la fois à partir d’une application Azure IoT Central.
>
> Quand un travail a été effectué et que vous supprimez un appareil qui figure dans la liste des appareils associés à ce travail, l’entrée de l’appareil apparaît supprimée dans le nom de l’appareil. Le lien des détails n’est pas disponible pour l’appareil supprimé.

## <a name="import-devices"></a>Importer des appareils

Pour connecter un grand nombre d’appareils à votre application, vous pouvez importer en bloc des appareils à partir d’un fichier CSV. Vous trouverez un exemple de fichier CSV dans le [dépôt d’exemples Azure](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/bulk-upload-devices). Le fichier CSV doit inclure les en-têtes de colonne suivants :

| Colonne | Description |
| - | - |
| IOTC_DEVICEID | L’ID d’appareil est un identifiant unique qui sera utilisé par cet appareil pour se connecter. L’ID d’appareil peut contenir des lettres, des chiffres et le caractère `-`, sans espaces. La longueur maximale est de 128 caractères. |
| IOTC_DEVICENAME | facultatif. Le nom de l’appareil est un nom convivial qui sera affiché dans toute l’application. S’il n’est pas spécifié, il est identique à l’ID de l’appareil. La longueur maximale est de 148 caractères. |

Pour l’inscription en masse d’appareils :

1. Choisissez **Appareils** dans le volet gauche.

1. Dans le volet gauche, choisissez le modèle d’appareil pour lequel vous souhaitez créer des appareils en masse.

    > [!NOTE]
    > Si vous n’avez pas encore de modèle d’appareil, vous pouvez importer les appareils sous **Tous les appareils** et les inscrire sans modèle. Une fois les appareils importés, vous pouvez les migrer vers un modèle.

1. Sélectionnez **Importer**.

    :::image type="content" source="media/howto-manage-devices-in-bulk/bulk-import-1.png" alt-text="Capture d’écran montrant les paramètres des actions d’importation.":::

1. Sélectionnez le fichier CSV contenant la liste des ID d’appareil à importer.

1. L’importation de l’appareil démarre une fois le fichier téléchargé. Vous pouvez suivre l’état de l’importation dans le volet Opérations sur les appareils. Ce volet s’affiche automatiquement au démarrage de l’importation. Vous pouvez également y accéder en cliquant sur l’icône représentant une cloche dans le coin supérieur droit.

1. Une fois l’importation terminée, un message de réussite s’affiche dans le volet Opérations sur les appareils.

    :::image type="content" source="media/howto-manage-devices-in-bulk/bulk-import-2.png" alt-text="Capture d’écran montrant la réussite de l’importation.":::

Si l’importation de l’appareil échoue, un message d’erreur s’affiche dans le volet Opérations sur les appareils. Un fichier journal capturant toutes les erreurs est généré et vous pouvez le télécharger.

## <a name="export-devices"></a>Exporter des appareils

Pour connecter un appareil réel à IoT Central, vous avez besoin de sa chaîne de connexion. Vous pouvez exporter en bloc les détails de l’appareil pour obtenir les informations dont vous avez besoin afin de créer les chaînes de connexion de l'appareil. Le processus d’exportation crée un fichier CSV avec l’identité de l’appareil, le nom de l’appareil et les clés pour tous les appareils sélectionnés.

Pour exporter en bloc des appareils à partir de votre application :

1. Choisissez **Appareils** dans le volet gauche.

1. Dans le volet gauche, choisissez le modèle d’appareil à partir duquel vous souhaitez exporter les appareils.

1. Sélectionnez les appareils que vous souhaitez exporter, puis l’action **Exporter**.

    :::image type="content" source="media/howto-manage-devices-in-bulk/export-1.png" alt-text="Capture d’écran montrant les paramètres des actions d’exportation.":::

1. Le processus d’exportation démarre. Vous pouvez suivre l’état dans le volet Opérations sur les appareils.

1. Une fois l’exportation terminée, un message de réussite s’affiche avec un lien pour télécharger le fichier généré.

1. Cliquez sur le lien **Télécharger le fichier** pour télécharger le fichier vers un dossier local sur le disque.

    ![Exportation réussie](./media/howto-manage-devices-in-bulk/export-2.png)

1. Le fichier CSV exporté contient les colonnes suivantes : ID de l’appareil, nom de l’appareil, clés d’appareil et empreintes numériques de certificat X509 :

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Pour plus d’informations sur la connexion d’appareils réels à votre application IoT Central, consultez [Connectivité des appareils dans Azure IoT Central](concepts-get-connected.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer des appareils en bloc dans votre application Azure IoT Central, une étape suivante suggérée consiste à apprendre à [modifier un modèle d’appareil](howto-edit-device-template.md).
