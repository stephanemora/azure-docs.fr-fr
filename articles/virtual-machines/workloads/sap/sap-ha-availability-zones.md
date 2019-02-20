---
title: Configurations de la charge de travail SAP avec des zones de disponibilité Azure | Microsoft Docs
description: Scénarios et architecture de haute disponibilité pour SAP NetWeaver avec des zones de disponibilité Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e319fb5bc1e0ed3354a09b685405656c7f3131bc
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55858803"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Configurations de la charge de travail SAP avec des zones de disponibilité Azure

Parmi les fonctionnalités de haute disponibilité proposées par Azure figurent les [zones de disponibilité Azure](https://docs.microsoft.com/azure/availability-zones/az-overview). L’utilisation des zones de disponibilité améliore la disponibilité globale de la charge de travail SAP dans Azure. Les zones de disponibilité sont déjà proposées dans plusieurs [régions Azure](https://azure.microsoft.com/global-infrastructure/regions/). D’autres régions suivront. 

L’architecture de base de la haute disponibilité SAP peut être décrite comme dans ce graphique :

![Configuration de haute disponibilité standard](./media/sap-ha-availability-zones/standard-ha-config.png)

La couche Application SAP est déployée sur un seul [groupe à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) Azure. Pour que SAP Central Services soit hautement disponible, vous pouvez déployer deux machines virtuelles dans un autre groupe à haute disponibilité. En cas de problème lié au framework ou aux logiciels, servez-vous du service de cluster de basculement Windows ou de Pacemaker (Linux) comme d’un framework de haute disponibilité avec basculement automatique. Voici une liste de documents qui expliquent en détail ce type de déploiement :

- [Mettre en cluster une instance SAP ASCS/SCS sur un cluster de basculement Windows à l’aide d’un disque partagé de cluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Mettre en cluster une instance SAP ASCS/SCS sur un cluster de basculement Windows à l’aide d’un partage de fichiers](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Haute disponibilité pour SAP NetWeaver sur les machines virtuelles Azure sur SUSE Linux Enterprise Server pour les applications SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Haute disponibilité des machines virtuelles Azure pour SAP NetWeaver sur Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

Une architecture similaire s’applique à la couche SGBD des systèmes SAP NetWeaver, S/4HANA ou Hybris. Vous déployez la couche SGBD en mode actif/passif avec une solution de cluster de basculement afin d’éviter les défaillances liées au framework ou aux logiciels. La solution de cluster de basculement peut être un framework de basculement SGBD, le service de cluster de basculement Windows ou Pacemaker. 

Pour déployer la même architecture en utilisant des zones de disponibilité Azure, vous devez apporter certaines modifications à l’architecture décrite ci-dessus. Ces modifications sont décrites dans ce document.

## <a name="considerations-deploying-across-availability-zones"></a>Considérations sur le déploiement dans des zones de disponibilité

Quand vous utilisez des zones de disponibilité, vous devez tenir compte de certains éléments :

- Les zones de disponibilité Azure ne garantissent pas l’existence d’une distance entre les différentes zones d’une même région Azure.
- Les zones de disponibilité Azure ne constituent pas une solution de reprise d’activité idéale. Les catastrophes naturelles peuvent provoquer des dommages de grande échelle dans certaines régions du monde, y compris de lourds dommages au niveau de l’infrastructure d’alimentation. La distance entre les différentes zones peut ne pas être suffisamment grande pour être considérée comme une solution de reprise d’activité après sinistre appropriée.
- La latence réseau entre les zones de disponibilité varie selon les régions Azure. Dans certains cas, vous pouvez déployer et exécuter la couche Application SAP sur différentes zones, car la latence réseau d’une zone vers la machine virtuelle SGBD active reste acceptable du point de vue de l’impact sur les processus métier. Toutefois, dans certaines régions Azure, la latence entre la machine virtuelle SGBD active et l’instance d’application SAP, qui sont déployées dans des zones différentes, peut se révéler trop intrusive et donc inacceptable pour les processus métier SAP. Par conséquent, l’architecture de déploiement doit être différente, avec une architecture active/active pour l’application, ou une architecture active/passive là où la latence entre les zones est trop importante.
- Décidez de la configuration à utiliser pour les zones de disponibilité Azure, en fonction de la latence réseau que vous mesurez entre les différentes zones. La latence réseau joue un rôle important dans deux domaines :
    - La latence entre les deux instances de SGBD pour lesquelles une réplication synchrone doit être établie. Plus la latence du réseau est élevée, plus l’impact sur la scalabilité de votre charge de travail est potentiellement élevé
    - La différence de latence réseau entre une machine virtuelle exécutant une instance de dialogue SAP dans la même zone que l’instance SGBD active et une machine virtuelle similaire dans une autre zone. Plus cette différence est importante, plus l’impact sur la durée d’exécution des processus métier et des traitements par lots est élevé, selon qu’ils s’exécutent dans la même zone que le SGBD ou dans une autre zone.


Il existe certaines restrictions lorsque vous déployez des machines virtuelles Azure sur plusieurs zones de disponibilité et que vous mettez en place des solutions de basculement au sein d’une même région Azure. Ces restrictions sont traitées dans la liste suivante :

- L’utilisation de [disques managés Azure](https://azure.microsoft.com/services/managed-disks/) est obligatoire pour le déploiement dans les zones de disponibilité Azure 
- Le mappage des énumérations de zones aux zones physiques est fixe sur la base d’un abonnement Azure. Si vous utilisez différents abonnements pour déployer vos systèmes SAP, vous devez définir les zones idéales pour chaque abonnement.
- Vous ne pouvez pas déployer de groupes à haute disponibilité Azure au sein d’une zone de disponibilité Azure. Choisissez une zone de disponibilité Azure ou un groupe à haute disponibilité Azure comme infrastructure de déploiement pour des machines virtuelles.
- Vous ne pouvez pas utiliser un [équilibreur de charge Azure De base](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) pour créer des solutions de cluster de basculement basées sur Windows Failover Cluster Services ou sur Linux Pacemaker. À la place, vous devez utiliser la [référence SKU Standard Load Balancer d’Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)



## <a name="ideal-availability-zones-combination"></a>Combinaison des zones de disponibilité idéales
Avant de décider de la façon dont vous pouvez tirer parti des zones de disponibilité, vous devez effectuer une investigation qui vous donne :

- La latence du réseau entre les trois zones différentes d’une région Azure. Ainsi, vous pouvez choisir les zones qui ont la latence réseau la plus faible dans le trafic réseau entre zones.
- La différence entre la latence de machine virtuelle à machine virtuelle au sein d’une des zones que vous avez choisies et la latence du réseau entre les deux zones que vous avez choisies
- La confirmation que les types de machines virtuelles que vous devez déployer sont disponibles dans les deux zones choisies. Vous allez rencontrer des situations où les différentes références SKU de machines virtuelles de la série M sont disponibles seulement dans deux des trois zones.

### <a name="network-latency-between-zones-and-within-zone"></a>Latence du réseau entre les zones et au sein d’une zone
Pour trouver quelle est la latence entre les différentes zones, vous devez :

- Déployer la référence (SKU) de la machine virtuelle à utiliser pour votre instance de SGBD dans les trois zones. Vérifiez que l’[accélération réseau Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) est activée quand vous effectuez cette mesure.
- Une fois que vous avez trouvé les deux zones avec la latence du réseau la plus faible, déployez trois autres machines virtuelles de la référence (SKU) de machine virtuelle que vous voulez utiliser comme machine virtuelle de la couche Application sur les trois zones de disponibilité. Mesurez la latence du réseau avec les deux « machines virtuelles SGBD » dans les deux zones « SGBD » de votre choix. 
- Utilisez **niping** comme outil de mesure. Un outil de SAP, qui fonctionne comme décrit dans les notes du support SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) et [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Concentrez-vous sur les commandes SAP documentées pour les mesures de la latence. L’utilisation de **ping** n’est pas recommandée, car **ping** ne fonctionne pas dans les chemins de code de réseau accéléré d’Azure.

En fonction des mesures et de la disponibilité de vos références SKU de machine virtuelle dans les zones de disponibilité, vous devez prendre les décisions suivantes :

- Définir les zones idéales pour la couche SGBD
- En fonction des différences de latence réseau au sein d’une zone ou entre plusieurs zones, choisissez si vous souhaitez distribuer votre couche Application SAP active sur une, deux ou trois zones.
- Choisissez si vous souhaitez déployer une configuration de type active/passive ou active/active du point de vue de l’application (voir plus loin).

Pour prendre ces décisions, rappelez-vous également des recommandations de SAP quant à la latence du réseau, comme décrit dans la note SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

### <a name="be-aware-of"></a>Soyez attentif aux points suivants :

> [!IMPORTANT]
> Les mesures et les décisions que vous prenez sont valides pour l’abonnement Azure que vous avez utilisé pour prendre ces mesures. Si vous utilisez un autre abonnement Azure, vous devez effectuer de nouvelles mesures, car le mappage des zones énumérées peut changer en fonction de l’abonnement.


> [!IMPORTANT]
> Normalement, les mesures telles qu’elles sont effectuées ci-dessus doivent faire apparaître des résultats différents dans chaque région Azure prenant en charge des [zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview). Même si vos besoins en latence réseau ne changent pas, il peut être nécessaire d’adopter différentes stratégies de déploiement dans les différentes régions Azure, car la latence réseau entre les zones peut varier. Dans certaines régions Azure, la latence réseau entre les trois zones peut être très différente. Dans d’autres régions, cependant, la latence réseau entre les trois zones est plus uniforme. L’affirmation selon laquelle il y a **toujours** une latence du réseau entre zones de 1 à 2 millisecondes est **incorrecte**. La latence du réseau entre les zones de disponibilité dans des régions Azure ne peut pas être généralisée.


## <a name="activeactive-deployment"></a>Déploiement actif/actif
Cette architecture de déploiement est appelée active/active, car vous déployez vos instances de dialogue SAP actives entre deux ou trois zones. Les services centraux SAP utilisant la réplication en file d’attente sont déployés entre deux zones. Ceci vaut également pour la couche SGBD, qui est déployée sur les mêmes zones que le service central SAP.

Pour envisager une telle configuration, vous devez rechercher les deux zones de disponibilité dans votre région qui offrent une latence du réseau interzone acceptable pour votre charge de travail et pour la réplication synchrone de votre SGBD. En outre, vous voulez que le delta entre la latence du réseau dans les zones que vous avez sélectionnées et la latence du réseau entre zones ne soit pas trop important. Cela est dû au fait qu’il n’est pas souhaitable d’avoir de trop grandes variations dans les temps d’exécution de vos processus d’entreprise ou de vos traitements par lots, selon qu’un travail s’exécute dans la zone avec le serveur SGBD ou entre des zones. Certaines variations sont acceptables, mais pas des différences importantes.

Un schéma simplifié d’un déploiement actif/actif sur deux zones peut se présenter comme ceci :

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Les considérations suivantes s’appliquent pour cette configuration :

- Vous traitez les zones de disponibilité Azure comme domaines d’erreur et de mise à jour pour toutes les machines virtuelles, car les groupes à haute disponibilité ne peuvent pas être déployés dans des zones de disponibilité Azure.
- Les équilibreurs de charge Azure que vous utilisez pour les clusters de basculement de SAP Central Services et de la couche SGBD doivent être des [équilibreurs de charge de référence SKU standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). L’équilibreur de charge De base ne fonctionne pas entre des zones.
- Le réseau virtuel Azure et ses sous-réseaux que vous avez déployés pour héberger le système SAP s’étendent sur les différentes zones. Vous **n’avez pas besoin** de réseaux virtuels distincts pour chaque zone.
- Pour toutes les machines virtuelles que vous déployez, utilisez des [disques managés Azure](https://azure.microsoft.com/services/managed-disks/). Les disques non managés ne sont pas pris en charge pour les déploiements sur des zones.
- Le stockage Azure Premium ou le [stockage SSD Ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) ne prennent en charge aucun type de réplication de stockage entre des zones. Par conséquent, il incombe à l’application (SGBD ou services centraux SAP) de répliquer les données importantes.
- Il en va de même pour le répertoire partagé sapmnt, qui est un disque partagé (Windows), un partage CIFS (Windows) ou un partage NFS (Linux). Vous devez utiliser une technologie qui effectue une réplication, comme un disque partagé ou un partage entre les zones. Pour le moment, les technologies suivantes sont prises en charge :
    - Pour Windows, une solution de cluster qui utilise SIOS Datakeeper, comme décrit dans [Mettre en cluster une instance SAP ASCS/SCS sur un cluster de basculement Windows à l’aide d’un disque partagé de cluster dans Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) est prise en charge entre les zones.
    - Pour SUSE Linux, un partage NFS créé selon la procédure décrite dans [Haute disponibilité pour NFS sur les machines virtuelles Azure sur SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) est pris en charge.
    - La solution utilisant le SOFS Windows comme décrit dans [Préparation d’infrastructure Azure pour la haute disponibilité SAP à l’aide de cluster de basculement Windows et de partage de fichiers pour une instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **n’est pas prise en charge entre plusieurs zones**.
- La troisième zone est utilisée pour héberger le périphérique SBD au cas où vous créez un [cluster Pacemaker SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) ou d’autres instances d’application.
- Pour obtenir la cohérence d’exécution des processus métier critiques, vous pouvez essayer de diriger certains utilisateurs et tâches de traitement par lots vers des instances d’application qui se trouvent dans la même zone que l’instance SGBD active, à l’aide de groupes de serveurs par lots SAP, de groupes de connexions ou de groupes RFC. Cependant, en cas de basculement entre zones, vous devez déplacer manuellement ces groupes vers les instances qui sont exécutées sur les machines virtuelles qui se trouvent dans la même zone que la machine virtuelle de la base de données active.  
- Décidez si vous voulez déployer certaines instances de dialogue dormantes dans chacune des zones pour pouvoir accéder immédiatement à la capacité des ressources précédentes, lorsqu’une zone utilisée par une partie de vos instances d’application est hors service.


## <a name="activepassive-deployment"></a>Déploiement actif/passif
Si vous ne trouvez pas un delta acceptable entre la latence réseau au sein d’une même zone et le trafic réseau entre zones, l’architecture que vous pouvez déployer a un caractère actif/passif du point de vue de la couche Application SAP. Vous définissez une zone « active », qui est la zone dans laquelle vous déployez la couche Application complète, et où vous essayez d’exécuter à la fois l’instance SGBD active et l’instance SAP Central Services. Avec une telle configuration, vous évitez les variations importantes au niveau des durées d’exécution des transactions métier et des tâches de traitements par lots, selon qu’une tâche est exécutée dans la même zone que l’instance SGBD active.

La disposition de base d’une telle architecture se présente comme ceci :

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Les considérations suivantes s’appliquent pour cette configuration :

- Vous ne pouvez pas déployer des groupes à haute disponibilité dans les zones de disponibilité Azure. Dans ce cas, vous vous retrouvez avec un domaine de mise à jour et un domaine d’erreur pour votre couche Application. La raison en est qu’elle est déployée dans une seule zone. Cette configuration présente un léger inconvénient par rapport à l’architecture de référence, qui prévoit que vous allez déployer la couche Application dans un groupe à haute disponibilité Azure.
- Pour utiliser une telle architecture, vous devez surveiller attentivement et essayer de garder les instances de SGDB et des services centraux SAP actives dans la même zone que votre couche Application déployée. En cas de basculement du service central SAP ou de l’instance de SGBD, vous voulez être sûr de pouvoir basculer manuellement aussi vite que possible dans la zone avec la couche Application SAP déployée.
- Les équilibreurs de charge Azure que vous utilisez pour les clusters de basculement de SAP Central Services et de la couche SGBD doivent être des [équilibreurs de charge de référence SKU standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). L’équilibreur de charge De base ne fonctionne pas entre des zones.
- Le réseau virtuel Azure et ses sous-réseaux que vous avez déployés pour héberger le système SAP s’étendent sur les différentes zones. Vous **n’avez pas besoin** de réseaux virtuels distincts pour chaque zone.
- Pour toutes les machines virtuelles que vous déployez, vous devez utiliser des[disques managés Azure](https://azure.microsoft.com/services/managed-disks/). Les disques non managés ne sont pas pris en charge pour les déploiements sur des zones.
- Le stockage Azure Premium ou le [stockage SSD Ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) ne prennent en charge aucun type de réplication de stockage entre des zones. Par conséquent, il incombe à l’application (SGBD ou services centraux SAP) de répliquer les données importantes.
- Il en va de même pour le répertoire partagé sapmnt, qui est un disque partagé (Windows), un partage CIFS (Windows) ou un partage NFS (Linux). Vous devez utiliser une technologie qui effectue une réplication, comme un disque partagé ou un partage entre les zones. Pour le moment, les technologies suivantes sont prises en charge :
    - Pour Windows, une solution de cluster qui utilise SIOS Datakeeper, comme décrit dans [Mettre en cluster une instance SAP ASCS/SCS sur un cluster de basculement Windows à l’aide d’un disque partagé de cluster dans Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) est prise en charge entre les zones.
    - Pour SUSE Linux, un partage NFS créé selon la procédure décrite dans [Haute disponibilité pour NFS sur les machines virtuelles Azure sur SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) est pris en charge.
    - La solution utilisant le SOFS Windows comme décrit dans [Préparation d’infrastructure Azure pour la haute disponibilité SAP à l’aide de cluster de basculement Windows et de partage de fichiers pour une instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **n’est pas prise en charge entre plusieurs zones**.
- La troisième zone est utilisée pour héberger le périphérique SBD au cas où vous créez un [cluster Pacemaker SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) ou d’autres instances d’application.
- Déployez des machines virtuelles dormantes dans la zone passive (du point de vue du SGBD) pour être en mesure de démarrer les ressources de l’application en cas de défaillance d’une zone.
    - Vous ne pouvez pas utiliser [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) pour répliquer des machines virtuelles actives vers des machines virtuelles dormantes entre des zones. Pour le moment, Azure Site Recovery n’est pas en mesure de fonctionner de la sorte.
- Investissez dans l’automatisation qui vous permet, en cas de défaillance d’une zone, de démarrer automatiquement la couche Application SAP dans la deuxième zone.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Configuration combinée de la haute disponibilité et de la reprise d’activité
Microsoft ne partage aucune information concernant la distance géographique qui existe entre les sites qui hébergent différentes zones de disponibilité Azure dans une région Azure. En dépit de cela, certains clients tirent parti des zones pour obtenir une configuration de haute disponibilité et de reprise d’activité qui promet un objectif de point de récupération (**R**ecovery **P**oint **O**bjective, RPO) de zéro. Cela signifie que vous ne perdez normalement aucune transaction validée de la base de données, même en cas de reprise d’activité. 

> [!NOTE]
> Une configuration comme celle-ci est recommandée seulement pour des circonstances spécifiques. Il s’agit par exemple de cas où les données ne peuvent pas quitter la région Azure pour des raisons de sécurité et de conformité. 

Un exemple d’une telle configuration est illustré dans ce graphique :

![combined_ha_dr_in_zones](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Les considérations suivantes s’appliquent pour cette configuration :

- Vous supposez qu’il existe une distance significative entre les installations hébergeant une zone de disponibilité. Vous êtes obligé de rester dans une certaine région Azure. Vous ne pouvez pas déployer des groupes à haute disponibilité dans les zones de disponibilité Azure. Ainsi, vous vous retrouvez dans ce cas avec un seul domaine de mise à jour et d’erreur pour votre couche Application. La raison en est qu’elle est déployée dans une seule zone. Il s’agit d’un recul par rapport à l’architecture de référence, qui prévoit que vous déployez la couche Application dans un groupe à haute disponibilité Azure.
- Pour utiliser une telle architecture, vous devez surveiller attentivement et essayer de garder les instances de SGDB et des services centraux SAP actives dans la même zone que votre couche Application déployée. En cas de basculement du service central SAP ou de l’instance de SGBD, vous voulez être sûr de pouvoir basculer manuellement aussi vite que possible dans la zone avec la couche Application SAP déployée.
- Vous avez des instances d’application de production préinstallées dans les machines virtuelles qui exécutent les instances d’application d’assurance qualité actives.
- En cas de défaillance d’une zone, vous arrêtez les instances d’application d’assurance qualité et vous démarrez à la place les instances de production. Gardez à l’esprit que vous devez travailler avec des noms virtuels pour les instances d’application pour que cela fonctionne.
- Les équilibreurs de charge Azure que vous utilisez pour les clusters de basculement de SAP Central Services et de la couche SGBD doivent être des [équilibreurs de charge de référence SKU standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). L’équilibreur de charge De base ne fonctionne pas entre des zones.
- Le réseau virtuel Azure et ses sous-réseaux que vous avez déployés pour héberger le système SAP s’étendent sur les différentes zones. Vous **n’avez pas besoin** de réseaux virtuels distincts pour chaque zone.
- Pour toutes les machines virtuelles que vous déployez, vous devez utiliser des[disques managés Azure](https://azure.microsoft.com/services/managed-disks/). Les disques non managés ne sont pas pris en charge pour les déploiements sur des zones.
- Le stockage Azure Premium ou le [stockage SSD Ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) ne prennent en charge aucun type de réplication de stockage entre des zones. Par conséquent, il incombe à l’application (SGBD ou services centraux SAP) de répliquer les données importantes.
- Il en va de même pour le répertoire partagé sapmnt, qui est un disque partagé (Windows), un partage CIFS (Windows) ou un partage NFS (Linux). Vous devez utiliser une technologie qui effectue une réplication, comme un disque partagé ou un partage entre les zones. Pour le moment, les technologies suivantes sont prises en charge :
    - Pour Windows, une solution de cluster qui utilise SIOS Datakeeper, comme décrit dans [Mettre en cluster une instance SAP ASCS/SCS sur un cluster de basculement Windows à l’aide d’un disque partagé de cluster dans Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) est prise en charge entre les zones.
    - Pour SUSE Linux, un partage NFS créé selon la procédure décrite dans [Haute disponibilité pour NFS sur les machines virtuelles Azure sur SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) est pris en charge.
    - Pour le moment, la solution utilisant SOFS (Windows scale-Out File Services) comme décrit dans [Préparation d’infrastructure Azure pour la haute disponibilité SAP à l’aide de cluster de basculement Windows et de partage de fichiers pour une instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **n’est pas prise en charge entre des zones**.
- La troisième zone est utilisée pour héberger le périphérique SBD au cas où vous créez un [cluster Pacemaker SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) ou d’autres instances d’application.





## <a name="next-steps"></a>Étapes suivantes
Consultez les étapes suivantes pour déployer sur des zones de disponibilité Azure :

- [Mettre en cluster une instance SAP ASCS/SCS sur un cluster de basculement Windows à l’aide d’un disque partagé de cluster dans Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Préparer une infrastructure Azure pour la haute disponibilité SAP à l’aide d’un cluster de basculement Windows et un partage de fichiers pour une instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






