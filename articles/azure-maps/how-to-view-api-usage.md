---
title: Comment afficher l’utilisation de l’API Azure Maps | Microsoft Docs
description: Découvrez comment afficher les métriques de vos appels de l’API Azure Maps dans le portail.
author: dsk-2015
ms.author: dkshir
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: e62d2ff1fdd6bc94244511a2de95c4268a58d6f9
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2018
ms.locfileid: "42141877"
---
# <a name="how-to-view-the-azure-maps-api-usage"></a>Comment afficher l’utilisation de l’API Azure Maps
Cet article vous montre comment afficher les métriques d’utilisation de l’API pour votre compte Azure Maps dans le [portal](https://portal.azure.com). Les métriques sont affichées dans un format graphique pratique le long d’une chronologie personnalisable. 

## <a name="view-metric-snapshot"></a>Afficher l’instantané d’une métrique 

Vous pouvez voir certaines métriques courantes sur la page **Vue d’ensemble** de votre compte Azure Maps. Elle affiche actuellement le *nombre total de demandes*, le *nombre total d’erreurs* et la *disponibilité* sur une période de temps sélectionnable. 

![Vue d’ensemble des métriques d’Azure Maps](media/how-to-view-api-usage/portal-overview.png)

Si vous avez besoin de personnaliser ces graphiques pour votre analyse, passez à la section suivante.


## <a name="view-detailed-metrics"></a>Afficher des métriques détaillées

1. Connectez-vous à votre abonnement Azure sur le [portail](https://portal.azure.com). 

2. Cliquez sur l’option de menu **Toutes les ressources** à gauche, puis accédez à votre *Compte Azure Maps*.

3. Une fois votre compte Azure Maps ouvert, cliquez sur le menu **Métriques** à gauche.

4. Dans le volet **Métriques**, choisissez l’une des options suivantes :

    1. **Disponibilité**, qui affiche la *Moyenne* de disponibilité de l’API sur une période de temps ; 
    2. **Utilisation**, qui affiche le *nombre* d’utilisations de votre compte. 

    ![Volet Métriques d’Azure Maps](media/how-to-view-api-usage/portal-metrics.png)

5. Après avoir sélectionné les métriques, vous pouvez sélectionner l’*intervalle de temps* **12 dernières heures (automatique)** qui est la valeur par défaut. Vous pouvez également sélectionner la *Granularité temporelle*, ainsi que choisir d’afficher l’heure *locale* ou *GMT* dans la même liste déroulante. Cliquez sur **Appliquer**.

    ![Intervalle de temps des métriques d’Azure Maps](media/how-to-view-api-usage/time-range.png)
 
6. Après avoir ajouté votre métrique, vous pouvez **Ajouter un filtre** à partir des propriétés pertinentes pour cette métrique, puis sélectionner la valeur de la propriété dont vous souhaitez voir le graphique. 

    ![Filtre des métriques d’Azure Maps](media/how-to-view-api-usage/filter.png)

7. Vous pouvez également **Appliquer un fractionnement** à votre métrique en fonction de la propriété de la métrique sélectionnée. Cela a pour effet de fractionner le graphique en plusieurs graphiques, un pour chaque valeur de cette propriété. Par exemple, dans l’image suivante, la couleur de chaque graphique correspond à la valeur de propriété affichée au bas du graphique.

    ![Fractionnement des métriques d’Azure Maps](media/how-to-view-api-usage/splitting.png)
 
8. Vous pouvez également observer plusieurs métriques sur le même graphique, en cliquant simplement sur le bouton **Ajouter une métrique** en haut.


## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris à suivre votre utilisation d’Azure Maps, vous pouvez poursuivre votre découverte des API que vous utilisez en consultant la documentation suivante :

* [Documentation de l’API REST Azure Maps](https://docs.microsoft.com/rest/api/maps)
