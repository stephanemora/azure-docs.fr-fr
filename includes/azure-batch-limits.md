---
title: Fichier Include
description: Fichier Include
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 22bfc3c86605f4c2eed4c022919b3643f394ea95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "67080921"
---
| **Ressource** | **Limite par défaut** | **Limite maximale** |
| --- | --- | --- |
| Comptes Azure Batch par région et par abonnement | 1-3 |50 |
| Cœurs dédiés par compte Batch | 90-900 | Contacter le support technique |
| Cœurs à priorité basse par compte Batch | 10-100 | Contacter le support technique |
| Travaux et planifications de travaux **[actifs](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** par compte Batch (les travaux **terminés** n’ont aucune limite) | 100-300 | 1 000<sup>1</sup> |
| Pools par compte Batch | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> Les limites par défaut varient selon le type d’abonnement que vous utilisez pour créer un compte Batch. Les quotas de cœurs affichés correspondent aux comptes Batch en mode service Batch. [Affichez les quotas dans votre compte Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas).

<sup>1</sup>Si vous voulez solliciter un dépassement de cette limite, contactez le support Azure.
