---
title: Modèles de colonne Data Flow de mappage Azure Data Factory
description: Découvrez comment utiliser le modèle de colonne d’Azure Data Factory dans le mappage de flux de données pour créer des modèles de modèle généralisé pour transformer les champs dans un flux de données, quel que soit le schéma de métadonnées sous-jacentes
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 5131329f6675bc86374f5a5c081e0aaa7d36c0fe
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155236"
---
# <a name="azure-data-factory-mapping-data-flows-column-patterns"></a>Modèle de colonne de flux de données de mappage de Azure data factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Plusieurs transformations de flux de données Azure Data Factory supportent le concept de « Modèles de colonne » afin que vous puissiez créer des colonnes modèles basées sur des modèles et non pas sur des noms de colonne codés en dur. Vous pouvez utiliser cette fonctionnalité dans le Générateur d’expressions pour définir des modèles afin de faire correspondre les colonnes dans le cadre de la transformation au lieu d’exiger des noms de champs spécifiques exacts. Les modèles sont utiles si les champs sources entrantes changent souvent, en particulier dans le cas de modification des colonnes dans des fichiers texte ou des bases de données NoSQL. Cette condition est parfois appelée « Une dérive du schéma ».

![modèles de colonne](media/data-flow/columnpattern2.png "Modèles de colonne")

Les modèles de colonne sont utiles pour traiter à la fois les scénarios de dérive de schéma et les scénarios généraux. Cela est pratique lorsque vous n’êtes pas en mesure de connaître précisément le nom de chaque colonne. Vous pouvez faire correspondre un modèle avec le nom de colonne et le type de données de la colonne et construire une expression pour la transformation qui exécutera cette opération sur n’importe quel champ du flux de données qui correspond à vos modèles `name` & `type`.

Lorsque vous ajoutez une expression à une transformation qui accepte des modèles, choisissez « Ajouter un modèle de colonne ». Modèles de colonne permet de faire correspondre les modèles des colonnes de dérive de schéma.

Lors de la construction de modèles de colonnes de modèle, utilisez `$$` dans l’expression pour représenter une référence à chaque champ correspondant du flux de données entrant.

Si vous choisissez d’utiliser l’une des fonctions d’expression régulière du Générateur d’expressions, vous pouvez ensuite utiliser $1, $2, $3... pour référencer les sous-modèles correspondant à votre expression régulière.

L’utilisation de SUM avec une série de champs entrants constitue un exemple de scénario de modèle de colonne. Les calculs agrégés des calculs de SUM résident dans la transformation agrégée. Vous pouvez ensuite exécuter SUM sur chaque correspondance des types de champs qui correspondent à « integer », puis utiliser $$ pour référencer chaque correspondance dans votre expression.
