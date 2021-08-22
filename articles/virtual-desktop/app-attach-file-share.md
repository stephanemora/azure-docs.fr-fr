---
title: Configuration d’un partage de fichiers pour l’attachement d’application MSIX pour Azure Virtual Desktop – Azure
description: Procédure de configuration d’un partage de fichiers pour l’attachement d’application MSIX pour Azure Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: e0ba3363b6004afb8e42f420fae681b2827106fb
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114707086"
---
# <a name="set-up-a-file-share-for-msix-app-attach"></a>Configurer un partage de fichiers pour l’attachement d’application MSIX

Toutes les images MSIX doivent être stockées sur un partage réseau accessible par les utilisateurs d’un pool hôte avec des autorisations en lecture seule.

L’attachement d’application MSIX n’a pas de dépendances par rapport au type d’infrastructure de stockage utilisé par le partage de fichiers. Les considérations relatives au partage d’attachement d’application MSIX sont identiques à celles d’un partage FSLogix. Pour en savoir plus sur les exigences en matière de stockage, consultez [Options de stockage pour conteneurs de profils FSLogix dans Azure Virtual Desktop](store-fslogix-profile.md).

## <a name="performance-requirements"></a>Exigences en matière de performances

Les limites de taille d’image d’attachement d’application MSIX pour votre système dépendent du type de stockage que vous utilisez pour stocker les fichiers VHD ou VHDx, ainsi que des limitations de taille des fichiers VHD, VHDX ou CIM et du système de fichiers.

Le tableau suivant donne un exemple du nombre de ressources d’une seule image MSIX de 1 Go avec une application MSIX à l’intérieur de celle-ci pour chaque machine virtuelle :

| Resource             | Conditions requises |
|----------------------|--------------|
| E/S par seconde d’état stable    | 1 E/S par seconde       |
| Connexion au démarrage de l’ordinateur | 10 E/S par seconde      |
| Latence              | 400 ms       |

La configuration requise peut varier considérablement en fonction du nombre d’applications MSIX stockées dans l’image MSIX. Pour des images MSIX plus volumineuses, vous devez allouer davantage de bande passante.

### <a name="storage-recommendations"></a>Recommandations de stockage

Azure offre plusieurs options de stockage qui peuvent être utilisées pour l’attachement d’application MISX. Nous vous recommandons d’utiliser Azure Files ou Azure NetApp Files, car ces options offrent la meilleure valeur entre les coûts et les frais de gestion. L’article [Options de stockage pour conteneurs de profils FSLogix dans Azure Virtual Desktop](store-fslogix-profile.md) compare les différentes solutions de stockage géré offertes par Azure dans le contexte de Azure Virtual Desktop.

### <a name="optimize-msix-app-attach-performance"></a>Optimiser les performances d’attachement d’application MSIX

Voici d’autres recommandations pour optimiser les performances de l’attachement d’application MSIX :

- La solution de stockage que vous utilisez pour l’attachement d’application MSIX doit se trouver dans le même emplacement de centre de données que les hôtes de session.
- Pour éviter les goulots d’étranglement des performances, excluez les fichiers VHD, VHDX et CIM suivants des analyses antivirus :
   
    - <MSIXAppAttachFileShare\>\*.VHD
    - <MSIXAppAttachFileShare\>\*.VHDX
    - \\\\storageaccount.file.core.windows.net\\share\*\*.VHD
    - \\\\storageaccount.file.core.windows.net\\share\*\*.VHDX
    - <MSIXAppAttachFileShare>.CIM
    - \\\\storageaccount.file.core.windows.net\\share\*\*.CIM

- Séparez l’infrastructure de stockage pour l’attachement d’application MSIX des conteneurs de profils FSLogix.
- Tous les comptes système de machines virtuelles et les comptes d’utilisateur doivent disposer d’autorisations en lecture seule pour accéder au partage de fichiers.
- Tout plan de récupération d’urgence pour Azure Virtual Desktop doit inclure la réplication du partage de fichiers d’attachement d’application MSIX dans votre emplacement de basculement secondaire. Pour en savoir plus sur la récupération d’urgence, consultez [Configurer un plan de récupération d’urgence et de continuité d’activité](disaster-recovery.md).

## <a name="how-to-set-up-the-file-share"></a>Procédure de configuration du partage de fichiers

Le processus de configuration pour le partage de fichiers d’attachement d’application MSIX est en grande partie identique au [processus de configuration pour les partages de fichiers de profil FSLogix](create-host-pools-user-profile.md). Toutefois, vous devez affecter des autorisations différentes aux utilisateurs. L’attachement d’application MSIX nécessite des autorisations en lecture seule pour accéder au partage de fichiers.

