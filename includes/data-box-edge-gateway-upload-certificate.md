---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: d7a9923d5bd9e357bcd75fae6e0a7d1bcd437a53
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64732654"
---
Un certificat SSL approprié permet de s’assurer que vous envoyez des informations chiffrées au serveur adéquat. Outre le chiffrement, le certificat permet également pour l’authentification. Vous pouvez télécharger votre propre certificat SSL approuvé par le biais de l’interface PowerShell de l’appareil.

1. [Se connecter à l’interface PowerShell](#connect-to-the-powershell-interface).
2. Utilisez le `Set-HcsCertificate` applet de commande pour charger le certificat. Lorsque vous y êtes invité, fournissez les paramètres suivants :

   - `CertificateFilePath` -Chemin d’accès au partage qui contient le fichier de certificat dans *.pfx* format.
   - `CertificatePassword` -Mot de passe utilisé pour protéger le certificat.
   - `Credentials` -Nom d’utilisateur et mot de passe pour accéder au partage qui contient le certificat.

     L’exemple suivant illustre l’utilisation de cette applet de commande :

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username/Password"
     ```

