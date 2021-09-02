---
title: Créer des certificats pour Azure Stack Edge Pro GPU via Azure PowerShell | Microsoft Docs
description: Décrit comment créer des certificats pour un appareil Azure Stack Edge Pro GPU à l’aide de cmdlets Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/01/2021
ms.author: alkohli
ms.openlocfilehash: fc7ec5574e0f0223a66bc4e42ce9029db0ae4fc6
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113364035"
---
# <a name="use-certificates-with-azure-stack-edge-pro-gpu-device"></a>Utiliser des certificats avec des appareils Azure Stack Edge Pro avec GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article décrit la procédure de création de vos propres certificats à l’aide de cmdlets Azure PowerShell. L’article contient les instructions que vous devez suivre si vous envisagez de placer vos propres certificats sur un appareil Azure Stack Edge.

Les certificats garantissent que la communication entre votre appareil et les clients qui y accèdent est approuvée et que vous envoyez des informations chiffrées au serveur approprié. Lors de la configuration initiale de votre appareil Azure Stack Edge, les certificats auto-signés sont générés automatiquement. Si vous le souhaitez, vous pouvez apporter vos propres certificats. 

Vous pouvez utiliser l’une des méthodes suivantes pour créer vos propres certificats pour l’appareil :

 - Utilisez les cmdlets PowerShell pour Azure
 - Utilisez l’outil de vérification de la disponibilité d’Azure Stack Hub pour créer des requêtes de signature de certificat (CSR) qui aideront votre autorité de certification à émettre des certificats. 

Cet article traite uniquement de la création de vos propres certificats à l’aide des cmdlets Azure PowerShell. 

## <a name="prerequisites"></a>Configuration requise

Avant d’apporter vos propres certificats, assurez-vous que :

- Vous êtes familiarisé avec les [types de certificats qui peuvent être utilisés avec votre appareil Azure Stack Edge](azure-stack-edge-gpu-certificates-overview.md).
- Vous avez passé en revue les exigences relatives aux [certificats pour chaque type](azure-stack-edge-gpu-certificate-requirements.md).


## <a name="create-certificates"></a>Créer des certificats

La section suivante décrit la procédure de création de la chaîne de signature et des certificats de point de terminaison.


### <a name="certificate-workflow"></a>Workflow de certificat

Vous disposerez d’une méthode définie pour créer les certificats pour les appareils qui fonctionnent dans votre environnement. Vous pouvez utiliser les certificats qui vous sont fournis par votre administrateur informatique. 

**À des fins de développement ou de test uniquement, vous pouvez également utiliser Windows PowerShell pour créer des certificats sur votre système local.** Lors de la création des certificats pour le client, suivez ces instructions :

1. Vous pouvez créer un des types de certificats suivants :

    - Créez un certificat valide unique pour une utilisation avec un nom de domaine complet (FQDN) unique. Par exemple *mydomain.com*.
    - Créez un certificat générique pour sécuriser le nom de domaine principal ainsi que plusieurs sous-domaines. Par exemple * *.mydomain.com*.
    - Créez un certificat SAN (autre nom de l’objet) qui couvre plusieurs noms de domaine dans un seul certificat. 

2. Si vous apportez votre propre certificat, vous aurez besoin d’un certificat racine pour la chaîne de signature. Consultez les étapes pour [Créer des certificats de chaîne de signature](#create-signing-chain-certificate).

3. Vous pouvez ensuite créer les certificats de point de terminaison pour l’interface utilisateur locale de l’appliance, l’objet Blob et Azure Resource Manager. Vous pouvez créer 3 certificats distincts pour l’appliance, l’objet Blob et Azure Resource Manager, ou vous pouvez créer un seul certificat pour les 3 points de terminaison. Pour obtenir des instructions détaillées, consultez [Créer des certificats de signature et de point de terminaison](#create-signed-endpoint-certificates).

4. Que créiez 3 certificats séparés ou un seul certificat, spécifiez les noms d’objet (SN) et les autres noms de l’objet (SAN) conformément aux instructions fournies pour chaque type de certificat. 

### <a name="create-signing-chain-certificate"></a>Créer un certificat de chaîne de signature

Créez ces certificats via Windows PowerShell exécuté en mode administrateur. **Les certificats créés de cette manière doivent être utilisés à des fins de développement ou de test uniquement.**

Le certificat de chaîne de signature ne doit être créé qu’une seule fois. Les autres certificats de point de terminaison feront référence à ce certificat pour la signature.
 

```azurepowershell
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=RootCert" -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\LocalMachine\My" -KeyUsageProperty Sign -KeyUsage CertSign
```


### <a name="create-signed-endpoint-certificates"></a>Créer des certificats de point de terminaison signés

Créez ces certificats via Windows PowerShell exécuté en mode administrateur.

Dans ces exemples, les certificats des points de terminaison sont créés pour un appareil avec :
    - Nom de l’appareil : `DBE-HWDC1T2`
    - Domaine DNS : `microsoftdatabox.com`

Remplacez avec le nom et le domaine DNS de votre appareil pour créer des certificats pour votre appareil.
 
**Certificat de point de terminaison Blob**

Créez un certificat pour le point de terminaison d’objet Blob dans votre magasin personnel.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "*.blob.$AppName.$domain" -Subject "CN=*.blob.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Certificat de point de terminaison Azure Resource Manager**

Créez un certificat pour les points de terminaison Azure Resource Manager dans votre magasin personnel.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "management.$AppName.$domain","login.$AppName.$domain" -Subject "CN=management.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Certificat de l’interface utilisateur web locale de l’appareil**

Créez un certificat pour l’interface utilisateur web locale de l’appareil dans votre magasin personnel.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Certificat multi-SAN unique pour tous les points de terminaison**

Créez un seul certificat pour tous les points de terminaison de votre magasin personnel.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"
$DeviceSerial = "HWDC1T2"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain","$DeviceSerial.$domain","management.$AppName.$domain","login.$AppName.$domain","*.blob.$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

Une fois les certificats créés, l’étape suivante consiste à télécharger les certificats sur votre appareil Azure Stack Edge Pro GPU.

## <a name="next-steps"></a>Étapes suivantes

[Charger des certificats sur votre appareil](azure-stack-edge-gpu-manage-certificates.md).
