---
title: Fichier Include
description: Fichier Include
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: fe6c6d461fd6b99ce2ce57ebdd61a3f3e961489c
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318238"
---
## <a name="storage"></a>Stockage

|  |  |
|---------|---------|
| [Références SKU autorisées pour les comptes de stockage et les machines virtuelles](../articles/governance/policy/samples/allowed-skus-storage.md) | Exige que les comptes de stockage et les machines virtuelles utilisent des références SKU approuvées. Utilise des stratégies intégrées pour garantir les références SKU approuvées. Spécifiez un tableau de références SKU de machine virtuelle approuvées et un tableau de références SKU de compte de stockage approuvées. |
| [Références SKU de compte de stockage autorisées](../articles/governance/policy/samples/allowed-storage-account-skus.md) | Exige que les comptes de stockage utilisent une référence SKU approuvée. Spécifiez un tableau de références SKU approuvées. |
| [Refuser le recours au niveau d’accès froid pour les comptes de stockage](../articles/governance/policy/samples/deny-cool-access-tiering.md) | Interdit le recours au niveau d’accès froid pour les comptes de stockage d’objets blob.  |
| [Garantir uniquement le trafic https pour le compte de stockage](../articles/governance/policy/samples/ensure-https-storage-account.md) | Exige que les comptes de stockage utilisent le trafic HTTPS.  |
| [Vérifier le chiffrement des fichiers de stockage](../articles/governance/policy/samples/ensure-storage-file-encryption.md) | Exige que le chiffrement soit activé pour les comptes de stockage.  |
| [Exiger le chiffrement du compte de stockage](../articles/governance/policy/samples/require-storage-account-encryption.md) | Oblige le stockage de compte à utiliser le chiffrement des objets blob.  |