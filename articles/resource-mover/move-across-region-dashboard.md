---
title: Tableau de bord Déplacements entre régions
description: Supervisez les déplacements de vos ressources entre régions à l’aide du tableau de bord Déplacements entre régions.
author: Aarthi-Vijayaraghavan
manager: sutalasi
ms.service: resource-move
ms.topic: how-to
ms.date: 08/30/2021
ms.author: AarthiV
ms.openlocfilehash: 9339b61135d4b8b21f88b35a842a080dac9f8e69
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129621091"
---
# <a name="move-across-region-dashboard"></a>Tableau de bord Déplacements entre régions
Cet article explique comment superviser les ressources que vous déplacez entre des régions via le tableau de bord Déplacements entre régions dans Azure Resource Mover. 
## <a name="monitor-via-the-dashboard"></a>Superviser via le tableau de bord
1. Dans **Azure Resource Mover**, sélectionnez **Vue d’ensemble** dans le volet de navigation de gauche. Vous pouvez basculer entre deux pages, **Bien démarrer** et **Tableau de bord Déplacements entre régions**. La page **Bien démarrer** fournit des options pour déplacer vos ressources entre des abonnements, entre des groupes de ressources et entre des régions.
La page **Tableau de bord Déplacements entre régions** combine en un seul endroit toutes les informations de supervision de la région de destination.
    [![Onglet du tableau de bord Déplacements entre régions](media\move-across-region-dashboard\move-across-region-dashboard-tab.png)](media\move-across-region-dashboard\move-across-region-dashboard-tab.png)
2. Le tableau de bord répertorie toutes les combinaisons de déplacement que vous avez créées. Les deux sections suivantes permettent de capturer l’état des déplacements entre des régions.
    Dans **Ressources par état de déplacement**, supervisez le pourcentage et le nombre de ressources dans chaque état.
    Dans **Récapitulatif des erreurs**, supervisez les erreurs actives qui doivent être résolues avant de pouvoir effectuer le déplacement vers la région de destination.
    [![Section État et problèmes](media\move-across-region-dashboard\move-across-region-dashboard-status-issues.png)](media\move-across-region-dashboard\move-across-region-dashboard-status-issues.png)
> [!NOTE]
> Seules les combinaisons source-destination déjà créées dans votre abonnement choisi sont répertoriées dans le tableau de bord.

3. Utilisez les filtres pour choisir votre **abonnement**, **région source** et **région de destination** préférés.
    [![Filtres](media\move-across-region-dashboard\move-across-region-dashboard-filters.png)](media\move-across-region-dashboard\move-across-region-dashboard-filters.png)
4. Accédez à la page des détails en sélectionnant **Afficher toutes les ressource** à côté de la combinaison source-destination.
    [![Détails](media\move-across-region-dashboard\move-across-region-dashboard-details.png)](media\move-across-region-dashboard\move-across-region-dashboard-details.png)
## <a name="next-steps"></a>Étapes suivantes
[Découvrez](about-move-process.md) le processus de déplacement.
