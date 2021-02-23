---
title: Se connecter à un compte de stockage sécurisé à partir de votre espace de travail Azure Synapse
description: Cet article explique comment se connecter à un compte de stockage sécurisé à partir de votre espace de travail Azure Synapse.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 02/10/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 599cf17e1ab2b85aac77893e8b2d520d412e1cea
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100416588"
---
# <a name="connect-to-a-secure-azure-storage-account-from-your-synapse-workspace"></a>Se connecter à un compte de stockage Azure sécurisé à partir de votre espace de travail Azure Synapse

Cet article explique comment se connecter à un compte de stockage Azure sécurisé à partir de votre espace de travail Microsoft Azure Synapse Analytics. Vous pouvez lier un compte de stockage Azure à votre espace de travail Azure Synapse lorsque vous créez votre espace de travail. Vous pouvez lier davantage de comptes de stockage après avoir créé votre espace de travail.


## <a name="secured-azure-storage-accounts"></a>Sécuriser des comptes de stockage Azure
Le stockage Azure fournit un modèle de sécurité à plusieurs niveaux qui vous permet de sécuriser et de contrôler l’accès à vos comptes de stockage. Vous pouvez configurer des règles de pare-feu IP pour permettre au trafic de certaines plages d’adresses IP publiques d’accéder à votre compte de stockage. Vous pouvez également configurer des règles de réseau pour autoriser le trafic de certains réseaux virtuels à accéder à votre compte de stockage. Vous pouvez combiner sur le même compte de stockage des règles de pare-feu IP qui autorisent l’accès à partir de certaines plages d’adresses IP et des règles de réseau qui accordent l’accès à partir de certains réseaux virtuels. Ces règles s’appliquent s’appliquent au point de terminaison public d’un compte de stockage. Vous n’avez pas besoin de règles d’accès pour autoriser le trafic entre les points de terminaison privés gérés créés dans votre espace de travail et un compte de stockage. Les règles de pare-feu de stockage peuvent être appliquées aux comptes de stockage existants ou aux nouveaux comptes de stockage lorsque vous les créez. Vous pouvez en savoir plus sur la sécurisation de votre compte de stockage [ici](https://docs.microsoft.com/azure/storage/common/storage-network-security).

## <a name="synapse-workspaces-and-virtual-networks"></a>Espaces de travail Azure Synapse et réseaux virtuels
Lorsque vous créez un espace de travail Azure Synapse, vous pouvez choisir d’y associer un réseau virtuel géré. Si vous n’activez pas le réseau virtuel géré pour votre espace de travail lorsque vous le créez, votre espace de travail se trouve dans un réseau virtuel partagé avec d’autres espaces de travail Azure Synapse auxquels aucun réseau virtuel géré n’est associé. Si vous avez activé le réseau virtuel géré lors de la création de l’espace de travail, votre espace de travail est associé à un réseau virtuel dédié géré par Azure Synapse. Ces réseaux virtuels ne sont pas créés dans votre abonnement client. Par conséquent, vous ne pourrez pas accorder au trafic de ces réseaux virtuels l’accès à votre compte de stockage sécurisé en utilisant les règles de réseau décrites ci-dessus.  

## <a name="access-a-secured-storage-account"></a>Accéder à un compte de stockage sécurisé
Azure Synapse fonctionne à partir de réseaux qui ne peuvent pas être inclus dans vos règles de réseau. Vous devez effectuer les opérations suivantes pour permettre l’accès à votre compte de stockage sécurisé depuis votre espace de travail :

* Créez un espace de travail Azure Synapse avec un réseau virtuel géré qui lui est associé et créez des points de terminaison privés gérés entre celui-ci et le compte de stockage sécurisé.
* Accordez à votre espace de travail Azure Synapse l’accès à votre compte de stockage sécurisé en tant que service Azure approuvé. En tant que service de confiance, Azure Synapse utilisera alors une authentification forte pour se connecter en toute sécurité à votre compte de stockage.   

### <a name="create-a-synapse-workspace-with-a-managed-virtual-network-and-create-managed-private-endpoints-to-your-storage-account"></a>Créer un espace de travail Azure Synapse avec un réseau virtuel géré et créer des points de terminaison privés gérés sur votre compte de stockage
Vous pouvez suivre [ces étapes](./synapse-workspace-managed-vnet.md) pour créer un espace de travail Azure Synapse auquel est associé un réseau virtuel géré. Une fois l’espace de travail et le réseau virtuel géré associés créés, vous pouvez créer un point de terminaison privé géré sur votre compte de stockage sécurisé en suivant les étapes répertoriées [ici](./how-to-create-managed-private-endpoints.md). 

### <a name="grant-your-azure-synapse-workspace-access-to-your-secure-storage-account-as-a-trusted-azure-service"></a>Accorder à votre espace de travail Azure Synapse l’accès à votre compte de stockage sécurisé en tant que service Azure approuvé
Les capacités analytiques, telles que les pools SQL dédiés et les pools SQL serverless, utilisent une infrastructure mutualisée qui n’est pas déployée dans le réseau virtuel géré. Pour que le trafic provenant de ces capacités puisse accéder au compte de stockage sécurisé, vous devez configurer l’accès à votre compte de stockage en fonction de l’identité managée affectée par le système de l’espace de travail en suivant les étapes ci-dessous.

Dans Portail Azure, accédez à votre compte de stockage sécurisé. Sélectionnez **Mise en réseau** dans le volet de navigation de gauche. Dans la section **Instances de ressource**, sélectionnez *Microsoft.Synapse/workspaces* comme **type de ressource**, puis entrez le nom de votre espace de travail pour **Nom de l’instance**. Sélectionnez **Enregistrer**.

![Configuration réseau du compte de stockage.](./media/connect-to-a-secure-storage-account/secured-storage-access.png)

Vous devriez maintenant pouvoir accéder à votre compte de stockage sécurisé à partir de l’espace de travail.


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [Réseau virtuel d’espace de travail managé](./synapse-workspace-managed-vnet.md).

En savoir plus sur les [Points de terminaison privés managés](./synapse-workspace-managed-private-endpoints.md).
