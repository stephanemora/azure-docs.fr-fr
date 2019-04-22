---
title: Azure Data Factory - Mappage d’une transformation d'existence de flux de données
description: Azure Data Factory - Mappage d’une transformation d'existence de flux de données
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 6ce27ba699ae766ed4d2428f67d91379464bb9f1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59783460"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Azure Data Factory - Mappage d’une transformation Exists de flux de données

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

La transformation Exists est une transformation de filtrage de lignes qui stoppe ou autorise l'acheminement des lignes de vos données. La transformation Exists est similaire à ```SQL WHERE EXISTS``` et ```SQL WHERE NOT EXISTS```. Après une transformation de filtre, les lignes obtenues à partir de votre flux de données incluent toutes les lignes où les valeurs de colonne de la source 1 existent dans la source 2 ou n’existent pas dans la source 2.

![Paramètres Exists](media/data-flow/exsits.png "exists 1")

Sélectionnez la deuxième source pour votre Exists pour permettre au flux de données de comparer les valeurs du Flux 1 par rapport au Flux 2.

Sélectionnez la colonne de la Source 1 et de la Source 2 dont vous souhaitez vérifier les valeurs Exists ou Not Exists.

## <a name="multiple-exists-conditions"></a>Il existe plusieurs conditions

En regard de chaque ligne dans vos conditions de colonne pour existe, vous trouverez un + connexion disponibles lorsque vous pointez sur Atteindre la ligne. Cela vous permettra d’ajouter plusieurs lignes pour les conditions de Exists.

## <a name="next-steps"></a>Étapes suivantes

