---
title: Attacher des pools de disques à des hôtes Azure VMware Solution (préversion)
description: Découvrez comment attacher un pool de disques exposé par le biais d’une cible iSCSI comme magasin de données VMware d’un cloud privé Azure VMware Solution. Une fois que le magasin de données est configuré, vous pouvez y créer des volumes et les attacher à votre instance VMware.
ms.topic: how-to
ms.date: 07/13/2021
ms.openlocfilehash: fefb014f221a121259c0b8d6411de362e11a63c0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532526"
---
# <a name="attach-disk-pools-to-azure-vmware-solution-hosts-preview"></a>Attacher des pools de disques à des hôtes Azure VMware Solution (préversion)

Les [pools de disques Azure](../virtual-machines/disks-pools.md) offrent un stockage de bloc persistant pour les applications et les charges de travail reposant sur des disques Azure. Vous pouvez utiliser des disques comme stockage persistant pour Azure VMware Solution et ainsi bénéficier d’une solution optimale en termes de coût et de performances. Par exemple, vous pouvez effectuer un scale-up en utilisant des pools de disques plutôt que de mettre à l’échelle des clusters si vous hébergez des charges de travail gourmandes en stockage. Vous pouvez également utiliser des disques pour répliquer des données à partir d’environnements VMware locaux ou principaux vers un stockage sur disque pour le site secondaire. Pour mettre à l’échelle le stockage indépendamment des hôtes Azure VMware Solution, nous prenons en charge l’exposition des [disques Ultra](../virtual-machines/disks-types.md#ultra-disk) et des [disques SSD Premium](../virtual-machines/disks-types.md#premium-ssd) comme magasins de données.  

>[!IMPORTANT]
>Les pools de disques Azure sur Azure VMware Solution (préversion) sont actuellement en préversion publique.
>Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
>Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les disques managés Azure sont attachés à une machine virtuelle de contrôleur iSCSI déployée dans le groupe de ressources Azure VMware Solution. Les disques sont déployés comme cibles de stockage sur un pool de disques et chaque cible de stockage se présente sous la forme d’un numéro d’unité logique iSCSI sous la cible iSCSI. Vous pouvez exposer un pool de disques comme cible iSCSI connectée à des hôtes Azure VMware Solution comme magasin de données. Un pool de disques est exposé comme point de terminaison unique pour tous les disques sous-jacents ajoutés comme cibles de stockage. Chaque pool de disques ne peut avoir qu’un contrôleur iSCSI.

Le diagramme illustre le fonctionnement des pools de disques avec les hôtes Azure VMware Solution. Chaque contrôleur iSCSI accède au disque managé à l’aide d’un protocole Azure standard et les hôtes Azure VMware Solution peuvent accéder au contrôleur iSCSI sur iSCSI.


:::image type="content" source="media/disk-pools/azure-disks-attached-to-managed-iscsi-controllers.png" alt-text="Diagramme illustrant le fonctionnement des pools de disques avec les hôtes Azure VMware Solution." border="false":::


## <a name="supported-regions"></a>Régions prises en charge

Vous pouvez connecter le pool de disques uniquement à un cloud privé Azure VMware Solution dans la même région. Pour obtenir la liste des régions prises en charge, consultez [Disponibilité régionale](../virtual-machines/disks-pools.md#regional-availability).  Si votre cloud privé est déployé dans une région non prise en charge, vous pouvez le redéployer dans une région prise en charge. Le cloud privé Azure VMware Solution et la colocation du pool de disques offrent des performances optimales avec une latence réseau minime.


## <a name="prerequisites"></a>Configuration requise

- Les besoins de vos charges de travail en termes de scalabilité et de performances sont identifiées. Pour plus d’informations, consultez [Planification des pools de disques Azure](../virtual-machines/disks-pools-planning.md).

- [Cloud privé Azure VMware Solution](deploy-azure-vmware-solution.md) déployé avec un [réseau virtuel configuré](deploy-azure-vmware-solution.md#connect-to-azure-virtual-network-with-expressroute). Pour plus d’informations, consultez [Liste de vérification pour la planification réseau[ et ](tutorial-configure-networking.md)Configurer le réseau pour votre cloud privé VMware](tutorial-network-checklist.md). 

   - Si vous choisissez des disques Ultra, utilisez Ultra-performance pour le cloud privé Azure VMware Solution, puis [activez ExpressRoute FastPath](../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

   - Si vous choisissez des disques SSD Premium, utilisez Standard (1 Gbit/s) pour le cloud privé Azure VMware Solution.  Vous devez utiliser Standard\_DS##\_v3 pour héberger la cible iSCSI.  Si vous rencontrez des problèmes de quota, demandez une augmentation des [limites de quota des processeurs virtuels](../azure-portal/supportability/per-vm-quota-requests.md) de chaque série de machines virtuelles Azure pour la série Dsv3.

- Pool de disques comme stockage de sauvegarde déployé et exposé comme cible iSCSI avec chaque disque comme numéro d’unité logique individuel. Pour plus d’informations, consultez [Déployer un pool de disques Azure](../virtual-machines/disks-pools-deploy.md).

   >[!IMPORTANT]
   > Le pool de disques doit être déployé dans le même abonnement que le cluster VMware et attaché au même réseau virtuel que le cluster VMware.

## <a name="attach-a-disk-pool-to-your-private-cloud"></a>Attacher un pool de disques à votre cloud privé
Vous allez attacher un pool de disques exposé par le biais d’une cible iSCSI comme magasin de données VMware d’un cloud privé Azure VMware Solution.

>[!IMPORTANT]
>En **préversion publique**, attachez un pool de disques uniquement à un cluster de test ou hors production.

1. Vérifiez si l’abonnement est inscrit auprès de `Microsoft.AVS` :

   ```azurecli
   az provider show -n "Microsoft.AVS" --query registrationState
   ```

   S’il n’est pas déjà inscrit, inscrivez-le :

   ```azurecli
   az provider register -n "Microsoft.AVS"
   ```

1. Vérifiez si l’abonnement est inscrit auprès de l’AFEC `CloudSanExperience` dans Microsoft.AVS :

   ```azurecli
   az feature show --name "CloudSanExperience" --namespace "Microsoft.AVS"
   ```

   - S’il n’est pas déjà inscrit, inscrivez-le :

      ```azurecli
      az feature register --name "CloudSanExperience" --namespace "Microsoft.AVS"
      ```

      L’inscription peut prendre environ 15 minutes et vous pouvez en vérifier l’état en cours :
      
      ```azurecli
      az feature show --name "CloudSanExperience" --namespace "Microsoft.AVS" --query properties.state
      ```

      >[!TIP]
      >Si l’inscription reste bloquée à un état intermédiaire pendant plus de 15 minutes, annulez-la et recommencez :
      >
      >```azurecli
      >az feature unregister --name "CloudSanExperience" --namespace "Microsoft.AVS"
      >az feature register --name "CloudSanExperience" --namespace "Microsoft.AVS"
      >```

1. Vérifiez si l’extension `vmware ` est installée : 

   ```azurecli
   az extension show --name vmware
   ```

   - Si l’extension est déjà installée, vérifiez si la version est **3.0.0**. Si une version antérieure est installée, mettez l’extension à jour :

      ```azurecli
      az extension update --name vmware
      ```

   - Si l’extension n’est pas déjà installée, installez-la :

      ```azurecli
      az extension add --name vmware
      ```

3. Créez et attachez un magasin de données iSCSI dans le cluster de cloud privé Azure VMware Solution en utilisant la cible iSCSI fournie `Microsoft.StoragePool` :

   ```bash
   az vmware datastore disk-pool-volume create --name iSCSIDatastore1 --resource-group MyResourceGroup --cluster Cluster-1 --private-cloud MyPrivateCloud --target-id /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/ResourceGroup1/providers/Microsoft.StoragePool/diskPools/mpio-diskpool/iscsiTargets/mpio-iscsi-target --lun-name lun0
   ```

   >[!TIP]
   >Vous pouvez afficher l’aide sur les magasins de données :
   >
   >   ```azurecli
   >   az vmware datastore -h
   >   ```
   

4. Affichez les détails d’un magasin de données iSCSI dans un cluster de cloud privé :
   
   ```azurecli
   az vmware datastore show --name MyCloudSANDatastore1 --resource-group MyResourceGroup --cluster -Cluster-1 --private-cloud MyPrivateCloud
   ```

5. Listez tous les magasins de données dans un cluster de cloud privé :

   ```azurecli
   az vmware datastore list --resource-group MyResourceGroup --cluster Cluster-1 --private-cloud MyPrivateCloud
   ```

## <a name="delete-an-iscsi-datastore-from-your-private-cloud"></a>Supprimer un magasin de données iSCSI de votre cloud privé

Quand vous supprimez un magasin de données du cloud privé, les ressources du pool de disques ne sont pas supprimées. Aucune fenêtre de maintenance n’est nécessaire pour cette opération.

1. Mettez hors tension les machines virtuelles et supprimez tous les objets associés aux magasins de données iSCSI, y compris :

   - Machines virtuelles (à supprimer de l’inventaire)

   - Modèles

   - Instantanés

2. Supprimez le magasin de données du cloud privé :

   ```azurecli
   az vmware datastore delete --name MyCloudSANDatastore1 --resource-group MyResourceGroup --cluster Cluster-1 --private-cloud MyPrivateCloud
   ```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez attaché un pool de disques à vos hôtes Azure VMware Solution, vous voudrez peut-être découvrir les sujets suivants :

- [Gestion d’un pool de disques Azure](../virtual-machines/disks-pools-manage.md ).  Après avoir déployé un pool de disques, vous avez à votre disposition différentes actions de gestion. Vous pouvez ajouter un disque à un pool de disques ou supprimer un disque d’un pool de disques, mettre à jour le mappage iSCSI/numéro d’unité logique ou ajouter des listes de contrôle d’accès.

- [Suppression d’un pool de disques](../virtual-machines/disks-pools-deprovision.md#delete-a-disk-pool). Quand vous supprimez un pool de disques, toutes les ressources du groupe de ressources managé sont également supprimées.

- [Désactivation de la prise en charge iSCSI sur un disque](../virtual-machines/disks-pools-deprovision.md#disable-iscsi-support). Si vous désactivez la prise en charge iSCSI sur un pool de disques, vous ne pouvez plus utiliser ce pool.

- [Déplacement de pools de disques vers un autre abonnement](../virtual-machines/disks-pools-move-resource.md). Vous pouvez déplacer un pool de disques Azure vers un autre abonnement, ce qui implique de déplacer le pool de disques lui-même, les disques contenus, le groupe de ressources managé et toutes les ressources. 

- [Résolution des problèmes liés aux pools de disques](../virtual-machines/disks-pools-troubleshoot.md). Passez en revue les codes d’échec courants liés aux pools de disques Azure (préversion). Cet article propose également des solutions possibles et certaines clarifications sur les états des pools de disques.
