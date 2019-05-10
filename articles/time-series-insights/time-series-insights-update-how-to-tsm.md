---
title: Modélisation des données dans la préversion de Azure Time Series Insights | Microsoft Docs
description: Découvrez la modélisation des données dans Azure Time Series Insights (préversion).
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 1c8886cada80c02e99782159099aa626da35fc50
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466614"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modélisation des données dans Azure Time Series Insights (préversion)

Ce document explique comment utiliser les modèles Time Series dans la préversion d’Azure Time Series Insights. Il décrit en détail plusieurs scénarios de données courants.

Pour en savoir plus sur l’utilisation de la mise à jour, consultez la page sur l’[Explorateur de la préversion d’Azure Time Series Insights](./time-series-insights-update-explorer.md).

## <a name="types"></a>Types

### <a name="create-a-single-type"></a>Créer un type unique

1. Accédez au panneau du sélecteur de modèles Time Series, puis sélectionnez **Types** dans le menu. Réduisez le panneau pour ne garder que les types de modèles Time Series.

    [![Créer un type unique](media/v2-update-how-to-tsm/portal_one.png)](media/v2-update-how-to-tsm/portal_one.png#lightbox)

1. Sélectionnez **Ajouter**.
1. Entrez tous les détails se rapportant aux types, puis sélectionnez **Créer**. Cette action crée des types dans l’environnement.

    [![Ajouter un type](media/v2-update-how-to-tsm/portal_two.png)](media/v2-update-how-to-tsm/portal_two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Charger un ou plusieurs types en bloc

1. Sélectionnez **Charger un fichier JSON**.
1. Sélectionnez le fichier qui contient la charge utile de type.
1. Sélectionnez **Télécharger**.

    [![Télécharger JSON](media/v2-update-how-to-tsm/portal_three.png)](media/v2-update-how-to-tsm/portal_three.png#lightbox)

### <a name="edit-a-single-type"></a>Modifier un type unique

1. Sélectionnez le type, puis **Modifier**. 
1. Apportez les modifications nécessaires, puis sélectionnez **Enregistrer**.

    [![Modifier un type](media/v2-update-how-to-tsm/portal_four.png)](media/v2-update-how-to-tsm/portal_four.png#lightbox)

### <a name="delete-a-type"></a>Supprimer un type

1. Sélectionnez le type, puis **Supprimer**.
1. Si aucune instance n’est associée aux types, ils sont supprimés.

    [![Supprimer un type](media/v2-update-how-to-tsm/portal_five.png)](media/v2-update-how-to-tsm/portal_five.png#lightbox)

## <a name="hierarchies"></a>Hiérarchies

### <a name="create-a-single-hierarchy"></a>Créer une hiérarchie unique

1. Accédez au panneau du sélecteur de modèles Time Series, puis sélectionnez **Hiérarchies** dans le menu. Réduisez le panneau pour ne garder que les hiérarchies de modèles Time Series.

    [![Sélectionner des hiérarchies](media/v2-update-how-to-tsm/portal_six.png)](media/v2-update-how-to-tsm/portal_six.png#lightbox)

1. Sélectionnez **Ajouter**.

    [![Ajouter une hiérarchie](media/v2-update-how-to-tsm/portal_seven.png)](media/v2-update-how-to-tsm/portal_seven.png#lightbox)

1. Sélectionnez **Ajouter un niveau** dans le volet droit.

    [![Ajouter un niveau](media/v2-update-how-to-tsm/portal_eight.png)](media/v2-update-how-to-tsm/portal_eight.png#lightbox)

1. Entrez les détails de la hiérarchie, puis sélectionnez **Créer**.

    [![Créer un niveau](media/v2-update-how-to-tsm/portal_nine.png)](media/v2-update-how-to-tsm/portal_nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Charger une ou plusieurs hiérarchies en bloc

1. Sélectionnez **Charger un fichier JSON**.
1. Sélectionnez le fichier qui contient la charge utile de hiérarchie.
1. Sélectionnez **Télécharger**.

    [![Hiérarchies de chargement en bloc](media/v2-update-how-to-tsm/portal_ten.png)](media/v2-update-how-to-tsm/portal_ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Modifier une hiérarchie unique

1. Sélectionnez la hiérarchie, puis **Modifier**.
1. Apportez les modifications nécessaires, puis sélectionnez **Enregistrer**.

    [![Modifier une hiérarchie unique](media/v2-update-how-to-tsm/portal_eleven.png)](media/v2-update-how-to-tsm/portal_eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Supprimer une hiérarchie

1. Sélectionnez la hiérarchie, puis **Supprimer**. 
1. Si aucune instance n’est associée à la hiérarchie, elle est supprimée.

    [![Supprimer une hiérarchie](media/v2-update-how-to-tsm/portal_twelve.png)](media/v2-update-how-to-tsm/portal_twelve.png#lightbox)

## <a name="instances"></a>Instances

### <a name="create-a-single-instance"></a>Créer une instance unique

1. Accédez au panneau du sélecteur de modèles Time Series, puis sélectionnez **Instances** dans le menu. Réduisez le panneau pour ne garder que les instances de modèles Time Series.

    [![Créer une instance unique](media/v2-update-how-to-tsm/portal_thirteen.png)](media/v2-update-how-to-tsm/portal_thirteen.png#lightbox)

1. Sélectionnez **Ajouter**.

    [![Ajouter une instance](media/v2-update-how-to-tsm/portal_fourteen.png)](media/v2-update-how-to-tsm/portal_fourteen.png#lightbox)

1. Entrez les détails de l’instance, sélectionnez l’association de type et de hiérarchie, puis cliquez sur **Créer**.

### <a name="bulk-upload-one-or-more-instances"></a>Charger une ou plusieurs instances en bloc

1. Sélectionnez **Charger un fichier JSON**.
1. Sélectionnez le fichier qui contient la charge utile des instances.

    [![Une ou plusieurs instances de téléchargement en bloc](media/v2-update-how-to-tsm/portal_fifteen.png)](media/v2-update-how-to-tsm/portal_fifteen.png#lightbox)

1. Sélectionnez **Télécharger**.

### <a name="edit-a-single-instance"></a>Modifier une instance unique

1. Sélectionnez l’instance, puis **Modifier**. 
1. Apportez les modifications nécessaires, puis sélectionnez **Enregistrer**.

    [![Modifier une seule instance](media/v2-update-how-to-tsm/portal_sixteen.png)](media/v2-update-how-to-tsm/portal_sixteen.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les modèles Time Series, consultez [Modélisation des données](./time-series-insights-update-tsm.md).

- Pour en savoir plus sur la préversion, consultez [Visualiser les données dans l’Explorateur de la préversion d’Azure Time Series Insights](./time-series-insights-update-explorer.md).

- Pour en savoir plus sur les structures JSON prises en charge, consultez [Structures JSON prises en charge](./time-series-insights-send-events.md#json).
