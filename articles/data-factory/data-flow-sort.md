---
title: Transformation du tri des flux de données de mappage d’Azure Data Factory
description: Transformation du tri des données de mappage d’Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: f9d2699c870de21daea9ce421c79f089c04b99fa
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029127"
---
# <a name="azure-data-factory-data-flow-sort-transformations"></a>Transformations du tri des flux de données d’Azure Data Factory



![Paramètres de tri](media/data-flow/sort.png "Tri")

La transformation de tri vous permet de trier les lignes entrantes sur le flux de données actuel. Les lignes sortantes de la transformation de tri suivront par la suite les règles de classement que vous avez définies. Vous pouvez choisir des colonnes individuelles et les trier dans l’ordre croissant ou décroissant, en utilisant l’indicateur de flèche à côté de chaque champ. Si vous devez modifier la colonne avant d’appliquer le tri, cliquez sur « Colonnes calculées » pour lancer l’éditeur d’expressions. Cela donnera l’occasion de créer une expression pour l’opération de tri au lieu d’appliquer simplement une colonne pour le tri.

## <a name="case-insensitive"></a>Insensible à la casse
Vous pouvez activer « Ignorer la casse » si vous souhaitez ignorer la casse lors du tri de champs de chaîne ou de texte.

« Trier uniquement au sein de partitions » s’appuie sur le partitionnement de données Spark. En triant les données entrantes uniquement au sein de chaque partition, les flux de données peuvent trier les données partitionnées au lieu de trier le flux de données complet.

Chacune des conditions de tri dans la transformation de tri peut être réorganisée. Donc, si vous devez déplacer une colonne plus haut dans la priorité de tri, saisissez cette ligne avec votre souris et déplacez-la plus haut ou plus bas dans la liste de tri.

Effets du partitionnement sur le tri

Le Data Flow ADF est exécuté sur des clusters Spark de big data avec les données réparties entre plusieurs nœuds et partitions. Il est important de garder cela à l’esprit lors de la création de votre flux de données si vous comptez sur la transformation de tri pour conserver les données dans le même ordre. Si vous choisissez de repartitionner vos données dans une transformation ultérieure, vous risquez de perdre le tri en raison de ce remaniement de données.

## <a name="next-steps"></a>Étapes suivantes

Après le tri, vous souhaiterez peut-être utiliser la [transformation d’agrégation](data-flow-aggregate.md)
