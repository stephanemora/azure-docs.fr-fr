---
title: Modélisation des données dans la préversion de Azure Time Series Insights | Microsoft Docs
description: Découvrez la modélisation des données dans Azure Time Series Insights (préversion).
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/04/2019
ms.custom: seodec18
ms.openlocfilehash: 245a69f5e5834e68bbbd17a96859a93bc16eacbe
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744370"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modélisation des données dans Azure Time Series Insights (préversion)

Ce document explique comment utiliser les modèles Time Series dans la préversion d’Azure Time Series Insights. Il décrit en détail plusieurs scénarios de données courants.

Pour en savoir plus sur l’utilisation de la mise à jour, consultez la page sur l’[Explorateur de la préversion d’Azure Time Series Insights](./time-series-insights-update-explorer.md).

## <a name="types"></a>Types

### <a name="create-a-single-type"></a>Créer un type unique

1. Accédez au panneau du sélecteur de modèles Time Series, puis sélectionnez **Types** dans le menu. Réduisez le panneau pour ne garder que les types de modèles Time Series.

    [![Créer un type unique](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Sélectionnez **Ajouter**.
1. Entrez tous les détails se rapportant aux types, puis sélectionnez **Créer**. Cette action crée des types dans l’environnement.

    [![Ajouter un type](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Charger un ou plusieurs types en bloc

1. Sélectionnez **Charger un fichier JSON**.
1. Sélectionnez le fichier qui contient la charge utile de type.
1. Sélectionnez **Télécharger**.

    [![Charger un fichier JSON](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Modifier un type unique

1. Sélectionnez le type, puis **Modifier**. 
1. Apportez les modifications nécessaires, puis sélectionnez **Enregistrer**.

    [![Modifier un type](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Supprimer un type

1. Sélectionnez le type, puis **Supprimer**.
1. Si aucune instance n’est associée aux types, ils sont supprimés.

    [![Supprimer un type](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Hiérarchies

### <a name="create-a-single-hierarchy"></a>Créer une hiérarchie unique

1. Accédez au panneau du sélecteur de modèles Time Series, puis sélectionnez **Hiérarchies** dans le menu. Réduisez le panneau pour ne garder que les hiérarchies de modèles Time Series.

    [![Sélectionner des hiérarchies](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Sélectionnez **Ajouter**.

    [![Ajouter une hiérarchie](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. Sélectionnez **+ Ajouter un niveau** dans le volet droit.

    [![Ajouter un niveau](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Entrez les détails de la hiérarchie, puis sélectionnez **Créer**.

    [![Créer un niveau](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Charger une ou plusieurs hiérarchies en bloc

1. Sélectionnez **Charger un fichier JSON**.
1. Sélectionnez le fichier qui contient la charge utile de hiérarchie.
1. Sélectionnez **Télécharger**.

    [![Chargement de hiérarchies en bloc](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Modifier une hiérarchie unique

1. Sélectionnez la hiérarchie, puis **Modifier**.
1. Apportez les modifications nécessaires, puis sélectionnez **Enregistrer**.

    [![Modifier une hiérarchie unique](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Supprimer une hiérarchie

1. Sélectionnez la hiérarchie, puis **Supprimer**. 
1. Si aucune instance n’est associée à la hiérarchie, elle est supprimée.

    [![Supprimer une hiérarchie](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>Instances

### <a name="create-a-single-instance"></a>Créer une instance unique

1. Accédez au panneau du sélecteur de modèles Time Series, puis sélectionnez **Instances** dans le menu. Réduisez le panneau pour ne garder que les instances de modèles Time Series.

    [![Créer une instance unique](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Sélectionnez **Ajouter**.

    [![Ajouter une instance](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Entrez les détails de l’instance, sélectionnez l’association de type et de hiérarchie, puis cliquez sur **Créer**.

### <a name="bulk-upload-one-or-more-instances"></a>Charger une ou plusieurs instances en bloc

1. Sélectionnez **Charger un fichier JSON**.
1. Sélectionnez le fichier qui contient la charge utile des instances.

    [![Charger une ou plusieurs instances en bloc](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Sélectionnez **Télécharger**.

### <a name="edit-a-single-instance"></a>Modifier une instance unique

1. Sélectionnez l’instance, puis **Modifier**. 
1. Apportez les modifications nécessaires, puis sélectionnez **Enregistrer**.

    [![Modifier une instance unique](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les modèles Time Series, consultez [Modélisation des données](./time-series-insights-update-tsm.md).

- Pour en savoir plus sur la préversion, consultez [Visualiser les données dans l’Explorateur de la préversion d’Azure Time Series Insights](./time-series-insights-update-explorer.md).

- Pour en savoir plus sur les structures JSON prises en charge, consultez [Structures JSON prises en charge](./time-series-insights-send-events.md#json).
