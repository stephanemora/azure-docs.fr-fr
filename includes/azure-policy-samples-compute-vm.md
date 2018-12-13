---
title: Fichier Include
description: Fichier Include
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: b6587a3928c2ddf0d00c90e07643525314690e42
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318208"
---
### <a name="virtual-machines"></a>Virtual Machines

|  |  |
|---------|---------|
| [Autoriser une image de machine virtuelle personnalisée à partir d’un groupe de ressources](../articles/governance/policy/samples/allow-custom-vm-image.md) |  Exige que les images personnalisées proviennent d’un groupe de ressources approuvées. Spécifiez le nom du groupe de ressources approuvées. |
| [Références SKU autorisées pour les comptes de stockage et les machines virtuelles](../articles/governance/policy/samples/allowed-skus-storage.md) | Exige que les comptes de stockage et les machines virtuelles utilisent des références SKU approuvées. Utilise des stratégies intégrées pour garantir les références SKU approuvées. Spécifiez un tableau de références SKU de machine virtuelle approuvées et un tableau de références SKU de compte de stockage approuvées. |
| [Images de machine virtuelle approuvées](../articles/governance/policy/samples/allowed-custom-images.md) | Exige que seules les images personnalisées approuvées soient déployées dans votre environnement. Spécifiez un tableau d’identifiants d’images approuvées. |
| [Auditer si une extension n’existe pas](../articles/governance/policy/samples/audit-extension-not-exist.md) | Vérifie si une extension n’est pas déployée avec une machine virtuelle. Spécifiez l’éditeur de l’extension et le type pour vérifier si elle a été déployée. |
| [Extensions de machine virtuelle non autorisées](../articles/governance/policy/samples/not-allowed-vm-extension.md) | Interdit l’utilisation d’extensions spécifiées. Spécifiez un tableau qui contient les types d’extension interdite. |