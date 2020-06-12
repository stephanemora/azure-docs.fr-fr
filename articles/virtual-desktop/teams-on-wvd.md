---
title: Microsoft Teams sur Windows Virtual Desktop - Azure
description: Comment utiliser Microsoft Teams sur Windows Virtual Desktop
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3a14ffc9f103e58681418eacbb35b72b704f2d61
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267135"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Utiliser Microsoft Teams sur Windows Virtual Desktop

>[!IMPORTANT]
>La fonction d’optimisation des médias de Microsoft Teams est actuellement en préversion publique. Nous vous recommandons d’évaluer l’expérience utilisateur optimisée de ce produit avant de le déployer pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.

>[!NOTE]
>La fonction d’optimisation des médias de Microsoft Teams est uniquement disponible pour le client Windows Desktop sur les machines Windows 10. Elle nécessite la version 1.2.1026.0 ou ultérieure du client Windows Desktop.

Microsoft Teams sur Windows Virtual Desktop prend en charge les discussions et la collaboration. Avec l’optimisation des médias, cette solution gère également les fonctionnalités d’appel et de réunion. Pour en savoir plus sur l’utilisation de Microsoft Teams dans les environnements VDI (Virtual Desktop Infrastructure), voir [Teams pour Virtual Desktop Infrastructure](/microsoftteams/teams-for-vdi/).

