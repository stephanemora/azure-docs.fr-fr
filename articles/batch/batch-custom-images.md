---
title: Provisionner un pool personnalisé à partir d’une image managée
description: Créez un pool Batch à partir d’une ressource d’image managée pour approvisionner les nœuds de calcul avec les logiciels et les données pour votre application.
ms.topic: article
ms.date: 09/16/2019
ms.openlocfilehash: 10e3932bc6006e1d91fbc7e4cf58a5d98c043520
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82117316"
---
# <a name="use-a-managed-image-to-create-a-pool-of-virtual-machines"></a>Utiliser une image managée pour créer un pool de machines virtuelles

Pour créer une image personnalisée destinée aux machines virtuelles de votre pool Batch, vous pouvez utiliser [Shared Image Gallery](batch-sig-images.md) ou une ressource d’*image managée*.

> [!TIP]
> Dans la plupart des cas, vous devez créer des images personnalisées à l’aide de Shared Image Gallery. À l’aide de Shared Image Gallery, vous pouvez approvisionner des pools plus rapidement, mettre à l’échelle de grandes quantités de machines virtuelles et améliorer la fiabilité lors de la configuration des machines virtuelles. Pour en savoir plus, consultez [Utiliser Shared Image Gallery pour créer un pool personnalisé](batch-sig-images.md).

## <a name="prerequisites"></a>Prérequis

