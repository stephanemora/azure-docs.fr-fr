---
title: Configurez un partage de profil utilisateur pour un pool d’ordinateur hôte Windows Virtual Desktop Preview - Azure
description: Comment configurer un conteneur de profil FSLogix pour un pool d’ordinateur hôte Windows Virtual Desktop Preview.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: c9c2ca2cc27c5fa757b8ff6846e0a6a8f7087875
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403712"
---
# <a name="set-up-a-user-profile-share-for-a-host-pool"></a>Configurez un partage de profil utilisateur pour un pool de l’hôte

Le service de Windows Virtual Desktop Preview offre des conteneurs de profil FSLogix comme solution de profil utilisateur recommandée. Nous déconseillons l’utilisation de la solution de disque de profil utilisateur (UPD), et il sera déconseillé dans les futures versions de bureau virtuel Windows.

Cette section vous indique comment configurer un partage de conteneur FSLogix profil pour un pool de l’hôte.

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Créer une machine virtuelle qui agira comme un partage de fichiers

Lorsque vous créez la machine virtuelle, veillez à placer sur un même réseau virtuel que les machines virtuelles du pool hôte ou sur un réseau virtuel qui a une connectivité avec les machines virtuelles du pool hôte. Vous pouvez créer une machine virtuelle de plusieurs façons :

- [Créer une machine virtuelle à partir d’une image de galerie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Créer une machine virtuelle à partir d’une image managée](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Créer une machine virtuelle à partir d’une image non managée](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Après avoir créé la machine virtuelle, joignez-la au domaine en procédant comme suit :

1. [Se connecter à la machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) avec les informations d’identification que vous avez fourni lors de la création de la machine virtuelle.
2. Sur la machine virtuelle, lancez **le panneau de configuration** et sélectionnez **système**.
3. Sélectionnez **nom de l’ordinateur**, sélectionnez **modifier les paramètres**, puis sélectionnez **modification...**
4. Sélectionnez **domaine** , puis entrez le domaine Active Directory sur le réseau virtuel.
5. S’authentifier avec un compte de domaine qui dispose de privilèges pour les machines de jonction de domaine.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Préparer la machine virtuelle d’agir comme un partage de fichiers pour les profils utilisateur

Instructions générales sur la façon de préparer une machine virtuelle d’agir comme un partage de fichiers pour les profils utilisateur sont les suivantes :

1. Rejoindre les session héberger des ordinateurs virtuels à un [groupe de sécurité Active Directory](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups). Ce groupe de sécurité sera utilisé pour authentifier les machines de virtuelles des hôtes de session à la machine virtuelle de partage de fichiers que vous venez de créer.
2. [Se connecter à la machine virtuelle de partage de fichiers](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine).
3. Sur la machine virtuelle de partage de fichiers, créez un dossier sur le **lecteur C** qui sera utilisé en tant que le partage de profil.
4. Cliquez sur le nouveau dossier, sélectionnez **propriétés**, sélectionnez **partage**, puis sélectionnez **partage avancé...** .
5. Sélectionnez **partager ce dossier**, sélectionnez **autorisations...** , puis sélectionnez **ajouter...** .
6. Recherchez le groupe de sécurité auquel vous avez ajouté les session héberger des ordinateurs virtuels, puis assurez-vous que ce groupe dispose **contrôle total**.
7. Après avoir ajouté le groupe de sécurité, cliquez sur le dossier, sélectionnez **propriétés**, sélectionnez **partage**, puis copiez le **chemin d’accès réseau** à utiliser pour une utilisation ultérieure.

Pour obtenir des recommandations sur les autorisations, consultez la rubrique suivante [FSLogix documentation](https://support.fslogix.com/index.php/forum-main/faqs/84-best-practices#120).

## <a name="configure-the-fslogix-profile-container"></a>Configurer le conteneur de profil FSLogix

Pour configurer les machines virtuelles avec le logiciel FSLogix, procédez comme suit sur chaque machine inscrite dans le pool de l’hôte :

1. [Se connecter à la machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) avec les informations d’identification que vous avez fourni lors de la création de la machine virtuelle.
2. Lancez un navigateur internet et accédez à ce qui suit [lien](https://go.microsoft.com/fwlink/?linkid=2084562) pour télécharger l’agent FSLogix. Dans le cadre de la préversion publique de bureau virtuel de Windows, vous obtenez une clé de licence pour activer le logiciel FSLogix. La clé est le fichier LicenseKey.txt inclus dans le fichier .zip de l’agent FSLogix.
3. Installez l’agent FSLogix.
4. Accédez à **Program Files** > **FSLogix** > **applications** pour confirmer l’agent est installé.
5. À partir du menu Démarrer, exécutez **RegEdit** en tant qu’administrateur. Accédez à **ordinateur\\HKEY_LOCAL_MACHINE\\logiciel\\FSLogix\\profils**
6. Créez les valeurs suivantes :

| Nom                | Type               | Valeur des données                        |
|---------------------|--------------------|-----------------------------------|
| activé             | VALEUR DWORD              | 1                                 |
| VHDLocations        | Valeur de chaînes multiples | « Chemin d’accès réseau pour le partage de fichiers » |
| VolumeType          | Chaîne             |  VHDX                              |
| SizeInMBs           | VALEUR DWORD              | « entier pour la taille de profil »     |
| IsDynamic           | VALEUR DWORD              | 1                                 |
| LockedRetryCount    | VALEUR DWORD              | 1                                 |
| LockedRetryInterval | VALEUR DWORD              | 0                                 |
