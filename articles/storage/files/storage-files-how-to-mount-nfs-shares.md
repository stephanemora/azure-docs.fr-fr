---
title: Monter un partage de fichiers Azure NFS - Azure Files
description: Apprenez à monter un partage NFS (Network File System).
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 530ae82720e6b4eb6a3e4d1021c0b37b9f4dbf5c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90707439"
---
# <a name="how-to-mount-an-nfs-file-share"></a>Monter un partage de fichiers NFS

[Azure Files](storage-files-introduction.md) est le système de fichiers cloud facile à utiliser de Microsoft. Les partages de fichiers Azure peuvent être montés dans des distributions Linux à l'aide du protocole SMB (Server Message Block) ou du protocole NFS (Network File System). Cet article est consacré au montage avec NFS. Pour en savoir plus sur le montage avec SMB, consultez [Utiliser Azure Files avec Linux](storage-how-to-use-files-linux.md). Pour plus d'informations sur chacun des protocoles disponibles, consultez [Protocoles de partage de fichiers Azure](storage-files-compare-protocols.md).

## <a name="limitations"></a>Limites

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Disponibilité régionale

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Prérequis

- [Créez un partage NFS](storage-files-how-to-create-nfs-shares.md).

    > [!IMPORTANT]
    > Les partages NFS ne sont accessibles qu'à partir de réseaux approuvés. Les connexions à votre partage NFS doivent provenir de l'une des sources suivantes :

- Utilisez l'une des solutions de mise en réseau suivantes :
    - [Créez un point de terminaison privé](storage-files-networking-endpoints.md#create-a-private-endpoint) (recommandé) ou [restreignez l'accès à votre point de terminaison public](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Configurez un VPN point à site (P2S) sous Linux pour une utilisation avec Azure Files](storage-files-configure-p2s-vpn-linux.md).
    - [Configurez un VPN site à site pour une utilisation avec Azure Files](storage-files-configure-s2s-vpn.md).
    - Configurez [ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="disable-secure-transfer"></a>Désactiver le transfert sécurisé

1. Connectez-vous au portail Azure et accédez au compte de stockage contenant le partage NFS que vous avez créé.
1. Sélectionnez **Configuration**.
1. Sous **Transfert sécurisé requis**, sélectionnez **Désactivé**.
1. Sélectionnez **Enregistrer**.

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Capture de l'écran de configuration du compte de stockage, sur lequel le transfert sécurisé est désactivé. ":::

## <a name="mount-an-nfs-share"></a>Monter un partage NFS

1. Une fois le partage de fichiers créé, sélectionnez-le et choisissez **Se connecter à partir de Linux**.
1. Entrez le chemin de montage que vous souhaitez utiliser, puis copiez le script.
1. Connectez-vous à votre client et utilisez le script de montage fourni.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="Capture de l'écran de configuration du compte de stockage, sur lequel le transfert sécurisé est désactivé. ":::

Votre partage NFS est désormais monté.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d'informations sur Azure Files, consultez notre article [Planifier un déploiement Azure Files](storage-files-planning.md).
- Si vous rencontrez des problèmes, consultez [Résoudre les problèmes liés aux partages de fichiers Azure NFS](storage-troubleshooting-files-nfs.md).