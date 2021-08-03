---
title: Prise en charge de Network File System 3.0 dans Stockage Blob Azure (préversion) | Microsoft Docs
description: Le stockage Blob Azure prend désormais en charge le protocole NFS (Network File System) 3.0. Cette prise en charge permet aux clients Linux de monter un conteneur dans Stockage Blob à partir d’une machine virtuelle Azure ou d’un ordinateur qui s’exécute localement.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 04/28/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 709f4ed6fb57dc11d4a2b8672f253664835f20e3
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111965018"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>Prise en charge du protocole NFS (Network File System) 3.0 dans Stockage Blob Azure (préversion)

Le stockage Blob Azure prend désormais en charge le protocole NFS (Network File System) 3.0. Cette prise en charge assure la compatibilité du système de fichiers Linux au niveau du stockage des objets, et permet aux clients Linux de monter un conteneur dans Stockage Blob à partir d’une machine virtuelle Azure ou d’un ordinateur local. 

> [!NOTE]
> La prise en charge du protocole NFS 3.0 dans Stockage Blob Azure est en préversion publique. Il prend en charge les comptes de stockage GPV2 avec des performances de niveau standard et les comptes de stockage d’objets blob de blocs avec un niveau de performances Premium dans toutes les régions publiques.

Il a toujours été difficile d’exécuter des charges de travail héritées à grande échelle, telles que HPC (High Performance Computing), dans le cloud. L’une des raisons est que les applications utilisent souvent des protocoles de fichiers traditionnels tels que NFS ou SMB (Server Message Block) pour accéder aux données. En outre, les services de stockage cloud natifs sont axés sur le stockage d’objets qui ont un espace de noms plat et des métadonnées étendues, plutôt que des systèmes de fichiers qui fournissent un espace de noms hiérarchique et des opérations de métadonnées efficaces. 

Le Stockage Blob prend désormais en charge un espace de noms hiérarchique et, lorsqu’il est associé à la prise en charge du protocole NFS 3.0, Azure facilite grandement l’exécution d’applications héritées par-dessus le stockage d’objets cloud à grande échelle. 

## <a name="applications-and-workloads-suited-for-this-feature"></a>Applications et charges de travail adaptées à cette fonctionnalité

La fonctionnalité de protocole NFS 3.0 convient davantage au traitement des charges de travail à haut débit, à grande échelle et à lecture intensive, telles que le traitement multimédia, les simulations de risques et le séquençage génomique. Vous devez envisager d’utiliser cette fonctionnalité pour tout autre type de charge de travail qui utilise plusieurs lecteurs et de nombreux threads, ce qui nécessite une bande passante élevée. 

## <a name="nfs-30-and-the-hierarchical-namespace"></a>NFS 3.0 et l’espace de noms hiérarchique

La prise en charge du protocole NFS 3.0 impose que les blobs soient organisés dans un espace de noms hiérarchique. Vous pouvez activer un espace de noms hiérarchique lorsque vous créez un compte de stockage. La possibilité d’utiliser un espace de noms hiérarchique a été introduite par Azure Data Lake Storage Gen2. Il organise les objets (fichiers) selon une hiérarchie de répertoires et sous-répertoires, de la même façon que le système de fichiers sur votre ordinateur.  L’espace de noms hiérarchique est mis à l’échelle de façon linéaire, et ne dégrade pas la capacité ou les performances des données. Différents protocoles s’étendent à partir de l’espace de noms hiérarchique. Le protocole NFS 3.0 est l’un de ces protocoles disponibles.   

> [!div class="mx-imgBorder"]
> ![espace de noms hiérarchique](./media/network-protocol-support/hierarchical-namespace-and-nfs-support.png)
  
## <a name="data-stored-as-block-blobs"></a>Données stockées en tant qu’objets blob de blocs

Si vous activez la prise en charge du protocole NFS 3.0, toutes les données de votre compte de stockage sont stockées en tant qu’objets blob de blocs. Les objets blob de blocs sont optimisés pour traiter efficacement de grandes quantités de données à lecture intensive. Les objets blob de blocs sont composés de blocs. Chaque bloc est identifié par un ID de bloc. Un objet blob de blocs peut inclure jusqu’à 50 000 blocs. Chaque bloc dans un objet blob de blocs peut avoir une taille différente, jusqu’à la taille maximale autorisée pour la version de service utilisée par votre compte.

Lorsque votre application effectue une requête à l’aide du protocole NFS 3.0, cette requête est traduite en une combinaison d’opérations d’objets blob de blocs. Par exemple, les requêtes NFS 3.0 de lecture RPC (Remote Procedure Call) sont traduites en opérations [Get Blob](/rest/api/storageservices/get-blob). Les requêtes d’écriture RPC NFS 3.0 sont traduites en une combinaison d’opérations [Get Block List](/rest/api/storageservices/get-block-list), [Put Block](/rest/api/storageservices/put-block) et [Put Block List](/rest/api/storageservices/put-block-list).

## <a name="general-workflow-mounting-a-storage-account-container"></a>Workflow général : Monter un conteneur de compte de stockage

