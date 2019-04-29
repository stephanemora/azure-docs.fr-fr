---
title: Transformation de recherche de mappage de Data Flow pour Azure Data Factory
description: Transformation de recherche de mappage de Data Flow pour Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: ef473ea5f88b9108894787785fe1e9083fab1b0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61348016"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Transformation de recherche de mappage de Data Flow pour Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Utilisez la recherche pour ajouter des données de référence d’une autre source à votre Data Flow. La transformation de recherche requiert une source définie qui pointe vers votre table de référence et qui correspond aux champs clés.

![Transformation de recherche](media/data-flow/lookup1.png "Recherche")

Sélectionnez les champs clés que vous souhaitez mettre en correspondance entre les champs de flux de données entrantes et les champs provenant de la source de référence. Vous devez d’abord créer une nouvelle source sur le canevas de conception du Data Flow à utiliser comme partie droite de la recherche.

Lorsque les correspondances sont trouvées, les lignes et les colonnes résultantes provenant de la source de référence seront ajoutés à votre flux de données. Vous pouvez choisir les champs d’intérêt que vous souhaitez inclure dans votre récepteur à la fin de votre Data Flow.

## <a name="optimizations"></a>Optimisations

Dans Data Factory, exécutez avec le flux de données dans des environnements à grande échelle Spark. Si votre jeu de données peut tenir dans l’espace de mémoire de nœud de travail, nous pouvons optimiser les performances de votre recherche.

![Jointure de diffusion](media/data-flow/broadcast.png "jointure de diffusion")

### <a name="broadcast-join"></a>Jonction de diffusion

Sélectionnez gauche et/ou à droite de diffusion jointure pour demander l’ADF pour transmettre l’ensemble du dataset à partir de chaque côté de la relation de recherche dans la mémoire.

### <a name="data-partitioning"></a>Partitionnement des données

Vous pouvez également spécifier le partitionnement de vos données en sélectionnant « Partitionnement du jeu » sur l’onglet Optimisation de la transformation de recherche pour créer des jeux de données qui s’adapte mieux à la mémoire par worker.

## <a name="next-steps"></a>Étapes suivantes

[Joindre](data-flow-join.md) et [Exists](data-flow-exists.md) transformations effectuent des tâches similaires dans ADF les flux de données de mappage. Examinons ces transformations suivant.
