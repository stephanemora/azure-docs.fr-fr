---
title: Créer un certificat public auto-signé pour authentifier votre application | Azure
titleSuffix: Microsoft identity platform
description: Créez un certificat public auto-signé pour authentifier votre application.
services: active-directory
author: FaithOmbongi
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/10/2021
ms.author: ombongifaith
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: scenarios:getting-started
ms.openlocfilehash: 3cc084fe1b9df8a4ab4db5b926bb7b44646f17c2
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123030322"
---
# <a name="create-a-self-signed-public-certificate-to-authenticate-your-application"></a>Créer un certificat public auto-signé pour authentifier votre application

Azure Active Directory (Azure AD) prend en charge deux types d’authentification pour les principaux de service : l’**authentification par mot de passe** (secret d’application) et l’**authentification par certificat**. Bien que les secrets d’application puissent facilement être créés dans le portail Azure, il est recommandé que votre application utilise un certificat.

À des fins de test, vous pouvez utiliser un certificat public auto-signé au lieu d’un certificat signé par une autorité de certification. Cet article explique comment utiliser Windows PowerShell pour créer et exporter un certificat auto-signé.

> [!CAUTION]
> L’utilisation d’un certificat auto-signé est uniquement recommandée pour le développement et non pour la production.

Vous configurez différents paramètres pour le certificat. Par exemple, les algorithmes de hachage et de chiffrement, la période de validité du certificat et votre nom de domaine. Exportez ensuite le certificat avec ou sans sa clé privée en fonction des besoins de votre application. 

L’application qui initie la session d’authentification a besoin de la clé privée tandis que l’application qui confirme l’authentification a besoin de la clé publique. Par conséquent, si vous vous authentifiez auprès d’Azure AD à partir de votre application de bureau PowerShell, vous exportez uniquement la clé publique (fichier `.cer`) et la chargez sur le portail Azure. Votre application PowerShell utilise la clé privée de votre magasin de certificats local pour initier l’authentification et obtenir des jetons d’accès pour Microsoft Graph.

Votre application peut également être exécutée à partir d’un autre ordinateur, par exemple Azure Automation. Dans ce scénario, vous exportez la paire de clés publique et privée de votre magasin de certificats local et chargez la clé publique sur le portail Azure et la clé privée (un fichier `.pfx`) sur Azure Automation. Votre application exécutée dans Azure Automation utilisera la clé privée pour initier l’authentification et obtenir des jetons d’accès pour Microsoft Graph.

Cet article utilise la cmdlet PowerShell `New-SelfSignedCertificate` pour créer le certificat auto-signé et la cmdlet `Export-Certificate` pour l’exporter vers un emplacement facilement accessible. Ces cmdlets sont intégrées aux versions modernes de Windows (Windows 8.1 et versions ultérieures et Windows Server 2012R2 et versions ultérieures). Le certificat auto-signé aura la configuration suivante :

+ Une longueur de clé de 2048 bits. Bien que des valeurs plus longues soient prises en charge, la taille de 2048 bits est vivement recommandée pour une meilleure combinaison de sécurité et de performances.
+ Utilise l’algorithme de chiffrement RSA. Azure AD ne prend actuellement en charge que RSA.
+ Le certificat est signé avec l’algorithme de hachage SHA256. Azure AD prend également en charge les certificats signés avec les algorithmes de hachage SHA384 et SHA512.
+ Le certificat n’est valide que pendant un an.
+ Le certificat peut être utilisé pour l’authentification du client et du serveur.

> [!NOTE]
> Pour personnaliser la date de début et d’expiration, ainsi que d’autres propriétés du certificat, consultez la [référence `New-SelfSignedCertificate`](/powershell/module/pki/new-selfsignedcertificate?view=windowsserver2019-ps&preserve-view=true).


## <a name="option-1--create-and-export-your-public-certificate-without-a-private-key"></a>Option 1 : Créer et exporter votre certificat public sans clé privée

Utilisez le certificat que vous créez à l’aide de cette méthode pour vous authentifier à partir d’une application exécutée sur votre machine. Par exemple, authentifiez-vous à partir de Windows PowerShell.

Dans une invite PowerShell avec élévation de privilèges, exécutez la commande suivante et laissez la session de console PowerShell ouverte. Remplacez `{certificateName}` par le nom que vous souhaitez donner à votre certificat.

```powershell

$cert = New-SelfSignedCertificate -Subject "CN={certificateName}" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256    ## Replace {certificateName}

```

