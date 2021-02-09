---
title: Fichier include
description: Fichier include
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 01/08/2021
ms.author: dasto
ms.openlocfilehash: 2106a48a583f120f8b4dde4eb32a30f1a1b1d85b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947653"
---
### <a name="provider-limits--quota"></a>Limites et quotas du fournisseur

Le service Azure Quantum prend en charge les fournisseurs de services internes et tiers. Les fournisseurs tiers possèdent leurs propres limites et quotas. Le portail Azure permet aux utilisateurs de consulter les offres et les limites lors de la configuration de fournisseurs tiers dans le panneau correspondant. 

Vous trouverez ci-dessous les limites de quota publiées pour le fournisseur de solutions d’optimisation interne de Microsoft. 

#### <a name="learn--develop-sku"></a>Référence SKU Apprendre et développer

| Ressource | Limite |
| --- | --- |
| Travaux simultanés basés sur le processeur | jusqu’à 5 travaux simultanés |
| Travaux simultanés basés sur les FPGA | jusqu’à 2 travaux simultanés |
| Heures du solveur basé sur le processeur | 20 heures par mois  |
| Heures du solveur basé sur les FPGA | 1 heure par mois  |

Si vous utilisez la référence SKU Apprendre et développer, vous **ne pouvez pas** demander une augmentation de vos limites de quota. Vous êtes dès lors invité à opter pour la référence SKU Performances à grande échelle.

#### <a name="performance-at-scale-sku"></a>Référence SKU Performances à grande échelle

| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Travaux simultanés basés sur le processeur | jusqu’à 100 travaux simultanés | identique à la limite par défaut |
| Travaux simultanés basés sur les FPGA | jusqu’à 10 travaux simultanés | identique à la limite par défaut |
| Heures du solveur | 1 000 heures par mois  | jusqu’à 50 000 heures par mois |

S’il vous faut demander une augmentation de limite, contactez le support Azure. 

Pour plus d’informations, consultez la [page de tarification Azure Quantum](https://aka.ms/AQ/Pricing).
Pour plus d’informations sur les offres tierces, consultez la page correspondant au fournisseur qui convient dans le portail Azure.
