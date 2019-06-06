---
title: Conteneurs de profil FSLogix et Azure Files dans un bureau virtuel Windows - Azure
description: Cet article décrit les conteneurs de profil FSLogix dans les fichiers de bureau virtuel Windows et Azure.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: v-chjenk
ms.openlocfilehash: b3032aa796b3c79572bbf8b2beb85efc252ff73b
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497539"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>Conteneurs de profil FSLogix et fichiers Azure

Le service de Windows Virtual Desktop Preview recommande des conteneurs de profil FSLogix comme une solution de profil utilisateur. FSLogix est conçu pour se déplacer de profils dans des environnements informatiques à distance, telles que bureau virtuel Windows. Elle stocke un profil complet de l’utilisateur dans un seul conteneur. Lors de la connexion, ce conteneur est dynamiquement attaché à l’environnement informatique à l’aide de disque dur virtuel Hyper-V et de disque dur virtuel (VHD) pris en charge en mode natif (VHDX). Le profil utilisateur est immédiatement disponible et apparaît dans le système exactement comme un profil utilisateur natives.

Dans cet article, nous décrirons les conteneurs de profil FSLogix utilisés avec Azure Files. Les informations figurent dans le contexte de bureau virtuel de Windows, ce qui a été [annoncées sur 3/21](https://www.microsoft.com/microsoft-365/blog/2019/03/21/windows-virtual-desktop-public-preview/).

## <a name="user-profiles"></a>Profils utilisateur

Un profil utilisateur contient des éléments de données sur une personne, y compris les informations de configuration telles que les paramètres des bureaux, des connexions réseau persistantes et paramètres de l’application. Par défaut, Windows crée un profil utilisateur local qui est étroitement intégré au système d’exploitation.

Un profil utilisateur à distance fournit une partition entre les données utilisateur et le système d’exploitation. Il permet au système d’exploitation être remplacé ou modifié sans affecter les données utilisateur. Dans l’hôte de Session de bureau à distance (RDSH) et Virtual Desktop Infrastructure (VDI), le système d’exploitation peut être remplacé pour les raisons suivantes :

- Une mise à niveau du système d’exploitation
- Un remplacement d’une Machine virtuelle (machine virtuelle existante)
- Un utilisateur faisant partie d’un environnement RDSH ou VDI (non persistants) mis en pool

Produits Microsoft fonctionnent avec plusieurs technologies pour les profils utilisateur distant, y compris de ces technologies :
- Profils utilisateur itinérants (RUP)
- Disques de profil utilisateur (UPD)
- État entreprise itinérance (ESR)

UPD et profil utilisateur itinérant sont les technologies les plus couramment utilisées pour les profils utilisateur dans les environnements d’hôte de Session de bureau à distance (RDSH) et le disque dur virtuel (VHD).

### <a name="challenges-with-previous-user-profile-technologies"></a>Défis avec les technologies précédentes du profil utilisateur

Les solutions Microsoft existant pour les profils utilisateur fournie avec plusieurs vérifications. Aucune solution précédente ne géré tous les besoins de profil utilisateur sont fournis avec un environnement RDSH ou VDI. Par exemple, UPD ne peut pas gérer de gros fichiers OST et profil utilisateur itinérant ne conserve pas les paramètres modernes.

#### <a name="functionality"></a>Fonctionnalités

Le tableau suivant présente les avantages et les limites des technologies de profil utilisateur précédente.

| Technology | Paramètres modernes | Paramètres de Win32 | Paramètres de système d’exploitation | Données utilisateur | Prise en charge sur le serveur de référence (SKU) | Stockage back-end sur Azure | Stockage back-end en local | Versions prises en charge | Pour les connexions suivantes dans le temps |Notes|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Disques de profil utilisateur (UPD)** | Oui | OUI | OUI | OUI | Oui | Non | Oui | Win 7 + | Oui | |
| **Itinérance de profil utilisateur (RUP), mode de maintenance** | Non | OUI | OUI | OUI | Oui| Non | Oui | Win 7 + | Non | |
| **Enterprise State Roaming (ESR)** | Oui | Non | Oui | Non | Voir les remarques | Oui | Non | Win 10 | Non | Fonctions sur le serveur de référence (SKU), mais aucune interface utilisateur prise en charge |
| **User Experience Virtualization (UE-V)** | Oui | OUI | Oui | Non | Oui | Non | Oui | Win 7 + | Non |  |
| **Fichiers de cloud OneDrive** | Non | Non | Non | Oui | Voir les remarques | Voir les remarques  | Consultez les notes de publication | Win 10 RS3 | Non | Pas testé sur le serveur de référence (SKU). Stockage back-end sur Azure dépend du client de synchronisation. Stockage principal local a besoin d’un client de synchronisation. |

#### <a name="performance"></a>Performances

UPD nécessite [espaces de stockage Direct (S2D)](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment) pour répondre aux exigences de performances. UPD protocole Server Message Block (SMB). Il copie le profil à la machine virtuelle dans laquelle l’utilisateur est consignée. UPD avec S2D était la solution recommandée de l’équipe des services Bureau à distance pour bureau virtuel Windows pendant la préversion du service.  

#### <a name="cost"></a>Coût

Alors que les clusters S2D obtenir les performances nécessaires, le coût est coûteux pour les clients enterprise, mais particulièrement coûteux pour les clients des petites et moyennes entreprises (PME). Pour cette solution, les entreprises paient pour les disques de stockage premium, ainsi que le coût des machines virtuelles qui utilisent les disques pour un partage.

#### <a name="administrative-overhead"></a>Surcharge administrative

Les clusters S2D nécessitent un système d’exploitation est corrigé, mis à jour, et géré dans un état sécurisé. Ces processus et la complexité de la configuration de la récupération d’urgence de S2D rendent S2D réalisable uniquement pour les entreprises avec une équipe informatique dédiée.

## <a name="fslogix-profile-containers"></a>Conteneurs de profil FSLogix

Le 19 novembre 2018, [Microsoft a acquis FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix résout de nombreux défis de conteneur de profil. Les plus importants sont :

- **Performances :** Le [conteneurs de profil FSLogix](https://fslogix.com/products/profile-containers) haute performance et de résoudre les problèmes de performances ont bloqué historiquement mis en cache en mode exchange.
- **OneDrive :** Sans les conteneurs de profil FSLogix, OneDrive entreprise n’est pas pris en charge dans les environnements RDSH ou VDI non persistant. [OneDrive pour les meilleures pratiques d’entreprise et FSLogix](https://fslogix.com/products/technical-faqs/284-onedrive-for-business-and-fslogix-best-practices) décrit la façon dont ils interagissent. Pour plus d’informations, consultez [utiliser le client de synchronisation sur les bureaux virtuels](https://docs.microsoft.com/deployoffice/rds-onedrive-business-vdi).
- **Dossiers supplémentaires :** FSLogix offre la possibilité d’étendre les profils utilisateur pour inclure des dossiers supplémentaires.

Depuis l’acquisition, Microsoft démarré en remplaçant les solutions de profil utilisateur existantes, telles que les UPD, avec des conteneurs de profil FSLogix.

## <a name="azure-files-integration-with-azure-active-directory"></a>Intégration de fichiers Azure avec Azure Active Directory

Fonctionnalités et performances des conteneurs de profil FSLogix tirent parti du cloud. Le 24 septembre 2018, Microsoft Azure Files a annoncé une préversion publique de [Azure Files prenant en charge l’authentification Azure Active Directory](https://azure.microsoft.com/blog/azure-active-directory-integration-for-smb-access-now-in-public-preview/). En traitant les coûts et la surcharge administrative, Azure Files avec l’authentification Azure Active Directory est une solution de premium pour les profils utilisateur dans le nouveau service de bureau virtuel Windows.

## <a name="best-practices-for-windows-virtual-desktop"></a>Meilleures pratiques pour le bureau virtuel Windows

Bureau virtuel Windows offre un contrôle total sur la taille, type et nombre de machines virtuelles qui sont utilisés par les clients. Pour plus d’informations, consultez [What ' s Windows Virtual Desktop Preview ?](https://docs.microsoft.com/azure/virtual-desktop/overview).

Pour vous assurer de votre bureau virtuel Windows environnement respecte les bonnes pratiques :

- Compte de stockage de fichiers Azure doit être dans la même région que l’hôte de session des machines virtuelles.
- Les autorisations de fichiers Azure doivent correspondre à des autorisations décrites dans [exigences - profil conteneurs](https://docs.fslogix.com/display/20170529/Requirements+-+Profile+Containers).
- Chaque pool de l’hôte doit être généré du même type et taille de machine virtuelle basée sur la même image maître.
- Chaque machine virtuelle du pool hôte doit être du même groupe de ressources afin de faciliter la gestion, mise à l’échelle et la mise à jour.
- Pour des performances optimales, emplacement du centre de la solution de stockage et le FSLogix conteneur de profil doit être dans les mêmes données.
- Le compte de stockage contenant l’image principale doit être dans la même région et abonnement dans lequel les machines virtuelles sont en cours d’approvisionnement.

## <a name="next-steps"></a>Étapes suivantes

Utilisez les instructions suivantes pour configurer un environnement de bureau virtuel Windows.

- Pour commencer à créer votre solution de virtualisation de bureau, consultez [créer un client de bureau virtuel Windows](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory).
- Pour créer un pool d’hôtes au sein de votre client de bureau virtuel de Windows, consultez [créer un pool de l’hôte avec la place de marché Azure](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace).
- Pour configurer entièrement géré les partages de fichiers dans le cloud, consultez [configurer le partage Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable).
- Pour configurer des conteneurs de profil FSLogix, consultez [configurez un partage de profil utilisateur pour un pool de l’hôte](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-user-profile).
- Pour affecter des utilisateurs à un pool de l’hôte, consultez [gérer des groupes d’applications de bureau virtuel Windows](https://docs.microsoft.com/azure/virtual-desktop/manage-app-groups).
- Pour accéder à vos ressources de bureau virtuel Windows à partir d’un navigateur web, consultez [se connecter au bureau virtuel Windows](https://docs.microsoft.com/azure/virtual-desktop/connect-web).
