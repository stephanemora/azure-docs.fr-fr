---
title: Prérequis pour Azure HPC Cache
description: Prérequis à l’utilisation d’Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 90b84d936bda4e3a974e60934e82ac6c3389d85a
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645767"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Prérequis pour Azure HPC Cache

Avant d’utiliser le portail Azure pour créer une nouvelle instance d’Azure HPC Cache, vérifiez que votre environnement répond à ces exigences.

## <a name="azure-subscription"></a>Abonnement Azure

Un abonnement payant est recommandé.

> [!NOTE]
> Durant les premiers mois de la version mise à la disposition générale, l’équipe Azure HPC Cache doit ajouter votre abonnement à la liste d’accès afin que vous puissiez l’utiliser pour créer une instance de cache. Cette procédure garantit à chaque client une très grande réactivité de ses caches. Remplissez [ce formulaire](https://aka.ms/onboard-hpc-cache) pour demander l’accès.

## <a name="network-infrastructure"></a>Infrastructure réseau

Avant de pouvoir utiliser votre cache, vous devez configurer deux prérequis liés au réseau :

* Un sous-réseau dédié pour l’instance Azure HPC Cache
* La prise en charge du DNS afin que le cache puisse accéder au stockage et aux autres ressources

### <a name="cache-subnet"></a>Sous-réseau du cache

Azure HPC Cache a besoin d’un sous-réseau dédié avec les qualités suivantes :

* Le sous-réseau doit disposer d’au moins 64 adresses IP.
* Le sous-réseau ne peut pas héberger d’autres machines virtuelles, même pour les services associés tels que les ordinateurs clients.
* Si vous utilisez plusieurs instances Azure HPC Cache, chacune d’elles doit avoir son propre sous-réseau.

La méthode recommandée consiste à créer un nouveau sous-réseau pour chaque cache. Vous pouvez créer un réseau virtuel et un sous-réseau dans le cadre de la création du cache.

### <a name="dns-access"></a>Accès DNS

Le cache a besoin d’un accès DNS pour accéder aux ressources situées en dehors de son réseau virtuel. Selon les ressources que vous utilisez, vous devrez peut-être configurer un serveur DNS personnalisé et configurer un transfert entre ce serveur et les serveurs Azure DNS :

* Pour accéder aux points de terminaison du stockage Blob Azure et à d’autres ressources internes, vous avez besoin d’un serveur DNS basé sur Azure.
* Pour accéder au stockage local, vous devez configurer un serveur DNS personnalisé capable de résoudre les noms d’hôte de votre stockage.

Si vous avez uniquement besoin d’accéder au stockage Blob, vous pouvez utiliser le serveur DNS par défaut qui est fourni par Azure pour votre cache. Toutefois, si vous avez besoin d’accéder à d’autres ressources, vous devez créer un serveur DNS personnalisé et le configurer dans le but de transférer toutes les requêtes de résolution concernant Azure vers le serveur Azure DNS

Un simple serveur DNS peut également être utilisé pour équilibrer la charge des connexions clientes entre tous les points de montage du cache disponibles.

Pour plus d’informations sur les réseaux virtuels Azure et les configurations de serveur DNS, consultez [Résolution de noms des ressources dans les réseaux virtuels Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

## <a name="permissions"></a>Autorisations

Consultez les prérequis liés aux autorisations avant de créer votre cache.

* L’instance de cache doit pouvoir créer des interfaces réseau virtuelles (NIC). L’utilisateur qui crée le cache doit disposer de privilèges suffisants dans l’abonnement pour créer des cartes réseau.

* Si vous utilisez le stockage Blob, Azure HPC Cache a besoin d’une autorisation pour accéder à votre compte de stockage. Utilisez un contrôle d'accès basé sur le rôle (RBAC) pour permettre au cache d’accéder à votre stockage Blob. Deux rôles sont nécessaires : Contributeur de comptes de stockage et Contributeur aux données Blob du stockage.

  Suivez les instructions fournies dans [Ajouter des cibles de stockage](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account).

## <a name="storage-infrastructure"></a>Infrastructure du stockage

Le cache prend en charge les conteneurs d’objets blob Azure et les exportations de stockage matériel NFS. Ajoutez des cibles de stockage après avoir créé le cache.

Chaque type de stockage possède des conditions préalables spécifiques.

### <a name="nfs-storage-requirements"></a>Conditions requises pour le stockage NFS

Si vous utilisez du stockage matériel local, le cache doit disposer d’un accès réseau à bande passante élevée afin de pouvoir accéder au centre de ressources à partir de son sous-réseau. Il est recommandé de disposer d’un accès [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) ou similaire.

Le stockage back-end NFS doit être une plateforme matérielle ou logicielle compatible. Pour plus d’informations, contactez l’équipe Azure HPC Cache.

### <a name="blob-storage-requirements"></a>Exigences relatives au stockage Blob

Si vous souhaitez utiliser le stockage Blob Azure avec votre cache, vous aurez besoin d’un compte de stockage compatible, et soit d’un conteneur d’objets blob vide, soit d’un conteneur comprenant des données au format Azure HPC Cache. Pour plus d’informations, consultez [Déplacer des données vers le stockage Blob Azure](hpc-cache-ingest.md).

Créez le compte avant de tenter d’ajouter une cible de stockage. Vous pouvez créer un conteneur lorsque vous ajoutez la cible.

Pour créer un compte de stockage compatible, utilisez les paramètres suivants :

* Performances : **Standard**
* Type de compte : **StorageV2 (usage général v2)**
* Réplication : **Stockage localement redondant (LRS)**
* Niveau d’accès (par défaut) : **Chaud**

Il est recommandé d’utiliser un compte de stockage se trouvant au même emplacement que votre cache.
<!-- clarify location - same region or same resource group or same virtual network? -->

Vous devez également autoriser l’application de cache à accéder à votre compte de stockage Azure, tel que mentionné dans [Autorisations](#permissions), ci-dessus. Suivez la procédure indiquée dans [Ajouter des cibles de stockage](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) pour accorder au cache les rôles d’accès requis. Si vous n’êtes pas le propriétaire du compte de stockage, demandez au propriétaire d’effectuer cette étape.

## <a name="next-steps"></a>Étapes suivantes

* [Créez une instance d’Azure HPC Cache](hpc-cache-create.md) à partir du portail Azure.
