---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 216380cf7069468d13c4e533fc90b2596aa211c4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58114332"
---
Un certificat SSL approprié permet de s’assurer que vous envoyez des informations chiffrées au serveur adéquat. Outre le chiffrement, le certificat permet également pour l’authentification. Vous pouvez télécharger votre propre certificat SSL approuvé par le biais de l’interface PowerShell de l’appareil.

1. [Se connecter à l’interface PowerShell](#connect-to-the-powershell-interface).
2. Utilisez le `Set-HcsCertificate` applet de commande pour charger le certificat. Lorsque vous y êtes invité, fournissez les paramètres suivants :

   - `CertificateFilePath` -Chemin d’accès au partage qui contient le fichier de certificat dans *.pfx* format.
   - `CertificatePassword` -Mot de passe utilisé pour protéger le certificat.
   - `Credentials` -Nom d’utilisateur et mot de passe pour accéder au partage qui contient le certificat.

     L’exemple suivant illustre l’utilisation de cette applet de commande :

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credentials "Username/Password"
     ```

