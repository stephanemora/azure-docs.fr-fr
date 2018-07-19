---
title: Azure Disk Encryption pour Windows | Microsoft Docs
description: Déploiement d’Azure Disk Encryption sur une machine virtuelle Windows à l’aide d’une extension de machine virtuelle.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: danis
ms.openlocfilehash: f9b8c64aadf813ab1157820fc4051d48989e48c4
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448829"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Disk Encryption pour Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Vue d'ensemble

Azure Disk Encryption s’appuie sur Bitlocker pour fournir un chiffrement de disque complet des machines virtuelles Azure exécutant Windows.  La solution est intégrée à Azure Key Vault, ce qui vous permet de gérer les clés de chiffrement de disque et les secrets dans votre abonnement Key Vault. 

## <a name="prerequisites"></a>Prérequis

Pour obtenir la liste complète des conditions requises, consultez la page de [Prérequis pour Azure Disk Encryption](
../../security/azure-security-disk-encryption.md#prerequisites).

### <a name="operating-system"></a>Système d’exploitation

Pour obtenir la liste des versions de Windows actuellement prises en charge, consultez [Prérequis pour Azure Disk Encryption](../../security/azure-security-disk-encryption.md#prerequisites).

### <a name="internet-connectivity"></a>Connectivité Internet

Azure Disk Encryption doit être connecté à Internet pour accéder à Active Directory, à Key Vault, au stockage et aux points de terminaison de gestion des packages.  Pour plus d’informations sur les paramètres de sécurité réseau, consultez [Prérequis pour Azure Disk Encryption](
../../security/azure-security-disk-encryption.md#prerequisites).

## <a name="extension-schema"></a>Schéma d’extensions

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```

### <a name="property-values"></a>Valeurs de propriétés

| NOM | Valeur/Exemple | Type de données |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | chaîne |
| Type | AzureDiskEncryptionForWindows| chaîne |
| typeHandlerVersion | 1.0, 2.2 (VMSS) | int |
| (facultatif) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| (facultatif) AADClientSecret | password | chaîne |
| (facultatif) AADClientCertificate | thumbprint | chaîne |
| EncryptionOperation | EnableEncryption | chaîne | 
| KeyEncryptionAlgorithm | RSA-OAEP | chaîne |
| KeyEncryptionKeyURL | url | chaîne |
| KeyVaultURL | url | chaîne |
| SequenceVersion | uniqueidentifier | chaîne |
| VolumeType | Système d’exploitation, données, tout | chaîne |

## <a name="template-deployment"></a>Déploiement de modèle
Pour obtenir un exemple de déploiement de modèle, consultez [ Create a new encrypted Windows VM from gallery image](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image) (créer une machine virtuelle Windows chiffrée à partir de l’image de la galerie).

## <a name="azure-cli-deployment"></a>Déploiement de l’interface de ligne de commande Azure

Vous trouverez des instructions dans la dernière version de la [documentation Azure CLI](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Dépannage et support technique

### <a name="troubleshoot"></a>Résolution des problèmes

Consultez le [Guide de dépannage Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/community/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les extensions de machine virtuelle, consultez [Extensions et fonctionnalités de machine virtuelle pour Windows](features-windows.md).