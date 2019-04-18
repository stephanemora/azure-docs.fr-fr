---
title: Transformation de clé de substitution de Data Flow de mappage pour Azure Data Factory
description: Comment utiliser mappage de flux de substitut clé Transformation du Azure Data Factory des données pour générer des valeurs de clés séquentielles
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: eaa1c577f7e208400d3430222b006e0dbbd7956a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698433"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Transformation clés de substitution de flux de données de mappage

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Utilisez la transformation de clé de substitution pour ajouter une valeur de clé arbitraire non professionnelle à incrémentation à l’ensemble de lignes de votre flux de données. Cela est utile lors de la conception de tables de dimension dans un modèle de données analytique de schéma en étoile où chaque membre de vos tables de dimension doit avoir une clé unique qui est une clé non professionnelle, faisant partie de la méthodologie Kimball DW.

![Transformation de la clé de substitution](media/data-flow/surrogate.png "Transformation de la clé de substitution")

« Colonne clé » est le nom que vous donnerez à votre nouvelle colonne de clé de substitution.

« Valeur de début » est le point de début de la valeur incrémentielle.

## <a name="increment-keys-from-existing-sources"></a>Clés d’incrément de sources existantes

Si vous souhaitez démarrer votre séquence à partir d’une valeur qui existe dans une Source, vous pouvez utiliser une transformation de colonne dérivée qui suit immédiatement la transformation de votre clé de substitution et additionner les deux valeurs :

![SK ajouter Max](media/data-flow/sk006.png "substitut clé Transformation ajouter Max")

Pour amorcer la valeur de clé avec la valeur maximale précédente, il existe deux techniques que vous pouvez utiliser :

### <a name="database-sources"></a>Sources de base de données

Utilisez l’option « Requête » pour sélectionner les MAX() à partir de votre source à l’aide de la transformation Source :

![Requête de clé de substitution](media/data-flow/sk002.png "requête de Transformation de la clé de substitution")

### <a name="file-sources"></a>Sources de fichiers

Si votre valeur maximale précédente est dans un fichier, vous pouvez utiliser votre transformation Source ainsi que d’une transformation d’agrégation et utiliser la fonction d’expression MAX() pour obtenir la valeur maximale précédente :

![Fichier de clé de substitution](media/data-flow/sk008.png "fichier de clé de substitution")

Dans les deux cas, vous devez joindre vos nouvelles données entrantes avec votre source qui contient la valeur maximale précédente :

![Jointure de clé de substitution](media/data-flow/sk004.png "jointure de clé de substitution")

## <a name="next-steps"></a>Étapes suivantes

Ces exemples utilisent la [joindre](data-flow-join.md) et [colonne dérivée](data-flow-derived-column.md) transformations.
