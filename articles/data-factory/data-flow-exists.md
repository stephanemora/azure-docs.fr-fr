---
title: Transformation de données fabrique de mappage des données de flux existe Azure
description: Flux de la vérification pour les lignes existantes à l’aide de données de mappage data factory avec Exists transformation
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: b98b7afb21f2f50d44ba93ed793b6efb20f75164
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235961"
---
# <a name="mapping-data-flow-exists-transformation"></a>Mappage de flux de données existe transformation

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

La transformation Exists est une transformation de filtrage de lignes qui stoppe ou autorise l'acheminement des lignes de vos données. La transformation Exists est similaire à ```SQL WHERE EXISTS``` et ```SQL WHERE NOT EXISTS```. Après la Transformation existe, les lignes obtenues à partir de votre flux de données seront soit inclure toutes les lignes où les valeurs de colonne de la source 1 existent dans la source 2 ou n’existent pas dans la source 2.

![Paramètres Exists](media/data-flow/exists.png "exists 1")

Sélectionnez la deuxième source pour votre Exists pour permettre au flux de données de comparer les valeurs du Flux 1 par rapport au Flux 2.

Sélectionnez la colonne de la Source 1 et de la Source 2 dont vous souhaitez vérifier les valeurs Exists ou Not Exists.

## <a name="multiple-exists-conditions"></a>Il existe plusieurs conditions

En regard de chaque ligne dans vos conditions de colonne pour Exists, vous trouverez un + connexion disponibles lorsque vous pointez sur Atteindre la ligne. Cela vous permettra d’ajouter plusieurs lignes pour les conditions de Exists. Chaque condition supplémentaire est un « Et ».

## <a name="custom-expression"></a>Expression personnalisée

![Il existe des paramètres personnalisés](media/data-flow/exists1.png "existe personnalisé")

Vous pouvez cliquer sur « Expression personnalisée » pour créer à la place une expression de forme libre comme votre existe ou non-condition. Cochez cette case vous permettra à taper votre propre expression en tant que condition.

## <a name="next-steps"></a>Étapes suivantes

Transformations similaires sont [recherche](data-flow-lookup.md) et [joindre](data-flow-join.md).
