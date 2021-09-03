---
title: Implémenter NLS/SDA SAP avec SAP IQ sur Azure | Microsoft Docs
description: Planifiez, déployez et configurez la solution SAP NLS/SDA avec SAP IQ sur Azure.
services: virtual-machines,virtual-machines-windows,virtual-machines-linux,virtual-network,storage,azure-netapp-files,azure-shared-disk,managed-disk
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/11/2021
ms.author: depadia
ms.openlocfilehash: a0641f6c00712e0d3dd32ff04d7bff9695dc4ce1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532861"
---
# <a name="sap-bw-near-line-storage-nls-implementation-guide-with-sap-iq-on-azure"></a>Guide d’implémentation de SAP BW-Near Line Storage (NLS) avec SAP IQ sur Azure

## <a name="overview"></a>Vue d'ensemble

Au fil des années, le client exécutant le système SAP BW constate une croissance exponentielle de la taille de la base de données, ce qui entraîne une augmentation du coût de calcul. Pour obtenir un meilleur équilibre des coûts et des performances, le client peut utiliser le stockage near-line (NLS) pour migrer les données historiques. L’implémentation NLS basée sur SAP IQ est la méthode standard utilisée par SAP pour déplacer des données historiques d’une base de données primaire (SAP HANA ou anyDB). L’adaptateur SAP IQ en tant que solution near-line est fourni avec le système SAP BW. L’intégration de SAP IQ permet de séparer les données fréquemment consultées des données rarement utilisées, ce qui réduit la demande de ressources dans le système SAP BW. 

Ce guide vous fournit les instructions pour la planification, le déploiement et la configuration du stockage SAP BW near-line (NLS) avec SAP IQ sur Azure. Ce guide est destiné à couvrir les services et fonctionnalités Azure courants qui sont pertinents pour le déploiement de SAP IQ-NLS et ne couvre pas les solutions NLS des partenaires. Ce guide n’a pas pour but de remplacer la documentation standard de SAP sur le déploiement de NLS avec SAP IQ, mais vient compléter les documents d’installation et d’administration officiels. 

## <a name="solution-overview"></a>Vue d’ensemble de la solution

Dans un système BW opérationnel, le volume de données augmente constamment et ces données sont nécessaires pour une période plus longue en raison de différentes exigences commerciales et légales. Ce volume important de données peut affecter les performances du système et augmente le travail d’administration, ce qui entraîne la nécessité d’implémenter une stratégie d’antériorité des données. Si vous souhaitez conserver toutes vos données dans votre système SAP BW sans les supprimer, vous pouvez utiliser l’archivage des données. Les données sont d’abord déplacées vers l’archive ou le stockage near-line, puis supprimées du système BW. Vous pouvez soit accéder directement aux données, soit les charger en fonction des besoins, en fonction de la façon dont les données ont été archivées. 

Les utilisateurs SAP BW peuvent utiliser SAP IQ en tant que solution de stockage near-line (NLS). L’adaptateur SAP IQ en tant que solution near-line est fourni avec le système SAP BW. Avec NLS implémenté, les données fréquemment utilisées sont stockées dans une base de données SAP BW en ligne (SAP HANA ou any DB), tandis que les données rarement consultées sont stockées dans SAP IQ, ce qui réduit le coût de la gestion des données et améliore les performances du système SAP BW. Pour garantir la cohérence entre les données en ligne et les données near-line, les partitions archivées sont verrouillées et sont en lecture seule. 

SAP IQ prend en charge deux types d’architectures : simplex et multiplex. Dans l’architecture simplex, une seule instance du serveur SAP IQ s’exécute sur une seule machine virtuelle et les fichiers peuvent se trouver sur une machine hôte ou sur un dispositif de stockage réseau. 

> [!Important]
> Pour la solution SAP-NLS, seule l’architecture simplex est disponible/évaluée par SAP.

![Vue d’ensemble de la solution SAP IQ](media/sap-iq-deployment-guide/sap-iq-solution-overview.png)

Dans Azure, le serveur SAP IQ doit être implémenté sur une machine virtuelle distincte. Il n’est pas recommandé d’installer le logiciel SAP IQ sur un serveur existant qui dispose déjà d’une autre instance de base de données en cours d’exécution, car SAP IQ utilise l’intégralité du processeur et de la mémoire pour sa propre utilisation. Un serveur SAP IQ peut être utilisé pour plusieurs implémentations SAP-NLS. 