- **Une ressource d’image managée**. Pour créer un pool de machines virtuelles à l’aide d’une image personnalisée, vous devez avoir ou créer une ressource d’image managée dans le même abonnement et la même région Azure que le compte Batch. L’image doit être créée à partir d’instantanés du disque de système d’exploitation de la machine virtuelle et, éventuellement, ses disques de données associés. Pour plus d’informations et connaître les étapes de préparation d’une image managée, consultez la section suivante.
  - Utilisez une image personnalisée unique pour chaque pool que vous créez.
  - Pour créer un pool avec l’image à l’aide des API Batch, spécifiez **l’ID de ressource** de l’image, qui est au format `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. Pour utiliser le portail, utilisez le **nom** de l’image.  
  - La ressource d’image managée doit exister pour faire monter la durée de vie du pool en puissance. Elle peut être supprimée une fois que le pool est supprimé.

- **Authentification Azure Active Directory (AAD)** . L’API du client Batch doit utiliser l’authentification AAD. La prise en charge d’Azure Batch pour AAD est documentée dans [Authentifier les solutions de service Batch avec Active Directory](batch-aad-auth.md).

## <a name="prepare-a-custom-image"></a>Préparer une image personnalisée

Dans Azure, vous pouvez préparer une image managée à partir de ce qui suit :

- Captures instantanées du système d'exploitation et des disques de données d'une machine virtuelle Azure
- Machine virtuelle Azure généralisée dotée de disques managés
- Disque dur virtuel local généralisé chargé dans le cloud

Pour mettre à l’échelle des pools Batch de manière fiable avec une image personnalisée, nous vous recommandons de créer une image managée *uniquement* à l’aide de la première méthode: en utilisant des captures instantanées des disques de la machine virtuelle. Consultez les étapes suivantes pour préparer une machine virtuelle, prendre un instantané et créer une image à partir de l’instantané.

### <a name="prepare-a-vm"></a>Préparer une machine virtuelle

Si vous créez une machine virtuelle pour l'image, utilisez une image propriétaire de la Place de Marché Azure prise en charge par Batch comme image de base pour votre image managée. Seules les images propriétaires peuvent être utilisées comme image de base. Pour obtenir la liste complète des références d'image de la Place de marché Azure prises en charge par Azure Batch, consultez l'opération [Lister les références SKU d'agent de nœud](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus).

> [!NOTE]
> Vous ne pouvez pas, comme image de base, utiliser une image de fournisseurs tiers qui comporte des conditions de licence et d’achat supplémentaires. Pour plus d’informations sur ces images de la Place de marché, consultez les recommandations émises pour les machines virtuelles [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) ou [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
).

- Assurez-vous que la machine virtuelle est créée avec un disque managé. Il s’agit du paramètre de stockage par défaut quand vous créez une machine virtuelle.
- N’installez pas d’extensions Azure, comme l’extension de script personnalisé, sur la machine virtuelle. Si l’image contient une extension préinstallée, Azure peut rencontrer des problèmes lors du déploiement du pool Batch.
- Lorsque vous utilisez des disques de données attachés, vous devez monter et formater les disques à partir d'une machine virtuelle pour les utiliser.
- Vérifiez que l’image du système d’exploitation de base que vous fournissez utilise le lecteur temporaire par défaut. L’agent de nœud Batch s’attend actuellement à ce que le lecteur temporaire par défaut soit utilisé.
- Une fois que la machine virtuelle s’exécute, connectez-la via le protocole RDP (pour Windows) ou SSH (pour Linux). Le cas échéant, installez les logiciels nécessaires ou copiez les données souhaitées.  

### <a name="create-a-vm-snapshot"></a>Créer un instantané de la machine virtuelle

Une capture instantanée est une copie complète en lecture seule d’un disque dur virtuel. Pour créer un instantané des disques de système d’exploitation ou de données d’une machine virtuelle, vous pouvez utiliser les outils en ligne de commande ou le portail Azure. Pour les étapes et les options permettant de créer un instantané, consultez les conseils pour les machines virtuelles [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) ou [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md).

### <a name="create-an-image-from-one-or-more-snapshots"></a>Créer une image à partir d’un ou plusieurs instantanés

Pour créer une image managée à partir d’un instantané, utilisez les outils en ligne de commande Azure comme la commande [az image création](/cli/azure/image). Vous pouvez créer une image en spécifiant l’instantané d’un disque du système d’exploitation et éventuellement un ou plusieurs instantanés de disque de données.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Créer un pool à partir d’une image personnalisée dans le portail

Une fois que vous avez enregistré votre image personnalisée et que vous connaissez son nom ou son ID de ressource, créez un pool Batch à partir de cette image. Les étapes suivantes montrent comment créer un pool à partir du portail Azure.

> [!NOTE]
> Si vous créez le pool à l’aide des API Batch, vérifiez que l’identité que vous utilisez pour l’authentification AAD dispose des autorisations d’accès à la ressource d’image. Consultez [Authentifier des solutions de service Batch avec Active Directory](batch-aad-auth.md).
>
> La ressource destinée à l'image managée doit exister pendant toute la durée de vie du pool. Si la ressource sous-jacente est supprimée, le pool ne peut pas être mis à l'échelle.

1. Accédez à votre compte  Batch dans le portail Azure. Ce compte doit relever du même abonnement et de la même région que le groupe de ressources contenant l’image personnalisée.
2. Dans la fenêtre **Paramètres** située à gauche, sélectionnez l’élément de menu **Pools**.
3. Dans la fenêtre **Pools**, sélectionnez la commande **Ajouter**.
4. Dans la fenêtre **Ajouter un pool**, sélectionnez **Image personnalisée (Linux/Windows)** dans la liste déroulante **Type d’image**. Dans la liste déroulante **Image de machine virtuelle personnalisée**, sélectionnez le nom de l’image (forme abrégée de l’ID de ressource).
5. Sélectionnez le **Serveur de publication/Offre/Référence (SKU)** correspondant à votre image personnalisée.
6. Spécifiez les autres paramètres obligatoires, notamment **Taille de nœud**, **Nœuds dédiés cibles** et **Nœuds basse priorité** et éventuellement les paramètres facultatifs.

    Par exemple, pour une image personnalisée Microsoft Windows Server Datacenter 2016, la fenêtre **Ajouter un pool** s’affiche comme suit :

    ![Ajouter un pool à partir d’une image Windows personnalisée](media/batch-custom-images/add-pool-custom-image.png)
  
Pour vérifier si un pool existant est basé sur une image personnalisée, consultez la propriété **Système d’exploitation** dans la section récapitulative des ressources de la fenêtre **Pool**. Si le pool a été créé à partir d’une image personnalisée, sa valeur est **Image de machine virtuelle personnalisée**.

Toutes les images personnalisées associées à un pool sont affichées dans la fenêtre **Propriétés** de ce dernier.

## <a name="considerations-for-large-pools"></a>Considérations relatives aux grands pools

Si vous envisagez de créer un pool avec des centaines de machines virtuelles ou plus, à l’aide d’une image personnalisée, il est important de suivre les instructions précédentes pour utiliser une image créée à partir d’un instantané de machine virtuelle.

Tenez également compte des points suivants :

- **Limites de taille** : Batch limite la taille du pool à 2 500 nœuds de calcul dédiés ou 1 000 nœuds de faible priorité, lorsque vous utilisez une image personnalisée.

  Si vous utilisez la même image (ou plusieurs images basées sur le même instantané sous-jacent) pour créer plusieurs pools, les nœuds de calcul totaux dans les pools ne peuvent pas dépasser les limites précédentes. Nous ne recommandons pas l’utilisation d’une image ou de son instantané sous-jacent au-delà d’un seul pool.

  Les limites peuvent être réduites si vous configurez le pool avec des [pools NAT de trafic entrant](pool-endpoint-configuration.md).

- **Redimensionner le délai d’attente** : si votre pool contient un nombre de nœuds fixe (pas de mise à l’échelle), augmentez la propriété de l’élément resizeTimeout du pool jusqu’à une valeur telle que 20 à 30 minutes. Si votre pool n’atteint pas sa taille cible dans le délai imparti, effectuez une autre [opération de redimensionnement](/rest/api/batchservice/pool/resize).

  Si vous prévoyez un pool avec plus de 300 nœuds de calcul, vous devrez peut-être redimensionner le pool plusieurs fois afin d’atteindre la taille cible.
  
À l’aide de [Shared Image Gallery](batch-sig-images.md), vous pouvez créer des pools plus grands avec vos images personnalisées ainsi qu’avec d’autres réplicas d’image partagée. À l’aide d’images partagées, le pool atteint jusqu’à 25 % plus rapidement l’état stable et la latence d’inactivité de la machine virtuelle est inférieure à 30 %.

## <a name="considerations-for-using-packer"></a>Considérations relatives à l'utilisation de Packer

Pour créer directement une ressource d'image managée à l'aide de Packer, vous devez impérativement utiliser un compte Batch en mode Abonnement utilisateur. Avec un compte en mode Service Batch, vous devez d'abord créer un disque dur virtuel, puis l'importer dans une ressource d'image managée. En fonction du mode d'allocation de pool (Abonnement utilisateur ou Service Batch), la procédure de création d'une ressource d'image managée varie.

Veillez à ce que la ressource utilisée pour créer l'image managée existe pendant toute la durée de vie du pool qui référence l'image personnalisée, faute de quoi des échecs d'allocation de pool et/ou des échecs de redimensionnement peuvent survenir.

Si l'image ou la ressource sous-jacente est supprimée, une erreur semblable à la suivante peut apparaître : `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`. Si vous obtenez cette erreur, assurez-vous que la ressource sous-jacente n’a pas été supprimée.

Pour plus d'informations sur l'utilisation de Packer pour créer une machine virtuelle, consultez [Créer une image Linux avec Packer](../virtual-machines/linux/build-image-with-packer.md) ou [Créer une image Windows avec Packer](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une présentation détaillée de Batch, consultez [Développer des solutions de calcul parallèles à grande échelle avec Batch](batch-api-basics.md).
