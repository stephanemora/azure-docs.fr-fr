---
title: Groupes de placements de proximité Azure pour une latence réseau optimale avec les applications SAP | Microsoft Docs
description: Décrit des scénarios de déploiement SAP avec des groupes de placements de proximité Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 02dcb7174dd9cb2926ef2fafda4b521b939ae68a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70077986"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Groupes de placement de proximité Azure pour une latence réseau optimale avec les applications SAP
Les applications SAP basées sur l’architecture SAP NetWeaver ou SAP S/4HANA sont sensibles à la latence réseau entre la couche Application SAP et la couche Base de données SAP. La raison de cette sensibilité de la part de ces architectures tient du fait que la majeure partie de la logique métier est exécutée dans la couche Application. Suite à l’exécution de la logique métier, la couche Application SAP émet des requêtes à la couche Base de données à des fréquences élevées de milliers et de dizaines de milliers par seconde. Dans la plupart des cas, la nature de ces requêtes est simple. Et elles peuvent souvent être exécutées sur la couche Base de données en moins de 500 microsecondes, voire moins. Le temps passé sur le réseau à envoyer une telle requête de la couche Application à la couche Base de données et à recevoir le jeu de résultats de la couche Base de données a un impact majeur sur le temps nécessaire à l’exécution des processus métier. Cette sensibilité à la latence réseau est la raison pour laquelle du temps est nécessaire dans les projets de déploiement SAP pour atteindre une latence réseau optimale. Dans [Note SAP n° 1100926 - FAQ : Performances réseau](https://launchpad.support.sap.com/#/notes/1100926/E), SAP a publié des instructions sur la façon de classer la latence réseau.

D’une part, dans de nombreuses régions Azure, le nombre de centres de données a augmenté, ce qui a également été déclenché par l’introduction de zones de disponibilité. D’autre part, les clients, en particulier pour les systèmes SAP haut de gamme, ont utilisé des références SKU de machines virtuelles plus spéciales hors de la série M ou de grandes instances HANA. Les types de machines virtuelles Azure ne sont pas présents dans tous les centres de données d’une région Azure spécifique. En conséquence de ces deux tendances, les clients ont rencontré des cas où la latence réseau ne se trouvait pas dans la plage optimale, ce qui se traduisait, dans certains cas, par des performances non optimales de leurs systèmes SAP.

Pour éviter ce type de problème, Azure propose une construction appelée [groupe de placements de proximité Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). Cette nouvelle fonctionnalité a déjà été utilisée pour déployer plusieurs systèmes SAP différents. Consultez l’article référencé pour connaître les restrictions des groupes de placements de proximité. Il aborde les différents scénarios SAP dans lesquels les groupes de placements de proximité Azure peuvent ou doivent être utilisés.

## <a name="what-are-proximity-placement-groups"></a>Définition des groupes de placements de proximité 
Un groupe de placements de proximité Azure est une construction logique qui, lors de la phase de définition, est liée à une région Azure et à un groupe de ressources Azure. Lors du déploiement de machines virtuelles, un groupe de placements de proximité est référencé par les éléments suivants :

- La première machine virtuelle Azure déployée à installer sur le centre de données. La première machine virtuelle peut être considérée comme une machine virtuelle d’ancrage qui est déployée dans un centre de données en fonction d’algorithmes d’allocation Azure, éventuellement associés à des définitions d’utilisateurs pour une zone de disponibilité Azure spécifique.
- Toutes les machines virtuelles suivantes déployées référençant le groupe de placements de proximité afin de placer toutes les machines virtuelles Azure déployées suivantes dans le même centre de données que celui dans lequel la première machine virtuelle a été placée.

> [!NOTE]
> Si aucun matériel hôte pouvant exécuter un type de machine virtuelle spécifique dans le même centre de données que celui dans lequel la première machine virtuelle a été placée n’est déployé, le déploiement du type de machine virtuelle demandé échoue et se termine par un message d’échec. Il peut s’agir de cas où d’autres machines virtuelles non standard, comme des machines virtuelles avec des GPU ou des types de machines virtuelles HPC, doivent être centrées sur, par exemple, une machine virtuelle de série M qui a été déployée comme premier type de machine virtuelle

Plusieurs groupes de placements de proximité peuvent être affectés à un seul et même [groupe de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal). Toutefois, un groupe de placements de proximité ne peut être affecté qu’à un seul groupe de ressources Azure.

Si vous utilisez des groupes de placements de proximité, vous devez avoir connaissance des points suivants :

- Si vous souhaitez obtenir des performances optimales pour votre système SAP et que vous vous limitez à un seul centre de données Azure pour ce système en utilisant des groupes de placements de proximité, vous pouvez ne pas être en mesure de combiner tous les types de familles de machines virtuelles dans ce groupe de placements de proximité. La raison en est que certains matériels hôtes nécessaires à l’exécution exclusive d’un certain type de machine virtuelle peuvent ne pas être présents dans le centre de données où votre « machine virtuelle d’ancrage » du groupe de placements a été déployée.
- Dans le cycle de vie d’un tel système SAP, vous pouvez être contraint de déplacer le système vers un autre centre de données. Ce type de déplacement peut être contraint dans les cas où vous avez décidé que votre couche SGBD HANA avec scale-out doit, par exemple, passer de quatre nœuds à 16 nœuds. Mais la capacité restante est insuffisante pour obtenir 12 machines virtuelles supplémentaires du type que vous avez déjà utilisé dans le même centre de données.
- En raison de la désaffectation du matériel, Microsoft peut générer des capacités pour le ou les types de machines virtuelles que vous avez utilisés dans un autre centre de données, plutôt que dans le même centre de données. Une telle situation peut signifier que vous devez déplacer toutes les machines virtuelles du groupe de placements de proximité vers un autre centre de données.


## <a name="azure-proximity-placement-groups-with-sap-systems-using-azure-vms-exclusively"></a>Groupes de placements de proximité Azure avec des systèmes SAP utilisant exclusivement des machines virtuelles Azure
La majorité des déploiements de systèmes SAP NetWeaver et S/4HANA sur Azure n’utilisent pas de [grandes instances HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). Pour les déploiements de ces systèmes, il est important de fournir les performances optimales entre la couche Application SAP et la couche SGBD. Pour ce faire, vous devez définir un groupe de placements de proximité Azure uniquement pour ce système. Dans la plupart des déploiements clients, les clients ont choisi de créer un seul [groupe de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) pour les systèmes SAP. Dans ce cas, il existe une relation 1:1 entre, par exemple, les groupes de ressources du système ERP de production et le groupe de placements de proximité. Dans certains autres cas de déploiement, les clients organisaient leurs groupes de ressources horizontalement et collectaient tous les systèmes de production dans un seul groupe de ressources. Vous avez alors une relation 1 à plusieurs entre votre groupe de ressources pour les systèmes SAP de production et plusieurs groupes de placements de proximité de votre système ERP SAP de production, SAP BW, etc. Le regroupement de plusieurs, voire même de tous les systèmes de non-production ou de production SAP dans un seul groupe de placements de proximité doit être évité. Dans les exceptions, où un petit nombre de systèmes SAP ou bien un système SAP et certaines applications environnantes sont nécessaires pour une communication réseau à faible latence, vous pouvez envisager de déplacer ces systèmes vers un seul groupe de placements de proximité. La raison d’une recommandation telle que celle-ci est que plus vous regroupez de systèmes dans un groupe de placements de proximité, plus la probabilité est élevée :

- Que vous ayez besoin d’un type de machine virtuelle qui ne peut pas être exécuté dans le centre de données spécifique dans lequel le groupe de placements de proximité a été ancré.
- Que les ressources de certaines machines virtuelles non standard, comme la série M, ne puissent plus être traitées quand vous en demandez davantage lors de l’ajout de logiciels supplémentaires à un groupe de placements de proximité existant au fil du temps.

L’utilisation idéale telle que décrite ressemble à ceci :

![Groupes de placements de proximité pour toutes les machines virtuelles Azure](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

Dans ce cas, les systèmes SAP uniques sont regroupés dans un groupe de ressources, chacun avec un groupe de placements de proximité. Il n’existe aucune dépendance quant à l’utilisation de configurations de scale-out HANA ou de scale-up SGBD.


## <a name="azure-proximity-placement-groups-and-hana-large-instances"></a>Groupes de placements de proximité Azure et grandes instances HANA
Pour les cas où certains de vos systèmes SAP s’appuient sur de [grandes instances HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) comme couche Application, des améliorations importantes de la latence réseau entre l’unité de grande instance HANA et les machines virtuelles Azure peuvent être obtenues lors de l’utilisation d’unités de grandes instances HANA déployées dans les [tampons ou lignes de la révision 4](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). L’une des améliorations est que les unités de grandes instances HANA, une fois déployées, obtiennent un groupe de placements de proximité déjà déployé. Vous pouvez utiliser ce groupe de placements de proximité pour déployer vos machines virtuelles de couche Application. Par conséquent, ces machines virtuelles vont être déployées dans le même centre de données que celui qui héberge votre unité de grande instance HANA.

Pour déterminer si votre unité de grande instance HANA est déployée dans un tampon ou une ligne de la révision 4, consultez l’article [Contrôle des grandes instances Azure HANA à l’aide du portail Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit). Dans la vue d’ensemble des attributs de votre unité de grande instance HANA, vous pouvez également déterminer le nom du groupe de placements de proximité tel qu’il a été créé au moment du déploiement pour votre unité de grande instance HANA. Le nom affiché dans la vue d’ensemble des attributs est le nom du groupe de placements de proximité que vous devez utiliser pour y déployer vos machines virtuelles de couche Application.

Contrairement aux systèmes SAP qui utilisent uniquement des machines virtuelles Azure, le choix du nombre de [groupes de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) à utiliser est, dans une certaine mesure, ne vous incombe plus quand vous utilisez des grandes instances HANA. Toutes les unités de grande instance HANA d’un [locataire de grande instance HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms) sont regroupées dans un seul groupe de ressources Azure, comme décrit [ici](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal). À moins que vous souhaitiez un déploiement dans plusieurs locataires pour séparer, par exemple, les systèmes de production et de non-production ou certains systèmes, toutes vos unités de grandes instances HANA sont déployées dans un locataire de grande instance HANA, qui a aussi une relation 1:1 avec un groupe de ressources Azure. En revanche, toutes les unités uniques auront un groupe de placements de proximité distinct défini. 

Par conséquent, le regroupement entre les groupes de ressources Azure et les groupes de placements de proximité pour un locataire unique ressemble à ce qui suit :

![Groupes de placements de proximité pour toutes les machines virtuelles Azure](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)


## <a name="short-example-of-deploying-with-azure-proximity-placement-groups"></a>Petit exemple de déploiement avec des groupes de placements de proximité Azure
Pour illustrer la façon dont vous pouvez utiliser des groupes de placements de proximité Azure pour déployer votre machine virtuelle, voici une liste de commandes PowerShell dans un premier petit exercice avec les groupes de placements de proximité Azure

La première étape après vous être connecté avec votre instance [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) consiste à vérifier si vous êtes dans l’abonnement Azure que vous voulez utiliser pour le déploiement, avec la commande :

<pre><code>
Get-AzureRmContext
</code></pre>

Si vous devez changer d’abonnement, vous pouvez le faire en exécutant la commande suivante :

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

Dans une troisième étape, vous voulez créer un groupe de ressources Azure à l’aide de cette commande :

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

Vous pouvez à présent créer le groupe de placements de proximité avec :

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

Vous pouvez maintenant commencer à déployer votre première machine virtuelle dans ce groupe de placements de proximité à l’aide d’une commande telle que :

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

La commande ci-dessus déploie une machine virtuelle Windows. Une fois le déploiement de la machine virtuelle effectué, l’étendue du centre de données du groupe de placements de proximité est défini dans la région Azure. Tous les déploiements de machines virtuelles suivants qui référencent le groupe de placements de proximité comme dans la dernière commande seront effectués dans le même centre de ressources Azure tant que le type de machine virtuelle peut être hébergé sur le matériel placé dans ce centre de ressources et/ou que de la capacité est disponible pour ce type de machine virtuelle.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Combiner des groupes à haute disponibilité et des zones de disponibilité avec des groupes de placements de proximité 
L’un des inconvénients que présente l’utilisation de zones de disponibilité pour des déploiements de systèmes SAP est que la couche Application SAP ne peut pas être contrôlée une fois déployée à l’aide de groupes à haute disponibilité dans la zone spécifique. Étant donné que vous voulez que la couche Application SAP soit déployée dans les mêmes zones que la couche SGBD, et que le référencement d’une zone de disponibilité et d’un groupe à haute disponibilité lors du déploiement d’une machine virtuelle unique n’est pas pris en charge, vous avez été contraint de déployer la couche Application en référençant une zone. Par conséquent, vous avez perdu la possibilité de vérifier que les machines virtuelles de la couche Application ont été réparties sur différents domaines de mise à jour et de défaillance. Avec l’aide des groupes de placements de proximité, vous pouvez déroger à cette restriction. La séquence de déploiements peut ressembler à ce qui suit :

- Créer un groupe de placements de proximité
- Déployer votre « machine virtuelle d’ancrage », généralement le serveur SGBD, en référençant une certaine zone de disponibilité Azure
- Créer un groupe à haute disponibilité qui référence le groupe de placements de proximité Azure (voir ci-dessous)
- Déployer les machines virtuelles de la couche Application en référençant le groupe à haute disponibilité et le groupe de placements de proximité

Au lieu de déployer la première machine virtuelle comme illustré ci-dessus, vous référencez une zone de disponibilité Azure et le groupe de placements de proximité lors du déploiement de la machine virtuelle, comme ceci :

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

Un déploiement réussi de cette machine virtuelle hébergerait l’instance de base de données de mon système SAP dans une zone de disponibilité Azure. L’étendue du groupe de placements de proximité est fixée sur l’un des centres de données représentant la zone de disponibilité que vous avez définie.

Nous supposons que vous déployez les machines virtuelles des services centraux de la même façon que les machines virtuelles SGBD en référençant la ou les mêmes zones que pour les machines virtuelles SGBD et les mêmes groupes de placements de proximité. À l’étape suivante, vous devez créer le ou les groupes à haute disponibilité que vous voulez utiliser pour la couche Application de votre système SAP.
Le groupe de placements de proximité doit être défini et créé. La commande permettant de créer le groupe à haute disponibilité nécessite une référence supplémentaire à l’ID de groupe de placements de proximité (et non pas à son nom). Vous pouvez obtenir l’ID du groupe de placements de proximité avec :



<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

Quand vous créez le groupe à haute disponibilité, vous devez prendre en compte d’autres paramètres lors de l’utilisation de disques managés (par défaut, sauf indication contraire) et des groupes de placements de proximité :

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

Dans l’idéal, vous devez utiliser trois domaines d’erreur. Toutefois, le nombre de domaines d’erreur pris en charge peut varier d’une région à l’autre. Dans le cas présent, le nombre maximal de domaines d’erreur possibles pour les régions spécifiques était de deux. Pour déployer vos machines virtuelles de couche Application, vous devez ajouter une référence au nom de votre groupe à haute disponibilité et au nom du groupe de placements de proximité, comme illustré ici :

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Le résultat final de cette séquence sera une couche SGBD et les services centraux de votre système SAP qui se trouve dans une zone de disponibilité spécifique et une couche Application SAP qui se trouve dans les groupes à haute disponibilité des mêmes centres de données Azure que ceux où la ou les machines virtuelles SGBD ont été déployées.

> [!NOTE]
> Quand vous déployez une machine virtuelle SGBD dans une zone et la seconde dans une autre zone pour créer une configuration de haute disponibilité, vous devez exiger des groupes de placements de proximité différents pour chacune des zones. Il en va de même pour le groupe à haute disponibilité que vous utilisez éventuellement

## <a name="get-an-existing-system-into-azure-proximity-placement-groups"></a>Obtenir un système existant dans des groupes de placements de proximité Azure
Comme vous avez déjà des systèmes SAP déployés, vous pouvez souhaiter optimiser la latence réseau de certains de vos systèmes critiques et localiser la couche Application et la couche SGBD dans le même centre de données. Dans l’étape de la préversion publique de la fonctionnalité de groupe de placements de proximité, il est nécessaire de supprimer les machines virtuelles et de les recréer pour effectuer un tel déplacement vers des groupes de placements de proximité. À ce stade de la fonctionnalité, il n’est pas suffisant d’arrêter les machines virtuelles pour pouvoir les attribuer à des groupes de placements de proximité.


## <a name="next-steps"></a>Étapes suivantes
Consultez la documentation suivante :

- [Check-list relative à la planification et au déploiement de la charge de travail SAP sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [Préversion : Déployer des machines virtuelles dans des groupes de placements de proximité avec Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [Préversion : Déployer des machines virtuelles dans des groupes de placements de proximité avec PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [Facteurs à prendre en compte pour le déploiement SGBD des machines virtuelles Azure pour la charge de travail SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

