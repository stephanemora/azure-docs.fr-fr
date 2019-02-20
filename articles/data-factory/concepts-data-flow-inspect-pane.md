---
title: Volet Inspecter et Aperçu de données de Data Flow pour Azure Data Factory
description: Le mappage de flux de données d’Azure Data Factory possède un volet qui affiche les métadonnées de flux de données (Inspecter) et l’aperçu des données en mode débogage (aperçu des données).
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: e7d03ecd8ea4aecf1e0cfdd02d3b9bc5300abe6b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212384"
---
# <a name="azure-data-factory-mapping-data-flow-transformation-inspect-tab"></a>Onglet d’inspection de transformation de Data Flow pour Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Volet Inspecter](media/data-flow/agg3.png "Volet Inspecter")

Le volet Inspecter permet de visualiser les métadonnées du flux de données que vous êtes en train de transformer. Vous pourrez afficher le nombre de colonnes, les colonnes modifiées, les colonnes ajoutées, les types de données, l’ordre des colonnes et les références de colonne. «Inspecter » est un affichage en lecture seule de vos métadonnées. Vous n’avez pas besoin d’avoir activé le mode Débogage pour voir les métadonnées dans le volet d’inspection.

Au fur et à mesure que vous modifiez la forme de vos données par des transformations, les changements de métadonnées sont visibles dans le volet d’inspection. Si votre transformation source ne comporte pas de schéma défini, les métadonnées ne sont pas visibles dans le volet d’inspection. L’absence de métadonnées est fréquent dans les scénarios de dérive de schéma.

![Aperçu des données](media/data-flow/datapreview.png "Aperçu des données")

Aperçu des données est un volet qui fournit une vue en lecture seule de vos données en cours de transformation. Vous pouvez visualiser la sortie de vos transformations et expressions afin de valider votre flux de données. Vous devez avoir activé le Mode débogage pour voir les aperçus de données. Lorsque vous cliquez sur les colonnes de la grille d’aperçu des données, un panneau s’affiche alors à droite. La fenêtre contextuelle indique les statistiques de profil pour chacune des colonnes que vous sélectionnez.

![Graphique Aperçu des données](media/data-flow/chart.png "Graphique Aperçu des données")

