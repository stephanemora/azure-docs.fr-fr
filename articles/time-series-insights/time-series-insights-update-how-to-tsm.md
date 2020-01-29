---
title: Modélisation des données dans les environnements de préversion – Azure Time Series Insights | Microsoft Docs
description: Renseignez-vous sur la modélisation des données dans Azure Time Series Insights (préversion).
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/20/2019
ms.custom: seodec18
ms.openlocfilehash: 591233154ab79b9493d77030727462bae68ea7d5
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76152355"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modélisation des données dans Azure Time Series Insights (préversion)

Cet article décrit comment utiliser le modèle Time Series d’Azure Time Series Insights (préversion). Il décrit en détail plusieurs scénarios de données courants.

> [!TIP]
> * Découvrez le [Modèle Time Series](time-series-insights-update-tsm.md) en préversion.
> * Apprenez-en davantage sur la navigation dans l’interface utilisateur de l’[explorateur Azure Time Series Insights en préversion](./time-series-insights-update-explorer.md).

## <a name="instances"></a>Instances

L’Explorateur Azure Time Series Insights prend en charge les opérations **CRÉER**, **LIRE**, **METTRE À JOUR**et **SUPPRIMER** d’Instance dans le navigateur. 

Pour commencer, sélectionnez l’affichage **Modèle** dans l’affichage **Analyser** de l’explorateur Time Series Insights.

### <a name="create-a-single-instance"></a>Créer une instance unique

1. Accédez au panneau de sélection du modèle Time Series, puis sélectionnez **Instances** dans le menu. Toutes les instances associées à l’environnement Time Series Insights sélectionné s’affichent.

    [![Créer une instance unique en commençant par sélectionner Instances.](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. Sélectionnez **Ajouter**.

    [![Ajouter une instance en sélectionnant le bouton + Ajouter.](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. Entrez les détails de l’instance, sélectionnez l’association de type et de hiérarchie, puis cliquez sur **Créer**.

### <a name="bulk-upload-one-or-more-instances"></a>Charger une ou plusieurs instances en bloc

> [!TIP]
> Vous pouvez enregistrer vos instances sur votre bureau dans un fichier JSON. Vous pouvez ensuite charger le fichier JSON téléchargé en procédant comme suit.

1. Sélectionnez **Charger un fichier JSON**.
1. Sélectionnez le fichier qui contient la charge utile des instances.

    [![Charger en bloc des instances à l’aide d’un fichier JSON.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. Sélectionnez **Télécharger**.

### <a name="edit-a-single-instance"></a>Modifier une instance unique

1. Sélectionnez l’instance, puis **Modifier** ou l’**icône Crayon**. 
1. Apportez les modifications nécessaires, puis sélectionnez **Enregistrer**.

    [![Modifier une instance unique.](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>Supprimer une instance

1. Sélectionnez l'instance, puis **Supprimer** ou l’**icône Corbeille**.

   [![Supprimez une instance en sélectionnant Supprimer.](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. Confirmez la suppression en sélectionnant **Supprimer**.

> [!NOTE]
> Pour qu’une instance soit supprimée, elle doit passer avec succès un contrôle de validation.

## <a name="hierarchies"></a>Hierarchies

L’Explorateur Azure Time Series Insights prend en charge les opérations **CRÉER**, **LIRE**, **METTRE À JOUR**et **SUPPRIMER** de Hiérarchie dans le navigateur. 

Pour commencer, sélectionnez l’affichage **Modèle** dans l’affichage **Analyser** de l’explorateur Time Series Insights.

### <a name="create-a-single-hierarchy"></a>Créer une hiérarchie unique

1. Accédez au panneau de sélection du modèle Time Series, puis sélectionnez **Hiérarchies** dans le menu. Toutes les hiérarchies associées à l’environnement Time Series Insights sélectionné s’affichent.

    [![Créer une hiérarchie dans le volet.](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. Sélectionnez **Ajouter**.

    [![Bouton + Ajouter une hiérarchie.](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. Sélectionnez **+ Ajouter un niveau** dans le volet droit.

    [![Ajouter un niveau à la hiérarchie.](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. Entrez les détails de la hiérarchie, puis sélectionnez **Enregistrer**.

    [![Spécifier les détails de la hiérarchie.](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Charger une ou plusieurs hiérarchies en bloc

> [!TIP]
> Vous pouvez enregistrer vos hiérarchies sur votre bureau dans un fichier JSON. Vous pouvez ensuite charger le fichier JSON téléchargé en procédant comme suit.

1. Sélectionnez **Charger un fichier JSON**.
1. Sélectionnez le fichier qui contient la charge utile de hiérarchie.
1. Sélectionnez **Télécharger**.

    [![Sélections pour le chargement en bloc de hiérarchies.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Modifier une hiérarchie unique

1. Sélectionnez la hiérarchie, puis **Modifier** ou l’**icône Crayon**.
1. Apportez les modifications nécessaires, puis sélectionnez **Enregistrer**.

    [![Sélections pour modifier une hiérarchie unique.](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>Supprimer une hiérarchie

1. Sélectionnez la hiérarchie, puis **Supprimer** ou l’**icône Corbeille**. 

    [![Supprimer une hiérarchie en sélectionnant le bouton Supprimer.](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. Confirmez la suppression en sélectionnant **Supprimer**.

## <a name="types"></a>Types

L’Explorateur Azure Time Series Insights prend en charge les opérations **CRÉER**, **LIRE**, **METTRE À JOUR**et **SUPPRIMER** de Type dans le navigateur. 

Pour commencer, sélectionnez l’affichage **Modèle** dans l’affichage **Analyser** de l’explorateur Time Series Insights.

### <a name="create-a-single-type"></a>Créer un type unique

1. Accédez au panneau de sélection du modèle Time Series, puis sélectionnez **Types** dans le menu. Tous les types associés à l’environnement Time Series Insights sélectionné s’affichent.

    [![Volet des types de modèles Time Series.](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. Sélectionnez **+ Ajouter** pour afficher le menu contextuel modal **Ajouter un nouveau type**.
1. Entrez les propriétés et les variables pour votre type. Une fois celles-ci entrées, sélectionnez **Enregistrer**. 

    [![Paramètres de configuration pour ajouter un type.](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Charger un ou plusieurs types en bloc

> [!TIP]
> Vous pouvez enregistrer vos types sur votre bureau dans un fichier JSON. Vous pouvez ensuite charger le fichier JSON téléchargé en procédant comme suit.

1. Sélectionnez **Charger un fichier JSON**.
1. Sélectionnez le fichier qui contient la charge utile de type.
1. Sélectionnez **Télécharger**.

    [![Options de chargement de types en bloc.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>Modifier un type unique

1. Sélectionnez le type, puis **Modifier** ou l’**icône Crayon**.
1. Apportez les modifications nécessaires, puis sélectionnez **Enregistrer**.

    [![Modifier un type dans le volet.](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>Supprimer un type

1. Sélectionnez le type, puis **Supprimer** ou l’**icône Corbeille**. .

   [![Supprimer un type en sélectionnant Supprimer.](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. Confirmez la suppression en sélectionnant **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le modèle Time Series, consultez [Modélisation des données](./time-series-insights-update-tsm.md).

- Pour en savoir plus sur la préversion, consultez [Visualiser les données dans l’Explorateur de la préversion d’Azure Time Series Insights](./time-series-insights-update-explorer.md).

- Pour en savoir plus sur les structures JSON prises en charge, consultez [Structures JSON prises en charge](./time-series-insights-send-events.md#supported-json-shapes).
