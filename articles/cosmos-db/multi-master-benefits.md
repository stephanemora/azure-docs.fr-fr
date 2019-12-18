---
title: Avantages d’avoir plusieurs maîtres Azure Cosmos DB
description: Découvrez les avantages du multimaître dans Azure Cosmos DB, de la comparaison des latences et des exigences SLA dans des emplacements d’écriture uniques et multiples.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: b21b6ba82ba1ada0103501b8beeca270df86abd9
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872007"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Comprendre les avantages du multimaître dans Azure Cosmos DB

Les opérateurs de compte COSMOS DB doivent choisir la configuration de distribution globale appropriée afin de garantir la latence, la disponibilité et les exigences de RTO pour leurs applications. Les comptes Azure Cosmos configurés avec plusieurs emplacements d’écriture offrent des avantages significatifs par rapport aux comptes avec un seul emplacement d’écriture, dont la disponibilité SLA à 99,999 %, une latence d’écriture SLA < 10 ms au 99e centile et un RTO = 0 en cas de sinistre régional.

## <a name="comparison-of-features"></a>Comparaison des fonctionnalités

|Spécification de l’application|Plusieurs emplacements d’écriture|Emplacement d’écriture unique|Remarque|
|---|---|---|---|
|Latence d’écriture SLA < 10 ms à 99 %|**Oui**|Non|Les comptes avec un emplacement d’écriture unique subissent une latence réseau supplémentaire entre les régions pour chaque écriture.|
|Latence de lecture SLA < 10 ms à 99 %|**Oui**|OUI| |
|Écriture SLA à 99,999 %|**Oui**|Non|Les comptes avec un emplacement d’écriture unique ont une garantie par contrat SLA de 99,99 %|
|RTO = 0|**Oui**|Non|Temps d’indisponibilité nul pour les écritures en cas de sinistres régionaux. Les comptes avec un emplacement d’écriture unique ont un RTO de 15 minutes.|

## <a name="next-steps"></a>Étapes suivantes

Si vous voulez toujours désactiver EnableMultipleWriteLocations dans votre compte Azure Cosmos, veuillez [ouvrir un ticket de support](https://azure.microsoft.com/support/create-ticket/).
