---
title: Modèles de colonne Data Flow de mappage Azure Data Factory
description: Découvrez comment utiliser le modèle de colonne d’Azure Data Factory dans le mappage de flux de données pour créer des modèles de modèle généralisé pour transformer les champs dans un flux de données, quel que soit le schéma de métadonnées sous-jacentes
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 08cdaafe00b7dc586ea75f6ff03fdb89107edee9
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430757"
---
# <a name="azure-data-factory-mapping-data-flows-column-patterns"></a>Modèle de colonne de flux de données de mappage de Azure data factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Plusieurs transformations de flux de données Azure Data Factory supportent le concept de « Modèles de colonne » afin que vous puissiez créer des colonnes modèles basées sur des modèles et non pas sur des noms de colonne codés en dur. Vous pouvez utiliser cette fonctionnalité dans le Générateur d’expressions pour définir des modèles pour faire correspondre les colonnes pour la transformation au lieu de demander des noms de champ précis. Les modèles sont utiles si les champs sources entrantes changent souvent, en particulier dans le cas de modification des colonnes dans des fichiers texte ou des bases de données NoSQL. Cette condition est parfois appelée « Une dérive du schéma ».

![modèles de colonne](media/data-flow/columnpattern2.png "Modèles de colonne")

Les modèles de colonne sont utiles pour traiter à la fois les scénarios de dérive de schéma et les scénarios généraux. Cela est pratique lorsque vous n’êtes pas en mesure de connaître précisément le nom de chaque colonne. Vous pouvez faire correspondre un modèle avec le nom de colonne et le type de données de la colonne et construire une expression pour la transformation qui exécutera cette opération sur n’importe quel champ du flux de données qui correspond à vos modèles `name` & `type`.

Lorsque vous ajoutez une expression à une transformation qui accepte des modèles, choisissez « Ajouter un modèle de colonne ». Modèles de colonne permet de faire correspondre les modèles des colonnes de dérive de schéma.

Lors de la construction de modèles de colonnes de modèle, utilisez `$$` dans l’expression pour représenter une référence à chaque champ correspondant du flux de données entrant.

Si vous choisissez d’utiliser une des fonctions d’expression régulière de générateur d’expressions, vous pouvez ensuite par la suite utiliser $1, $2, $3... Pour référencer les sous-modèles en correspondance à partir de votre expression régulière.

L’utilisation de SUM avec une série de champs entrants constitue un exemple de scénario de modèle de colonne. Les calculs agrégés des calculs de SUM résident dans la transformation agrégée. Vous pouvez ensuite utiliser la somme sur chaque correspondance des types de champ qui correspond à « integer », puis utilisez $$ pour chaque correspondance dans votre expression de référence.
