---
author: whhender
ms.author: whhender
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 6/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0ea867a13772b4a0d10d21d3ca732320b404d9c5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131010881"
---
### <a name="view-your-scans-and-scan-runs"></a>Afficher vos analyses et exécutions d’analyse

Pour afficher les analyses existantes, procédez comme suit :

1. Accédez à [Purview Studio](https://web.purview.azure.com/resource/). Sélectionnez l’onglet **Data Map** sous le volet gauche.

1. Sélectionnez la source de données souhaitée. Vous verrez une liste des analyses existantes sur cette source de données sous **Analyses récentes**, ou vous pouvez afficher toutes les analyses sous l’onglet **Analyses**.

1. Sélectionnez l’analyse dont vous souhaitez afficher les résultats.

1. Cette page affiche toutes les exécutions précédentes de l’analyse ainsi que l’état et les métriques de chaque exécution de l’analyse. Elle indique également si votre analyse a été planifiée ou était manuelle, le nombre de ressources pour lesquelles ont été appliquées des classifications, le nombre total de ressources découvertes, l’heure de début et de fin de l’analyse et la durée totale de l’analyse.

### <a name="manage-your-scans---edit-delete-or-cancel"></a>Gérer vos analyses : modifier, supprimer ou annuler

Pour gérer ou supprimer une analyse, procédez comme suit :

1. Accédez à [Purview Studio](https://web.purview.azure.com/resource/). Sélectionnez l’onglet **Data Map** sous le volet gauche.

1. Sélectionnez la source de données souhaitée. Vous verrez une liste des analyses existantes sur cette source de données sous **Analyses récentes**, ou vous pouvez afficher toutes les analyses sous l’onglet **Analyses**.

1. Sélectionnez l’analyse que vous souhaitez gérer. Vous pouvez modifier l’analyse en sélectionnant **Modifier une analyse**.

1. Vous pouvez annuler une analyse en cours en sélectionnant **Annuler une exécution d’analyse**.

1. Vous pouvez supprimer votre analyse en sélectionnant **Supprimer une analyse**.

> [!NOTE]
> * La suppression de votre analyse ne supprime pas les ressources de votre catalogue créées à partir des analyses précédentes.
> * La ressource n’est plus mise à jour avec les modifications de schéma si votre table source a été modifiée et que vous relancez l’analyse de la table source après avoir modifié la description sous l’onglet Schéma de Purview.