## <a name="support-matrix"></a>Matrice de prise en charge

Cette section décrit le tableau de prise en charge de la solution SAP IQ-NLS, qui est décrite plus en détail dans ce document. Vérifiez aussi le [tableau de disponibilité des produits](https://userapps.support.sap.com/sap/support/pam) pour obtenir les dernières dates en fonction de votre version de SAP IQ. 

- **Système d’exploitation** : SAP IQ est certifié au niveau du système d’exploitation uniquement. Vous pouvez exécuter le système d’exploitation certifié SAP IQ sur un environnement Azure, à condition qu’ils soient compatibles pour s’exécuter sur l’infrastructure Azure. Pour plus d’informations, consultez la note SAP [2133194](https://launchpad.support.sap.com/#/notes/2133194).

- **Compatibilité SAP BW** : le stockage near-line pour SAP IQ est disponible uniquement pour les systèmes SAP BW qui s’exécutent déjà sous Unicode. Suivez la note SAP [1796393](https://launchpad.support.sap.com/#/notes/1796393) qui contient des informations sur SAP BW.

- **Stockage** : dans Azure, SAP IQ prend en charge le disque managé premium (Windows/Linux), le disque partagé Azure (Windows uniquement) et Azure NetApp Files (NFS-Linux uniquement). 

## <a name="sizing"></a>Dimensionnement

Le dimensionnement de SAP IQ est limité au processeur, à la mémoire et au stockage. Vous trouverez les instructions de dimensionnement générales pour SAP IQ sur Azure dans la note SAP [1951789](https://launchpad.support.sap.com/#/notes/1951789). La suggestion de dimensionnement que vous recevez dans les instructions doit être mappée aux types de machines virtuelles Azure certifiés pour SAP. La Note SAP [1928533](https://launchpad.support.sap.com/#/notes/1928533) fournit la liste des produits SAP et des types de machines virtuelles Azure pris en charge. 

> [!Tip]
>
> Pour le système de production, nous vous recommandons d’utiliser des machines virtuelles de la série E en raison de leur rapport entre les cœurs et la mémoire. 

La feuille de calcul de dimensionnement et de guide de dimensionnement SAP IQ mentionnée dans la note SAP [1951789](https://launchpad.support.sap.com/#/notes/1951789) ont été développés pour une utilisation native de la base de données SAP IQ et ne reflètent pas les ressources pour la planification de la <SID>base de données IQ. Vous pourriez vous retrouver avec des ressources inutilisées pour SAP-NLS.

## <a name="azure-resources"></a>Ressources Azure

### <a name="choosing-regions"></a>Choix des régions

Si vous exécutez déjà vos systèmes SAP sur Azure, votre région est probablement identifiée. Le déploiement SAP IQ doit se trouver dans la même région que celle de votre système SAP BW pour lequel vous implémentez la solution NLS. Toutefois vous devez vérifier que les services nécessaires à SAP IQ comme Azure NetApp Files (NFS-Linux uniquement) sont disponibles dans ces régions pour déterminer l’architecture SAP IQ. Pour vérifier la disponibilité du service dans votre région, vous pouvez consulter le site [Produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/).

### <a name="availability-sets"></a>Groupes à haute disponibilité 

Pour assurer la redondance des systèmes SAP dans l’infrastructure Azure, l’application doit être déployée dans des groupes à haute disponibilité ou des zones de disponibilité. Techniquement, la haute disponibilité de SAP IQ peut être obtenue à l’aide de l’architecture IQ multiplex, mais l’architecture multiplex n’est pas suffisante pour la solution NLS. Pour arriver à une haute disponibilité pour l’architecture SAP IQ simplex, vous devez configurer deux clusters à deux nœuds avec une solution personnalisée. Le cluster SAP IQ à deux nœuds peut être déployé dans des groupes à haute disponibilité ou des zones de disponibilité, mais le stockage Azure qui est lié aux nœuds détermine sa méthode de déploiement. Actuellement, le disque premium partagé Azure et Azure NetApp Files ne prennent pas en charge le déploiement par zone, qui permet uniquement le déploiement de SAP IQ dans le groupe à haute disponibilité. 

### <a name="virtual-machines"></a>Machines virtuelles

En fonction du dimensionnement de SAP IQ, vous devez mapper vos besoins sur des machines virtuelles Azure, qui sont prises en charge dans le produit Azure pour SAP. La note SAP [1928533](https://launchpad.support.sap.com/#/notes/1928533) est un bon point de départ pour répertorier les types de machines virtuelles Azure pris en charge pour les produits SAP sous Windows et Linux. Gardez également à l'esprit qu'outre la sélection de types de machines virtuelles strictement pris en charge, vous devez également vérifier si ceux-ci sont disponibles dans une région donnée. Vous pouvez vérifier la disponibilité d'un type de machine virtuelle sur la page [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/). Pour choisir le modèle de tarification, vous pouvez consulter [Machines virtuelles Azure pour les charges de travail SAP](planning-guide.md#azure-virtual-machines-for-sap-workload). 

Pour le système de production, nous vous recommandons d’utiliser des machines virtuelles de la série E en raison de leur rapport entre les cœurs et la mémoire.

### <a name="storage"></a>Stockage

Stockage Azure propose aux clients différents types de stockage, présentés en détail dans l’article [Quels sont les types de disques disponibles dans Azure ?](../../disks-types.md). L'usage de certains types de stockage est limité dans le cadre des scénarios SAP. Mais certains types de stockage Azure sont adaptés ou optimisés pour des scénarios de charge de travail spécifiques à SAP. Pour plus d'informations, consultez le guide [Types de stockage Azure pour charge de travail SAP](planning-guide-storage.md), car il met en évidence les différentes options de stockage adaptées à SAP. Pour SAP IQ sur Azure, le stockage Azure peut être utilisé en fonction de votre système d’exploitation (Windows ou Linux) et de la méthode de déploiement (autonome ou à haute disponibilité).

- Disques managés Azure

  Il s'agit d'un volume de stockage de niveau bloc qui est géré par Azure. Vous pouvez utiliser des disques managés Azure pour le déploiement de SAP IQ simplex. Différents types de [Disques managés Azure](../../managed-disks-overview.md) sont disponibles, mais il est recommandé d’utiliser des [disques SSD Premium](../../disks-types.md#premium-ssd) pour SAP IQ. 

- Disques partagés Azure

  La fonctionnalité [Disques partagés Azure](../../disks-shared.md) est une nouvelle fonctionnalité pour disques managés Azure qui vous permet de connecter simultanément un disque managé à plusieurs machines virtuelles. Les disques managés partagés n’offrent pas en mode natif un système de fichiers entièrement géré accessible via SMB/NFS. Vous devez utiliser un gestionnaire de cluster, comme le [cluster de basculement Windows Server](https://github.com/MicrosoftDocs/windowsserverdocs/blob/master/WindowsServerDocs/failover-clustering/failover-clustering-overview.md) (WSFC), qui gère la communication des nœuds de cluster ainsi que le verrouillage en écriture. Pour déployer une solution à haute disponibilité pour l’architecture SAP IQ simplex sur Windows, vous pouvez utiliser des disques Azure partagés entre deux nœuds gérés par WSFC. L’architecture de déploiement SAP IQ avec disque Azure partagé est décrite dans l’article [Déployer la solution haute disponibilité NLS SAP IQ à l’aide d’un disque partagé Azure sur Windows Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-shared-disk-on-windows/ba-p/2433089).

- Azure NetApp Files

  Le déploiement de SAP IQ sur Linux peut utiliser [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) comme système de fichiers (protocole NFS) pour s’installer en tant que solution autonome ou hautement disponible. Cette offre de stockage n’étant pas disponible dans toutes les régions, consultez le site [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/) pour bénéficier d’informations à jour. L’architecture de déploiement SAP IQ avec Azure NetApp Files est décrite dans l’article [Déployer la solution haute disponibilité SAP IQ-NLS à l’aide d’Azure NetApp Files sur SUSE Linux Enterprise Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172).

Le tableau suivant répertorie les suggestions de chaque type de stockage en fonction du système d’exploitation. 

| Type de stockage        | Windows | Linux |
| ------------------- | ------- | ----- |
| Disques managés Azure | Oui     | Oui   |
| Disques partagés Azure  | Oui     | Non    |
| Azure NetApp Files  | Non      | Oui   |

### <a name="networking"></a>Mise en réseau

Azure fournit une infrastructure réseau, qui permet le mappage de tous les scénarios pouvant être réalisés pour le système SAP BW qui utilise SAP IQ comme stockage near-line, comme la connexion au système local, aux systèmes de différents réseaux virtuels et autres. Pour plus d’informations, consultez [Mise en réseau Microsoft Azure pour les charges de travail SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking).

## <a name="deploy-sap-iq-on-windows"></a>Déployer SAP IQ sur Windows

### <a name="server-preparation-and-installation"></a>Installation et préparation du serveur

Suivez le dernier guide SAP pour préparer les serveurs pour l’implémentation NLS avec SAP IQ sur Windows. Pour obtenir les informations les plus récentes, reportez-vous au premier document d’aide publié par SAP, que vous pouvez trouver dans la note SAP [2780668-SAP First Guidance-BW nls Implementation with SAP IQ](https://launchpad.support.sap.com/#/notes/0002780668). Elle couvre les informations complètes relatives aux conditions requises pour les systèmes SAP BW, le système de fichiers IQ, l’installation, la configuration de la publication et l’intégration de la gestion des fichiers NLS avec IQ.

### <a name="high-availability-deployment"></a>Déploiement de la haute disponibilité

SAP IQ prend en charge une architecture simplex et multiplex. Pour la solution NLS, seule l’architecture de serveur simplex est disponible et évaluée. Simplex est une instance unique d’un serveur SAP IQ s’exécutant sur une seule machine virtuelle. Techniquement, la haute disponibilité de SAP IQ peut être obtenue à l’aide de l’architecture serveur multiplex, mais l’architecture multiplex n’est pas suffisante pour la solution NLS. Pour l’architecture de serveur simplex, SAP ne fournit pas de fonctionnalités ou de procédures pour exécuter SAP IQ dans une configuration à haute disponibilité. 

Pour configurer la haute disponibilité de SAP IQ sur Windows pour l’architecture de serveur simplex, vous devez configurer une solution personnalisée, ce qui nécessite une configuration supplémentaire comme un cluster de basculement Microsoft Windows Server, un disque partagé, etc. Une telle solution personnalisée pour SAP IQ sur Windows est décrite en détail dans le blog [Déployer la solution haute disponibilité NLS SAP IQ à l’aide d’un disque partagé Azure sur Windows Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-shared-disk-on-windows/ba-p/2433089).

### <a name="back-up-and-restore"></a>Sauvegarde et restauration

Dans Azure, vous pouvez planifier la sauvegarde de la base de données SAP IQ comme décrit par SAP dans l’[administration IQ : sauvegarde, restauration et récupération de données](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/5b8309b37f4e46b089465e380c24df59.html). SAP IQ fournit différents types de sauvegardes de base de données et les détails de chaque type de sauvegarde se trouvent dans les [scénarios de sauvegarde](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880dc1f84f21015af84f1a6b629dd7a.html).

- Sauvegarde complète : copie complète de la base de données. 
- Sauvegarde incrémentielle : copie toutes les transactions depuis la dernière sauvegarde de n’importe quel type. 
- Incrémentielle depuis la sauvegarde complète : copie toutes les modifications apportées à la base de données depuis la dernière sauvegarde complète.
- Sauvegarde virtuelle : copie l’ensemble de la base de données, à l’exception des données de table et des métadonnées du magasin IQ.

En fonction de la taille de votre base de données IQ, vous pouvez planifier la sauvegarde de votre base de données à partir de n’importe quel scénario de sauvegarde. Toutefois, si vous utilisez SAP IQ avec l’interface NLS fournie par SAP et que vous souhaitez automatiser le processus de sauvegarde de la base de données IQ, ce qui garantit que la base de données SAP IQ est toujours restaurée dans un état cohérent sans perte de données par rapport aux processus de déplacement de données entre la base de données primaire et la base de données SAP. Reportez-vous à la Note SAP [2741824 : configuration de l’automatisation de la sauvegarde pour le magasin à froid/stockage near-line SAP IQ](https://launchpad.support.sap.com/#/notes/2741824), qui fournit des détails sur la configuration de l’automatisation du stockage en quasi-ligne sap iq. 

Pour une base de données IQ volumineuse, vous pouvez utiliser la sauvegarde virtuelle dans SAP IQ. Pour plus d’informations sur la sauvegarde virtuelle, consultez [Sauvegardes virtuelles](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880672184f21015a08dceedc7d19776.html), [Introduction à la sauvegarde virtuelle dans SAP Sybase IQ](https://wiki.scn.sap.com/wiki/display/SYBIQ/Introduction+Virtual+BackUp+(+general++back+up+method+)+in+SAP+Sybase+IQ) et la note SAP [2461985 - Comment sauvegarder de grandes bases de données SAP IQ](https://launchpad.support.sap.com/#/notes/0002461985).

Si vous utilisez un lecteur réseau (protocole SMB) pour sauvegarder et restaurer le serveur SAP IQ sur Windows, assurez-vous d’utiliser un chemin d’accès UNC pour la sauvegarde. Trois barres obliques inverses « \\\\\ » sont requises lors de l’utilisation du chemin d’accès UNC pour la sauvegarde/restauration.

```sql
BACKUP DATABASE FULL TO '\\\sapiq.internal.contoso.net\sapiq-backup\backup\data\<filename>'
```

## <a name="deploy-sap-iq-on-linux"></a>Déployer SAP IQ sur Linux

### <a name="server-preparation-and-installation"></a>Installation et préparation du serveur

Suivez le dernier guide SAP pour préparer les serveurs pour l’implémentation NLS avec SAP IQ sur Linux. Pour obtenir les informations les plus récentes, reportez-vous au premier document d’aide publié par SAP, que vous pouvez trouver dans la note SAP [2780668-SAP First Guidance-BW nls Implementation with SAP IQ](https://launchpad.support.sap.com/#/notes/0002780668). Elle couvre les informations complètes relatives aux conditions requises pour les systèmes SAP BW, le système de fichiers IQ, l’installation, la configuration de la publication et l’intégration de la gestion des fichiers NLS avec IQ.

### <a name="high-availability-deployment"></a>Déploiement de la haute disponibilité

SAP IQ prend en charge une architecture simplex et multiplex. Pour la solution NLS, seule l’architecture de serveur simplex est disponible et évaluée. Simplex est une instance unique d’un serveur SAP IQ s’exécutant sur une seule machine virtuelle. Techniquement, la haute disponibilité de SAP IQ peut être obtenue à l’aide de l’architecture serveur multiplex, mais l’architecture multiplex n’est pas suffisante pour la solution NLS. Pour l’architecture de serveur simplex, SAP ne fournit pas de fonctionnalités ou de procédures pour exécuter SAP IQ dans une configuration à haute disponibilité. 

Pour configurer la haute disponibilité SAP IQ sur Linux pour l’architecture de serveur simplex, vous devez configurer une solution personnalisée, ce qui nécessite une configuration supplémentaire comme celle de Pacemaker. Une telle solution personnalisée pour SAP IQ sur Linux est décrite en détail dans le blog [Déployer la solution haute disponibilité NLS SAP IQ à l’aide d’Azure NetApp Files sur SUSE Linux Enterprise Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172).

### <a name="back-up-and-restore"></a>Sauvegarde et restauration

Dans Azure, vous pouvez planifier la sauvegarde de la base de données SAP IQ comme décrit par SAP dans l’[administration IQ : sauvegarde, restauration et récupération de données](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/5b8309b37f4e46b089465e380c24df59.html). SAP IQ fournit différents types de sauvegardes de base de données et les détails de chaque type de sauvegarde se trouvent dans les [scénarios de sauvegarde](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880dc1f84f21015af84f1a6b629dd7a.html).

- Sauvegarde complète : copie complète de la base de données. 
- Sauvegarde incrémentielle : copie toutes les transactions depuis la dernière sauvegarde de n’importe quel type. 
- Incrémentielle depuis la sauvegarde complète : copie toutes les modifications apportées à la base de données depuis la dernière sauvegarde complète.
- Sauvegarde virtuelle : copie l’ensemble de la base de données, à l’exception des données de table et des métadonnées du magasin IQ.

En fonction de la taille de votre base de données IQ, vous pouvez planifier la sauvegarde de la base de données. Toutefois, si vous utilisez SAP IQ avec l’interface NLS fournie par SAP et que vous souhaitez automatiser le processus de sauvegarde de la base de données IQ, ce qui garantit que la base de données SAP IQ est toujours restaurée dans un état sans perte de données par rapport aux processus de déplacement de données entre la base de données primaire et la base de données SAP. Reportez-vous à la Note SAP [2741824 : configuration de l’automatisation de la sauvegarde pour le magasin à froid/stockage near-line SAP IQ](https://launchpad.support.sap.com/#/notes/2741824), qui fournit des détails sur la configuration de l’automatisation du stockage en quasi-ligne sap iq. 

Pour une base de données IQ volumineuse, vous pouvez utiliser la sauvegarde virtuelle dans SAP IQ. Pour plus d’informations sur la sauvegarde virtuelle, consultez [Sauvegardes virtuelles](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880672184f21015a08dceedc7d19776.html), [Introduction à la sauvegarde virtuelle dans SAP Sybase IQ](https://wiki.scn.sap.com/wiki/display/SYBIQ/Introduction+Virtual+BackUp+(+general++back+up+method+)+in+SAP+Sybase+IQ) et la note SAP [2461985 - Comment sauvegarder de grandes bases de données SAP IQ](https://launchpad.support.sap.com/#/notes/0002461985).

## <a name="disaster-recovery"></a>Récupération d'urgence

Cette section explique la stratégie pour fournir une protection de récupération d’urgence (DR) pour la solution SAP IQ-NLS. Elle complète le document [Reprise d’activité après sinistre pour SAP](../../../site-recovery/site-recovery-sap.md), qui représente les principales ressources d’une approche globale de la reprise d’activité après sinistre SAP. Le processus décrit dans ce document est un résumé. Vous devez valider les étapes exactes et effectuer un test minutieux de votre stratégie de récupération d’urgence. 

Pour SAP IQ, consultez la note SAP [2566083](https://launchpad.support.sap.com/#/notes/0002566083), qui décrit les méthodes d’implémentation d’un environnement de récupération d’urgence en toute sécurité. Dans Azure, vous pouvez aussi utiliser [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) comme stratégie de récupération d’urgence pour SAP IQ. La stratégie de récupération d’urgence de SAP IQ dépend de la façon dont elle est déployée dans Azure et elle doit également être conforme à votre système SAP BW. 

- Déploiement autonome de SAP IQ

  Si vous avez installé SAP IQ en tant que système autonome qui ne dispose pas de redondance au niveau de l’application ou de la haute disponibilité, mais votre entreprise nécessite la configuration de la récupération d’urgence. Sur un système IQ autonome, tous les disques (disques managés Azure) attachés à la machine virtuelle sont locaux. [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) peut être utilisé pour répliquer une machine virtuelle SAP IQ autonome sur la région secondaire. Il réplique les serveurs et tous les disques managés attachés dans la région secondaire. Ainsi, en cas de catastrophe ou de panne, vous pouvez facilement basculer vers votre environnement répliqué et continuer à travailler. Pour lancer la réplication des machines virtuelles SAP IQ vers la région de récupération d’urgence Azure, suivez les instructions dans [Répliquer une machine virtuelle vers Azure](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md). 

- Déploiement à haute disponibilité de SAP IQ

  Si vous avez installé SAP IQ en tant que système hautement disponible où les fichiers binaires et les fichiers de base de données IQ se trouvent sur un disque partagé Azure (Windows uniquement) ou sur le lecteur réseau comme Azure NetApp Files (Linux uniquement). Dans ce type de configuration, vous devez déterminer si vous avez besoin d’un site SAP IQ également hautement disponible sur le site de récupération d’urgence ou si une installation SAP IQ autonome suffira à vos besoins. Si vous avez besoin d’un serveur SAP IQ autonome sur un site de récupération d’urgence, vous pouvez utiliser [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) pour répliquer la machine virtuelle SAP IQ principale sur la région secondaire. Cette opération réplique les serveurs et tous les disques managés attachés localement dans la région secondaire, mais il ne réplique pas le disque partagé Azure ou le lecteur réseau comme Azure NetApp Files. Pour copier des données à partir d’un disque partagé Azure ou d’un lecteur réseau, vous pouvez utiliser n’importe quel outil de copie de fichiers pour répliquer des données entre des régions Azure. Pour plus d’informations sur la façon de copier un volume Azure NetApp Files dans une autre région, consultez [Questions fréquentes (FAQ) sur Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region).

## <a name="next-steps"></a>Étapes suivantes

- [Configurer la reprise d’activité pour un déploiement d’application SAP multiniveau](../../../site-recovery/site-recovery-sap.md)
- [Planification et implémentation de machines virtuelles Azure pour SAP](planning-guide.md)
- [Déploiement de machines virtuelles Azure pour SAP](deployment-guide.md)
