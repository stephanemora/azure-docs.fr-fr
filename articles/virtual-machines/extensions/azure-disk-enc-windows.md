---
title: Azure Disk Encryption pour Windows | Microsoft Docs
description: Déploiement d’Azure Disk Encryption sur une machine virtuelle Windows à l’aide d’une extension de machine virtuelle.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 00891122015bb3e6adb500b6f6c30fa031161b92
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597999"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Disk Encryption pour Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Vue d'ensemble

Azure Disk Encryption s’appuie sur BitLocker pour fournir un chiffrement de disque complet des machines virtuelles Azure exécutant Windows.  La solution est intégrée à Azure Key Vault, ce qui vous permet de gérer les clés de chiffrement de disque et les secrets dans votre abonnement Key Vault. 

## <a name="prerequisites"></a>Prérequis

Pour obtenir la liste complète des conditions préalables requises, consultez [Azure Disk Encryption pour les machines virtuelles Linux](../linux/disk-encryption-overview.md), en particulier les sections suivantes :

- [Azure Disk Encryption pour les machines virtuelles Linux](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Exigences réseau](../windows/disk-encryption-overview.md#networking-requirements)
- [Exigences de stratégies de groupe](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schemata"></a>Schémas d’extension

Il existe deux schémas pour Azure Disk Encryption : v1.1, un schéma plus récent et recommandé qui n’utilise pas de propriétés d’Azure Active Directory (AAD), et v0.1, un schéma plus ancien qui exige des propriétés d’AAD. Vous devez utiliser la version de schéma correspondant à l’extension que vous utilisez : schéma v1.1 pour la version 1.1 de l’extension AzureDiskEncryption, schéma v0.1 pour la version 0.1 de l’extension AzureDiskEncryption.

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
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
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
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
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
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
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
| KeyVaultURL | url | string |
| (facultatif) Passphrase | password | string | 
| SequenceVersion | uniqueidentifier | string |
| VolumeType | Système d’exploitation, données, tout | string |

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
Pour plus d’informations sur les extensions de machine virtuelle, consultez la page [Extensions et fonctionnalités de machine virtuelle pour Windows](features-windows.md).