Vos clients Linux peuvent monter un conteneur dans le Stockage Blob à partir d’une machine virtuelle Azure ou d’un ordinateur local. Pour monter un conteneur de compte de stockage, vous devez effectuer les opérations suivantes.

1. Inscrivez la fonctionnalité de protocole NFS 3.0 avec votre abonnement.

2. Vérifier que l’appareil est enregistré.

3. Créez un réseau virtuel Azure (Vnet).

4. Configurez la sécurité du réseau.

5. Créez et configurez un compte de stockage qui accepte le trafic uniquement à partir du réseau virtuel.

6. Créez un conteneur dans le compte de stockage.

7. Montez le conteneur.

Pour obtenir des instructions pas à pas, consultez [Monter le stockage Blob à l’aide du protocole NFS (Network File System) 3.0 (préversion)](network-file-system-protocol-support-how-to.md).

> [!IMPORTANT]
> Il est important d’effectuer ces tâches dans l’ordre. Vous ne pouvez pas monter les conteneurs que vous créez avant d’activer le protocole NFS 3.0 sur votre compte. De plus, une fois que vous avez activé le protocole NFS 3.0 sur votre compte, vous ne pouvez pas le désactiver.

## <a name="network-security"></a>Sécurité du réseau

Votre compte de stockage doit se trouver dans un réseau virtuel. Un réseau virtuel permet aux clients de se connecter en toute sécurité à votre compte de stockage. La seule façon de sécuriser les données dans votre compte consiste à utiliser un réseau virtuel et d’autres paramètres de sécurité réseau. Tous les autres outils utilisé pour sécuriser les données, notamment l’autorisation de clé de compte, la sécurité Azure Active Directory (AD) et les listes de contrôle d’accès (ACL), ne sont pas encore pris en charge dans les comptes sur lesquels la prise en charge du protocole NFS 3.0 est activée. 

