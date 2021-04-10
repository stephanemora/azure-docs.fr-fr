---
title: Restaurer des objets blob Azure
description: Découvrez comment restaurer des objets blob Azure (en préversion).
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 4cbd47ea654115f00095e30f7d5114aec0f2c85a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101743141"
---
# <a name="restore-azure-blobs-in-preview"></a>Restaurer des objets blob Azure (en préversion)

Les objet blob de blocs dans des comptes de stockage avec la sauvegarde opérationnelle configurée peuvent être restaurés à n’importe quel instant dans le passé compris durant la période de conservation. Vous pouvez également étendre vos restaurations à tous les objets blob de blocs dans le compte de stockage ou à un sous-ensemble d’objets blob.

## <a name="before-you-start"></a>Avant de commencer

- Les objets blob seront restaurés sur le même compte de stockage. Ainsi, les objets blob qui ont été modifiés depuis l’instant choisi pour la restauration seront écrasés.
- Seuls les objets blob de blocs d’un compte de stockage universel v2 peuvent être restaurés dans le cadre d’une opération de restauration. Les objets blob d’ajout, les objets blob de pages et les objets blob de blocs Premium ne sont pas restaurés.
- Quand un travail de restauration est en cours, les objets blob du stockage ne peuvent pas être lus ou écrits.
- Un objet blob avec un bail actif ne peut pas être restauré. Si un objet blob avec un bail actif est inclus dans la plage d’objets blob à restaurer, l’opération de restauration échoue de façon automatique. Arrêtez tout bail actif avant de démarrer l’opération de restauration.
- Les instantanés ne sont pas créés ou supprimés dans le cadre d’une opération de restauration. Seul l’objet blob de base est restauré à son état précédent.
- Si vous supprimez un conteneur du compte de stockage en appelant l’opération **Supprimer le conteneur**, ce conteneur ne peut pas être restauré à l’aide d’une opération de restauration. Au lieu de supprimer un conteneur entier, supprimez chacun des objets blob si vous souhaitez les restaurer plus tard. En outre, Microsoft recommande d’activer la suppression réversible pour les conteneurs, en plus de la sauvegarde opérationnelle, pour éviter toute suppression accidentelle de conteneurs.
- Reportez-vous à la [matrice de prise en charge](blob-backup-support-matrix.md) pour connaître toutes les limitations et les scénarios pris en charge.

## <a name="restore-blobs"></a>Restaurer des objets blob

Vous pouvez lancer une restauration via le Centre de sauvegarde.

1. Dans le Centre de sauvegarde, accédez à **Restaurer** dans la barre supérieure.

    ![Restaurer dans le Centre de sauvegarde](./media/blob-restore/backup-center-restore.png)

1. Dans l’onglet **Lancer la restauration**, choisissez **Objets blob Azure (Stockage Azure)** comme type de source de source et sélectionnez **l’instance de sauvegarde** que vous souhaitez restaurer. L’instance de sauvegarde est ici le compte de stockage qui contient les objets blob que vous souhaitez restaurer.

     ![Sélection d’une instance de sauvegarde](./media/blob-restore/select-backup-instance.png)

1. Sous l’onglet **Sélectionner un point de récupération**, choisissez la date et l’heure que vous souhaitez pour restaurer vos données. Vous pouvez également utiliser le curseur pour choisir l’instant dans le passé à partir duquel effectuer la restauration. L’info-bulle à côté de la date indique la durée valide pour la restauration de vos données. La sauvegarde opérationnelle pour les objets blob en sauvegarde continue vous donne un contrôle granulaire sur les instants à partir desquels vous pouvez récupérer des données.

    >[!NOTE]
    > L’heure mentionnée ici correspond à votre heure locale.

    ![Date et heure pour la restauration](./media/blob-restore/date-and-time.png)

