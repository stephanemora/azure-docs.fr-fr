---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: d7a9923d5bd9e357bcd75fae6e0a7d1bcd437a53
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66161191"
---
Un certificat SSL correct vérifie que vous envoyez des données chiffrées au serveur adéquat. Outre le chiffrement, le certificat permet également l’authentification. Vous pouvez charger votre propre certificat SSL approuvé par l’intermédiaire de l’interface PowerShell de l’appareil.

1. [Connectez-vous à l’interface PowerShell](#connect-to-the-powershell-interface).
2. Utilisez la cmdlet `Set-HcsCertificate` pour charger le certificat. À l’invite, fournissez les paramètres suivants :

   - `CertificateFilePath` : chemin d’accès au partage qui contient le fichier de certificat, au format *.pfx*.
   - `CertificatePassword` : mot de passe permettant de protéger le certificat.
   - `Credentials` : nom d’utilisateur et mot de passe grâce auxquels vous pouvez accéder au partage qui contient le certificat.

     L’exemple suivant montre comment utiliser cette cmdlet :

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username/Password"
     ```

