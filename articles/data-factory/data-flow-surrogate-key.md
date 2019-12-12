---
title: Transformation de clé de substitution du mappage de flux de données
description: Comment utiliser une transformation de clé de substitution du mappage de flux de données Azure Data Factory pour générer des valeurs de clés séquentielles
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: bab48aa9079c1b8020bb828a6bb91bd244a78cf1
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930197"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Transformation de clé de substitution du mappage de flux de données



Utilisez la transformation de clé de substitution pour ajouter une valeur de clé arbitraire non professionnelle à incrémentation à l’ensemble de lignes de votre flux de données. Cela est utile lors de la conception de tables de dimension dans un modèle de données analytique de schéma en étoile où chaque membre de vos tables de dimension doit avoir une clé unique qui est une clé non professionnelle, faisant partie de la méthodologie Kimball DW.

![Transformation de clé de substitution](media/data-flow/surrogate.png "Transformation de clé de substitution")

« Colonne clé » est le nom que vous donnerez à votre nouvelle colonne de clé de substitution.

« Valeur de début » est le point de début de la valeur incrémentielle.

## <a name="increment-keys-from-existing-sources"></a>Incrémenter les clés de sources existantes

Si vous souhaitez démarrer votre séquence à partir d’une valeur existant dans une source, vous pouvez utiliser une transformation de colonne dérivée qui suit immédiatement votre transformation de clé de substitution et additionner les deux valeurs :

![Ajouter max SK](media/data-flow/sk006.png "Ajout maximum de transformation de clé de substitution")

Pour amorcer la valeur de clé avec la valeur maximale précédente, il existe deux techniques :

### <a name="database-sources"></a>Sources de base de données

Utilisez l’option « Requête » pour sélectionner la valeur MAX() dans votre source en utilisant la transformation de source :

![Requête de clé de substitution](media/data-flow/sk002.png "Requête de transformation de clé de substitution")

### <a name="file-sources"></a>Sources de fichiers

Si votre valeur maximale précédente figure dans un fichier, vous pouvez utiliser votre transformation Source ainsi qu’une transformation Agrégation, et utiliser la fonction d’expression MAX() pour obtenir la valeur maximale précédente :

![Fichier de clé de substitution](media/data-flow/sk008.png "Fichier de clé de substitution")

Dans les deux cas, vous devez joindre vos nouvelles données entrantes avec votre source contenant la valeur maximale précédente :

![Jointure de clé de substitution](media/data-flow/sk004.png "Jointure de clé de substitution")

## <a name="next-steps"></a>Étapes suivantes

Ces exemples utilisent les transformations [Joindre](data-flow-join.md) et [Colonne dérivée](data-flow-derived-column.md).
