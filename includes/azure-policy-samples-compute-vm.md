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
ms.openlocfilehash: eedce1ef3a4e7d56cfaf3142a72e370c96f9f575
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664455"
---
### <a name="virtual-machines"></a>Virtual Machines

|  |  |
|---------|---------|
| [Autoriser une image de machine virtuelle personnalisée à partir d’un groupe de ressources](../articles/azure-policy/scripts/allow-custom-vm-image.md) |  Exige que les images personnalisées proviennent d’un groupe de ressources approuvées. Spécifiez le nom du groupe de ressources approuvées. |
| [Références SKU autorisées pour les comptes de stockage et les machines virtuelles](../articles/azure-policy/scripts/allowed-skus-storage.md) | Exige que les comptes de stockage et les machines virtuelles utilisent des références SKU approuvées. Utilise des stratégies intégrées pour garantir les références SKU approuvées. Spécifiez un tableau de références SKU de machine virtuelle approuvées et un tableau de références SKU de compte de stockage approuvées. |
| [Images de machine virtuelle approuvées](../articles/azure-policy/scripts/allowed-custom-images.md) | Exige que seules les images personnalisées approuvées soient déployées dans votre environnement. Spécifiez un tableau d’identifiants d’images approuvées. |
| [Auditer si une extension n’existe pas](../articles/azure-policy/scripts/audit-ext-not-exist.md) | Vérifie si une extension n’est pas déployée avec une machine virtuelle. Spécifiez l’éditeur de l’extension et le type pour vérifier si elle a été déployée. |
| [Extensions de machine virtuelle non autorisées](../articles/azure-policy/scripts/not-allowed-vm-ext.md) | Interdit l’utilisation d’extensions spécifiées. Spécifiez un tableau qui contient les types d’extension interdite. |