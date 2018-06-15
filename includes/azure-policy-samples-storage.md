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
ms.openlocfilehash: 3009948c72b0cad6ba603a3ccbe35db8d93fe2c7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664504"
---
## <a name="storage"></a>Stockage

|  |  |
|---------|---------|
| [Références SKU autorisées pour les comptes de stockage et les machines virtuelles](../articles/azure-policy/scripts/allowed-skus-storage.md) | Exige que les comptes de stockage et les machines virtuelles utilisent des références SKU approuvées. Utilise des stratégies intégrées pour garantir les références SKU approuvées. Spécifiez un tableau de références SKU de machine virtuelle approuvées et un tableau de références SKU de compte de stockage approuvées. |
| [Références SKU de compte de stockage autorisées](../articles/azure-policy/scripts/allowed-stor-acct-skus.md) | Exige que les comptes de stockage utilisent une référence SKU approuvée. Spécifiez un tableau de références SKU approuvées. |
| [Refuser le recours au niveau d’accès froid pour les comptes de stockage](../articles/azure-policy/scripts/deny-cool-access-tiering.md) | Interdit le recours au niveau d’accès froid pour les comptes de stockage d’objets blob.  |
| [Garantir uniquement le trafic https pour le compte de stockage](../articles/azure-policy/scripts/ensure-https-stor-acct.md) | Exige que les comptes de stockage utilisent le trafic HTTPS.  |
| [Vérifier le chiffrement des fichiers de stockage](../articles/azure-policy/scripts/ensure-store-file-enc.md) | Exige que le chiffrement soit activé pour les comptes de stockage.  |
| [Exiger le chiffrement du compte de stockage](../articles/azure-policy/scripts/req-store-acct-enc.md) | Oblige le stockage de compte à utiliser le chiffrement des objets blob.  |