---
title: Azure Data Factory - Mappage d’une transformation d'existence de flux de données
description: Azure Data Factory - Mappage d’une transformation d'existence de flux de données
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 9d21b304f55ec746da4b7b42194fe0d168261b53
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271273"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Azure Data Factory - Mappage d’une transformation Exists de flux de données

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

La transformation Exists est une transformation de filtrage de lignes qui stoppe ou autorise l'acheminement des lignes de vos données. La transformation Exists est similaire à ```SQL WHERE EXISTS``` et ```SQL WHERE NOT EXISTS```. Après une transformation de filtre, les lignes obtenues à partir de votre flux de données incluent toutes les lignes où les valeurs de colonne de la source 1 existent dans la source 2 ou n’existent pas dans la source 2.

![Paramètres Exists](media/data-flow/exsits.png "exists 1")

Sélectionnez la deuxième source pour votre Exists pour permettre au flux de données de comparer les valeurs du Flux 1 par rapport au Flux 2.

Sélectionnez la colonne de la Source 1 et de la Source 2 dont vous souhaitez vérifier les valeurs Exists ou Not Exists.
