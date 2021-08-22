---
title: Mondes de sécurité Azure Key Vault | Microsoft Docs
description: Azure Key Vault est un service mutualisé. Il utilise un pool de modules HSM dans chaque région Azure. Toutes les régions au sein d’une région géographique partagent une limite de chiffrement.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 07/03/2017
ms.openlocfilehash: 0753108df52f0dcaea1d6c79eab266eeb000889a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532619"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Mondes de sécurité Azure Key Vault et limites géographiques

Les produits Azure sont disponibles dans plusieurs [géographies Azure](https://azure.microsoft.com/global-infrastructure/geographies/), chacune contenant une ou plusieurs régions. Par exemple, la géographie Europe contient deux régions : Europe Nord et Europe Ouest, tandis que la seule région de la géographie Brésil est Brésil Sud.

Azure Key Vault est un service mutualisé qui utilise un pool de modules de sécurité matériel (HSM). Tous les modules HSM d’une zone géographique partagent la même limite de chiffrement, appelée « monde de sécurité ». Chaque géographie correspond à un monde de sécurité unique, et vice versa.

Les régions USA Est et USA Ouest partagent le même monde de sécurité car elles appartiennent à l’emplacement géographique États-Unis. De même, toutes les régions Azure du Japon partagent le même monde de sécurité, tout comme l’ensemble des régions Azure en Australie, et ainsi de suite.

>[!NOTE]
> US DOD EAST et US DOD CENTRAL sont des exceptions, dans la mesure où ils possèdent leurs propres mondes de sécurité.

## <a name="backup-and-restore-behavior"></a>Comportement de sauvegarde et de restauration

La sauvegarde d’une clé d’un coffre de clés dans une région Azure peut être restaurée dans un coffre de clés situé dans une autre région Azure, tant que les deux conditions suivantes sont remplies :

- Les deux régions Azure appartiennent à la même géographie.
- Les deux coffres de clés appartiennent au même abonnement Azure.

Par exemple, une sauvegarde effectuée à partir d’une clé dans un coffre de clés Inde Ouest peut être restaurée sur un autre coffre de clés dans le même abonnement dans la zone géographique Inde (les régions Inde Ouest, Inde Centre et Inde Sud).

## <a name="next-steps"></a>Étapes suivantes

- Voir [Produits Microsoft par région](https://azure.microsoft.com/regions/services/)
