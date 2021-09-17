---
title: Automanage pour Windows Server Services (préversion)
description: Présentation de Automanage pour les Services et fonctionnalités Windows Server avec Windows Server Azure Edition
author: nwashburn-ms
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 07/09/2021
ms.author: niwashbu
ms.openlocfilehash: 09b011d76a570aaed1a9ea8c0b9bdc74e80eca53
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772185"
---
# <a name="automanage-for-windows-server-services-preview"></a>Automanage pour Windows Server Services (préversion)

Automanage pour Windows Server Services offre de nouvelles fonctionnalités spécifiques à _Windows Server Azure Edition_.  Ces fonctionnalités sont les suivantes :
- Mise à jour corrective à chaud
- SMB sur QUIC
- Réseau étendu

> [!IMPORTANT]
> Automanage pour Windows Server Services est actuellement en préversion publique. Une procédure de consentement est requise pour utiliser la fonctionnalité de Patch à chaud (Hotpatch) décrite ci-dessous.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Hotpatch peut être évalué sur _Windows Server 2022 Datacenter : édition Azure (Core) Preview_.  Hotpatch sur _Windows Server 2019 Datacenter: Azure Edition Preview_ n’est plus disponible pour évaluation.

Vous trouverez les fonctionnalités Windows Server dans une ou plusieurs de ces images de _Windows Server Azure Edition_ : 

- Centre de données Windows Server 2022 : Azure Edition (expérience Bureau)
- Centre de données Windows Server 2022 : Azure Edition (Core)

Les fonctionnalités varient selon les images, consultez [démarrage](#getting-started-with-windows-server-azure-edition) pour plus d’informations.

## <a name="automanage-for-windows-server-capabilities"></a>Automanage pour les fonctionnalités Windows Server

### <a name="hotpatch"></a>Mise à jour corrective à chaud

Hotpatch est disponible en préversion publique sur les images suivantes :

- Centre de données Windows Server 2022 : Azure Edition (Core)

Hotpatch vous donne la possibilité d’appliquer des mises à jour de sécurité sur votre machine virtuelle sans redémarrage.  En outre, Automanage pour Windows Server automatise l’intégration, la configuration et l’orchestration de la mise à jour corrective à chaud.  Pour en savoir plus, consultez [Patch à chaud](automanage-hotpatch.md).  

### <a name="smb-over-quic"></a>SMB sur QUIC

SMB sur QUIC est disponible en préversion publique sur les images suivantes :

- Centre de données Windows Server 2022 : Azure Edition (expérience Bureau)
- Centre de données Windows Server 2022 : Azure Edition (Core)

SMB sur QUIC permet aux utilisateurs d’accéder aux fichiers lorsqu’ils travaillent à distance sans VPN, par trafic SMB en tunneling via le protocole QUIC.  Pour en savoir plus, consultez [SMB sur QUIC](/windows-server/storage/file-server/smb-over-quic).  

### <a name="azure-extended-network"></a>Réseau étendu Azure

Le réseau étendu Azure est disponible en préversion publique sur les images suivantes :

- Centre de données Windows Server 2022 : Azure Edition (expérience Bureau)
- Centre de données Windows Server 2022 : Azure Edition (Core)

Le réseau étendu Azure vous permet d’étirer un sous-réseau local dans Azure pour permettre aux machines virtuelles locales de conserver leurs adresses IP privées locales d’origine lors de la migration vers Azure. Pour en savoir plus, consultez [Réseau étendu Azure](/windows-server/manage/windows-admin-center/azure/azure-extended-network).  


## <a name="getting-started-with-windows-server-azure-edition"></a>Bien démarrer Windows Server Azure Edition

il est important de prendre en compte l’amont, quelles fonctionnalités d’Automanage pour Windows Server vous souhaitez utiliser, puis de choisir une image de machine virtuelle correspondante qui prend en charge toutes ces fonctionnalités.  Certaines des images de _Windows Server Azure Edition_ ne prennent en charge qu’un sous-ensemble de fonctionnalités, pour plus d’informations, consultez la table ci-dessous.

### <a name="deciding-which-image-to-use"></a>Décider quelle image utiliser 

|Image|Fonctionnalités|
|--|--|
|Centre de données Windows Server 2022 : Azure Edition (expérience Bureau) | SMB sur QUIC, réseau étendu | 
| Centre de données Windows Server 2022 : Azure Edition (Core) | Hotpatch, SMB sur QUIC, réseau étendu | 

### <a name="creating-a-vm"></a>Création d’une machine virtuelle

Pour commencer à utiliser Automanage pour les fonctionnalités Windows Server sur une nouvelle machine virtuelle, utilisez la méthode de votre choix pour créer une machine virtuelle Azure, puis sélectionnez l’image _Windows Server Azure Edition_ qui correspond à l’ensemble de [fonctionnalités](#getting-started-with-windows-server-azure-edition) que vous souhaitez utiliser.  La configuration de ces fonctionnalités peut être nécessaire lors de la création de la machine virtuelle. Vous pouvez en savoir plus sur la configuration des machines virtuelles dans les rubriques relatives aux fonctionnalités individuelles (par exemple, [Hotpatch](automanage-hotpatch.md)).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur Azure Automanage](automanage-virtual-machines.md)