La variable **$cert** de la commande précédente stocke votre certificat dans la session active et vous permet de l’exporter. La commande ci-dessous exporte le certificat au format `.cer`. Vous pouvez également l’exporter dans d’autres formats pris en charge sur le portail Azure, notamment `.pem` et `.crt`.

```powershell

Export-Certificate -Cert $cert -FilePath "C:\Users\admin\Desktop\{certificateName}.cer"   ## Specify your preferred location and replace {certificateName}

```

Votre certificat est maintenant prêt à être chargé sur le portail Azure. Une fois qu’il est chargé, récupérez l’empreinte du certificat afin de l’utiliser pour authentifier votre application.


## <a name="option-2-create-and-export-your-public-certificate-with-its-private-key"></a>Option 2 : Créer et exporter votre certificat public avec sa clé privée

Utilisez cette option pour créer un certificat et sa clé privée si votre application doit être exécutée à partir d’une autre machine ou d’un autre cloud, comme Azure Automation.

Dans une invite PowerShell avec élévation de privilèges, exécutez la commande suivante et laissez la session de console PowerShell ouverte. Remplacez `{certificateName}` par le nom que vous souhaitez donner à votre certificat.

```powershell

$cert = New-SelfSignedCertificate -Subject "CN={certificateName}" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256    ## Replace {certificateName}

```

La variable **$cert** de la commande précédente stocke votre certificat dans la session active et vous permet de l’exporter. La commande ci-dessous exporte le certificat au format `.cer`. Vous pouvez également l’exporter dans d’autres formats pris en charge sur le portail Azure, notamment `.pem` et `.crt`.


```powershell

Export-Certificate -Cert $cert -FilePath "C:\Users\admin\Desktop\{certificateName}.cer"   ## Specify your preferred location and replace {certificateName}

```

Toujours dans la même session, créez un mot de passe pour la clé privée de votre certificat et enregistrez-la dans une variable. Dans la commande suivante, remplacez `{myPassword}` par le mot de passe que vous souhaitez utiliser pour protéger la clé privée de votre certificat.

```powershell

$mypwd = ConvertTo-SecureString -String "{myPassword}" -Force -AsPlainText  ## Replace {myPassword}

```

À présent, en utilisant le mot de passe que vous avez stocké dans la variable `$mypwd`, sécurisez et exportez votre clé privée.

```powershell

Export-PfxCertificate -Cert $cert -FilePath "C:\Users\admin\Desktop\{privateKeyName}.pfx" -Password $mypwd   ## Specify your preferred location and replace {privateKeyName}

```

Votre certificat (fichier `.cer`) est maintenant prêt à être chargé sur le portail Azure. Vous disposez également d’une clé privée (fichier `.pfx`) qui est chiffrée et ne peut pas être lue par d’autres parties. Une fois qu’il est chargé, récupérez l’empreinte du certificat afin de l’utiliser pour authentifier votre application.


## <a name="optional-task-delete-the-certificate-from-the-keystore"></a>Tâche facultative : Supprimer le certificat du magasin de clés

Si vous avez créé le certificat à l’aide de l’option 2, vous pouvez supprimer la paire de clés de votre magasin personnel. Tout d’abord, exécutez la commande suivante pour récupérer l’empreinte du certificat.

```powershell

Get-ChildItem -Path "Cert:\CurrentUser\My" | Where-Object {$_.Subject -Match "{certificateName}"} | Select-Object Thumbprint, FriendlyName    ## Replace {privateKeyName} with the name you gave your certificate

```

Ensuite, copiez l’empreinte qui s’affiche et utilisez-la pour supprimer le certificat et sa clé privée.

```powershell

Remove-Item -Path Cert:\CurrentUser\My\{pasteTheCertificateThumbprintHere} -DeleteKey

```

### <a name="know-your-certificate-expiry-date"></a>Connaître la date d’expiration de votre certificat

Le certificat auto-signé que vous avez créé en suivant les étapes ci-dessus a une durée de vie limitée avant d’expirer. Dans la section **Inscription d’applications** du portail Azure, l’écran **Certificats et secrets** affiche la date d’expiration du certificat. Si vous utilisez Azure Automation, l’écran **Certificats** du compte Automation affiche la date d’expiration du certificat. Suivez les étapes précédentes pour créer un nouveau certificat auto-signé.

## <a name="next-steps"></a>Étapes suivantes

[Gérer des certificats pour l’authentification unique fédérée sur Azure Active Directory](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