Pour en savoir plus, consultez les [recommandations de sécurité réseau pour le stockage Blob](security-recommendations.md#networking).

## <a name="supported-network-connections"></a>Connexions réseau prises en charge

Un client peut se connecter via un point de terminaison public ou [un point de terminaison privé](../common/storage-private-endpoints.md) à partir de l’un des emplacements réseau suivants :

- Le réseau virtuel que vous configurez pour votre compte de stockage. 

  Dans cet article, nous ferons référence à ce réseau virtuel en tant que *réseau virtuel principal*. Pour en savoir plus, consultez [Autoriser l'accès à partir d'un réseau virtuel](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- Un réseau virtuel associé qui se trouve dans la même région que le réseau virtuel principal.

  Vous devrez configurer votre compte de stockage pour autoriser l’accès à ce réseau virtuel associé. Pour en savoir plus, consultez [Autoriser l'accès à partir d'un réseau virtuel](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- Un réseau local connecté à votre réseau virtuel principal à l’aide d'une [passerelle VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) ou d’une [passerelle ExpressRoute](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md). 

  Pour en savoir plus, consultez [Configuration de l’accès à partir de réseaux locaux](../common/storage-network-security.md#configuring-access-from-on-premises-networks).

- Un réseau local connecté à un réseau associé.

  Cette opération peut être effectuée avec une [passerelle VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) ou une [passerelle ExpressRoute](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md) ainsi qu’avec le [transit de la passerelle](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit). 

> [!IMPORTANT]
> Si vous vous connectez à partir d’un réseau local, assurez-vous que votre client autorise les communications sortantes via les ports 111 et 2048. Le protocole NFS 3.0 utilise ces ports.

<a id="azure-storage-features-not-yet-supported"></a>

## <a name="support-for-azure-storage-features"></a>Prise en charge des fonctionnalités du Stockage Azure

Le tableau suivant indique le niveau actuel de prise en charge des fonctionnalités du Stockage Azure dans les comptes pour lesquels la fonctionnalité NFS 3.0 est activée. 

L’état des éléments figurant dans ce tableau évolue au fil du temps, car la prise en charge continue de s’étendre.

| Fonctionnalité Stockage | Premium | standard |Fonctionnalité Stockage | Premium | standard |
|-----------------|---------|----------|----------------|---------|----------|
| [API REST du service Blob](/rest/api/storageservices/blob-service-rest-api)  | ✔️ |  ✔️ | [API REST Azure Data Lake Storage](/rest/api/storageservices/data-lake-storage-gen2) | ✔️ |  ✔️ |
| [Niveaux d’accès de Stockage Blob Azure](storage-blob-storage-tiers.md) |    ✔️ |    ✔️ | [Étiquettes d’index d’objet blob](storage-blob-index-how-to.md) |  ⛔ | ⛔ |
| [Gestion de cycle de vie du Stockage Blob Azure](storage-lifecycle-management-concepts.md) | ✔️  |   ✔️ | [Journalisation Azure Storage Analytics](../common/storage-analytics-logging.md?toc=/azure/storage/blobs/toc.json) | ⛔ |  ⛔ |
|  [Inventaire des objets blob du Stockage Azure](blob-inventory.md) |  ✔️  |   ✔️ | [Modifier le flux](storage-blob-change-feed.md) |   ⛔ | ⛔ |
| [Azure Monitor](monitor-blob-storage.md) |    ✔️ |    ✔️ | [Contrôle de version des blobs](versioning-enable.md) | ⛔ |  ⛔ |
| [Instantanés d’objet blob](snapshots-overview.md) | ✔️  |   ✔️ | [Restauration dans le temps pour les objets blob de blocs](point-in-time-restore-overview.md) | ⛔ |   ⛔ |
| [Points de terminaison privés](../common/storage-private-endpoints.md?toc=/azure/storage/blobs/toc.json) | ✔️  | ✔️ | [Intégration à la Sauvegarde Azure](../../backup/blob-backup-overview.md) | ⛔ | ⛔ |
| [Points de terminaison de service](../../virtual-network/virtual-network-service-endpoints-overview.md) | ✔️  |  ✔️ | [Suppression réversible des conteneurs](soft-delete-container-overview.md) |  ⛔ | ⛔ |
| [Règles de pare-feu](../common/storage-network-security.md?toc=/azure/storage/blobs/toc.json) | ✔️  | ✔️ | [Suppression réversible pour les objets blob](soft-delete-blob-overview.md) |    ⛔ | ⛔ |
| [Désactivation de l’autorisation Shared Key](../common/shared-key-authorization-prevent.md)  | ✔️ |    ✔️ | [Suivi de l’heure du dernier accès pour la gestion de cycle de vie](storage-lifecycle-management-concepts.md#move-data-based-on-last-accessed-date-preview) | ⛔|  ⛔ |
| [Clés gérées par le client pour le chiffrement du stockage Azure](../common/customer-managed-keys-overview.md) |   ✔️ |    ✔️ | [Clés fournies par le client pour le chiffrement du Stockage Azure](encryption-customer-provided-keys.md)  | ⛔ | ⛔ |
| [Stockage Blob immuable](storage-blob-immutable-storage.md) | ✔️    | ✔️ | [Hébergement de sites web statiques](storage-blob-static-website.md) |    ⛔  |    ⛔ |
| [Objets blob d’ajout](storage-blobs-introduction.md#blobs) | ✔️   |  ✔️ | [Objets blob de pages](storage-blobs-introduction.md#blobs) | ⛔ |    ⛔ |
| [Sécurité d’Azure Active Directory (AD)](../common/storage-auth-aad.md?toc=/azure/storage/blobs/toc.json) | ⛔ | ⛔ | [Étendues de chiffrement](encryption-scope-overview.md)  |    ⛔ | ⛔ |
| [Réplication d'objets blob de blocs](object-replication-overview.md) | ⛔  |   ⛔ | [Basculement de compte géré par le client](../common/storage-disaster-recovery-guidance.md?toc=/azure/storage/blobs/toc.json) | ⛔ |    ⛔ |
| [Événements de stockage Blob](storage-blob-event-overview.md)| ⛔ |    ⛔ 

  
## <a name="known-issues"></a>Problèmes connus

- La prise en charge NFS 3.0 ne peut pas être activée sur les comptes de stockage existants.

- Une fois activée, la prise en charge NFS 3.0 ne peut pas être désactivée dans un compte de stockage.

### <a name="nfs-30-features-not-yet-supported"></a>Fonctionnalités NFS 3.0 pas encore prises en charge

Les fonctionnalités NFS 3.0 suivantes ne sont pas encore prises en charge.

- NFS 3.0 sur UDP. Seul NFS 3.0 sur TCP est pris en charge.

- Verrouillage des fichiers avec NLM (Network Lock Manager). Les commandes de montage doivent inclure le paramètre `-o nolock`.

- Montage des sous-répertoires. Vous pouvez monter uniquement le répertoire racine (conteneur).

- Répertorier les montages (par exemple : à l’aide de la commande `showmount -a`)

- Répertorier les exportations (par exemple : à l’aide de la commande `showmount -e`)

- Lien physique

- Exportation d’un conteneur en lecture seule

### <a name="nfs-30-clients-not-yet-supported"></a>Les clients NFS 3.0 pas encore pris en charge

Les clients NFS 3.0 suivants ne sont pas encore pris en charge.

- Client Windows pour NFS

## <a name="pricing"></a>Tarifs

Pendant la préversion, les données stockées dans votre compte de stockage sont facturées selon le même taux de capacité que le stockage Blob par Go par mois. 

Une transaction n’est pas facturée pendant la préversion. La tarification des transactions est sujette à modification et sera déterminée lorsqu’elle sera en disponibilité générale.

## <a name="next-steps"></a>Étapes suivantes

- Pour démarrer, consultez [Monter le stockage Blob sur Linux à l’aide du protocole NFS (Network File System) 3.0 (préversion)](network-file-system-protocol-support-how-to.md).

- Pour optimiser les performances, consultez [Considérations relatives aux performances de NFS (Network File System) 3.0 dans Stockage Blob Azure (préversion)](network-file-system-protocol-support-performance.md).
