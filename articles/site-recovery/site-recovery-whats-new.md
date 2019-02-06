---
title: Nouveautés d’Azure Site Recovery | Microsoft Docs
description: Fournit un récapitulatif des nouvelles fonctionnalités introduites dans Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: raynew
ms.openlocfilehash: 61e66a19b625141c69a9473373d3d5d808e18fde
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211115"
---
# <a name="whats-new-in-site-recovery"></a>Nouveautés de Site Recovery

Le service [Azure Site Recovery](site-recovery-overview.md) fait l’objet de mises à jour et d’améliorations continues. Pour vous aider à rester à jour, cet article vous donne des informations sur les versions les plus récentes, les nouvelles fonctionnalités et le nouveau contenu. Cette page est régulièrement mise à jour.

Si vous avez des suggestions concernant les fonctionnalités de Site Recovery, [faites-nous part de vos commentaires](https://feedback.azure.com/forums/256299-site-recovery).

## <a name="q1-2019"></a>Premier trimestre 2019

### <a name="linux-support"></a>Prise en charge de Linux

Le [correctif cumulatif 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) met à jour les agents et les fournisseurs Site Recovery. La mise à jour ajoute la prise en charge des fonctionnalités suivantes pour Linux :

- **Reprise d’activité des machines virtuelles Azure** : RedHat Workstation 6/7 ; nouvelles versions de noyau pour Ubuntu, Debian et SUSE.
- **Reprise d’activité des machines virtuelles VMware/serveurs physiques vers Azure** : Red Hat Enterprise Linux 7.6 ; RedHat Workstation 6/7 ; Oracle Linux 6.10/7.6 ; nouvelles versions de noyau pour Ubuntu, Debian et SUSE.



## <a name="q4-2018"></a>Quatrième trimestre 2018

## <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Calculatrice de prix pour la reprise d’activité de machines virtuelles Azure

La reprise d’activité de machines virtuelles Azure occasionne des coûts liés aux licences des machines virtuelles ainsi que des frais de stockage et de réseau. Azure fournit une [calculatrice de prix](https://aka.ms/a2a-cost-estimator) pour vous aider à déterminer ces coûts. Site Recovery propose désormais un [exemple d’estimation du prix](https://aka.ms/a2a-cost-estimator) d’un déploiement basé sur une application à trois niveaux comprenant six machines virtuelles avec 12 disques HDD Standard et 6 disques SSD Premium. L’exemple suppose des changements de données totalisant 10 Go par jour pour le stockage Standard et 20 Go pour le stockage Premium. Pour estimer les coûts de votre propre déploiement, changez les variables. Vous pouvez spécifier le nombre de machines virtuelles, le nombre et le type de disques managés ainsi que le taux de changement de données total attendu sur les machines virtuelles. Vous pouvez également appliquer un facteur de compression pour estimer les coûts de bande passante. [Lisez](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) l’annonce.

### <a name="support-for-azure-vms-in-zones"></a>Prise en charge des machines virtuelles Azure dans les zones

Les zones de disponibilité Azure sont des emplacements physiques uniques au sein d’une région Azure. Chaque zone de disponibilité est composée d’un ou de plusieurs centres de données équipés d’une alimentation, d’un système de refroidissement et d’un réseau indépendants. Vous pouvez désormais activer la réplication pour une machine virtuelle Azure et définir comme cible du basculement une instance de machine virtuelle unique, une machine virtuelle dans un groupe à haute disponibilité ou une machine virtuelle dans une zone de disponibilité. Le paramètre n’impacte pas la réplication. [Lisez](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) l’annonce.
 
### <a name="disaster-recovery-for-encrypted-vms"></a>Reprise d’activité pour des machines virtuelles chiffrées

Site Recovery prend en charge les machines virtuelles Azure chiffrées au moyen d’ADE (Azure Disk Encryption) avec l’application Azure AD. [Plus d’informations](azure-to-azure-how-to-enable-replication-ade-vms.md)

### <a name="disaster-recovery-for-vms-using-accelerated-networking"></a>Reprise d’activité pour les machines virtuelles utilisant l’accélération réseau

L’accélération réseau permet d’opérer la virtualisation d’E/S d’une racine unique (SR-IOV) sur une machine virtuelle, améliorant ainsi les performances réseau. Quand vous activez la réplication pour une machine virtuelle Azure, Site Recovery détecte si l’accélération réseau est activée ou non. Si elle l’est, Site Recovery configure automatiquement l’accélération réseau sur la machine virtuelle Azure du réplica cible après le basculement, à la fois sur [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) et sur [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms). [Plus d’informations](azure-vm-disaster-recovery-with-accelerated-networking.md)

### <a name="automatic-updates-for-the-mobility-service-extension"></a>Mises à jour automatiques de l’extension du service Mobilité

Site Recovery propose désormais une option pour mettre à jour automatiquement l’extension du service Mobilité. L’extension du service Mobilité est installée sur chaque machine virtuelle Azure répliquée par Site Recovery. Quand vous activez la réplication, vous pouvez autoriser Site Recovery à gérer les mises à jour de l’extension. Les mises à jour ne nécessitent pas le redémarrage de la machine virtuelle et n’affectent pas la réplication. [Plus d’informations](azure-to-azure-autoupdate.md)

### <a name="support-for-standard-ssd-disks"></a>Prise en charge des disques SSD Standard

Azure a introduit les disques [SSD (Solid State Drive) Standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd) afin de fournir une solution de stockage économique pour certaines applications, notamment les serveurs web qui ont besoin de performances constantes mais qui ne présentent pas un nombre d’IOPS de disque élevé. Ils combinent des éléments des disques SSD Premium et HDD Standard. Site Recovery assure la reprise d’activité pour les machines virtuelles Azure utilisant un disque SSD Standard. Par défaut, le type de disque est conservé après le basculement vers la région cible.

### <a name="support-for-azure-storage-firewall"></a>Prise en charge du pare-feu de stockage Azure

Vous pouvez sécuriser les comptes de stockage Azure sur un ensemble spécifique de réseaux en activant des règles de pare-feu pour le compte. Configurez les comptes de stockage pour refuser par défaut le trafic issu des réseaux internes et de l’Internet, puis accordez l’accès au trafic provenant de réseaux virtuels spécifiques. Site Recovery prend en charge la réplication de machines virtuelles équipées de disques non managés sur des comptes de stockage avec pare-feu activé vers une région secondaire. Dans la région cible, pour les disques non managés, vous pouvez sélectionner des comptes de stockage avec pare-feu activé. Vous pouvez également limiter l’accès au compte de stockage de cache en autorisant uniquement l’accès au réseau où se trouvent les machines virtuelles sources. Notez que vous devez [autoriser l’accès](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) aux services Microsoft approuvés.

## <a name="q3-2018"></a>Troisième trimestre 2018 

### <a name="linux-support"></a>Prise en charge de Linux

#### <a name="update-rollup-28"></a>Correctif cumulatif 28

Le [correctif cumulatif 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) met à jour les agents et les fournisseurs Site Recovery. La mise à jour ajoute la prise en charge des fonctionnalités suivantes pour Linux :

- **Reprise d’activité des machines virtuelles Azure** : Red Hat Enterprise Linux 6.10 ; CentOS 6.10 ; les machines virtuelles Linux utilisant le style de partition GPT (GUID Partition Table) en mode de compatibilité avec un BIOS hérité sont désormais prises en charge.
- **Reprise d’activité des machines virtuelles VMware/serveurs physiques vers Azure** : Red Hat Enterprise Linux 6.10 ; CentOS 6.10 ; les machines virtuelles Linux utilisant le style de partition GPT (GUID Partition Table) en mode de compatibilité avec un BIOS hérité sont désormais prises en charge.

#### <a name="update-rollup-27"></a>Correctif cumulatif 27

Le [correctif cumulatif 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) met à jour les agents et les fournisseurs Site Recovery. La mise à jour ajoute la prise en charge des fonctionnalités suivantes pour Linux :

- **Reprise d’activité des machines virtuelles Azure** : Red Hat Enterprise Linux 7.5
- **Reprise d’activité des machines virtuelles VMware/serveurs physiques vers Azure** : SUSE Linux Enterprise Server 12, Red Hat Enterprise Linux 7.5

### <a name="support-for-azure-vms-running-on-windows-server-2016"></a>Prise en charge des machines virtuelles Azure s’exécutant sur Windows Server 2016

Les machines virtuelles Azure s’exécutant sur Windows Server 2016 peuvent être répliquées dans plusieurs régions Azure avec Azure Site Recovery.

### <a name="support-for-azure-vms-running-storage-spaces-direct"></a>Prise en charge des machines virtuelles Azure exécutant des espaces de stockage direct

Les [espaces de stockage direct](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) (disponibles à compter de Windows Server 2016) regroupent des lecteurs dans un pool de stockage et utilisent la capacité du pool pour créer des espaces de stockage. Les espaces de stockage peuvent être utilisés sur une machine virtuelle autonome, sur un [cluster invité de machines virtuelles Azure](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) utilisant le stockage local pour chaque nœud de cluster ou sur un stockage partagé au sein du cluster.

## <a name="next-steps"></a>Étapes suivantes

Pour vous tenir informé de nos mises à jour, consultez la page [Mises à jour Azure](https://azure.microsoft.com/updates/?product=site-recovery).


