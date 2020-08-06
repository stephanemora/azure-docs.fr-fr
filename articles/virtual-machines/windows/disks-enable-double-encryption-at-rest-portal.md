---
title: Activer le double chiffrement au repos – Portail Azure – Disques managés
description: Activez le double chiffrement au repos pour les données de vos disques managés à l’aide du portail Azure.
author: roygara
ms.date: 07/23/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: b244ef36e4e1a15327c053402b28ee2272fc71f0
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87135947"
---
# <a name="azure-portal---enable-double-encryption-at-rest-on-your-managed-disks"></a>Portail Azure : Activer le double chiffrement au repos sur vos disques managés

Le Stockage sur disque Azure prend en charge le double chiffrement au repos pour les disques managés. Pour obtenir des informations conceptuelles sur le double chiffrement au repos ainsi que d’autres types de chiffrement de disque managé, consultez la section [Double chiffrement au repos](disk-encryption.md#double-encryption-at-rest) de notre article sur le chiffrement de disque.

[!INCLUDE [virtual-machines-double-encryption-at-rest-portal](../../../includes/virtual-machines-disks-double-encryption-at-rest-portal.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Azure PowerShell : Activer les clés gérées par le client avec les disques managés – chiffrement côté serveur](disks-enable-customer-managed-keys-powershell.md)
- [Exemples de modèles Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)