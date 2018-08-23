---
ms.assetid: ''
title: Mondes de sécurité Azure Key Vault | Microsoft Docs
ms.service: key-vault
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 07/03/2017
ms.openlocfilehash: 7fd7357a317d5059d6169de2c1536568a254e016
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2018
ms.locfileid: "42145308"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Mondes de sécurité Azure Key Vault et limites géographiques

Azure Key Vault est un service mutualisé qui utilise un pool de modules de sécurité matériel (HSM) dans chaque emplacement Azure. 

Tous les modules de sécurité matériel présents dans les emplacements Azure au sein d’une même région géographique partagent la même limite de chiffrement (monde de sécurité Thales). Par exemple, les régions USA Est et USA Ouest partagent le même monde de sécurité car elles appartiennent à l’emplacement géographique USA. De même, tous les emplacements Azure au Japon partagent le même monde de sécurité, tout comme les emplacements Azure en Australie, en Inde, etc. 

## <a name="backup-and-restore-behavior"></a>Comportement de sauvegarde et de restauration

La sauvegarde d’une clé d’un coffre de clés dans un emplacement Azure peut être restaurée dans un coffre de clés situé dans un autre emplacement Azure, tant que les deux conditions suivantes sont remplies :

- Les deux emplacements Azure appartiennent à la même région géographique
- Les deux coffres de clés appartiennent au même abonnement Azure

Par exemple, la sauvegarde d’une clé effectuée par un abonnement spécifique dans un coffre de clés dans la région Inde Ouest peut uniquement être restaurée dans un coffre de clés appartenant aux mêmes abonnement et emplacement géographique (Inde Ouest, Inde Centre ou Inde Sud).

## <a name="regions-and-products"></a>Régions et produits

- [Régions Azure](https://azure.microsoft.com/regions/)
- [Produits Microsoft par région](https://azure.microsoft.com/regions/services/)

Les régions sont mappées aux mondes de sécurité, illustrés sous forme d’en-têtes principaux dans les tableaux :

Dans l’article « Produits par région », par exemple, l’onglet **Amérique** contient les éléments EAST US, CENTRAL US, WEST US, tous mappés à la région Amérique. 

>[!NOTE]
>US DOD EAST et US DOD CENTRAL sont des exceptions, dans la mesure où ils possèdent leurs propres mondes de sécurité. 

De même, dans l’onglet **Europe**, NORTH EUROPE et WEST EUROPE sont tous deux mappés à la région Europe. Cela vaut également pour l’onglet **Asie Pacifique**.