1. Sous l’onglet **Paramètres de restauration**, indiquez si vous souhaitez restaurer tous les objets blob dans le compte de stockage, des conteneurs spécifiques ou un sous-ensemble d’objets blob en utilisant la correspondance de préfixe. Quand vous utilisez la correspondance de préfixe, vous pouvez spécifier jusqu’à 10 plages de préfixes ou chemins de fichier. Plus d’informations sur l’utilisation de la correspondance de préfixe [ici](#use-prefix-match-for-restoring-blobs).

    ![Paramètres de restauration](./media/blob-restore/restore-parameters.png)

    Choisissez une de ces options :

    - **Restaurer tous les objets blob dans le compte de stockage** : l’utilisation de cette option restaure tous les objets blob de blocs dans le compte de stockage en les restaurant à l’instant dans le passé qui a été sélectionné. La restauration des comptes de stockage contenant de grandes quantités de données ou témoin d’une attrition élevée peut prendre plus de temps.

    - **Parcourir et restaurer les conteneurs sélectionnés** : cette option vous permet de parcourir et de sélectionner jusqu’à 10 conteneurs à restaurer. Vous devez disposer des autorisations suffisantes pour afficher les conteneurs dans le compte de stockage, sinon vous ne pourrez peut-être pas afficher le contenu du compte de stockage.

    - **Sélectionner les objets blob à restaurer en utilisant la correspondance de préfixe** : cette option vous permet de restaurer un sous-ensemble d’objets blob en utilisant une correspondance de préfixe. Vous pouvez spécifier jusqu’à 10 plages lexicographiques d’objets blob dans un seul conteneur ou dans plusieurs conteneurs pour ramener ces objets blob à l’état qu’ils avaient à un instant dans le passé donné. Voici quelques points à prendre en compte :

        - Vous pouvez utiliser une barre oblique (/) pour délimiter le nom du conteneur du préfixe de l’objet blob
        - Le début de la plage spécifiée est inclusif mais la plage spécifiée est exclusive.

    Pour plus d’informations sur l’utilisation des préfixes pour restaurer des plages d’objets blob, consultez [cette section](#use-prefix-match-for-restoring-blobs).

1. Une fois que vous avez terminé de spécifier les objets blob à restaurer, passez à l’onglet **Vérifier + restaurer**, puis sélectionnez **Restaurer** pour lancer la restauration.

1. **Suivi de la restauration** : utilisez la vue **Travaux de sauvegarde** pour suivre les détails et l’état des restaurations. Pour cela, accédez à **Centre de sauvegarde** > **Travaux de sauvegarde**. L’état indique **En cours** pendant l’exécution de la restauration.

    ![Onglet Travaux de sauvegarde](./media/blob-restore/backup-jobs.png)

    Quand l’opération de restauration s’est effectuée correctement, l’état passe à **Terminé**. Une fois que la restauration s’est effectuée correctement, vous êtes de nouveau en mesure de lire et d’écrire des objets blob dans le compte de stockage.

## <a name="additional-topics"></a>Rubriques supplémentaires

### <a name="use-prefix-match-for-restoring-blobs"></a>Utiliser la correspondance de préfixe pour la restauration des objets blob

Prenons l’exemple suivant :

![Restaurer avec une correspondance de préfixe](./media/blob-restore/prefix-match.png)

L’opération de restauration indiquée dans l’image effectue les actions suivantes :

- Restauration du contenu complet de *container1*.
- Restauration des objets blob dans la plage lexicographique de *blob1* à *blob5* dans *container2*. Cette plage restaure les objets blob avec des noms tels que *blob1*, *blob11*, *blob100*, *blob2*, et ainsi de suite. Étant donné que la fin de la plage est exclusive, elle restaure les objets blob dont le nom commence par *blob4*, mais pas les objets blob dont le nom commence par *blob5*.
- Elle restaure tous les objets blob dans *container3* et *container4*. Étant donné que la fin de la plage est exclusive, cette plage ne restaure pas *container5*.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de la sauvegarde opérationnelle pour des objets blob Azure (préversion)](blob-backup-overview.md)
