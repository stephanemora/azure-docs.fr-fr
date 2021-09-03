---
title: Se connecter à Azure Virtual Desktop - Windows 10 ou 7 - Azure
description: Guide pratique pour se connecter à Azure Virtual Desktop à l’aide du client Windows Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 07/20/2021
ms.author: helohr
manager: femila
ms.custom: template-how-to
ms.openlocfilehash: 644572ccfcc8ee0218e472757073588f2b47d4c5
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114452676"
---
# <a name="connect-with-the-windows-desktop-client"></a>Se connecter avec le client Windows Desktop

Vous pouvez accéder aux ressources Azure Virtual Desktop sur des appareils avec Windows 10, Windows 10 IoT Entreprise et Windows 7 à l’aide du client Bureau Windows. 

> [!IMPORTANT]
> Cette méthode ne prend pas en charge Windows 8 ni Windows 8.1.
> 
> Cette méthode ne prend en charge que les objets Azure Resource Manager. Pour prendre en charge les objets sans Azure Resource Manager, consultez [Se connecter avec le client Windows Desktop (classique)](../virtual-desktop-fall-2019/connect-windows-7-10-2019.md).
> 
> Cette méthode ne prend pas en charge le client RemoteApp et Connexions Bureau (RADC) ou le client Connexion Bureau à distance (MSTSC).

## <a name="install-the-windows-desktop-client"></a>Installer le client Windows Desktop

Téléchargez le client en fonction de votre version de Windows :

- [Windows 64 bits](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32 bits](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

Lors l’installation pour déterminer l’accès, sélectionnez l’une options suivantes :

- **Installer uniquement pour vous**
- **Installer pour tous les utilisateurs de cette machine** (requiert des droits d’administrateur)

Pour lancer le client après installation, utilisez le menu **Démarrer** et recherchez **Bureau à distance**.

## <a name="subscribe-to-a-workspace"></a>S’abonner à un espace de travail

Pour vous abonner à un espace de travail, choisissez l’une options suivantes :

- Utiliser un compte professionnel ou scolaire et demander au client de découvrir les ressources disponibles
- Utiliser l’URL spécifique de la ressource

Pour lancer la ressource après abonnement, accédez au **Centre de connexion** et double-cliquez sur la ressource.

> [!TIP]
> Pour lancer une ressource à partir du menu **Démarrer**, vous pouvez rechercher le dossier avec le nom de l’espace de travail ou entrer le nom de la ressource dans la barre de recherche.

### <a name="use-a-user-account"></a>Utiliser un compte d'utilisateur

1. Sélectionnez **S’abonner** dans la page principale.
2. Connectez-vous avec votre compte d’utilisateur quand vous y êtes invité.

Les ressources groupées par espace de travail s’affichent dans le **Centre de connexion**.

   > [!NOTE]
   > Le client Windows passe automatiquement par défaut à Azure Virtual Desktop (classique). 
   > 
   > Toutefois, si le client détecte des ressources Azure Resource Manager supplémentaires, il les ajoute automatiquement ou avertit l’utilisateur qu’elles sont disponibles.

### <a name="use-a-specific-url"></a>Utiliser une URL spécifique

1. Sélectionnez **S’abonner avec une URL** dans la page principale.
2. Entrez l'*URL de l’espace de travail* ou une *adresse e-mail* :
   - Pour l’**URL de l’espace de travail**, utilisez l’URL fournie par votre administrateur.

   |Ressources disponibles|URL|
   |-|-|
   |Azure Virtual Desktop (classique)|`https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`|
   |Azure Virtual Desktop|`https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`|
   |Azure Virtual Desktop (US Gov)|`https://rdweb.wvd.azure.us/api/arm/feeddiscovery`|
   |Azure Virtual Desktop (Chine)|`https://rdweb.wvd.azure.cn/api/arm/feeddiscovery`|
   
   - Pour **Adresse e-mail**, utilisez votre adresse e-mail. 
      
   Le client trouvera l’URL associée à votre adresse e-mail sous réserve que votre administrateur ait activé la [détection des e-mails](/windows-server/remote/remote-desktop-services/rds-email-discovery).

3. Sélectionnez **Suivant**.
4. Connectez-vous avec votre compte d’utilisateur quand vous y êtes invité.

Les ressources groupées par espace de travail s’affichent dans le **Centre de connexion**.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation du client, consultez [Bien démarrer avec le client Windows Desktop](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).

En tant qu’administrateur, si vous souhaitez en savoir plus sur les fonctionnalités du client, consultez [Client Windows Desktop pour administrateurs](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-admin).
