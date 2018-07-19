---
title: Azure Disk Encryption pour Linux | Microsoft Docs
description: Déploiement d’Azure Disk Encryption pour Linux sur une machine virtuelle à l’aide d’une extension de machine virtuelle.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: danis
ms.openlocfilehash: e7926fa488ad38784a3db96035055a80f841aed8
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449016"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption pour Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Vue d'ensemble

Azure Disk Encryption tire profit du sous-système dm-crypt de Linux pour fournir un chiffrement de disque complet sur [une sélection de distributions Linux Azure](https://aka.ms/adelinux).  Cette solution est intégrée à Azure Key Vault pour gérer les secrets et clés de chiffrement de disque.

## <a name="prerequisites"></a>Prérequis

Pour obtenir la liste complète des conditions requises, consultez la page des [prérequis pour Azure Disk Encryption](
../../security/azure-security-disk-encryption.md#prerequisites).

### <a name="operating-system"></a>Système d’exploitation

Azure Disk Encryption est actuellement pris en charge sur une sélection de distributions et de versions.  Consultez la [FAQ Azure Disk Encryption](../../security/azure-security-disk-encryption-faq.md#what-linux-distributions-does-azure-disk-encryption-support) pour connaître la liste des distributions Linux prises en charge.

### <a name="internet-connectivity"></a>Connectivité Internet

Azure Disk Encryption pour Linux doit être connecté à Internet accéder à Active Directory, à Key Vault, au stockage et aux points de terminaison de gestion des packages.  Pour plus d’informations, consultez la page des [prérequis pour Azure Disk Encryption](
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
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```

### <a name="property-values"></a>Valeurs de propriétés

| NOM | Valeur/Exemple | Type de données |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | chaîne |
| Type | AzureDiskEncryptionForLinux | chaîne |
| typeHandlerVersion | 0.1, 1.1 (VMSS) | int |
| AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| AADClientSecret | password | chaîne |
| AADClientCertificate | thumbprint | chaîne |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Dictionnaire JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | chaîne | 
| KeyEncryptionAlgorithm | « RSA-OAEP », « RSA-OAEP-256 », « RSA1_5 » | chaîne |
| KeyEncryptionKeyURL | url | chaîne |
| KeyVaultURL | url | chaîne |
| Phrase secrète | password | chaîne | 
| SequenceVersion | uniqueidentifier | chaîne |
| VolumeType | Système d’exploitation, données, tout | chaîne |

## <a name="template-deployment"></a>Déploiement de modèle

Pour obtenir un exemple de déploiement du modèle, consultez la page [Activer le chiffrement sur une VM Linux en cours d’exécution](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

## <a name="azure-cli-deployment"></a>Déploiement de l’interface de ligne de commande Azure

Vous trouverez des instructions dans la dernière version de la [documentation Azure CLI](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Dépannage et support technique

### <a name="troubleshoot"></a>Résolution des problèmes

Pour la résolution des problèmes, reportez-vous au [guide de dépannage Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/community/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les extensions de machine virtuelle, consultez la page [Extensions et fonctionnalités de machine virtuelle pour Linux](features-linux.md).