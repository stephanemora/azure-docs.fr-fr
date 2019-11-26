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
ms.date: 10/29/2019
ms.custom: seodec18
ms.openlocfilehash: fb1bfb30d531f8b93489c8fc1dfbc6b8172339a3
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006457"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modélisation des données dans Azure Time Series Insights (préversion)

Cet article décrit comment utiliser le modèle Time Series d’Azure Time Series Insights (préversion). Il décrit en détail plusieurs scénarios de données courants.

Pour en savoir plus sur l’utilisation de la mise à jour, consultez la page sur l’[Explorateur de la préversion d’Azure Time Series Insights](./time-series-insights-update-explorer.md).

## <a name="types"></a>Types

### <a name="create-a-single-type"></a>Créer un type unique

1. Accédez au panneau de sélection du modèle Time Series, puis sélectionnez **Types** dans le menu. Réduisez le panneau pour ne garder que les types de modèles Time Series.

    [![Volet « types »](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Sélectionnez **Ajouter**.
1. Entrez tous les détails se rapportant aux types, puis sélectionnez **Créer**. Cette action crée des types dans l’environnement.

    [![Sélections pour ajouter un type](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Charger un ou plusieurs types en bloc

1. Sélectionnez **Charger un fichier JSON**.
1. Sélectionnez le fichier qui contient la charge utile de type.
1. Sélectionnez **Télécharger**.

    [![Sélections pour le chargement en lot d’un ou de plusieurs types](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Modifier un type unique

1. Sélectionnez le type, puis **Modifier**. 
1. Apportez les modifications nécessaires, puis sélectionnez **Enregistrer**.

    [![Sélections pour modifier un type](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Supprimer un type

1. Sélectionnez le type, puis **Supprimer**.
1. Si aucune instance n’est associée aux types, ils sont supprimés.

    [![Bouton « Supprimer »](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Hiérarchies

### <a name="create-a-single-hierarchy"></a>Créer une hiérarchie unique

1. Accédez au panneau de sélection du modèle Time Series, puis sélectionnez **Hiérarchies** dans le menu. Réduisez le panneau pour ne garder que les hiérarchies de modèles Time Series.

    [![Volet « Hiérarchies »](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Sélectionnez **Ajouter**.

    [![Bouton « Ajouter »](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. Sélectionnez **+ Ajouter un niveau** dans le volet droit.

    [![Bouton « Ajouter un niveau »](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Entrez les détails de la hiérarchie, puis sélectionnez **Créer**.

    [![Détails de la hiérarchie et bouton « créer »](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Charger une ou plusieurs hiérarchies en bloc

1. Sélectionnez **Charger un fichier JSON**.
1. Sélectionnez le fichier qui contient la charge utile de hiérarchie.
1. Sélectionnez **Télécharger**.

    [![Sélections pour le chargement en lot de hiérarchies](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Modifier une hiérarchie unique

1. Sélectionnez la hiérarchie, puis **Modifier**.
1. Apportez les modifications nécessaires, puis sélectionnez **Enregistrer**.

    [![Sélections pour modifier une hiérarchie unique](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Supprimer une hiérarchie

1. Sélectionnez la hiérarchie, puis **Supprimer**. 
1. Si aucune instance n’est associée à la hiérarchie, elle est supprimée.

    [![Bouton « Supprimer »](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>Instances

### <a name="create-a-single-instance"></a>Créer une instance unique

1. Accédez au panneau de sélection du modèle Time Series, puis sélectionnez **Instances** dans le menu. Réduisez le panneau pour ne garder que les instances de modèles Time Series.

    [![Volet « Instances »](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Sélectionnez **Ajouter**.

    [![Sélections pour ajouter une instance](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Entrez les détails de l’instance, sélectionnez l’association de type et de hiérarchie, puis cliquez sur **Créer**.

### <a name="bulk-upload-one-or-more-instances"></a>Charger une ou plusieurs instances en bloc

1. Sélectionnez **Charger un fichier JSON**.
1. Sélectionnez le fichier qui contient la charge utile des instances.

    [![Sélections pour le chargement en lot d’une ou de plusieurs instances](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Sélectionnez **Télécharger**.

### <a name="edit-a-single-instance"></a>Modifier une instance unique

1. Sélectionnez l’instance, puis **Modifier**. 
1. Apportez les modifications nécessaires, puis sélectionnez **Enregistrer**.

    [![Sélections pour modifier une instance unique](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le modèle Time Series, consultez [Modélisation des données](./time-series-insights-update-tsm.md).

- Pour en savoir plus sur la préversion, consultez [Visualiser les données dans l’Explorateur de la préversion d’Azure Time Series Insights](./time-series-insights-update-explorer.md).

- Pour en savoir plus sur les structures JSON prises en charge, consultez [Structures JSON prises en charge](./time-series-insights-send-events.md#supported-json-shapes).
