---
title: Avantages d’avoir plusieurs maîtres Azure Cosmos DB
description: Comprendre les avantages du multimaître dans Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: mjbrown
ms.openlocfilehash: c78e5e4f8d396d777738bddfd6baf086c0b2ecf4
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67788580"
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
