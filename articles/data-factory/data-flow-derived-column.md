---
title: Transformation de colonne dérivée du Data Flow d’Azure Data Factory
description: Transformation de colonne dérivée du Data Flow d’Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: aee005aed52563e214e24148af2563fd7869de76
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271183"
---
# <a name="azure-data-factory-data-flow-derived-column-transformation"></a>Transformation de colonne dérivée du Data Flow d’Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Utilisez la transformation de colonne dérivée pour générer de nouvelles colonnes dans votre flux de données ou pour modifier des champs existants.

![dériver une colonne](media/data-flow/dc1.png "Colonne dérivée")

Vous pouvez effectuer plusieurs actions de colonne dérivée dans une transformation de colonne dérivée unique. Cliquez sur « Ajouter une colonne » pour transformer plusieurs colonnes lors de l’étape de transformation unique.

Dans le champ de colonne, sélectionnez une colonne existante à remplacer par une nouvelle valeur dérivée, ou cliquez sur « Créer une colonne » pour générer une nouvelle colonne avec la valeur qui vient d’être dérivée.

La zone de texte Expression ouvre le Générateur d’expressions où vous pouvez créer l’expression pour les colonnes dérivées, à l’aide des fonctions d’expression.
