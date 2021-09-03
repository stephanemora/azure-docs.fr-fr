---
title: Comparer l'accès NFS à Azure Files, Stockage Blob et NetApp Files Azure
description: Comparez l'accès NFS pour Azure Files, Stockage Blob Azure et NetApp Files Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: rogarana
ms.openlocfilehash: 6f179fe2e91da9e68a0809763bd9f38a5947e8a4
ms.sourcegitcommit: 9caa850a2b26773e238f8ba6f4ca151c47260915
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2021
ms.locfileid: "113600601"
---
# <a name="compare-access-to-azure-files-blob-storage-and-azure-netapp-files-with-nfs"></a>Comparer l'accès avec NFS à Azure Files, Stockage Blob et NetApp Files Azure

Cet article fournit une comparaison entre chacune de ces offres si vous y accédez par le biais du protocole [NFS (Network File System)](https://en.wikipedia.org/wiki/Network_File_System). Cette comparaison ne s'applique pas si vous utilisez une autre méthode d'accès.

Plus généralement, consultez [cet article](storage-introduction.md) pour comparer Stockage Blob Azure et Azure Files, ou [cet article](../files/storage-files-netapp-comparison.md) pour comparer Azure Files et Azure NetApp Files.

## <a name="comparison"></a>Comparaison

|Category  |Stockage Blob Azure  |Azure Files  |Azure NetApp Files  |
|---------|---------|---------|---------|
|Cas d'utilisation     |Le service Stockage Blob est celui qui convient le mieux aux charges de travail à accès séquentiel et à lecture intensive à grande échelle, où les données sont ingérées une fois et peu modifiées ultérieurement.<br></br>Stockage Blob offre le coût total de possession le plus bas, si la maintenance est faible ou nulle.<br></br>Exemples de scénarios : données analytiques à grande échelle, calcul haute performance sensible au débit, sauvegarde et archivage, conduite autonome, rendu multimédia ou séquençage génomique.         |Azure Files est un service hautement disponible qui convient parfaitement aux charges de travail à accès aléatoire.<br></br>Pour les partages NFS, Azure Files offre une prise en charge complète des systèmes de fichiers POSIX et peut facilement être utilisé à partir de plateformes de conteneurs comme Azure Container Instance (ACI) et Azure Kubernetes Service (AKS) avec le pilote CSI intégré, en plus des plateformes basées sur des machines virtuelles.<br></br>Exemples de scénarios : fichiers partagés, bases de données, répertoires personnels, applications traditionnelles, ERP, systèmes de gestion de contenu, migrations NAS ne nécessitant pas de gestion avancée, et applications personnalisées nécessitant un stockage de fichiers avec montée en puissance parallèle.         |Service de fichiers complètement managé dans le cloud, avec NetApp et fonctionnalités de gestion avancées.<br></br>NetApp Files convient aux charges de travail nécessitant un accès aléatoire, et offre une prise en charge étendue des protocoles ainsi que des fonctionnalités de protection des données.<br></br>Exemples de scénarios : migration d'une solution NAS d'entreprise locale nécessitant des fonctionnalités de gestion enrichies, charges de travail sensibles à la latence comme SAP HANA, calcul haute performance sensible à la latence ou à forte intensité en termes d'IOPS, ou charges de travail nécessitant un accès multiprotocole simultané.         |
|Protocoles disponibles     |NFS 3.0<br></br>REST<br></br>Data Lake Storage Gen2         |SMB<br><br>NFS 4.1 (préversion)<br></br> (Aucune interopérabilité entre les deux protocoles)         |NFS 3.0 et 4.1<br></br>SMB         |
|Fonctionnalités clés     | Intégré au cache HPC pour les charges de travail à faible latence. <br> </br> Gestion intégrée, notamment pour le cycle de vie, les objets blob immuables, le basculement des données et l'index des métadonnées.         | Redondance interzone pour la haute disponibilité. <br></br> Latence constante de quelques millisecondes. <br></br>Performances et coûts prévisibles qui évoluent avec la capacité.         |Latence extrêmement faible (moins d'une milliseconde).<br></br>Fonctionnalités de gestion NetApp ONTAP enrichies, telles que SnapMirror dans le cloud.<br></br>Expérience cohérente en matière de cloud hybride.         |
|Performances (par volume)     |Jusqu'à 20 000 IOPS, jusqu'à 100 Gio/s de débit.         |Jusqu'à 100 000 IOPS, jusqu'à 80 Gio/s de débit.         |Jusqu'à 460 000 IOPS, jusqu'à 36 Gio/s de débit.         |
|Scale     | Jusqu'à 2 Pio pour un seul volume. <br></br> Jusqu'à 4,75 Tio max pour un seul fichier.<br></br>Aucune exigence de capacité minimale.         |Jusqu'à 100 Tio pour un seul partage de fichiers.<br></br>Jusqu'à 4 Tio pour un seul fichier.<br></br>Capacité minimale de 100 Gio.         |Jusqu'à 100 Tio pour un seul volume.<br></br>Jusqu'à 16 Tio pour un seul fichier.<br></br>Expérience cohérente en matière de cloud hybride.         |
|Tarifs     |[Tarifs Stockage Blob Azure](https://azure.microsoft.com/pricing/details/storage/blobs/)         |[Tarifs Azure Files](https://azure.microsoft.com/pricing/details/storage/files/)         |[Tarifs Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/)         |


## <a name="next-steps"></a>Étapes suivantes

- Pour accéder à Stockage Blob pour NFS, consultez [Prise en charge du protocole NFS (Network File System) 3.0 dans Stockage Blob Azure (préversion)](../blobs/network-file-system-protocol-support.md).
- Pour accéder à Azure Files avec NFS, consultez [Partages de fichiers NFS dans Azure Files](../files/files-nfs-protocol.md).
- Pour accéder à Azure NetApp Files avec NFS, consultez [Démarrage rapide : Configurer Azure NetApp Files et créer un volume NFS](../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md).
