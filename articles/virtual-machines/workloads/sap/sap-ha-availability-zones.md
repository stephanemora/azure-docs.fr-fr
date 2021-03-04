---
title: Configurations de la charge de travail SAP avec des zones de disponibilité Azure | Microsoft Docs
description: Scénarios et architecture de haute disponibilité pour SAP NetWeaver avec des zones de disponibilité Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 842c56ef1fb6f68c3d8b82e2633d9a604db9fde2
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671630"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Configurations de la charge de travail SAP avec des zones de disponibilité Azure
En plus du déploiement des différentes couches d’architecture SAP dans les groupes à haute disponibilité Azure, les nouvelles [Zones de disponibilité Azure](../../../availability-zones/az-overview.md) peuvent également être utilisées pour le déploiements des charges de travail SAP. La définition des zones de disponibilité Azure est la suivante : « Emplacements physiques uniques dans une région. Chaque zone de disponibilité est composée d’un ou de plusieurs centres de données équipés d’une alimentation, d’un système de refroidissement et d’un réseau indépendants. » Les zones de disponibilité Azure ne sont pas disponibles dans toutes les régions. Pour les régions Azure qui fournissent des Zones de disponibilité, consultez la [carte des régions Azure](https://azure.microsoft.com/global-infrastructure/geographies/). Cette carte vous montre quelles régions fournissent ou vont prochainement fournir des Zones de disponibilité. 

Dans l’architecture SAP NetWeaver ou S/4HANA standard, vous devez protéger trois couches :

- la couche d’application SAP, qui peut comprendre une machine virtuelle ou plusieurs dizaines de machines virtuelles. Vous souhaitez réduire le risque de déploiement des machines virtuelles sur le même serveur hôte. Vous souhaitez également que ces machines virtuelles se trouvent à proximité de la couche SGBD pour maintenir une latence réseau acceptable.
- Couche SAP ASCS/SCS qui représente un point de défaillance unique dans l’architecture SAP NetWeaver et S/4HANA. En général, vous visez deux machines virtuelles à couvrir avec une infrastructure de basculement. Par conséquent, ces machines virtuelles doivent être allouées dans des domaines de mise à jour et d’erreur d’infrastructure différents.
- Couche SAP SGBD, qui représente également un point de défaillance unique. Dans les cas habituels, elle est constituée de deux machines virtuelles couvertes par une infrastructure de basculement. Par conséquent, ces machines virtuelles doivent être allouées dans des domaines de mise à jour et d’erreur d’infrastructure différents. Les déploiements SAP HANA avec montée en puissance parallèle où plus de deux machines virtuelles peuvent être utilisées sont les seules exceptions.

Les principales différences entre le déploiement de vos machines virtuelles critiques via des groupes à haute disponibilité ou des zones de disponibilité sont les suivantes :

- Le déploiement avec un groupe à haute disponibilité aligne les machines virtuelles au sein du jeu dans une seule zone ou un seul centre de données (en fonction de ce qui s’applique à la région concernée). Par conséquent, le déploiement via le groupe à haute disponibilité n’est pas protégé des problèmes d’alimentation, de refroidissement ou de mise en réseau qui affectent les centres de données de la zone dans son ensemble. L’aspect positif est que les machines virtuelles sont alignées avec les domaines de mise à jour et d’erreur au sein de cette zone ou de ce centre de données. En particulier pour la couche SAP ASCS ou SGBD dans laquelle nous protégeons deux machines virtuelles par groupe à haute disponibilité, l’alignement avec les domaines d’erreur et de mise à jour empêche que les deux machines virtuelles se trouvent sur le même matériel hôte. 
- Le déploiement de machines virtuelles par le biais des zones de disponibilité Azure et le choix de différentes zones (trois au maximum jusqu’à présent) implique de déployer les machines virtuelles dans différents emplacements physiques et ajoute une protection supplémentaire contre les problèmes d’alimentation, de refroidissement ou de mise en réseau qui affectent les centres de données de la zone dans son ensemble. Toutefois, lorsque vous déployez plusieurs machines virtuelles de la même famille de machines virtuelles dans la même zone de disponibilité, aucune protection de ne s’applique aux machines virtuelles qui se retrouvent sur le même hôte. Par conséquent, le déploiement par le biais de Zones de disponibilité est idéal pour la couche SAP ASCS et SGBD, où nous utilisons en général deux machines virtuelles. Pour la couche application SAP, qui peut être comprendre bien plus de deux machines virtuelles, vous devrez peut-être revenir à un autre modèle de déploiement (voir plus loin).

Votre motivation pour un déploiement sur les Zones de disponibilité Azure doit être que vous souhaitez vous protéger contre des problèmes d’infrastructure plus importants susceptibles d’affecter la disponibilité d’un ou plusieurs centres de donnée Azure, en plus de vous protéger contre la défaillance d’une machine virtuelle ou de réduire les temps d’arrêt liés aux mises à jour logicielles. 

## <a name="considerations-for-deploying-across-availability-zones"></a>Considérations liées au déploiement dans des zones de disponibilité


Tenez compte des points suivants quand vous utilisez des zones de disponibilité :

- Il n’existe aucune garantie concernant les distances entre les différentes zones de disponibilité au sein d’une région Azure.
- Les zones de disponibilité ne constituent pas une solution de reprise d’activité idéale. Les catastrophes naturelles peuvent provoquer des dommages à grande échelle dans certaines régions du monde, y compris de lourds dommages au niveau des infrastructures d’alimentation. Les distances entre les différentes zones ne sont peut-être pas suffisantes pour constituer une solution de reprise d’activité appropriée.
- La latence réseau entre les zones de disponibilité n’est pas le même dans toutes les régions Azure. Dans certains cas, vous pouvez déployer et exécuter la couche Application SAP sur différentes zones, car la latence réseau entre une zone et la machine virtuelle SGBD active est acceptable. Mais dans certaines régions Azure, la latence entre la machine virtuelle SGBD active et l’instance d’application SAP peut être inacceptable pour les processus métier SAP, quand ces dernières sont déployées dans des zones différentes. Le cas échéant, l’architecture de déploiement doit être différente, avec une architecture active/active pour l’application, ou une architecture active/passive là où la latence réseau entre les zones est trop importante.
- Pour déterminer où utiliser des zones de disponibilité, tenez compte de la latence réseau entre les zones. La latence réseau joue un rôle important dans deux domaines :
    - La latence entre les deux instances SGBD qui ont besoin d’une réplication synchrone. Plus la latence réseau est élevée, plus la scalabilité de votre charge de travail a des chances d’être affectée.
    - La différence de latence réseau entre une machine virtuelle exécutant une instance de dialogue SAP dans la même zone que l’instance SGBD active et une machine virtuelle similaire dans une autre zone. Quand cette différence augmente, l’influence qu’elle exerce sur la durée d’exécution des processus métier et des programmes de traitement par lots augmente également, selon qu’ils s’exécutent dans la zone du SGBD ou dans une autre zone.

Quand vous déployez des machines virtuelles Azure sur des zones de disponibilité et établissez des solutions de basculement au sein d’une même région Azure, certaines restrictions s’appliquent :

- Vous devez utiliser [Azure Disques managés](https://azure.microsoft.com/services/managed-disks/) pour un déploiement sur des zones de disponibilité Azure. 
- Le mappage des énumérations de zones aux zones physiques est fixe sur la base d’un abonnement Azure. Si vous utilisez différents abonnements pour déployer vos systèmes SAP, vous devez définir les zones idéales pour chaque abonnement.
- Vous ne pouvez pas déployer de groupes à haute disponibilité Azure au sein d’une zone de disponibilité Azure, sauf si vous utilisez un [groupe de placements de proximité Azure](../../co-location.md). La façon dont vous pouvez déployer la couche SGBD SAP et les services centraux entre les zones et, en même temps, déployer la couche d’application SAP à l’aide de groupes à haute disponibilité tout en continuant à proposer une proximité des machines virtuelles est décrite dans l’article [Groupes de placements de proximité Azure pour une latence réseau optimale avec les applications SAP](sap-proximity-placement-scenarios.md). Si vous ne tirez pas parti des groupes de placements de proximité Azure, vous devez choisir l’un ou l’autre comme infrastructure de déploiement pour les machines virtuelles.
- Vous ne pouvez pas utiliser un [équilibreur de charge de base Azure](../../../load-balancer/load-balancer-overview.md) pour créer des solutions de cluster de basculement basées sur le clustering de basculement Windows Server ou Linux Pacemaker. Vous devez plutôt utiliser la [référence SKU Standard Load Balancer Azure](../../../load-balancer/load-balancer-standard-availability-zones.md).



## <a name="the-ideal-availability-zones-combination"></a>Combinaison de zones de disponibilité idéale
Si vous souhaitez déployer un système SAP NetWeaver ou S/4HANA sur plusieurs zones, vous avez le choix entre deux architectures :

- Active/active : La paire de machines virtuelles exécutant ASCS/SCS et la paire de machines virtuelles exécutant la couche SGBD sont réparties sur deux zones. Le nombre de machines virtuelles exécutant la couche application SAP est déployé en nombre pair dans les deux mêmes zones. En cas de basculement d’une machine virtuelle SGBD ou ASCS/SCS, certaines des transactions ouvertes et actives peuvent être restaurées. Mais les utilisateurs restent connectés. Les zones dans lesquelles la machine virtuelle s’exécutent les machines virtuelles SGBD actives et les instances d’application n’ont pas vraiment d’importance. Cette architecture est l’architecture privilégiée pour le déploiement entre zones.
- Active/passive : La paire de machines virtuelles exécutant ASCS/SCS et la paire de machines virtuelles exécutant la couche SGBD sont réparties sur deux zones. Le nombre de machines virtuelles exécutant la couche application SAP est déployé dans l’une des Zones de disponibilité. Vous exécutez la couche d’application dans la même zone que l’instance active ASCS/SCS et SGBD. Vous pouvez utiliser cette architecture de déploiement si la latence du réseau entre les différentes zones est trop élevée pour exécuter la couche d’application distribuée entre les zones. La couche d’application doit s’exécuter dans la même zone que l’instance active ASCS/SCS et/ou SGBD. Si une machine virtuelle ASCS/SCS ou SGBD bascule sur la zone secondaire, vous risquez de rencontrer plus de latence réseau et une réduction du débit. Et vous devez restaurer la machine virtuelle précédemment basculée le plus rapidement possible pour revenir aux niveaux de débit précédents. En cas de panne dans la zone, la couche application doit être basculée vers la zone secondaire. Activité à laquelle les utilisateurs sont confrontés lors de l’arrêt complet du système. Dans certaines régions Azure, cette architecture est la seule architecture viable lorsque vous souhaitez utiliser les zones de disponibilité. Si l’impact potentiel d’un basculement d’une machine virtuelle ASCS/SCS ou SGBD vers la zone secondaire n’est pas acceptable, il peut être préférable de conserver les déploiements de groupe à haute disponibilité.


Donc, avant de déterminer comment utiliser des zones de disponibilité, identifiez ce qui suit :

- La latence réseau entre les trois zones d’une région Azure. Le fait de connaître la latence réseau entre les zones d’une région vous permet de choisir les zones présentant la latence réseau la plus faible dans le trafic réseau entre zones.
- La différence entre la latence de machine virtuelle à machine virtuelle au sein de l’une des zones de votre choix et la latence réseau entre deux zones de votre choix.
- La disponibilité des types de machine virtuelle que vous avez besoin de déployer dans les deux zones que vous avez sélectionnées. Avec certaines machines virtuelles, en particulier celles de la série M, vous risquez de vous retrouver dans des situations où certaines références SKU sont disponibles uniquement dans deux des trois zones.

## <a name="network-latency-between-and-within-zones"></a>Latence réseau entre les zones et au sein des zones
Pour déterminer la latence entre les différentes zones, vous avez besoin d’effectuer les étapes suivantes :

- Déployer la référence (SKU) de la machine virtuelle à utiliser pour votre instance de SGBD dans les trois zones. Assurez-vous que l’[accélération réseau Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) est activée quand vous prenez cette mesure.
- Une fois que vous avez trouvé les deux zones avec la latence réseau la plus faible, déployez trois autres machines virtuelles de la référence SKU de machine virtuelle que vous voulez utiliser comme machine virtuelle de la couche Application sur les trois zones de disponibilité. Mesurez la latence réseau par rapport aux deux machines virtuelles SGBD dans les deux zones SGBD que vous avez sélectionnées. 
- Utilisez **`niping`** comme outil de mesure. Cet outil, fourni par SAP, est décrit dans les notes de support SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) et [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Concentrez-vous sur les commandes SAP documentées pour les mesures de latence. Étant donné que **ping** ne fonctionne pas par les chemins de code d’accélération réseau Azure, nous vous recommandons de ne pas l’utiliser.

Vous n’avez pas besoin d’effectuer ces tests manuellement. La procédure PowerShell [Test de latence de la zone de disponibilité](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) permet d’automatiser les tests de latence décrits. 

En fonction de vos mesures et de la disponibilité de vos références SKU de machine virtuelle dans les zones de disponibilité, vous devez prendre des décisions :

- Définissez les zones idéales pour la couche SGBD.
- En fonction des différences de latence réseau au sein d’une zone ou entre plusieurs zones, déterminez si vous voulez distribuer votre couche Application SAP active sur une, deux ou trois zones.
- Déterminez si vous voulez déployer une configuration active/passive ou une configuration active/active du point de vue de l’application. (Ces configurations sont expliquées plus loin dans cet article.)

Pour prendre ces décisions, tenez également compte des recommandations de SAP quant à la latence réseau, comme décrit dans la note SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> Les mesures et les décisions que vous prenez sont valides pour l’abonnement Azure que vous avez utilisé quand vous avez pris les mesures. Si vous utilisez un autre abonnement Azure, vous devez reprendre les mesures. Le mappage des zones énumérées peut être différent pour un autre abonnement Azure.


> [!IMPORTANT]
> Il est probable que les mesures décrites précédemment fournissent des résultats différents dans chaque région Azure qui prend en charge les [zones de disponibilité](https://azure.microsoft.com/global-infrastructure/geographies/). Même si vos besoins en latence réseau ne changent pas, il peut être nécessaire d’adopter différentes stratégies de déploiement dans les différentes régions Azure, car la latence réseau entre les zones peut varier. Dans certaines régions Azure, la latence réseau entre les trois différentes zones peut varier considérablement. Dans d’autres régions, la latence réseau entre les trois différentes zones peut être plus uniforme. L’idée qu’il existe toujours une latence réseau comprise entre 1 et 2 millisecondes est fausse. La latence réseau entre les zones de disponibilité dans les régions Azure ne peut pas être généralisée.

## <a name="activeactive-deployment"></a>Déploiement actif/actif
Cette architecture de déploiement est appelée active/active, car vous déployez vos serveurs d’applications SAP actifs sur deux ou trois zones. L’instance SAP Central Services qui utilise la réplication de la mise en file d’attente sera déployé entre deux zones. Ceci vaut également pour la couche SGBD, qui sera déployée sur les mêmes zones que SAP Central Service. Quand vous envisagez cette configuration, vous devez rechercher les deux zones de disponibilité dans votre région qui offrent une latence réseau interzone acceptable pour votre charge de travail et pour la réplication synchrone de votre SGBD. Vous devez également vous assurer que le delta entre la latence réseau au sein des zones que vous avez sélectionnées et la latence réseau interzone n’est pas trop important. 

La nature de l’architecture SAP fait que, sauf si vous la configurez différemment, les utilisateurs et les travaux par lots peuvent être exécutés dans les différentes instances de l’application. L’effet secondaire de ce fait avec le déploiement actif/actif est que les travaux par lots peuvent être exécutés par toutes les instances de l’application SAP indépendamment de leur exécution dans la même zone avec le SGBD actif ou non. Si la différence de latence réseau entre les différentes zones est faible par rapport à la latence réseau au sein d’une zone, la différence dans les temps d’exécution des travaux par lots peut ne pas être significative. Toutefois, plus la différence de latence réseau dans une zone par rapport à l’ensemble du trafic réseau de la zone est importante, plus la durée d’exécution des travaux par lots peut être affectée si le travail a été exécuté dans une zone où l’instance SGBD n’est pas active. C’est à vous, en tant que client, de décider des différences acceptables au moment de l’exécution. Et de latence réseau acceptable pour le trafic entre zones.

Les régions Azure où un tel déploiement actif/actif doit être possible sans grandes différences au niveau de la durée d’exécution et du débit au sein de la couche d’application déployée sur différents zones de disponibilité se présentent comme suit :

- USA Ouest2 (les trois zones)
- USA Est2 (les trois zones)
- USA Centre (les trois zones)
- Europe Nord (les trois zones)
- Europe Ouest (deux des trois zones)
- USA Est (deux des trois zones)
- USA Centre Sud (deux des trois zones)
- Royaume-Uni Sud (deux des trois zones)

Les régions Azure dans lesquelles cette architecture de déploiement SAP entre zones n’est pas recommandée sont les suivantes :

- France Centre 
- Afrique du Sud Nord
- Centre du Canada
- Japon Est

En fonction de ce que vous êtes prêt à tolérer en termes de différences d’exécution, les autres régions non répertoriées peuvent également être envisagées.


Voici un schéma simplifié d’un déploiement actif/actif sur deux zones :

![Déploiement de zone actif/actif](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Les considérations suivantes s’appliquent pour cette configuration :

- Si vous n’utilisez pas de [groupe de placements de proximité](../../co-location.md), vous traitez les zones de disponibilité Azure comme des domaines d’erreur et de mise à jour pour toutes les machines virtuelles, car les groupes à haute disponibilité ne peuvent pas être déployés dans des zones de disponibilité Azure.
- Si vous souhaitez combiner des déploiements zonaux pour la couche SGBD et les services centraux, mais que vous souhaitez utiliser des groupes à haute disponibilité Azure pour la couche Application, vous devez utiliser des groupes de proximité Azure, comme décrit dans l’article [Groupes de placements de proximité Azure pour une latence réseau optimale avec les applications SAP](sap-proximity-placement-scenarios.md).
- Pour les équilibreurs de charge des clusters de basculement de SAP Central Services et la couche SGBD, vous devez utiliser le service [Azure Load Balancer de la référence SKU standard](../../../load-balancer/load-balancer-standard-availability-zones.md). L’équilibreur de charge de base ne fonctionne pas entre des zones.
- Le réseau virtuel Azure que vous avez déployé pour héberger le système SAP, ainsi que ses sous-réseaux, est réparti sur plusieurs zones. Vous n’avez pas besoin de réseaux virtuels distincts pour chaque zone.
- Pour toutes les machines virtuelles que vous déployez, vous devez utiliser [Azure Disques managés](https://azure.microsoft.com/services/managed-disks/). Les disques non managés ne sont pas pris en charge pour les déploiements sur des zones.
- Le stockage Premium Azure , [stockage SSD Ultra](../../disks-types.md#ultra-disk) ou l’ANF ne prennent en charge aucun type de réplication de stockage entre zones. L’application (SGBD ou SAP Central Services) doit répliquer des données importantes.
- Il en va de même pour le répertoire partagé sapmnt, qui est un disque partagé (Windows), un partage CIFS (Windows) ou un partage NFS (Linux). Vous devez utiliser une technologie qui réplique ces disques partagés ou ces partages entre les zones. Ces technologies sont prises en charge :
  - Pour Windows, une solution de cluster qui utilise SIOS Datakeeper, comme décrit dans [Mettre en cluster une instance SAP ASCS/SCS sur un cluster de basculement Windows à l’aide d’un disque partagé de cluster dans Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
  - Pour SUSE Linux, un partage NFS créé comme décrit dans [Haute disponibilité pour NFS sur les machines virtuelles Azure sur SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
    Actuellement, la solution qui utilise le serveur de fichiers avec montée en puissance parallèle Microsoft, comme décrit dans [Préparation d’infrastructure Azure pour la haute disponibilité SAP à l’aide de cluster de basculement Windows et de partage de fichiers pour une instance SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md), n’est pas prise en charge entre plusieurs zones.
- La troisième zone est utilisée pour héberger l’appareil SBD si vous créez un [cluster SUSE Linux Pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) et si vous utilisez des appareils SBD au lieu de l’agent de délimitation Azure. Ou pour d’autres instances de l’application.
- Pour obtenir la cohérence d’exécution des processus métier critiques, vous pouvez essayer de diriger certains utilisateurs et tâches de traitement par lots vers des instances d’application qui se trouvent dans la même zone que l’instance SGBD active, à l’aide de groupes de serveurs par lots SAP, de groupes de connexions SAP ou de groupes RFC. Cependant, en cas de basculement entre zones, vous devez déplacer manuellement ces groupes vers les instances qui sont exécutées sur les machines virtuelles qui se trouvent dans la même zone que la machine virtuelle de la base de données active.  
- Vous pouvez éventuellement déployer des instances de dialogue dormantes dans chacune des zones. 

> [!IMPORTANT]
> Dans ce scénario actif/actif, des frais supplémentaires sont annoncés par Microsoft à partir du 01/04/2020 pour la bande passante. Consultez le document [Détails sur la tarification de la bande passante](https://azure.microsoft.com/pricing/details/bandwidth/). Le transfert de données entre la couche d'application SAP et la couche SGBD SAP est assez intensif. Par conséquent, le scénario actif/actif peut en partie contribuer aux coûts. Poursuivez la lecture de cet article pour connaître les coûts exacts. 


## <a name="activepassive-deployment"></a>Déploiement actif/passif
Si vous ne trouvez pas un delta acceptable entre la latence réseau au sein d’une même zone et la latence du trafic réseau interzone, vous pouvez déployer une architecture qui a un caractère actif/passif du point de vue de la couche Application SAP. Vous définissez une zone *active*, à savoir la zone dans laquelle vous déployez la couche Application complète et où vous essayez d’exécuter à la fois l’instance SGBD active et l’instance SAP Central Services. Avec une telle configuration, vous devez éviter les variations importantes au niveau des durées d’exécution des transactions métier et des programmes de traitement par lots, selon qu’un travail est exécuté dans la même zone que l’instance SGBD active.

Les régions Azure dans lesquelles ce type d’architecture de déploiement dans les différentes zones peut être préférable sont les suivantes :

- Asie Sud-Est
- Australie Est
- Brésil Sud
- Allemagne Centre-Ouest
- Afrique du Sud Nord
- France Centre 
- Centre du Canada
- Japon Est


La disposition de base de l’architecture ressemble à ceci :

![Déploiement de zone actif/passif](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

Les considérations suivantes s’appliquent pour cette configuration :

- Vous ne pouvez pas déployer des groupes à haute disponibilité dans des zones de disponibilité Azure. Pour compenser cela, vous pouvez utiliser des groupes de placement de proximité Azure, tel que décrit dans l’article [Groupes de placements de proximité Azure pour une latence réseau optimale avec les applications SAP](sap-proximity-placement-scenarios.md).
- Quand vous utilisez cette architecture, vous devez superviser l’état attentivement et essayer de maintenir actives les instances SGDB et SAP Central Services dans la même zone que votre couche Application déployée. En cas de basculement de SAP Central Service ou de l’instance SGBD, vous voulez être sûr de pouvoir basculer manuellement aussi vite que possible dans la zone avec la couche Application SAP déployée.
- Pour les équilibreurs de charge des clusters de basculement de SAP Central Services et la couche SGBD, vous devez utiliser le service [Azure Load Balancer de la référence SKU standard](../../../load-balancer/load-balancer-standard-availability-zones.md). L’équilibreur de charge de base ne fonctionne pas entre des zones.
- Le réseau virtuel Azure que vous avez déployé pour héberger le système SAP, ainsi que ses sous-réseaux, est réparti sur plusieurs zones. Vous n’avez pas besoin de réseaux virtuels distincts pour chaque zone.
- Pour toutes les machines virtuelles que vous déployez, vous devez utiliser [Azure Disques managés](https://azure.microsoft.com/services/managed-disks/). Les disques non managés ne sont pas pris en charge pour les déploiements sur des zones.
- Le stockage Premium Azure , [stockage SSD Ultra](../../disks-types.md#ultra-disk) ou l’ANF ne prennent en charge aucun type de réplication de stockage entre zones. L’application (SGBD ou SAP Central Services) doit répliquer des données importantes.
- Il en va de même pour le répertoire partagé sapmnt, qui est un disque partagé (Windows), un partage CIFS (Windows) ou un partage NFS (Linux). Vous devez utiliser une technologie qui réplique ces disques partagés ou ces partages entre les zones. Ces technologies sont prises en charge :
    - Pour Windows, une solution de cluster qui utilise SIOS Datakeeper, comme décrit dans [Mettre en cluster une instance SAP ASCS/SCS sur un cluster de basculement Windows à l’aide d’un disque partagé de cluster dans Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - Pour SUSE Linux, un partage NFS créé comme décrit dans [Haute disponibilité pour NFS sur les machines virtuelles Azure sur SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
  Actuellement, la solution qui utilise le serveur de fichiers avec montée en puissance parallèle Microsoft, comme décrit dans [Préparation d’infrastructure Azure pour la haute disponibilité SAP à l’aide de cluster de basculement Windows et de partage de fichiers pour une instance SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md), n’est pas prise en charge entre plusieurs zones.
- La troisième zone est utilisée pour héberger l’appareil SBD si vous créez un [cluster SUSE Linux Pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) et si vous utilisez des appareils SBD au lieu de l’agent de délimitation Azure. Ou pour d’autres instances de l’application.
- Vous devez déployer des machines virtuelles dormantes dans la zone passive (du point de vue du SGBD) pour être en mesure de démarrer les ressources de l’application en cas de défaillance d’une zone.
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) est actuellement incapable de répliquer des machines virtuelles actives sur des machines virtuelles dormantes entre des zones. 
- Il est conseillé d’investir dans l’automatisation qui vous permet de démarrer automatiquement la couche Application SAP dans la deuxième zone en cas de défaillance d’une zone.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Configuration combinée de la haute disponibilité et de la reprise d’activité
Microsoft ne partage aucune information concernant la distance géographique qui existe entre les sites qui hébergent différentes zones de disponibilité Azure dans une région Azure. Pourtant, certains clients utilisent des zones pour obtenir une configuration de haute disponibilité et de reprise d’activité qui promet un objectif de point de récupération (RPO) de zéro. Le RPO de zéro signifie que vous ne devez perdre aucune transaction de base de données validée même en cas de reprise d’activité après sinistre. 

> [!NOTE]
> Nous vous recommandons d’utiliser une configuration comme celle-ci uniquement dans certaines circonstances. Par exemple, vous pouvez l’utiliser quand les données ne peuvent pas quitter la région Azure pour des raisons de sécurité ou de conformité. 

Voici un exemple de la façon dont une telle configuration peut se présenter :

![Reprise d’activité et haute disponibilité combinées dans les zones](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Les considérations suivantes s’appliquent pour cette configuration :

- Vous supposez qu’il existe une distance importante entre les sites qui hébergent une zone de disponibilité ou que vous êtes obligé de rester au sein d’une certaine région Azure. Vous ne pouvez pas déployer des groupes à haute disponibilité dans des zones de disponibilité Azure. Pour compenser cela, vous pouvez utiliser des groupes de placement de proximité Azure, tel que décrit dans l’article [Groupes de placements de proximité Azure pour une latence réseau optimale avec les applications SAP](sap-proximity-placement-scenarios.md).
- Quand vous utilisez cette architecture, vous devez superviser l’état attentivement et essayer de maintenir actives les instances SGDB et SAP Central Services dans la même zone que votre couche Application déployée. En cas de basculement de SAP Central Service ou de l’instance SGBD, vous voulez être sûr de pouvoir basculer manuellement aussi vite que possible dans la zone avec la couche Application SAP déployée.
- Vous devez avoir des instances d’application de production préinstallées dans les machines virtuelles qui exécutent les instances d’application d’assurance qualité actives.
- En cas de défaillance d’une zone, vous arrêtez les instances d’application d’assurance qualité et vous démarrez à la place les instances de production. Vous devez utiliser les noms virtuels des instances d’application pour que cela fonctionne.
- Pour les équilibreurs de charge des clusters de basculement de SAP Central Services et la couche SGBD, vous devez utiliser le service [Azure Load Balancer de la référence SKU standard](../../../load-balancer/load-balancer-standard-availability-zones.md). L’équilibreur de charge de base ne fonctionne pas entre des zones.
- Le réseau virtuel Azure que vous avez déployé pour héberger le système SAP, ainsi que ses sous-réseaux, est réparti sur plusieurs zones. Vous n’avez pas besoin de réseaux virtuels distincts pour chaque zone.
- Pour toutes les machines virtuelles que vous déployez, vous devez utiliser [Azure Disques managés](https://azure.microsoft.com/services/managed-disks/). Les disques non managés ne sont pas pris en charge pour les déploiements sur des zones.
- Le stockage Premium Azure et le [stockage SSD Ultra](../../disks-types.md#ultra-disk) ne prennent en charge aucun type de réplication de stockage entre des zones. L’application (SGBD ou SAP Central Services) doit répliquer des données importantes.
- Il en va de même pour le répertoire partagé sapmnt, qui est un disque partagé (Windows), un partage CIFS (Windows) ou un partage NFS (Linux). Vous devez utiliser une technologie qui réplique ces disques partagés ou ces partages entre les zones. Ces technologies sont prises en charge :
    - Pour Windows, une solution de cluster qui utilise SIOS Datakeeper, comme décrit dans [Mettre en cluster une instance SAP ASCS/SCS sur un cluster de basculement Windows à l’aide d’un disque partagé de cluster dans Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - Pour SUSE Linux, un partage NFS créé comme décrit dans [Haute disponibilité pour NFS sur les machines virtuelles Azure sur SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).

  Actuellement, la solution qui utilise le serveur de fichiers avec montée en puissance parallèle Microsoft, comme décrit dans [Préparation d’infrastructure Azure pour la haute disponibilité SAP à l’aide de cluster de basculement Windows et de partage de fichiers pour une instance SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md), n’est pas prise en charge entre plusieurs zones.
- La troisième zone est utilisée pour héberger l’appareil SBD si vous créez un [cluster SUSE Linux Pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) et si vous utilisez des appareils SBD au lieu de l’agent de délimitation Azure. 





## <a name="next-steps"></a>Étapes suivantes
Voici certaines des étapes suivantes du déploiement sur des zones de disponibilité Azure :

- [Mettre en cluster une instance SAP ASCS/SCS sur un cluster de basculement Windows à l’aide d’un disque partagé de cluster dans Azure](./sap-high-availability-guide-wsfc-shared-disk.md)
- [Préparer une infrastructure Azure pour la haute disponibilité SAP à l’aide d’un cluster de basculement Windows et un partage de fichiers pour une instance SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md)