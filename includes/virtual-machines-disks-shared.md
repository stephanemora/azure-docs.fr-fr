---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a14ae76e15c1adb59917e61fbcbdaa34a7efa2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77472012"
---
Les disques partagés Azure (préversion) correspondent à une nouvelle fonctionnalité pour disques managés Azure qui permet d’attacher simultanément un disque managé Azure à plusieurs machines virtuelles. La connexion d'un disque managé à plusieurs machines virtuelles vous permet de déployer de nouvelles applications en cluster ou de migrer des applications en cluster existantes vers Azure.

## <a name="how-it-works"></a>Fonctionnement

Les machines virtuelles du cluster peuvent lire ou écrire sur le disque que vous avez attaché en fonction de la réservation choisie par l’application en cluster via les [réservations persistantes SCSI](https://www.t10.org/members/w_spc3.htm) (SCSI PR). SCSI PR est un standard du secteur bien connu dont tirent parti les applications qui s’exécutent sur un réseau de zone de stockage (SAN) local. L’activation des réservations persistantes SCSI sur un disque managé vous permet de migrer ces applications vers Azure en l’état.

Les disques managés pour lesquels la fonctionnalité Disques partagés est activée offrent un stockage en bloc partagé accessible à plusieurs machines virtuelles ; il est exposé sous forme de numéros d’unité logique (LUN). Les numéros d’unité logique sont ensuite présentés à un initiateur (machine virtuelle) à partir d’une cible (disque). Pour la machine virtuelle, ces numéros d’unité logiques ressemblent à un stockage en attachement direct (DAS) ou à un lecteur local.

Les disques managés pour lesquels la fonctionnalité Disques partagés est activée n’offrent pas en mode natif un système de fichiers entièrement géré accessible via SMB/NFS. Vous devez utiliser un gestionnaire de cluster, comme le cluster de basculement Windows Server (WSFC) ou Pacemaker, qui gère la communication des nœuds de cluster ainsi que le verrouillage en écriture.

## <a name="limitations"></a>Limites

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Tailles du disque

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Exemples de charges de travail

### <a name="windows"></a>Windows

Le clustering basé sur Windows repose essentiellement sur WSFC, qui gère toute l’infrastructure principale pour la communication des nœuds de cluster, ce qui permet à vos applications de tirer parti des modèles d’accès parallèle. WSFC active les options CSV et non CSV en fonction de votre version de Windows Server. Pour plus d’informations, consultez [Créer un cluster de basculement](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster).

Voici quelques exemples d’applications bien connues qui s’exécutent sur WSFC :

- Instances de cluster de basculement SQL Server (FCI)
- Serveur de fichiers avec montée en puissance parallèle (SoFS)
- Serveur de fichiers pour une utilisation générale (charge de travail IW)
- Disque de profil utilisateur de serveur Bureau à distance (RDS UPD)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

Les clusters Linux peuvent tirer parti de gestionnaires de cluster comme [Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker). Pacemaker repose sur [Corosync](http://corosync.github.io/corosync/), ce qui autorise la communication de cluster pour les applications déployées dans des environnements hautement disponibles. Parmi les systèmes de fichiers en cluster courants figurent [ocfs2](https://oss.oracle.com/projects/ocfs2/) et [gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). Vous pouvez manipuler les réservations et les inscriptions à l’aide des utilitaires que sont [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) et [sg_persist](https://linux.die.net/man/8/sg_persist) notamment.

## <a name="persistent-reservation-flow"></a>Flux de réservation persistante

Le diagramme suivant illustre un exemple d’application de base de données en cluster à deux nœuds qui se sert de SCSI PR pour activer le basculement d’un nœud vers l’autre.

![Cluster à deux nœuds. Une application s’exécutant sur le cluster gère l’accès au disque](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

Voici comment se déroule l’opération :

1. L’application en cluster s’exécutant sur Azure VM1 et Azure VM2 inscrit son intention de lire ou d’écrire sur le disque.
1. L’instance d’application sur VM1 prend ensuite une réservation exclusive pour écrire sur le disque.
1. Cette réservation est appliquée sur votre disque Azure et la base de données peut dès lors écrire exclusivement sur le disque. Les écritures éventuelles provenant de l’instance d’application sur VM2 échouent.
1. Si l’instance d’application sur VM1 cesse de fonctionner, l’instance sur VM2 peut alors lancer un basculement de base de données et prendre le contrôle du disque.
1. Cette réservation est dès lors appliquée sur le disque Azure et celui-ci n’accepte plus les écritures en provenance de VM1. Il accepte uniquement les écritures de VM2.
1. L’application en cluster peut mener à bien le basculement de base de données et traiter les demandes de VM2.

Le diagramme suivant illustre une autre charge de travail en cluster courante dans laquelle plusieurs nœuds lisent les données du disque de façon pour des processus parallèles en cours d’exécution, comme l’entraînement de modèles Machine Learning.

![Cluster de machines virtuelles à quatre nœuds ; chaque nœud inscrit une intention d’écrire ; l’application prend une réservation exclusive pour gérer correctement les résultats d’écriture](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

Voici comment se déroule l’opération :

1. L’application en cluster s’exécutant sur toutes les machines virtuelles inscrit l’intention de lire ou d’écrire sur le disque.
1. L’instance d’application sur VM1 prend une réservation exclusive pour écrire sur le disque tout en permettant aux autres machines virtuelles de lire sur le disque.
1. Cette réservation est appliquée sur votre disque Azure.
1. Tous les nœuds du cluster peuvent dès lors lire sur le disque. Un seul nœud écrit en retour les résultats sur le disque pour le compte de tous les nœuds du cluster.