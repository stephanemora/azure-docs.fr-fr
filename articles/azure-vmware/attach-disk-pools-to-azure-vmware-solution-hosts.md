---
title: Attacher des pools de disques à des hôtes Azure VMware Solution (préversion)
description: Découvrez comment attacher un pool de disques exposé par le biais d’une cible iSCSI comme magasin de données VMware d’un cloud privé Azure VMware Solution. Une fois que le magasin de données est configuré, vous pouvez y créer des volumes et les attacher à votre instance VMware.
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f2719b135860f448732f5f36285ef2c33ff0115e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131071625"
---
# <a name="attach-disk-pools-to-azure-vmware-solution-hosts-preview"></a>Attacher des pools de disques à des hôtes Azure VMware Solution (préversion)

Les [pools de disques Azure](../virtual-machines/disks-pools.md) offrent un stockage de bloc persistant pour les applications et les charges de travail reposant sur des disques Azure. Vous pouvez utiliser des disques comme stockage persistant pour Azure VMware Solution et ainsi bénéficier d’une solution optimale en termes de coût et de performances. Par exemple, vous pouvez effectuer un scale-up en utilisant des pools de disques plutôt que de mettre à l’échelle des clusters si vous hébergez des charges de travail gourmandes en stockage. Vous pouvez également utiliser des disques pour répliquer des données à partir d’environnements VMware locaux ou principaux vers un stockage sur disque pour le site secondaire. Pour mettre à l’échelle le stockage indépendamment des hôtes Azure VMware Solution, nous prenons en charge l’exposition des [disques Ultra](../virtual-machines/disks-types.md#ultra-disks), des [disques SSD Premium](../virtual-machines/disks-types.md#premium-ssds) et des [disques SSD Standard](../virtual-machines/disks-types.md#standard-ssds) comme magasins de données.  

>[!IMPORTANT]
>Les pools de disques Azure sur Azure VMware Solution (préversion) sont actuellement en préversion publique.
>Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
>Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les disques managés Azure sont attachés à une machine virtuelle de contrôleur iSCSI déployée dans le groupe de ressources Azure VMware Solution. Les disques sont déployés comme cibles de stockage sur un pool de disques et chaque cible de stockage se présente sous la forme d’un numéro d’unité logique iSCSI sous la cible iSCSI. Vous pouvez exposer un pool de disques comme cible iSCSI connectée à des hôtes Azure VMware Solution comme magasin de données. Un pool de disques est exposé comme point de terminaison unique pour tous les disques sous-jacents ajoutés comme cibles de stockage. Chaque pool de disques ne peut avoir qu’un contrôleur iSCSI.

Le diagramme illustre le fonctionnement des pools de disques avec les hôtes Azure VMware Solution. Chaque contrôleur iSCSI accède au disque managé à l’aide d’un protocole Azure standard et les hôtes Azure VMware Solution peuvent accéder au contrôleur iSCSI sur iSCSI.


:::image type="content" source="media/disk-pools/azure-disks-attached-to-managed-iscsi-controllers.png" alt-text="Diagramme illustrant le fonctionnement des pools de disques avec les hôtes Azure VMware Solution." border="false":::


## <a name="supported-regions"></a>Régions prises en charge

Vous pouvez connecter le pool de disques uniquement à un cloud privé Azure VMware Solution dans la même région. Pour obtenir la liste des régions prises en charge, consultez [Disponibilité régionale](../virtual-machines/disks-pools.md#regional-availability).  Si votre cloud privé est déployé dans une région non prise en charge, vous pouvez le redéployer dans une région prise en charge. Le cloud privé Azure VMware Solution et la colocation du pool de disques offrent des performances optimales avec une latence réseau minime.


## <a name="prerequisites"></a>Prérequis

- Les besoins de vos charges de travail en termes de scalabilité et de performances sont identifiées. Pour plus d’informations, consultez [Planification des pools de disques Azure](../virtual-machines/disks-pools-planning.md).

- [Cloud privé Azure VMware Solution](deploy-azure-vmware-solution.md) déployé avec un [réseau virtuel configuré](deploy-azure-vmware-solution.md#connect-to-azure-virtual-network-with-expressroute). Pour plus d’informations, consultez [Liste de vérification pour la planification réseau[ et ](tutorial-configure-networking.md)Configurer le réseau pour votre cloud privé VMware](tutorial-network-checklist.md). 

   - Si vous sélectionnez des disques Ultra, utilisez une passerelle de réseau virtuel ExpressRoute ultra-performante pour la connexion réseau du pool de disques à votre cloud privé Azure VMware Solution, puis [activez ExpressRoute FastPath](../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

   - Si vous sélectionnez des disques SSD Premium ou des disques SSD Standard, utilisez une passerelle de réseau virtuel ExpressRoute Standard (1 Gb/s) or Haute performance (2 Gb/s) pour la connexion réseau du pool de disques à votre cloud privé Azure VMware Solution.  

- Vous devez utiliser Standard\_DS##\_v3 pour héberger la cible iSCSI.  Si vous rencontrez des problèmes de quota, demandez une augmentation des [limites de quota des processeurs virtuels](../azure-portal/supportability/per-vm-quota-requests.md) de chaque série de machines virtuelles Azure pour la série Dsv3.

- Pool de disques comme stockage de sauvegarde déployé et exposé comme cible iSCSI avec chaque disque comme numéro d’unité logique individuel. Pour plus d’informations, consultez [Déployer un pool de disques Azure](../virtual-machines/disks-pools-deploy.md).

   >[!IMPORTANT]
   > Le pool de disques doit être déployé dans le même abonnement que le cluster VMware et attaché au même réseau virtuel que le cluster VMware.

## <a name="add-a-disk-pool-to-your-private-cloud"></a>Ajouter un pool de disques à votre cloud privé
Vous allez attacher un pool de disques exposé par le biais d’une cible iSCSI comme magasin de données VMware d’un cloud privé Azure VMware Solution.

>[!IMPORTANT]
>En **préversion publique**, attachez un pool de disques uniquement à un cluster de test ou hors production.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Vérifiez si l’abonnement est inscrit auprès de `Microsoft.AVS`.

```azurecli
az provider show -n "Microsoft.AVS" --query registrationState
```

S’il n’est pas déjà inscrit, inscrivez-le.

```azurecli
az provider register -n "Microsoft.AVS"
```

Vérifiez si l’abonnement est inscrit auprès de l’AFEC `CloudSanExperience` dans Microsoft.AVS.

```azurecli
az feature show --name "CloudSanExperience" --namespace "Microsoft.AVS"
```

S’il n’est pas déjà inscrit, inscrivez-le.

```azurecli
az feature register --name "CloudSanExperience" --namespace "Microsoft.AVS"
```

L’inscription pouvant prendre environ 15 minutes, vous pouvez utiliser la commande suivante pour vérifier l’état :

```azurecli
az feature show --name "CloudSanExperience" --namespace "Microsoft.AVS" --query properties.state
```

>[!TIP]
>Si l’inscription reste bloquée à un état intermédiaire pendant plus de 15 minutes, annulez-la et recommencez.
>
>```azurecli
>az feature unregister --name "CloudSanExperience" --namespace "Microsoft.AVS"
>az feature register --name "CloudSanExperience" --namespace "Microsoft.AVS"
>```

Vérifiez si l’extension `vmware ` est installée. 

```azurecli
az extension show --name vmware
```

Si l’extension est déjà installée, vérifiez si la version est **3.0.0**. Si une version antérieure est installée, mettez l’extension à jour.

```azurecli
az extension update --name vmware
```

Si l’extension n’est pas déjà installée, installez-la.

```azurecli
az extension add --name vmware
```

### <a name="attach-the-iscsi-lun"></a>Attacher le numéro d’unité logique iSCSI

Créez et attachez un magasin de données iSCSI dans le cluster de cloud privé Azure VMware Solution en utilisant la cible iSCSI fournie `Microsoft.StoragePool`. Le pool de disques est attaché à un réseau virtuel via un sous-réseau délégué. Cette opération est effectuée avec le fournisseur de ressources Microsoft.StoragePool/diskPools.  Si le sous-réseau n’est pas délégué, le déploiement échoue.

```bash
#Initialize input parameters
resourceGroupName='<yourRGName>'
name='<desiredDataStoreName>'
cluster='<desiredCluster>'
privateCloud='<privateCloud>'
lunName='<desiredLunName>'

az vmware datastore disk-pool-volume create --name $name --resource-group $resourceGroupName --cluster $cluster --private-cloud $privateCloud --target-id /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/ResourceGroup1/providers/Microsoft.StoragePool/diskPools/mpio-diskpool/iscsiTargets/mpio-iscsi-target --lun-name $lunName
```

>[!TIP]
>Vous pouvez afficher l’aide sur les magasins de données.
>
>   ```azurecli
>   az vmware datastore -h
>   ```


Pour vérifier que l’attachement a réussi, vous pouvez utiliser les commandes suivantes :

Affichez les détails d’un magasin de données iSCSI dans un cluster de cloud privé.

```azurecli
az vmware datastore show --name MyCloudSANDatastore1 --resource-group MyResourceGroup --cluster -Cluster-1 --private-cloud MyPrivateCloud
```

Listez tous les magasins de données dans un cluster de cloud privé.

```azurecli
az vmware datastore list --resource-group MyResourceGroup --cluster Cluster-1 --private-cloud MyPrivateCloud
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

### <a name="preview-registration"></a>Inscription de la version préliminaire

Tout d’abord, inscrivez votre abonnement à Microsoft.AVS et CloudSanExperience.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Recherchez et sélectionnez **Abonnements**.
1. Sélectionnez l’abonnement que vous souhaitez utiliser, puis sélectionnez **Fournisseurs de ressources** sous **Paramètres**.
1. Recherchez **Microsoft.AVS**, sélectionnez-le, puis sélectionnez **Inscrire**.
1. Sélectionnez **Fonctionnalités en préversion** sous **Paramètres**.
1. Recherchez et inscrivez **CloudSanExperience**.

### <a name="connect-your-disk-pool"></a>Connecter votre pool de disques

Votre abonnement ayant été correctement inscrit, vous pouvez connecter votre pool de disques à votre cluster de cloud privé Azure VMware Solution.

> [!IMPORTANT]
> Votre pool de disques est attaché à un réseau virtuel via un sous-réseau délégué. Cette opération est effectuée avec le fournisseur de ressources Microsoft.StoragePool. Si le sous-réseau n’est pas délégué, le déploiement échoue. Pour plus d’informations, consultez [Autorisation de déléguer un sous-réseau](../virtual-machines/disks-pools-deploy.md#delegate-subnet-permission).

1. Accédez à votre solution azure VMware Solution.
1. Sélectionnez **Stockage (préversion)** sous **Gérer**.
1. Sélectionnez **Connecter un pool de disques**.
1. Sélectionnez l’abonnement à utiliser.
1. Sélectionnez votre pool de disques et le cluster client auquel vous souhaitez le connecter.
1. Activez vos numéros d’unités logiques (le cas échéant), fournissez un nom de magasin de données (par défaut, le numéro d’unité logique est utilisé), puis sélectionnez **Connecter**.

:::image type="content" source="media/attach-disk-pools-to-azure-vmware-solution-hosts/connect-a-disk-pool-temp.png" alt-text="Capture d’écran de l’expérience de connexion d’un pool de disques." lightbox="media/attach-disk-pools-to-azure-vmware-solution-hosts/connect-a-disk-pool-temp.png":::

Quand la connexion est établie, les magasins de données sont ajoutés dans vCenter.

:::image type="content" source="media/attach-disk-pools-to-azure-vmware-solution-hosts/vsphere-datastores.png" alt-text="Capture d’écran de l’expérience vSphere, illustrant l’attachement de pools de disques en tant que magasin de données." lightbox="media/attach-disk-pools-to-azure-vmware-solution-hosts/vsphere-datastores.png":::

---

## <a name="disconnect-a-disk-pool-from-your-private-cloud"></a>Déconnecter un pool de disques de votre cloud privé

Quand vous déconnectez un pool de disques, ces ressources ne sont pas supprimées. Aucune fenêtre de maintenance n’est nécessaire pour cette opération. Mais soyez prudent quand vous l’effectuez.

Commencez par mettre hors tension les machines virtuelles et supprimer tous les objets associés aux magasins de données du pool de disques, y compris :

   - Machines virtuelles (à supprimer de l’inventaire)

   - Modèles

   - Instantanés

Ensuite, supprimez le magasin de données du cloud privé.

1. Accédez à votre solution Azure VMware Solution dans le portail Azure.
1. Sélectionnez **Stockage** sous **Gérer**.
1. Sélectionnez le pool de disques dont vous souhaitez vous déconnecter, puis sélectionnez **Se déconnecter**.

:::image type="content" source="media/attach-disk-pools-to-azure-vmware-solution-hosts/disconnect-a-disk-pool.png" alt-text="Capture d’écran de la page de stockage Azure VMware Solution, listant les pools de disques attachés et mettant en évidence l’option de déconnexion." lightbox="media/attach-disk-pools-to-azure-vmware-solution-hosts/disconnect-a-disk-pool.png":::

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez attaché un pool de disques à vos hôtes Azure VMware Solution, vous voudrez peut-être découvrir les sujets suivants :

- [Gestion d’un pool de disques Azure](../virtual-machines/disks-pools-manage.md).  Après avoir déployé un pool de disques, vous avez à votre disposition différentes actions de gestion. Vous pouvez ajouter un disque à un pool de disques ou supprimer un disque d’un pool de disques, mettre à jour le mappage iSCSI/numéro d’unité logique ou ajouter des listes de contrôle d’accès.

- [Suppression d’un pool de disques](../virtual-machines/disks-pools-deprovision.md#delete-a-disk-pool). Quand vous supprimez un pool de disques, toutes les ressources du groupe de ressources managé sont également supprimées.

- [Désactivation de la prise en charge iSCSI sur un disque](../virtual-machines/disks-pools-deprovision.md#disable-iscsi-support). Si vous désactivez la prise en charge iSCSI sur un pool de disques, vous ne pouvez plus utiliser ce pool.

- [Déplacement de pools de disques vers un autre abonnement](../virtual-machines/disks-pools-move-resource.md). Vous pouvez déplacer un pool de disques Azure vers un autre abonnement, ce qui implique de déplacer le pool de disques lui-même, les disques contenus, le groupe de ressources managé et toutes les ressources. 

- [Résolution des problèmes liés aux pools de disques](../virtual-machines/disks-pools-troubleshoot.md). Passez en revue les codes d’échec courants liés aux pools de disques Azure (préversion). Cet article propose également des solutions possibles et certaines clarifications sur les états des pools de disques.
