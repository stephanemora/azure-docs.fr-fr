---
title: Transformation de nouvelle branche de mappage de flux de données pour Azure Data Factory
description: Transformation de nouvelle branche de mappage de flux de données pour Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: af2225d749283c7124f89d5a7cd735b2f6bfd121
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792614"
---
# <a name="mapping-data-flow-union-transformation"></a>Mappage de transformation union de flux de données

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

L'union combinera plusieurs flux de données en un, avec l'union SQL de ces flux en tant que nouvelle sortie de la transformation d’union. Tous les schémas à partir de chaque flux d’entrée sont combinées à l’intérieur de votre flux de données, sans avoir à disposer d’une clé de jointure.

Vous pouvez combiner des n-nombre de flux dans le tableau de paramètres en sélectionnant l’icône « + » en regard de chaque ligne configurée, notamment la source de données ainsi que des flux à partir de transformations existantes dans votre flux de données.

![Transformation Union](media/data-flow/union.png "Union")

Dans ce cas, vous pouvez associer des métadonnées disparates provenant de plusieurs sources (dans cet exemple, trois différents fichiers sources) et les combiner en un seul flux :

![Vue d’ensemble de la transformation Union](media/data-flow/union111.png "1 d’Union")

Pour ce faire, ajoutez des lignes supplémentaires dans les paramètres d’Union en incluant toutes les sources que vous souhaitez ajouter. Il n’est pas nécessaire pour une clé de recherche ou de jointure commune :

![Paramètres de la transformation Union](media/data-flow/unionsettings.png "paramètres Union")

Si vous définissez une transformation sélectionnez après votre Union, vous ne pourrez pas renommer des champs qui se chevauchent ou qui n’étaient pas nommés à partir de sources sans en-tête. Cliquez sur « Inspecter » pour afficher les métadonnées de la combiner avec les colonnes total 132 dans cet exemple à partir de trois sources différentes :

![Transformation union finale](media/data-flow/union333.png "3 Union")

## <a name="name-and-position"></a>Nom et la position

Lorsque vous choisissez « union par nom », chaque valeur de colonne sera supprimée dans la colonne correspondante de chaque source, avec un nouveau schéma de métadonnées concaténée.

Si vous choisissez « union par position », chaque valeur de colonne supprimera dans la position d’origine à partir de chaque source correspondante, ce qui entraîne un nouveau flux combiné de données où les données de chaque source sont ajoutées au même flux :

![Sortie Union](media/data-flow/unionoutput.png "sortie Union")

## <a name="next-steps"></a>Étapes suivantes

Explorez les transformations similaire, y compris [joindre](data-flow-join.md) et [Exists](data-flow-exists.md).
