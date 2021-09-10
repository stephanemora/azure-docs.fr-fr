---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 19065a8a0bc828c30997edd05e9b268657e0fcb1
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221529"
---
Si vous n’avez pas encore de certificat, vous pouvez utiliser un certificat auto-signé. Un certificat auto-signé est un certificat de sécurité qui n’est pas signé par une autorité de certification et qui n’offre pas les garanties de sécurité d’un certificat signé par une autorité de certification. 

# <a name="windows"></a>[Windows](#tab/windows)

Sur Windows, utilisez l’applet de commande [New-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate) dans PowerShell pour générer un certificat.

1. Exécutez la commande PowerShell suivante pour générer un certificat auto-signé. Modifiez l’argument `-Subject` comme il convient pour votre application et le nom de locataire Azure AD B2C. Vous pouvez également ajuster la date de `-NotAfter` pour spécifier un délai d’expiration différent pour le certificat.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. Ouvrez **Gérer les certificats utilisateur** > **Utilisateur actuel** > **Personnel** > **Certificats** > *yourappname.yourtenant.onmicrosoft.com*.
1. Choisissez le certificat, puis sélectionnez **Action** > **Toutes les tâches** > **Exporter**.
1. Sélectionnez **Oui** > **Suivant** > **Oui, exporter la clé privée** > **Suivant**.
1. Acceptez les valeurs par défaut pour **Format de fichier d’exportation**.
1. Fournissez un mot de passe pour le certificat.

Pour qu’Azure AD B2C accepte le mot de passe du fichier .pfx, celui-ci doit être chiffré à l’aide de l’option TripleDES-SHA1 de l’utilitaire d’exportation du magasin de certificats Windows, par opposition à AES256-SHA256.

# <a name="macos"></a>[macOS](#tab/macos)

Sur macOS, utilisez l’[Assistant de certification](https://support.apple.com/guide/keychain-access/aside/glosa3ed0609/11.0/mac/11.0) dans Trousseaux d’accès pour générer un certificat.

1. Suivez les instructions pour [créer des certificats auto-signés d’Accès au Trousseau sur Mac](https://support.apple.com/guide/keychain-access/kyca8916/mac).
1. Dans l’application d’Accès au Trousseau de votre Mac, sélectionnez le certificat que vous avez créé.
1. Sélectionnez **Fichier** > **Exporter les éléments**.
1. Sélectionnez un nom de fichier pour enregistrer votre certificat. Par exemple, **certificat auto-signé.p12**.
1. Pour le **Format de fichier**, sélectionnez **Echange d’informations personnelles (.p12)** .
1. Sélectionnez **Enregistrer**.
1. Entrez un mot de passe dans les champs **Mot de passe**, et **Vérifier**.
1. Remplacez l’extension du fichier par pfx. Par exemple, **certificat auto-signé.pfx**.

---
