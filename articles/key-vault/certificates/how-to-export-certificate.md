---
title: Exporter un certificat à partir d’Azure Key Vault
description: Exporter un certificat à partir d’Azure Key Vault
services: key-vault
author: sebansal
tags: azure-key-vault
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.custom: mvc, devx-track-azurecli
ms.date: 08/11/2020
ms.author: sebansal
ms.openlocfilehash: afab65b22d9487f30da458346bf143a557bec0d8
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588890"
---
# <a name="export-certificate-from-azure-key-vault"></a>Exporter un certificat à partir d’Azure Key Vault

Azure Key Vault vous permet de provisionner, gérer et déployer facilement des certificats numériques pour votre réseau et de sécuriser les communications pour les applications. Pour plus d’informations générales sur les certificats, consultez [Certificats Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates).

## <a name="about-azure-key-vault-certificate"></a>À propos du certificat Azure Key Vault

### <a name="composition-of-certificate"></a>Composition d’un certificat
Lorsqu’un certificat Key Vault est créé, une clé et un secret adressables sont également créés avec le même nom. La clé Key Vault permet d’exécuter des opérations sur les clés et le secret Key Vault permet de récupérer la valeur du certificat en tant que secret. Un certificat Key Vault contient également des métadonnées de certificat x509 publiques. [En savoir plus](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate)

### <a name="exportable-or-non-exportable-keys"></a>Clés exportables ou non exportables
Lorsqu’un certificat Key Vault est créé, il peut être récupéré dans le secret adressable avec la clé privée au format PFX ou PEM. La stratégie utilisée pour créer le certificat doit indiquer que la clé est exportable. Si la stratégie indique qu’elle n’est pas exportable, la clé privée ne fait pas partie de la valeur quand elle est récupérée en tant que secret.

Deux types de clés sont pris en charge, RSA ou RSA HSM, avec les certificats. Exportable est autorisé avec RSA uniquement, il n’est pas pris en charge par RSA HSM. [En savoir plus](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#exportable-or-non-exportable-key)

Dans Azure Key Vault, il est possible d’exporter un certificat stocké à l’aide d’Azure CLI, de PowerShell ou du portail.

> [!NOTE]
> Il est important de noter que vous avez besoin du mot de passe d’un certificat uniquement quand vous l’importez dans le coffre de clés. Key Vault n’enregistre pas le mot de passe associé. Par conséquent, quand vous exportez le certificat, le mot de passe est vide.

## <a name="exporting-certificate-using-cli"></a>Exportation d’un certificat à l’aide de CLI
La commande suivante vous permet de télécharger la **partie publique** d’un certificat Key Vault.

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```
Des exemples et des définitions de paramètres sont disponibles [ici](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-download)



Si vous voulez télécharger l’intégralité du certificat, c’est-à-dire la **partie publique et la privée de sa composition**, vous pouvez télécharger le certificat en tant que secret.

```azurecli
az keyvault secret download –file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```
Des définitions de paramètres sont disponibles [ici](https://docs.microsoft.com/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-download)


## <a name="exporting-certificate-using-powershell"></a>Exporter un certificat à l’aide de PowerShell

Cette commande obtient le certificat nommé TestCert01 se trouvant dans le coffre de clés nommé ContosoKV01. Pour télécharger le certificat sous forme de fichier PFX, exécutez la commande suivante. Ces commandes accèdent à l’ID de secret (SecretId), puis enregistrent le contenu sous la forme d’un fichier PFX.

```azurepowershell
$cert = Get-AzKeyVaultCertificate -VaultName "ContosoKV01" -Name "TestCert01"
$kvSecret = Get-AzKeyVaultSecret -VaultName "ContosoKV01" -Name $Cert.Name
$kvSecretBytes = [System.Convert]::FromBase64String($kvSecret.SecretValueText)
$certCollection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection
$certCollection.Import($kvSecretBytes,$null,[System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable)
$password = '******'
$protectedCertificateBytes = $certCollection.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $password)
$pfxPath = [Environment]::GetFolderPath("Desktop") + "\MyCert.pfx"
[System.IO.File]::WriteAllBytes($pfxPath, $protectedCertificateBytes)
```
Cette opération exporte la totalité de la chaîne de certificats avec la clé privée et ce certificat sera protégé par mot de passe.
Pour plus d’informations sur la commande et les paramètres ```Get-AzKeyVaultCertificate```, reportez-vous à l’[exemple 2](https://docs.microsoft.com/powershell/module/az.keyvault/Get-AzKeyVaultCertificate?view=azps-4.4.0)

## <a name="exporting-certificate-using-portal"></a>Exportation d’un certificat à l’aide du portail

Sur le portail, quand vous créez/importez un certificat dans le panneau des certificats, vous recevez la notification indiquant que le certificat a été correctement créé. Quand vous sélectionnez le certificat, vous pouvez cliquer sur la version actuelle pour que l’option de téléchargement s’affiche.


En cliquant sur le bouton « Télécharger au format CER » ou « Télécharger au format PFX/PEM », vous pouvez télécharger le certificat.


![Téléchargement du certificat](../media/certificates/quick-create-portal/current-version-shown.png)


## <a name="exporting-app-service-certificate-from-key-vault"></a>Exportation d’un certificat App Service se trouvant dans un coffre de clés

Les certificats Azure App Service offrent un moyen pratique d’acheter des certificats SSL et de les attribuer à des applications Azure directement à partir du portail. Ces certificats peuvent également être exportés à partir du portail sous la forme de fichiers PFX à utiliser ailleurs.
Une fois importés, les certificats App Service peuvent **se trouver sous des secrets**.

Des instructions concernant l’exportation de certificats App Service sont disponibles [ici](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)

## <a name="read-more"></a>En savoir plus
* [Différents types et définitions de fichiers de certificats](https://docs.microsoft.com/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)