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
ms.openlocfilehash: 409a304296d3fdff897a203177e2c150162755c6
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55746215"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Configurations de la charge de travail SAP avec des zones de disponibilité Azure

Les [zones de disponibilité Azure](https://docs.microsoft.com/azure/availability-zones/az-overview) sont une des fonctionnalités de haute disponibilité offertes par Azure pour améliorer la disponibilité globale de la charge de travail SAP sur Azure. Les zones de disponibilité sont déjà disponibles dans différentes [régions Azure](https://azure.microsoft.com/global-infrastructure/regions/). D’autres régions suivront. 

L’architecture de base de la haute disponibilité SAP peut être décrite comme dans ce graphique :

![Configuration de haute disponibilité standard](./media/sap-ha-availability-zones/standard-ha-config.png)

La couche Application SAP est déployée sur un seul [groupe à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) Azure. Pour la haute disponibilité des Services centraux SAP, vous déployez deux machines virtuelles dans un groupe à haute disponibilité distinct, et vous utilisez Windows Failover Cluster Services or Pacemaker (Linux) pour déployer une infrastructure à haute disponibilité qui permet le basculement automatique en cas de problème de l’infrastructure ou d’un logiciel. Voici des documentations décrivant de tels déploiements :

- [Mettre en cluster une instance SAP ASCS/SCS sur un cluster de basculement Windows à l’aide d’un disque partagé de cluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Mettre en cluster une instance SAP ASCS/SCS sur un cluster de basculement Windows à l’aide d’un partage de fichiers](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Haute disponibilité pour SAP NetWeaver sur les machines virtuelles Azure sur SUSE Linux Enterprise Server pour les applications SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Haute disponibilité des machines virtuelles Azure pour SAP NetWeaver sur Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

Une architecture similaire s’applique à la couche SGBD des systèmes SAP NetWeaver, S/4HANA ou Hybris. Vous déployez la couche SGBD en mode actif/passif avec une solution de cluster de basculement, qui utilise des infrastructures de basculement spécifiques au SGBD, Windows Failover Cluster Services ou Pacemaker pour lancer les activités de basculement en cas d’échec de l’infrastructure ou d’un logiciel. 

Pour déployer la même architecture en utilisant des zones de disponibilité Azure, vous devez apporter certaines modifications à l’architecture décrite. Ces modifications sont décrites dans les différentes parties de ce document.

## <a name="considerations-deploying-across-availability-zones"></a>Considérations sur le déploiement dans des zones de disponibilité

Vous devez tenir compte de certains éléments quand vous utilisez des zones de disponibilité. Notamment :

- Les zones de disponibilité Azure ne donnent aucune garantie d’une certaine distance entre les différentes zones d’une même région Azure.
- Les zones de disponibilité Azure ne constituent pas une solution de reprise d’activité idéale. À un moment où d’énormes catastrophes naturelles provoquent des dommages conséquents dans certaines régions du monde, notamment des dégâts importants à l’infrastructure d’alimentation électrique, la distance entre les différentes zones peut ne pas être suffisamment grande pour une solution de reprise d’activité appropriée.
- La latence du réseau entre les différentes zones de disponibilité Azure dans les différentes régions Azure varie selon la région. Dans certains cas, vous pouvez exécuter la couche Application SAP déployée sur différentes zones, car la latence du réseau d’une zone vers la machine virtuelle SGBD active reste acceptable du point de vue de l’impact sur les processus métier. Il existe en revanche des scénarios dans certaines régions Azure où la latence entre la machine virtuelle SGBD active dans une zone et une instance d’application SAP dans une autre zone est trop intrusive et inacceptable pour les processus métier SAP. Par conséquent, l’architecture de déploiement doit être différente, avec une architecture active/active pour l’application, ou une architecture active/passive là où la latence entre les zones est trop importante.
- Prenez une décision quant à la configuration pour laquelle vous pourriez utiliser des zones de disponibilité Azure. En fonction de la latence du réseau que vous mesurez entre les différentes zones. La latence du réseau joue un rôle important dans deux domaines différents :
    - La latence entre les deux instances de SGBD pour lesquelles une réplication synchrone doit être établie. Plus la latence du réseau est élevée, plus l’impact sur la scalabilité de votre charge de travail est potentiellement élevé
    - La différence de latence du réseau entre une machine virtuelle exécutant une instance de dialogue SAP dans la même zone que l’instance de SGBD active et une machine virtuelle similaire dans une autre zone. Plus cette différence est importante, plus l’impact sur la durée d’exécution des processus métier et des traitements par lots est élevé, selon qu’ils s’exécutent dans la même zone que le SGBD ou dans une autre zone.


Pour l’utilisation de la fonctionnalité Azure, il existe certaines restrictions fonctionnelles qui peuvent être utilisées lors du déploiement de machines virtuelles Azure sur plusieurs zones et lors de l’établissement de solutions de basculement entre différentes zones de disponibilité au sein de la même région Azure. Voici ces restrictions :

- L’utilisation de [disques managés Azure](https://azure.microsoft.com/services/managed-disks/) est obligatoire pour le déploiement dans les zones de disponibilité Azure 
- Le mappage des énumérations de zones aux zones physiques est fixe sur la base d’un abonnement Azure. Si vous utilisez différents abonnements pour déployer vos systèmes SAP, vous devez définir les zones idéales pour chaque abonnement séparément
- Vous ne pouvez pas déployer de groupes à haute disponibilité Azure au sein d’une zone de disponibilité Azure. Choisissez une zone de disponibilité Azure ou un groupe à haute disponibilité Azure comme infrastructure de déploiement pour des machines virtuelles.
- Vous ne pouvez pas utiliser un [équilibreur de charge Azure De base](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) pour créer des solutions de cluster de basculement basées sur Windows Failover Cluster Services ou sur Linux Pacemaker. À la place, vous devez utiliser la [référence SKU Standard Load Balancer d’Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)



## <a name="ideal-zone-combination"></a>Combinaison de zones idéales
Pour décider de la façon dont vous pouvez tirer parti des zones de disponibilité, vous devez effectuer une investigation qui vous donne :

- La latence du réseau entre les trois zones différentes d’une région Azure. Ainsi, vous pouvez choisir les zones qui ont la latence du réseau la plus faible dans le trafic réseau entre zones
- La différence entre la latence de machine virtuelle à machine virtuelle au sein d’une des zones que vous avez choisies et la latence du réseau entre les deux zones que vous avez choisies
- La confirmation de ce que les types de machines virtuelles que vous devez déployer sur les zones de disponibilité choisies sont disponibles dans les deux zones choisies. En particulier avec les machines virtuelles de la série M, vous allez rencontrer des situations où les différentes références (SKU) de machines virtuelles de la série M sont disponibles seulement dans deux des trois zones

### <a name="network-latency-between-zones-and-within-zone"></a>Latence du réseau entre les zones et au sein d’une zone
Pour trouver quelle est la latence entre les différentes zones, vous devez :

- Déployer la référence (SKU) de la machine virtuelle à utiliser pour votre instance de SGBD dans les trois zones. Vérifiez que [l’accélération réseau Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) est activée quand vous effectuez cette mesure.
- Une fois que vous avez trouvé les deux zones avec la latence du réseau la plus faible, déployez trois autres machines virtuelles de la référence (SKU) de machine virtuelle que vous voulez utiliser comme machine virtuelle de la couche Application sur les trois zones de disponibilité. Mesurez la latence du réseau avec les deux « machines virtuelles SGBD » dans les deux zones « SGBD » de votre choix. 
- Utilisez **niping** comme outil de mesure. Un outil de SAP, qui fonctionne comme décrit dans les notes du support SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) et [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Concentrez-vous sur les commandes SAP documentées pour les mesures de la latence. **ping** n’est pas un outil recommandé, car il ne fonctionne pas dans les chemins de code de réseau accéléré d’Azure.

D’après les mesures et la disponibilité de vos références (SKU) de machine virtuelle dans les différentes zones, vous devez prendre les décisions suivantes :

- Définir les zones idéales pour la couche SGBD
- Répondez à cette question : en fonction des différences de latence du réseau au sein d’une zone ou entre des zones, pouvez-vous ou non distribuer votre couche Application SAP active sur une, deux ou trois zones différentes ?
- Répondez à cette question : voulez-vous déployer une configuration active/passive ou une configuration active/active du point de vue de l’application ? (voir plus loin)

Pour prendre ces décisions, rappelez-vous également des recommandations de SAP quant à la latence du réseau, comme décrit dans la note SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

### <a name="be-aware-of"></a>Soyez attentif aux points suivants :

> [!IMPORTANT]
> Les mesures et les décisions que vous prenez sont valides pour l’abonnement Azure que vous avez utilisé pour prendre ces mesures. Si vous utilisez un autre abonnement Azure, vous devez répéter les mesures, car le mappage de votre énumération des zones dépendant d’un abonnement peut changer avec un autre abonnement


> [!IMPORTANT]
> Normalement, les mesures telles qu’elles sont effectuées ci-dessus doivent faire apparaître des résultats différents dans chaque région Azure prenant en charge des [zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview). Même si vos exigences quant à la latence du réseau ne changent pas, il peut être nécessaire d’adapter différentes stratégies de déploiement dans les différentes régions Azure, car la latence du réseau entre les zones peut être différente. Dans certaines régions Azure, la latence du réseau entre les trois zones peut être très différente. Dans d’autres régions cependant, la latence du réseau entre les trois zones est plus uniforme. L’affirmation selon laquelle il y a **toujours** une latence du réseau entre zones de 1 à 2 millisecondes est **incorrecte**. La latence du réseau entre les zones de disponibilité dans des régions Azure ne peut pas être généralisée.


## <a name="activeactive-deployment"></a>Déploiement actif/actif
Cette architecture de déploiement est appelée active/active, car vous déployez vos instances de dialogue SAP actives entre deux ou trois zones. Les services centraux SAP utilisant la réplication en file d’attente sont déployés entre deux zones. Ceci vaut également pour la couche SGBD, qui est déployée sur les mêmes zones que le service central SAP.

Pour envisager une telle configuration, vous devez rechercher les deux zones de disponibilité dans votre région qui offrent une latence du réseau interzone acceptable pour votre charge de travail et pour la réplication synchrone de votre SGBD. En outre, vous voulez que le delta entre la latence du réseau dans les zones que vous avez sélectionnées et la latence du réseau entre zones ne soit pas trop important. Pour cette dernière, la raison en est que vous ne voulez pas de trop grandes variations dans les temps d’exécution de vos processus d’entreprise ou vos traitements par lots selon qu’un travail s’exécute dans la zone avec le serveur du SGBD ou entre des zones. Certaines variations sont acceptables, mais pas des différences importantes.

Un schéma simplifié d’un déploiement actif/actif sur deux zones peut se présenter comme ceci :

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Les considérations suivantes s’appliquent pour cette configuration :

- Vous traitez les zones de disponibilité Azure comme domaines d’erreur et de mise à jour pour toutes les machines virtuelles, car les groupes à haute disponibilité ne peuvent pas être déployés dans des zones de disponibilité Azure.
- Les équilibreurs de charge Azure que vous utilisez pour les clusters de basculement des services centraux SAP ainsi que la couche SGBD doivent être des [équilibreurs de charge de référence (SKU) Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). L’équilibreur de charge De base ne fonctionne pas entre des zones.
- Le réseau virtuel Azure et ses sous-réseaux que vous avez déployés pour héberger le système SAP s’étendent sur les différentes zones. Vous **n’avez pas besoin** de réseaux virtuels distincts pour chaque zone.
- Pour toutes les machines virtuelles que vous déployez, utilisez des [disques managés Azure](https://azure.microsoft.com/services/managed-disks/). Les disques non managés ne sont pas pris en charge pour les déploiements sur des zones.
- Le stockage Azure Premium ou le [stockage SSD Ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) ne prennent en charge aucun type de réplication de stockage entre des zones. Par conséquent, il incombe à l’application (SGBD ou services centraux SAP) de répliquer les données importantes.
- Il en va de même pour le répertoire partagé sapmnt, qui est un disque partagé (Windows), un partage CIFS (Windows) ou un partage NFS (Linux). Vous devez utiliser une technologie qui effectue une réplication, comme un disque partagé ou un partage entre les zones. Pour le moment, les technologies suivantes sont prises en charge :
    - Pour Windows, une solution de cluster qui utilise SIOS Datakeeper, comme décrit dans [Mettre en cluster une instance SAP ASCS/SCS sur un cluster de basculement Windows à l’aide d’un disque partagé de cluster dans Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) est prise en charge entre les zones.
    - Pour SUSE Linux, un partage NFS créé comme décrit dans [Haute disponibilité pour NFS sur les machines virtuelles Azure sur SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) est pris en charge.
    - Pour le moment, la solution utilisant SOFS (Windows scale-Out File Services) comme décrit dans [Préparation d’infrastructure Azure pour la haute disponibilité SAP à l’aide de cluster de basculement Windows et de partage de fichiers pour une instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **n’est pas prise en charge entre des zones**.
- La troisième zone est utilisée pour héberger le périphérique SBD au cas où vous créez un [cluster Pacemaker SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) ou d’autres instances d’application.
- Pour garantir la cohérence des temps d’exécution pour des transactions métier et/ou des travaux critiques. Vous pouvez essayer de diriger certains travaux par lots et des utilisateurs directement vers des instances d’application spécifiques qui se trouvent dans la même zone que l’instance SGBD active en utilisant des groupes de serveurs par lots SAP, des groupes de connexions ou des groupes RFC. Cependant, en cas de basculement entre zones, vous devez déplacer manuellement ces groupes vers les instances de dialogue qui se trouvent dans la ou les zones restantes 
- Décidez si voulez déployer certaines instances de dialogue dormantes dans chacune des zones pour pouvoir accéder immédiatement à la capacité des ressources précédentes, au cas où une zone où vous avez déployé une partie de vos instances d’application est hors service.


## <a name="activepassive-deployment"></a>Déploiement actif/passif
Dans les cas où vous ne trouvez pas un delta acceptable entre la latence du réseau au sein d’une même zone et du trafic réseau entre zones, l’architecture que vous pouvez déployer a un caractère actif/passif du point de vue de la couche Application SAP. Vous définissez une zone « active », qui est la zone dans laquelle vous déployez la couche Application complète, et où vous essayez d’exécuter l’instance SGBD active et l’instance active des services centraux SAP. Avec une telle configuration, vous n’avez à coup sûr pas de différence extrême des durées d’exécution dans les transactions métier et les traitements par lots, selon qu’un travail s’exécute ou non dans la même zone que l’instance SGBD active.

La disposition de base d’une telle architecture se présente comme ceci :

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Les considérations suivantes s’appliquent pour cette configuration :

- Vous ne pouvez pas déployer des groupes à haute disponibilité dans les zones de disponibilité Azure. Ainsi, vous vous retrouvez dans ce cas avec un seul domaine de mise à jour et d’erreur pour votre couche Application. La raison en est qu’elle est déployée dans une seule zone. Cette configuration n’est que légèrement en recul par rapport à l’architecture de référence, qui prévoit que vous déployez la couche Application dans un groupe à haute disponibilité Azure.
- Pour utiliser une telle architecture, vous devez surveiller attentivement et essayer de garder les instances de SGDB et des services centraux SAP actives dans la même zone que votre couche Application déployée. En cas de basculement du service central SAP ou de l’instance de SGBD, vous voulez être sûr de pouvoir basculer manuellement aussi vite que possible dans la zone avec la couche Application SAP déployée.
- Les équilibreurs de charge Azure que vous utilisez pour les clusters de basculement des services centraux SAP ainsi que la couche SGBD doivent être des [équilibreurs de charge de référence (SKU) Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). L’équilibreur de charge De base ne fonctionne pas entre des zones.
- Le réseau virtuel Azure et ses sous-réseaux que vous avez déployés pour héberger le système SAP s’étendent sur les différentes zones. Vous **n’avez pas besoin** de réseaux virtuels distincts pour chaque zone.
- Pour toutes les machines virtuelles que vous déployez, vous devez utiliser des[disques managés Azure](https://azure.microsoft.com/services/managed-disks/). Les disques non managés ne sont pas pris en charge pour les déploiements sur des zones.
- Le stockage Azure Premium ou le [stockage SSD Ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) ne prennent en charge aucun type de réplication de stockage entre des zones. Par conséquent, il incombe à l’application (SGBD ou services centraux SAP) de répliquer les données importantes.
- Il en va de même pour le répertoire partagé sapmnt, qui est un disque partagé (Windows), un partage CIFS (Windows) ou un partage NFS (Linux). Vous devez utiliser une technologie qui effectue une réplication, comme un disque partagé ou un partage entre les zones. Pour le moment, les technologies suivantes sont prises en charge :
    - Pour Windows, une solution de cluster qui utilise SIOS Datakeeper, comme décrit dans [Mettre en cluster une instance SAP ASCS/SCS sur un cluster de basculement Windows à l’aide d’un disque partagé de cluster dans Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) est prise en charge entre les zones.
    - Pour SUSE Linux, un partage NFS créé comme décrit dans [Haute disponibilité pour NFS sur les machines virtuelles Azure sur SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) est pris en charge.
    - Pour le moment, la solution utilisant SOFS (Windows scale-Out File Services) comme décrit dans [Préparation d’infrastructure Azure pour la haute disponibilité SAP à l’aide de cluster de basculement Windows et de partage de fichiers pour une instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **n’est pas prise en charge entre des zones**.
- La troisième zone est utilisée pour héberger le périphérique SBD au cas où vous créez un [cluster Pacemaker SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) ou d’autres instances d’application.
- Déployez des machines virtuelles dormantes dans la zone passive (du point de vue du SGBD) pour être en mesure de démarrer les ressources de l’application en cas de défaillance d’une zone.
    - Vous ne pouvez pas utiliser [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) pour répliquer des machines virtuelles actives vers des machines virtuelles dormantes entre des zones. Pour le moment, Azure Site Recovery n’est pas en mesure de fonctionner de la sorte.
- Investissez dans l’automatisation qui vous permet, en cas de défaillance d’une zone, de démarrer automatiquement la couche Application SAP dans la deuxième zone.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Configuration combinée de la haute disponibilité et de la reprise d’activité
En dépit du fait que Microsoft ne donne aucune information sur la distance géographique entre les installations qui hébergent les différentes zones de disponibilité Azure dans une région Azure, certains clients tirent parti des zones pour une configuration combinée de haute disponibilité et de reprise d’activité qui promet un objectif de point de récupération (RPO, **R**ecovery **P**oint **O**bjective) égal à zéro. Cela signifie que vous ne perdez normalement aucune transaction validée de la base de données, même en cas de reprise d’activité. 

> [!NOTE]
> Une configuration comme celle-ci est recommandée seulement pour des circonstances spécifiques. Il s’agit par exemple de cas où les données ne peuvent pas quitter la région Azure pour des raisons de sécurité et de conformité. 

Un exemple d’une telle configuration est illustré dans ce graphique :

![combined_ha_dr_in_zones](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Les considérations suivantes s’appliquent pour cette configuration :

- Vous supposez qu’il existe une distance significative entre les installations hébergeant une zone de disponibilité. Vous êtes obligé de rester dans une certaine région Azure. Vous ne pouvez pas déployer des groupes à haute disponibilité dans les zones de disponibilité Azure. Ainsi, vous vous retrouvez dans ce cas avec un seul domaine de mise à jour et d’erreur pour votre couche Application. La raison en est qu’elle est déployée dans une seule zone. Il s’agit d’un recul par rapport à l’architecture de référence, qui prévoit que vous déployez la couche Application dans un groupe à haute disponibilité Azure.
- Pour utiliser une telle architecture, vous devez surveiller attentivement et essayer de garder les instances de SGDB et des services centraux SAP actives dans la même zone que votre couche Application déployée. En cas de basculement du service central SAP ou de l’instance de SGBD, vous voulez être sûr de pouvoir basculer manuellement aussi vite que possible dans la zone avec la couche Application SAP déployée.
- Vous avez des instances d’application de production préinstallées dans les machines virtuelles qui exécutent les instances d’application d’assurance qualité actives.
- En cas de défaillance d’une zone, vous arrêtez les instances d’application d’assurance qualité et vous démarrez à la place les instances de production. Gardez à l’esprit que vous devez travailler avec des noms virtuels pour les instances d’application pour que cela fonctionne.
- Les équilibreurs de charge Azure que vous utilisez pour les clusters de basculement des services centraux SAP ainsi que la couche SGBD doivent être des [équilibreurs de charge de référence (SKU) Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). L’équilibreur de charge De base ne fonctionne pas entre des zones.
- Le réseau virtuel Azure et ses sous-réseaux que vous avez déployés pour héberger le système SAP s’étendent sur les différentes zones. Vous **n’avez pas besoin** de réseaux virtuels distincts pour chaque zone.
- Pour toutes les machines virtuelles que vous déployez, vous devez utiliser des[disques managés Azure](https://azure.microsoft.com/services/managed-disks/). Les disques non managés ne sont pas pris en charge pour les déploiements sur des zones.
- Le stockage Azure Premium ou le [stockage SSD Ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) ne prennent en charge aucun type de réplication de stockage entre des zones. Par conséquent, il incombe à l’application (SGBD ou services centraux SAP) de répliquer les données importantes.
- Il en va de même pour le répertoire partagé sapmnt, qui est un disque partagé (Windows), un partage CIFS (Windows) ou un partage NFS (Linux). Vous devez utiliser une technologie qui effectue une réplication, comme un disque partagé ou un partage entre les zones. Pour le moment, les technologies suivantes sont prises en charge :
    - Pour Windows, une solution de cluster qui utilise SIOS Datakeeper, comme décrit dans [Mettre en cluster une instance SAP ASCS/SCS sur un cluster de basculement Windows à l’aide d’un disque partagé de cluster dans Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) est prise en charge entre les zones.
    - Pour SUSE Linux, un partage NFS créé comme décrit dans [Haute disponibilité pour NFS sur les machines virtuelles Azure sur SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) est pris en charge.
    - Pour le moment, la solution utilisant SOFS (Windows scale-Out File Services) comme décrit dans [Préparation d’infrastructure Azure pour la haute disponibilité SAP à l’aide de cluster de basculement Windows et de partage de fichiers pour une instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **n’est pas prise en charge entre des zones**.
- La troisième zone est utilisée pour héberger le périphérique SBD au cas où vous créez un [cluster Pacemaker SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) ou d’autres instances d’application.





## <a name="next-steps"></a>Étapes suivantes
Consultez les étapes suivantes pour déployer sur des zones de disponibilité Azure :

- [Mettre en cluster une instance SAP ASCS/SCS sur un cluster de basculement Windows à l’aide d’un disque partagé de cluster dans Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Préparer une infrastructure Azure pour la haute disponibilité SAP à l’aide d’un cluster de basculement Windows et un partage de fichiers pour une instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






