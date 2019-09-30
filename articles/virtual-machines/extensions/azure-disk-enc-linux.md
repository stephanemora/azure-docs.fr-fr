---
title: Azure Disk Encryption pour Linux | Microsoft Docs
description: Déploiement d’Azure Disk Encryption pour Linux sur une machine virtuelle à l’aide d’une extension de machine virtuelle.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: ejarvi
ms.openlocfilehash: 6a81f105f9632a7ca7e2bf7188e358274020c78f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084769"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption pour Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Vue d'ensemble

Azure Disk Encryption tire profit du sous-système dm-crypt de Linux pour fournir un chiffrement de disque complet sur [une sélection de distributions Linux Azure](https://aka.ms/adelinux).  Cette solution est intégrée à Azure Key Vault pour gérer les secrets et clés de chiffrement de disque.

## <a name="prerequisites"></a>Prérequis

Pour obtenir la liste complète des conditions requises, consultez la page de [Prérequis pour Azure Disk Encryption](
../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="operating-system"></a>Système d’exploitation

Azure Disk Encryption est actuellement pris en charge sur une sélection de distributions et de versions.  Consultez les [systèmes d’exploitation pris en charge d’Azure Disk Encryption : Linux](../../security/azure-security-disk-encryption-prerequisites.md#linux) pour obtenir la liste des distributions Linux prises en charge.

### <a name="internet-connectivity"></a>Connectivité Internet

Azure Disk Encryption pour Linux doit être connecté à Internet accéder à Active Directory, à Key Vault, au stockage et aux points de terminaison de gestion des packages.  Pour plus d’informations, consultez la page des [prérequis pour Azure Disk Encryption](../../security/azure-security-disk-encryption-prerequisites.md).

## <a name="extension-schemata"></a>Schéma d’extension

Il existe deux schémas pour Azure Disk Encryption : v1.1, un schéma plus récent et recommandé qui n’utilise pas de propriétés d’Azure Active Directory (AAD), et v0.1, un schéma plus ancien qui exige des propriétés d’AAD. Vous devez utiliser la version de schéma correspondant à l’extension que vous utilisez : schéma v1.1 pour la version 1.1 de l’extension AzureDiskEncryptionForLinux, schéma v0.1 pour la version 0.1 de l’extension AzureDiskEncryptionForLinux.
### <a name="schema-v11-no-aad-recommended"></a>Schéma v1.1 : sans AAD (recommandé)

Le schéma v1.1 est recommandé et n’exige pas de propriétés d’Azure Active Directory.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "settings": {
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


### <a name="schema-v01-with-aad"></a>Schéma v0.1 : avec AAD 

Le schéma 0.1 exige `aadClientID` et `aadClientSecret` ou `AADClientCertificate`.

Utilisation de `aadClientSecret`:

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

Utilisation de `AADClientCertificate`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
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

| Nom | Valeur/Exemple | Type de données |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | string |
| type | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 0.1, 1.1 | int |
| (schéma 0.1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| (schéma 0.1) AADClientSecret | password | string |
| (schéma 0.1) AADClientCertificate | thumbprint | string |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Dictionnaire JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| KeyEncryptionAlgorithm | « RSA-OAEP », « RSA-OAEP-256 », « RSA1_5 » | string |
| KeyEncryptionKeyURL | url | string |
| (facultatif) KeyVaultURL | url | string |
| Passphrase | password | string | 
| SequenceVersion | uniqueidentifier | string |
| VolumeType | Système d’exploitation, données, tout | string |

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