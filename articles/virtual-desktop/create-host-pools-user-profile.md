---
title: Configurer un partage de profil utilisateur pour un pool hôte Windows Virtual Desktop Preview - Azure
description: Explique comment configurer un conteneur de profils FSLogix pour un pool hôte Windows Virtual Desktop Preview.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: 692902c28b336dd46a7c6f00d5cf5a61ee9f7328
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619100"
---
# <a name="set-up-a-user-profile-share-for-a-host-pool"></a>Configurer un partage de profil utilisateur pour un pool d’hôtes

Le service Windows Virtual Desktop Preview offre des conteneurs de profils FSLogix comme solution de profil utilisateur recommandée. Nous recommandons de ne pas utiliser la solution Disque de profil utilisateur (UPD), qui sera déconseillée dans les futures versions de Windows Virtual Desktop.

Cette section vous indique comment configurer un partage de conteneur de profils FSLogix pour un pool hôte. Pour obtenir une documentation générale concernant FSLogix, consultez le [site FSLogix](https://docs.fslogix.com/).

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Créer une machine virtuelle qui fera office de partage de fichiers

Lorsque vous créez la machine virtuelle, veillez à la placer sur le même réseau virtuel que les machines virtuelles du pool hôte ou sur un réseau virtuel disposant d’une connectivité avec les machines virtuelles du pool hôte. Vous pouvez créer une machine virtuelle de plusieurs façons :

- [Créer une machine virtuelle à partir d’une image Azure Gallery](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Créer une machine virtuelle à partir d’une image managée](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Créer une machine virtuelle à partir d’une image non managée](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Après avoir créé la machine virtuelle, joignez-la au domaine en procédant comme suit :

1. [Connectez-vous à la machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) avec les informations d’identification que vous avez indiquées lors de la création de la machine virtuelle.
2. Sur la machine virtuelle, lancez le **Panneau de configuration** et sélectionnez **Système**.
3. Sélectionnez successivement **Nom de l’ordinateur**, **Modifier les paramètres** et **Modifier...**
4. Sélectionnez **Domaine**, puis entrez le domaine Active Directory sur le réseau virtuel.
5. Authentifiez-vous avec un compte de domaine qui dispose de privilèges d’accès sur les machines de jonction de domaine.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Préparer la machine virtuelle pour qu’elle serve de partage de fichiers pour les profils utilisateur

Vous trouverez ci-dessous des instructions générales sur la préparation d’une machine virtuelle pour qu’elle serve de partage de fichiers pour les profils utilisateur :

1. Ajoutez les utilisateurs Active Directory de Windows Virtual Desktop à un [groupe de sécurité Active Directory](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups). Ce groupe de sécurité sera utilisé pour authentifier les utilisateurs de Windows Virtual Desktop auprès de la machine virtuelle du partage de fichiers que vous venez de créer.
2. [Connectez-vous à la machine virtuelle du partage de fichiers](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine).
3. Sur la machine virtuelle du partage de fichiers, créez un dossier sur le **lecteur C** qui sera utilisé comme partage de profil.
4. Cliquez avec le bouton droit sur le nouveau dossier, puis sélectionnez successivement **Propriétés**, **Partage** et **Partage avancé...**
5. Sélectionnez successivement **Partager ce dossier**, **Autorisations...**  et **Ajouter...**
6. Recherchez le groupe de sécurité dans lequel vous avez ajouté les utilisateurs de Windows Virtual Desktop, puis vérifiez que ce groupe dispose de l’accès **Contrôle total**.
7. Après avoir ajouté le groupe de sécurité, cliquez avec le bouton droit sur le dossier, sélectionnez **Propriétés**, puis **Partage**, puis copiez le **Chemin d’accès réseau** pour une utilisation ultérieure.

Pour plus d’informations sur les autorisations, consultez la [documentation FSLogix](https://docs.fslogix.com/display/20170529/Requirements%2B-%2BProfile%2BContainers).

## <a name="configure-the-fslogix-profile-container"></a>Configurer le conteneur de profils FSLogix

Pour configurer les machines virtuelles avec le logiciel FSLogix, procédez comme suit sur chaque machine inscrite dans le pool hôte :

1. [Connectez-vous à la machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) avec les informations d’identification que vous avez indiquées lors de la création de la machine virtuelle.
2. Lancez un navigateur Internet et accédez à [ce lien](https://go.microsoft.com/fwlink/?linkid=2084562) pour télécharger l’agent FSLogix. Dans le cadre de la préversion publique de Windows Virtual Desktop, vous obtenez une clé de licence pour activer le logiciel FSLogix. La clé est le fichier LicenseKey.txt inclus dans le fichier .zip de l’agent FSLogix.
3. Accédez à \\\\Win32\\Release ou \\\\X64\\Release dans le fichier .zip et exécutez **FSLogixAppsSetup** pour installer l’agent FSLogix.
4. Accédez à **Program Files** > **FSLogix** > **Apps** pour vérifier l’agent installé.
5. Dans le menu Démarrer, exécutez **RegEdit** en tant qu’administrateur. Accédez à **Computer\\HKEY_LOCAL_MACHINE\\software\\FSLogix**.
6. Créez une clé nommée **Profils**.
7. Créez les valeurs suivantes pour la clé Profils :

| Nom                | Type               | Données/Valeur                        |
|---------------------|--------------------|-----------------------------------|
| activé             | DWORD              | 1                                 |
| VHDLocations        | Valeur à chaînes multiples | « Chemin d’accès réseau pour le partage de fichiers »     |

>[!IMPORTANT]
>Pour contribuer à sécuriser votre environnement Windows Virtual Desktop dans Azure, nous vous recommandons de ne pas ouvrir le port entrant 3389 sur vos machines virtuelles. Windows Virtual Desktop ne nécessite pas l’ouverture du port entrant 3389 pour permettre aux utilisateurs d’accéder aux machines virtuelles du pool hôte. Si vous devez ouvrir le port 3389 pour résoudre des problèmes, nous vous recommandons d’utiliser un [accès à la machine virtuelle juste-à-temps](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).