Avec l’optimisation des médias pour Microsoft Teams, le client Windows Desktop gère les données audio et vidéo en local pour les appels et réunions Teams. Vous pouvez toujours utiliser Microsoft Teams sur Windows Virtual Desktop avec d’autres clients sans l’optimisation des réunions et des appels. Les fonctionnalités de collaboration et de discussion de Microsoft Teams sont prises en charge sur toutes les plateformes. Pour rediriger les appareils locaux de votre session à distance, voir [Personnaliser les propriétés de RDP pour un pool d’hôtes](#customize-remote-desktop-protocol-properties-for-a-host-pool).

## <a name="prerequisites"></a>Prérequis

Avant d'utiliser Microsoft Teams sur Windows Virtual Desktop, vous devez effectuer les opérations suivantes :

- [Préparez votre réseau](/microsoftteams/prepare-network/) pour Microsoft Teams.
- Installez le [client Windows Desktop](connect-windows-7-and-10.md) sur un appareil Windows 10 répondant à la [configuration matérielle requise pour Microsoft Teams sur un PC Windows](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/).
- Connectez-vous à une machine virtuelle Windows 10 multisession ou Windows 10 Entreprise.
- Installez l’application de bureau Teams sur l’hôte, à l’aide de l’installation par machine. La fonction d’optimisation des médias de Microsoft Teams requiert l’application de bureau Teams version 1.3.00.4461 ou ultérieure.

## <a name="install-the-teams-desktop-app"></a>Installer l’application de bureau Teams

Cette section vous montre comment installer l’application de bureau Teams sur votre image de machine virtuelle Windows 10 Entreprise ou Windows 10 multisession. Pour plus d’informations, voir [Installation ou mise à jour de l’application Teams sur VDI](/microsoftteams/teams-for-vdi#install-or-update-the-teams-desktop-app-on-vdi/).

### <a name="prepare-your-image-for-teams"></a>Préparer votre image pour Teams

Pour permettre l’installation par machine de Teams, définissez la clé de registre suivante sur l’hôte :

1. Dans le menu Démarrer, exécutez **RegEdit** en tant qu’administrateur. Accédez à **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams**.
2. Créez la valeur suivante pour la clé Teams :

| Nom             | Type   | Données/Valeur  |
|------------------|--------|-------------|
| IsWVDEnvironment | DWORD  | 1           |

### <a name="install-the-teams-websocket-service"></a>Installer le service WebSocket de Teams

Installez le [service WebSocket](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4vkL6) sur votre image de machine virtuelle. Si vous rencontrez une erreur d’installation, installez [la dernière version de Microsoft Visual C++ Redistributable](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads), puis réessayez.

### <a name="install-microsoft-teams"></a>Installer Microsoft Teams

Vous pouvez déployer l’application de bureau Teams à l’aide d’une installation par machine. Pour installer Microsoft Teams dans votre environnement Windows Virtual Desktop :

1. Téléchargez le [package MSI Teams](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/) correspondant à votre environnement. Nous vous recommandons d’utiliser le programme d’installation 64 bits sur un système d’exploitation 64 bits.

      > [!NOTE]
      > La fonction d’optimisation des médias de Microsoft Teams requiert l’application de bureau Teams version 1.3.00.4461 ou ultérieure.

2. Exécutez cette commande pour installer le MSI sur la machine virtuelle hôte.

      ```console
      msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSER=1 ALLUSERS=1
      ```

      Cela permet d’installer Microsoft Teams dans le dossier Program Files (x86) sur un système d’exploitation 64 bits, et dans le dossier Program Files sur un système d’exploitation 32 bits. À ce stade, la configuration de l’image finale (gold) est terminée. L’installation de Teams sur chaque machine est nécessaire pour les configurations non persistantes.

      La prochaine fois que vous ouvrirez Teams dans une session, vous serez invité à entrer vos informations d’identification.

      > [!NOTE]
      > Les utilisateurs et les administrateurs ne peuvent pas désactiver le lancement automatique de Teams lors de la connexion.

      Pour désinstaller le MSI de la machine virtuelle hôte, exécutez la commande suivante :

      ```console
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      Cela supprime Teams du dossier Program Files (x86) ou Program Files, en fonction de l’environnement du système d’exploitation.

      > [!NOTE]
      > Si vous installez Microsoft Teams avec le paramètre MSI ALLUSER=1, les mises à jour automatiques seront désactivées. Nous vous recommandons de mettre à jour Teams au moins une fois par mois. Pour en savoir plus sur le déploiement de l’application de bureau Teams, consultez [Déployer l’application de bureau Teams sur la machine virtuelle](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/).

### <a name="verify-media-optimizations-loaded"></a>Vérifier que les optimisations de médias sont chargées

Après l’installation du service WebSocket et de l’application de bureau Teams, procédez comme suit pour vérifier que les optimisations de médias de Teams sont chargées :

1. Sélectionnez votre image de profil utilisateur, puis **À propos**.
2. Sélectionner **Version**.

      Si les optimisations de médias sont chargées, la bannière vous indique **l’optimisation des médias WVD effectuée**. Si la bannière vous indique que **le média WVD n’est pas connecté**, quittez l’application Teams et réessayez.

3. Sélectionnez votre image de profil utilisateur, puis choisissez **Paramètres**.

      Si les optimisations de médias sont chargées, les appareils audio et les caméras disponibles en local sont répertoriés dans le menu de l’appareil. Si le menu affiche **Sortie audio de l’ordinateur distant**, quittez l’application Teams, puis réessayez. Si les appareils n’apparaissent toujours pas dans le menu, revenez à la page [Installer Microsoft Teams](#install-microsoft-teams) et assurez-vous que vous avez terminé le processus d’installation.

## <a name="known-issues-and-limitations"></a>Problèmes connus et limitations

Selon que l’environnement est virtualisé ou non, l’utilisation de Microsoft Teams est différente. Pour plus d’informations sur les limitations de Teams dans les environnements virtualisés, voir [Teams pour Virtual Desktop Infrastructure](/microsoftteams/teams-for-vdi#known-issues-and-limitations/).

### <a name="client-deployment-installation-and-setup"></a>Déploiement, installation et configuration du client

- Avec l’installation par machine, l’instance Teams sur l’infrastructure VDI n’est pas automatiquement mise à jour de la même façon que les clients Teams sur un autre type d’infrastructure. Pour mettre à jour le client, vous devez actualiser l’image de machine virtuelle en exécutant un nouveau fichier MSI.
- Actuellement, Teams affiche uniquement le fuseau horaire UTC dans le chat, les canaux et le calendrier.
- La fonction d’optimisation des médias de Microsoft Teams est uniquement prise en charge pour le client Windows Desktop sur les ordinateurs exécutant Windows 10.
- L’utilisation de proxys HTTP explicites définis sur un point de terminaison n’est pas prise en charge.

### <a name="calls-and-meetings"></a>Appels et réunions

- Le client de bureau Teams dans les environnements Windows Virtual Desktop ne gère pas les événements en direct. Pour l’instant, nous vous recommandons de rejoindre des événements en direct à partir du [client Web Teams](https://teams.microsoft.com) dans votre session à distance.
- Si vous réduisez l’affichage de l’application Teams lors d’un appel ou d’une réunion, vous risquez de faire disparaître le flux vidéo entrant lorsque vous redéveloppez l’application.
- Actuellement, les appels et les réunions ne prennent pas en charge le partage d’applications. Par contre, les sessions de bureau prennent en charge le partage de bureau.
- Lors du partage de bureau dans une configuration à plusieurs écrans, tous les écrans sont partagés.
- L’octroi et la prise de contrôle ne sont actuellement pas pris en charge.
- L’instance Teams sur Windows Virtual Desktop ne prend en charge qu’une seule entrée vidéo à la fois. De ce fait, chaque fois qu’un utilisateur tente de partager son écran, ce dernier s’affiche à la place de l’écran de l’organisateur de la réunion.
- En raison des limitations de WebRTC, la résolution du flux vidéo entrant et sortant est limitée à 720p.
- L’application Teams ne prend pas en charge les boutons HID ou les contrôles LED d’autres appareils.

Pour en savoir plus sur les problèmes connus de Teams qui ne sont pas liés à des environnements virtualisés, voir [Support pour Microsoft Teams au sein de votre organisation](/microsoftteams/known-issues/).

## <a name="feedback"></a>Commentaires

Envoyez des commentaires concernant Microsoft Teams sur Windows Virtual Desktop via le [site UserVoice](https://microsoftteams.uservoice.com/) de Teams.

## <a name="collect-teams-logs"></a>Collecter les journaux Teams

Si l’application de bureau Teams génère des problèmes dans l’environnement Windows Virtual Desktop, collectez les journaux des clients sur la machine virtuelle hôte, ici : **%appdata%\Microsoft\Teams\logs.txt**.

En cas de problèmes liés aux appels et aux réunions, collectez les journaux des clients Web de Microsoft Teams en appuyant sur **Ctrl** + **Alt** + **Maj** + **1**. Les journaux sont écrits sur la machine virtuelle de l’hôte, à l’emplacement suivant : **%userprofile%\Downloads\MSTeams Diagnostics Log DATE_HEURE.txt**.

## <a name="contact-microsoft-teams-support"></a>Contacter le support Microsoft Teams

Pour contacter le support Microsoft Teams, accédez au [Centre d’administration Microsoft 365](https://docs.microsoft.com/microsoft-365/admin/contact-support-for-business-products?view=o365-worldwide&tabs=online).

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personnaliser les propriétés de RDP pour un pool d’hôtes

La personnalisation des propriétés du protocole RDP (Remote Desktop Protocol) d’un pool d’hôtes, comme l’utilisation de plusieurs écrans ou l’activation de la redirection audio et du microphone, vous permet d’offrir une expérience optimale pour vos utilisateurs en fonction de leurs besoins.

Vous n’avez pas besoin d’activer la redirection des appareils lorsque vous utilisez Teams avec l’optimisation des médias. Si vous utilisez Teams sans aucune optimisation des médias, définissez les propriétés RDP suivantes de manière à activer la redirection du microphone et de la caméra :

- `audiocapturemode:i:1` active la capture audio à partir de l’appareil local et redirige les applications audio dans la session à distance.
- `audiomode:i:0` lit les données audio sur l’ordinateur local.
- `camerastoredirect:s:*` redirige toutes les caméras.

Pour en savoir plus, voir [Personnaliser les propriétés de RDP pour un pool d’hôtes](customize-rdp-properties.md).
