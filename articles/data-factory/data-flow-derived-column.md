---
title: Transformation de colonne dérivée dans le mappage Data Flow - Azure Data Factory | Microsoft Docs
description: Découvrez comment transformer des données à l’échelle dans Azure Data Factory avec la transformation de colonne dérivée du mappage Data Flow.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: aacd6f1799f1813e168bd04e78f18cf60ad5243f
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026855"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Transformation de colonne dérivée dans le mappage Data Flow

Utilisez la transformation de colonne dérivée pour générer de nouvelles colonnes dans votre flux de données ou pour modifier des champs existants.

## <a name="derived-column-settings"></a>Paramètres de la colonne dérivée

Pour remplacer une colonne existante, sélectionnez-la via le menu déroulant Colonne. Sinon, utilisez le champ de sélection de colonne en tant que zone de texte et tapez un nouveau nom pour votre colonne. Pour générer l’expression de la colonne dérivée, cliquez sur la zone « Entrez expression » pour ouvrir le [Générateur d’expressions Data Flow](concepts-data-flow-expression-builder.md).

![Paramètres de la colonne dérivée](media/data-flow/dc1.png "Paramètres de la colonne dérivée")

Pour ajouter des colonnes dérivées, pointez sur une colonne dérivée existante et cliquez sur « + ». Ensuite, choisissez « Ajouter une colonne » ou « Ajouter un modèle de colonne ». Les modèles de colonnes peuvent s’avérer utiles si le nom de vos colonnes est variable selon les sources. Pour plus d’informations, voir [Modèles de colonnes](concepts-data-flow-column-pattern.md).

![Nouvelle sélection de colonne dérivée](media/data-flow/columnpattern.png "Nouvelle sélection de colonne dérivée")

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [langage d’expression de mappage Data Flow](data-flow-expression-functions.md).
