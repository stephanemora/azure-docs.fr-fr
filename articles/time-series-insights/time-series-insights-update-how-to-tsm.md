---
title: Modélisation des données Azure Time Series Insights - Modélisation des données dans Azure Time Series Insights (préversion) | Microsoft Docs
description: Découvrez la modélisation des données dans Azure Time Series Insights (préversion).
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: e68bc01d2c0781333454fa753992d0136fac0c06
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269088"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modélisation des données dans Azure Time Series Insights (préversion)

Ce document explique comment utiliser les modèles Time Series dans la préversion d’Azure Time Series Insights. Il décrit en détail plusieurs scénarios de données courants.

Pour en savoir plus sur l’utilisation de la mise à jour, consultez la page sur l’[Explorateur de la préversion d’Azure Time Series Insights](./time-series-insights-update-explorer.md).

## <a name="types"></a>Types

### <a name="create-a-single-type"></a>Créer un type unique

1. Accédez au panneau du sélecteur de modèles Time Series, puis sélectionnez **Types** dans le menu. Réduisez le panneau pour ne garder que les types de modèles Time Series.

    ![Portal_one][1]

1. Sélectionnez **Ajouter**.
1. Entrez tous les détails se rapportant aux types, puis sélectionnez **Créer**. Cette action crée des types dans l’environnement.

    ![Portal_two][2]

### <a name="bulk-upload-one-or-more-types"></a>Charger un ou plusieurs types en bloc

1. Sélectionnez **Charger un fichier JSON**.
1. Sélectionnez le fichier qui contient la charge utile de type.
1. Sélectionnez **Télécharger**.

    ![Portal_three][3]

### <a name="edit-a-single-type"></a>Modifier un type unique

Sélectionnez le type, puis **Modifier**. Apportez les modifications nécessaires, puis sélectionnez **Enregistrer**.

![Portal_four][4]

### <a name="delete-a-type"></a>Supprimer un type

Sélectionnez le type, puis **Supprimer**. Si aucune instance n’est associée aux types, ils sont supprimés.

![Portal_five][5]

## <a name="hierarchies"></a>Hiérarchies

### <a name="create-a-single-hierarchy"></a>Créer une hiérarchie unique

1. Accédez au panneau du sélecteur de modèles Time Series, puis sélectionnez **Hiérarchies** dans le menu. Réduisez le panneau pour ne garder que les hiérarchies de modèles Time Series.

    ![Portal_six][6]

1. Sélectionnez **Ajouter**.

    ![Portal_seven][7]

1. Sélectionnez **Ajouter un niveau** dans le volet droit.

    ![Portal_eight][8]

1. Entrez les détails de la hiérarchie, puis sélectionnez **Créer**.

    ![Portal_nine][9]

### <a name="bulk-upload-one-or-more-hierarchies"></a>Charger une ou plusieurs hiérarchies en bloc

1. Sélectionnez **Charger un fichier JSON**.
1. Sélectionnez le fichier qui contient la charge utile de hiérarchie.
1. Sélectionnez **Télécharger**.

    ![Portal_ten][10]

### <a name="edit-a-single-hierarchy"></a>Modifier une hiérarchie unique

Sélectionnez la hiérarchie, puis **Modifier**. Apportez les modifications nécessaires, puis sélectionnez **Enregistrer**.

![Portal_eleven][11]

### <a name="delete-a-hierarchy"></a>Supprimer une hiérarchie

Sélectionnez la hiérarchie, puis **Supprimer**. Si aucune instance n’est associée à la hiérarchie, elle est supprimée.

![Portal_twelve][12]

## <a name="instances"></a>Instances

### <a name="create-a-single-instance"></a>Créer une instance unique

1. Accédez au panneau du sélecteur de modèles Time Series, puis sélectionnez **Instances** dans le menu. Réduisez le panneau pour ne garder que les instances de modèles Time Series.

    ![Portal_thirteen][13]

1. Sélectionnez **Ajouter**.

    ![Portal_fourteen][14]

1. Entrez les détails de l’instance, sélectionnez l’association de type et de hiérarchie, puis cliquez sur **Créer**.

### <a name="bulk-upload-one-or-more-instances"></a>Charger une ou plusieurs instances en bloc

1. Sélectionnez **Charger un fichier JSON**.
1. Sélectionnez le fichier qui contient la charge utile des instances.

    ![Portal_fifteen][15]

1. Sélectionnez **Télécharger**.

### <a name="edit-a-single-instance"></a>Modifier une instance unique

Sélectionnez l’instance, puis **Modifier**. Apportez les modifications nécessaires, puis sélectionnez **Enregistrer**.

![Portal_sixteen][16]

### <a name="delete-an-instance"></a>Supprimer une instance

Sélectionnez l’instance, puis **Supprimer**. Si aucun événement n’est associé aux instances, elles sont supprimées.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les modèles Time Series, consultez [Modélisation des données](./time-series-insights-update-tsm.md).
- Pour en savoir plus sur la préversion, consultez [Visualiser les données dans l’Explorateur de la préversion d’Azure Time Series Insights](./time-series-insights-update-explorer.md).
- Pour en savoir plus sur les structures JSON prises en charge, consultez [Structures JSON prises en charge](./time-series-insights-send-events.md#json).

<!-- Images -->
[1]: media/v2-update-how-to-tsm/portal_one.png
[2]: media/v2-update-how-to-tsm/portal_two.png
[3]: media/v2-update-how-to-tsm/portal_three.png
[4]: media/v2-update-how-to-tsm/portal_four.png
[5]: media/v2-update-how-to-tsm/portal_five.png
[6]: media/v2-update-how-to-tsm/portal_six.png
[7]: media/v2-update-how-to-tsm/portal_seven.png
[8]: media/v2-update-how-to-tsm/portal_eight.png
[9]: media/v2-update-how-to-tsm/portal_nine.png
[10]: media/v2-update-how-to-tsm/portal_ten.png
[11]: media/v2-update-how-to-tsm/portal_eleven.png
[12]: media/v2-update-how-to-tsm/portal_twelve.png
[13]: media/v2-update-how-to-tsm/portal_thirteen.png
[14]: media/v2-update-how-to-tsm/portal_fourteen.png
[15]: media/v2-update-how-to-tsm/portal_fifteen.png
[16]: media/v2-update-how-to-tsm/portal_sixteen.png