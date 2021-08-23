---
title: Créer et chiffrer un groupe de machines virtuelles identiques avec des modèles Azure Resource Manager
description: Dans ce guide de démarrage rapide, vous allez apprendre à utiliser les modèles Azure Resource Manager pour créer et chiffrer un groupe de machines virtuelles identiques
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: c6f572ad472bcafe0ca1ff301f2d32a34a924424
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112291814"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Chiffrer des groupes de machines virtuelles identiques avec Azure Resource Manager

Vous pouvez chiffrer ou déchiffrer des groupes de machines virtuelles identiques Linux à l’aide de modèles Azure Resource Manager.

## <a name="deploying-templates"></a>Déploiement de modèles

Tout d’abord, sélectionnez le modèle qui convient à votre scénario.

- [Activer le chiffrement de disque sur un groupe de machines virtuelles identiques Linux en cours d’exécution](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-running-vmss-linux)

- [Activer le chiffrement de disque sur un groupe de machines virtuelles identiques Windows en cours d’exécution](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-running-vmss-windows)

  - [Déployer un groupe de machines virtuelles identiques Linux avec une machine jumpbox et activer le chiffrement sur les groupes de machines virtuelles identiques Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-vmss-linux-jumpbox)

  - [Déployer un groupe de machines virtuelles identiques Windows avec une machine jumpbox et activer le chiffrement sur les groupes de machines virtuelles identiques Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-vmss-windows-jumpbox)

- [Désactiver le chiffrement de disque sur un groupe de machines virtuelles identiques Linux en cours d’exécution](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/decrypt-vmss-linux)

- [Désactiver le chiffrement de disque sur un groupe de machines virtuelles identiques Windows en cours d’exécution](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/decrypt-vmss-windows)

Exécutez ensuite les opérations qui suivent :

1. Cliquez sur **Déployer dans Azure**.
2. Renseignez les champs obligatoires, puis acceptez les conditions générales.
3. Sélectionnez **Acheter** pour déployer le modèle.

## <a name="next-steps"></a>Étapes suivantes

- [Azure Disk Encryption pour les groupes de machines virtuelles identiques](disk-encryption-overview.md)
- [Chiffrer un groupe de machines virtuelles identiques à l’aide d’Azure CLI](disk-encryption-cli.md)
- [Chiffrer un groupe de machines virtuelles identiques à l’aide d’Azure PowerShell](disk-encryption-powershell.md)
- [Créer et configurer un coffre de clés pour Azure Disk Encryption](disk-encryption-key-vault.md)
- [Utiliser Azure Disk Encryption avec le séquencement d’extensions de groupes de machines virtuelles identiques](disk-encryption-extension-sequencing.md)
