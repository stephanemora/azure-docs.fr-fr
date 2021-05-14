---
title: Modifier votre topologie Azure File Sync | Microsoft Docs
description: Conseils sur la modification de votre topologie de synchronisation Azure File Sync
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 4/23/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4977b56265f542f8f8ff13693abb468bc695a384
ms.sourcegitcommit: 18cd3c1c8cc47258c6a1a04e0e03d6248c52ef24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107992728"
---
# <a name="modify-your-azure-file-sync-topology"></a>Modifier votre topologie Azure File Sync

Cet article présente les méthodes les plus courantes que les clients peuvent utiliser pour modifier leur topologie Azure File Sync ainsi que nos recommandations en la matière. Si vous souhaitez modifier votre topologie Azure File Sync, veuillez suivre les meilleures pratiques ci-dessous afin d'éviter les erreurs et/ou les pertes de données potentielles.

## <a name="migrate-a-server-endpoint-to-a-different-azure-file-sync-storage-sync-service"></a>Migrer un point de terminaison de serveur vers un autre Service de synchronisation de stockage Azure File Sync

Après vous être assuré que vos données sont à jour sur votre serveur local, déprovisionnez votre point de terminaison de serveur. Pour obtenir des conseils, consultez [Déprovisionner votre point de terminaison de serveur Azure File Sync](./file-sync-deprovision-server-endpoint.md#scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share). Puis reprovisionnez-le dans le groupe de synchronisation et dans le Service de synchronisation de stockage souhaités.

Si vous souhaitez migrer tous les points de terminaison de serveur associés à un serveur vers un autre groupe de synchronisation ou un autre Service de synchronisation de stockage, consultez [Déprovisionner tous les points de terminaison de serveur associés à un serveur inscrit](#deprovision-all-server-endpoints-associated-with-a-registered-server).

## <a name="change-the-granularity-of-a-server-endpoint"></a>Modifier la granularité d'un point de terminaison de serveur

Après avoir confirmé que vos données sont à jour sur votre serveur local (voir [Déprovisionner votre point de terminaison de serveur Azure File Sync](./file-sync-deprovision-server-endpoint.md#scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share)), déprovisionnez votre point de terminaison de serveur. Puis reprovisionnez-le selon la granularité souhaitée.

## <a name="deprovision-azure-file-sync-topology"></a>Déprovisionner une topologie Azure File Sync

Les ressources Azure File Sync doivent être déprovisionnées dans un ordre spécifique : points de terminaison de serveur, groupe de synchronisation, puis Service de stockage. Bien que l'ensemble du flux soit documenté ci-dessous, vous pouvez vous arrêter au niveau de votre choix. 

Tout d'abord, accédez à la ressource Service de synchronisation de stockage sur le portail Azure, puis sélectionnez un groupe de synchronisation. Suivez les étapes décrites dans [Déprovisionner votre point de terminaison de serveur Azure File Sync](./file-sync-deprovision-server-endpoint.md) pour garantir l'intégrité et la disponibilité des données lors de la suppression des points de terminaison de serveur. Pour déprovisionner votre groupe de synchronisation ou votre Service de synchronisation de stockage, tous les points de terminaison de serveur doivent être supprimés. Si votre objectif est uniquement de supprimer des points de terminaison de serveur spécifiques, vous pouvez vous arrêter ici. 

Après avoir supprimé tous les points de terminaison de serveur du groupe de synchronisation, supprimez le point de terminaison cloud. 

Puis supprimez le groupe de synchronisation 

Répétez ces étapes pour tous les groupes de synchronisation que vous souhaitez supprimer dans le Service de synchronisation de stockage. Une fois tous les groupes de synchronisation de ce Service de synchronisation de stockage supprimés, supprimez la ressource Service de synchronisation de stockage.

## <a name="rename-a-server-endpoint-path-or-sync-group"></a>Renommer un chemin de point de terminaison de serveur ou un groupe de synchronisation

Cela n’est pas pris en charge. 

Si vous utilisez actuellement le lecteur D et que vous envisagez de migrer vers le cloud, consultez [Transformer le lecteur D: d'une machine virtuelle en disque de données - Machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/windows/change-drive-letter).

## <a name="deprovision-all-server-endpoints-associated-with-a-registered-server"></a>Déprovisionner tous les points de terminaison de serveur associés à un serveur inscrit

Pour vous assurer que vos données sont sécurisées et entièrement mises à jour avant le déprovisionnement, consultez [Déprovisionner votre point de terminaison de serveur Azure File Sync](./file-sync-deprovision-server-endpoint.md).

Naviguez vers votre ressource Service de synchronisation de stockage et accédez à l'onglet Serveurs inscrits. Sélectionnez le serveur que vous souhaitez désinscrire et choisissez **Désinscrire le serveur**. Tous les points de terminaison de serveur associés à ce serveur sont alors rapidement déprovisionnés.

## <a name="next-steps"></a>Étapes suivantes
* [Déprovisionner votre point de terminaison de serveur Azure File Sync](./file-sync-deprovision-server-endpoint.md)