Si vous stockez vos applications MSIX dans Azure Files, pour vos hôtes de session, vous devez affecter à toutes les machines virtuelles hôtes de session non seulement le contrôle d’accès en fonction du rôle (RBAC) du compte de stockage, mais également les autorisations NTFS sur le partage de fichiers.

| Objet Azure                      | Rôle requis                                     | Fonction du rôle                                  |
|-----------------------------------|--------------------------------------------------|-----------------------------------------------|
| Hôte de la session (objets ordinateur de machine virtuelle)| Collaborateur de partage SMB des données du fichier de stockage          | Lecture et exécution, Lecture, Répertorier le contenu du dossier  |
| Administrateurs sur le partage de fichiers              | Contributeur élevé de partage SMB de données de fichier de stockage | Contrôle total                                  |
| Utilisateurs sur le partage de fichiers               | Collaborateur de partage SMB des données du fichier de stockage          | Lecture et exécution, Lecture, Répertorier le contenu du dossier  |

Pour affecter des autorisations de machines virtuelles hôtes de session pour le compte de stockage et le partage de fichiers :

1. Créez un groupe de sécurité Active Directory Domain Services (AD DS).

2. Ajoutez les comptes d’ordinateur pour toutes les machines virtuelles hôtes de session en tant que membres du groupe.

3. Synchronisez le groupe AD DS avec Azure Active Directory (Azure AD).

4. Créez un compte de stockage.

5. Créez un partage de fichiers sous le compte de stockage en suivant les instructions fournies dans [Créer un partage de fichiers Azure](../storage/files/storage-how-to-create-file-share.md#create-a-file-share).

6. Joignez le compte de stockage à AD DS en suivant les instructions fournies dans [Première partie : activer l’authentification AD DS pour vos partages de fichiers Azure](../storage/files/storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module).

7. Affectez le groupe AD DS synchronisé à Azure AD et attribuez au compte de stockage le rôle Contributeur de partage SMB de données de fichier de stockage.

8. Montez le partage de fichiers sur n’importe quel hôte de session en suivant les instructions fournies dans [Deuxième partie : affecter des autorisations au niveau du partage à une identité](../storage/files/storage-files-identity-ad-ds-assign-permissions.md).

9. Accordez des autorisations NTFS sur le partage de fichiers au groupe AD DS.

10. Configurez des autorisations NTFS pour les comptes d’utilisateur. Vous avez besoin d’une unité d’organisation (UO) source des AD DS auxquels les comptes de la machine virtuelle appartiennent.

Une fois que vous avez attribué l’identité à votre stockage, suivez les instructions fournies dans les articles [Étapes suivantes](#next-steps) pour accorder d’autres autorisations requises à l’identité que vous avez affectée aux machines virtuelles.

Vous devez également vous assurer que vos machines virtuelles hôtes de session disposent d’autorisations NTFS. Vous devez disposer d’un conteneur d’unités opérationnelles provenant d’Active Directory Domain Services (AD DS) et vos utilisateurs doivent être membres de cette unité opérationnelle pour utiliser ces autorisations.

## <a name="next-steps"></a>Étapes suivantes

Voici les autres opérations que vous devez effectuer après avoir configuré le partage de fichiers :

- Pour en savoir plus sur la procédure de configuration d’Azure Active Directory Domain Services (AD DS), consultez [Créer un conteneur de profils avec Azure Files et AD DS](create-file-share.md).
- Pour en savoir plus sur la procédure de configuration d’Azure Files et d’Azure AD DS, consultez [Créer un conteneur de profils avec Azure Files et Azure AD DS](create-profile-container-adds.md).
- Pour en savoir plus sur la procédure de configuration d’Azure NetApp Files pour l’attachement d’application MSIX, consultez [Créer un conteneur de profils avec Azure NetApp Files et AD DS](create-fslogix-profile-container.md).
- Pour en savoir plus sur l’utilisation d’un partage de fichiers basé sur une machine virtuelle, consultez [Créer un conteneur de profils pour un pool d'hôtes à l'aide d’un partage de fichiers](create-host-pools-user-profile.md).

Une fois que vous avez terminé, vous trouverez peut-être utiles certaines des autres ressources suivantes :

- Posez vos questions concernant cette fonctionnalité à la [communauté technique d’Azure Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).
- Vous pouvez également formuler vos commentaires concernant Azure Virtual Desktop sur le [Hub de commentaires Azure Virtual Desktop](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).
- [Glossaire Attachement d’application MSIX](app-attach-glossary.md)
- [FAS sur l’attachement d’application MSIX](app-attach-faq.yml)
