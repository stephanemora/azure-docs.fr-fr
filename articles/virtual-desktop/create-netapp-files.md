---
title: Créer un partage Azure NetApp Files Azure Virtual Desktop - Azure
description: Cet article explique comment créer un partage Azure NetApp Files dans Azure Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 08/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 244ff34b462d519d111aad6d6b66d7261e612595
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122535224"
---
# <a name="upload-msix-images-to-azure-netapp-files-in-azure-virtual-desktop"></a>Charger des images MSIX sur Azure NetApp Files dans Azure Virtual Desktop

Cet article explique comment charger des images MSIX sur Azure NetApp Files dans Azure Virtual Desktop.

## <a name="requirements"></a>Configuration requise

Avant de commencer à charger les images, vous devez configurer Azure NetApp Files si ce n’est déjà fait.

Pour configurer Azure NetApp Files, vous aurez besoin des éléments suivants :

- Un compte Azure avec un accès contributeur ou administrateur

- Une machine virtuelle (VM) ou une machine physique jointe à Active Directory Domain Services (AD DS) et des autorisations pour y accéder

- Un pool d’hôtes Azure Virtual Desktop constitué d’hôtes de session joints à un domaine. Chaque hôte de session doit se trouver dans la même région que celle où vous créez votre partage Azure NetApp Files. Pour plus d’informations, consultez [Disponibilité des régions](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Si vos hôtes de session existants ne se trouvent pas dans l’une des régions disponibles, vous devez en créer d’autres.

## <a name="start-using-azure-netapp-files"></a>Commencer à utiliser Azure NetApp Files

Pour commencer à utiliser Azure NetApp Files :

1. Suivez les instructions de la rubrique [S’inscrire à Azure NetApp Files](../azure-netapp-files/azure-netapp-files-register.md) pour envoyer une demande de mise en file d’attente et inscrire votre fournisseur NetApp.
2. Configurez votre compte Azure NetApp Files en suivant les instructions de la rubrique [Configurer votre compte Azure NetApp Files](create-fslogix-profile-container.md#set-up-your-azure-netapp-files-account).
3. Créez un pool de capacité en suivant les instructions de la rubrique [Configurer un pool de capacité](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).
4. Joignez une connexion Azure Active Directory (Azure AD) en suivant les instructions de la rubrique [Joindre une connexion Active Directory](create-fslogix-profile-container.md#join-an-active-directory-connection).
5. Créez un volume en suivant les instructions des rubriques [Créer un volume](create-fslogix-profile-container.md#create-a-new-volume) et [Configurer les paramètres d’accès au volume](create-fslogix-profile-container.md#configure-volume-access-parameters).
6. Assurez-vous que votre connexion au partage Azure NetApp Files fonctionne en suivant les instructions de la rubrique [Vérifier que les utilisateurs peuvent accéder au partage Azure NetApp Files](create-fslogix-profile-container.md#make-sure-users-can-access-the-azure-netapp-file-share).

## <a name="upload-an-msix-image-to-the-azure-netapp-file-share"></a>Charger une image MSIX sur le partage Azure NetApp Files

Maintenant que vous avez configuré votre partage Azure NetApp Files, vous pouvez commencer à y charger des images.

Pour charger une image MSIX sur votre partage Azure NetApp Files :

1. Dans chaque hôte de session, installez le certificat avec lequel vous avez signé le package MSIX. Veillez à stocker les certificats dans le dossier nommé **Personnes autorisées**.
2. Copiez l’image MSIX que vous souhaitez ajouter au partage Azure NetApps Files.
3. Accédez à l’**Explorateur de fichiers** et entrez le chemin de montage, puis collez l’image MSIX dans le dossier du chemin de montage.

Votre image MSIX doit maintenant être accessible à vos hôtes de session lorsqu’ils ajoutent un package MSIX à l’aide du portail Azure ou de PowerShell.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé un partage Azure NetApp Files, voici quelques ressources décrivant de quelles manières vous pouvez l’utiliser dans Azure Virtual Desktop :

- [Créer un conteneur de profil avec Azure NetApp Files et Azure AD DS](create-fslogix-profile-container.md)
- [Options de stockage des conteneurs de profils FSLogix dans Azure Virtual Desktop](store-fslogix-profile.md)
- [Créer un peering de réplication pour Azure NetApp Files](../azure-netapp-files/cross-region-replication-create-peering.md)
