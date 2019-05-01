---
title: Configurez un partage de profil utilisateur pour un pool d’ordinateur hôte Windows Virtual Desktop Preview - Azure
description: Comment configurer un conteneur de profil FSLogix pour un pool d’ordinateur hôte Windows Virtual Desktop Preview.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: 0cb4df099faad8ca482fd15cf0bb50504c1528ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883868"
---
# <a name="set-up-a-user-profile-share-for-a-host-pool"></a>Configurer un partage de profil utilisateur pour un pool d’hôtes

Le service de Windows Virtual Desktop Preview offre des conteneurs de profil FSLogix comme solution de profil utilisateur recommandée. Nous ne recommandons pas à l’aide de la solution de disque de profil utilisateur (UPD), qui sera déconseillée dans les futures versions de bureau virtuel Windows.

Cette section vous indique comment configurer un partage de conteneur FSLogix profil pour un pool de l’hôte. Pour obtenir une documentation générale concernant FSLogix, consultez le [FSLogix site](https://docs.fslogix.com/).

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

1. Ajoutez les utilisateurs de Windows Active Directory de bureau virtuel à un [groupe de sécurité Active Directory](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups). Ce groupe de sécurité sera utilisé pour authentifier les utilisateurs du bureau virtuel Windows à la machine virtuelle de partage de fichiers que vous venez de créer.
2. [Se connecter à la machine virtuelle de partage de fichiers](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine).
3. Sur la machine virtuelle de partage de fichiers, créez un dossier sur le **lecteur C** qui sera utilisé en tant que le partage de profil.
4. Cliquez sur le nouveau dossier, sélectionnez **propriétés**, sélectionnez **partage**, puis sélectionnez **partage avancé...** .
5. Sélectionnez **partager ce dossier**, sélectionnez **autorisations...** , puis sélectionnez **ajouter...** .
6. Recherchez le groupe de sécurité auquel vous avez ajouté les utilisateurs du bureau virtuel de Windows, puis vérifiez que ce groupe a **contrôle total**.
7. Après avoir ajouté le groupe de sécurité, cliquez sur le dossier, sélectionnez **propriétés**, sélectionnez **partage**, puis copiez le **chemin d’accès réseau** à utiliser pour une utilisation ultérieure.

Pour plus d’informations sur les autorisations, consultez le [FSLogix documentation](https://docs.fslogix.com/display/20170529/Requirements%2B-%2BProfile%2BContainers).

## <a name="configure-the-fslogix-profile-container"></a>Configurer le conteneur de profil FSLogix

Pour configurer les machines virtuelles avec le logiciel FSLogix, procédez comme suit sur chaque machine inscrite dans le pool de l’hôte :

1. [Se connecter à la machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) avec les informations d’identification que vous avez fourni lors de la création de la machine virtuelle.
2. Lancez un navigateur internet et accédez à [ce lien](https://go.microsoft.com/fwlink/?linkid=2084562) pour télécharger l’agent FSLogix. Dans le cadre de la préversion publique de bureau virtuel de Windows, vous obtenez une clé de licence pour activer le logiciel FSLogix. La clé est le fichier LicenseKey.txt inclus dans le fichier .zip de l’agent FSLogix.
3. Accédez à un \\ \\Win32\\mise en production ou \\ \\X64\\mise en production dans le fichier .zip et exécutez **FSLogixAppsSetup** pour installer l’agent FSLogix.
4. Accédez à **Program Files** > **FSLogix** > **applications** pour confirmer l’agent est installé.
5. À partir du menu Démarrer, exécutez **RegEdit** en tant qu’administrateur. Accédez à **ordinateur\\HKEY_LOCAL_MACHINE\\logiciel\\FSLogix**.
6. Créez une clé nommée **profils**.
7. Créez les valeurs suivantes pour la clé de profils :

| Nom                | Type               | Valeur des données                        |
|---------------------|--------------------|-----------------------------------|
| activé             | VALEUR DWORD              | 1                                 |
| VHDLocations        | Valeur de chaînes multiples | « Chemin d’accès réseau pour le partage de fichiers »     |

>[!IMPORTANT]
>Pour contribuer à sécuriser votre environnement Windows Virtual Desktop dans Azure, nous vous recommandons de ne pas ouvrir le port entrant 3389 sur vos machines virtuelles. Windows Virtual Desktop ne nécessite pas l’ouverture du port entrant 3389 pour permettre aux utilisateurs d’accéder aux machines virtuelles du pool hôte. Si vous devez ouvrir le port 3389 pour résoudre des problèmes, nous vous recommandons d’utiliser un [accès à la machine virtuelle juste-à-temps](https://docs.microsoft.com/en-us/azure/security-center/security-center-just-in-time).