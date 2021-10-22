---
title: Implémenter SAP BW NLS avec SAP IQ sur Azure | Microsoft Docs
description: Planifiez, déployez et configurez la solution SAP BW NLS avec SAP IQ sur Azure.
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
ms.openlocfilehash: 7ece73f467e2dc148e79512c7dd1a6fcc19cf270
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130072629"
---
# <a name="sap-bw-nls-implementation-guide-with-sap-iq-on-azure"></a>Guide d’implémentation SAP BW NLS avec SAP IQ sur Azure

Au fil des années, les clients qui exécutent le système SAP Business Warehouse (BW) voient une croissance exponentielle de la taille des bases de données, ce qui augmente le coût du calcul. Pour obtenir le bon équilibre des coûts et des performances, le client peut utiliser le stockage near-line (NLS) pour migrer les données historiques. 

L’implémentation NLS basée sur SAP IQ est la méthode standard utilisée par SAP pour déplacer des données historiques d’une base de données primaire (SAP HANA ou AnyDB). L’intégration de SAP IQ permet de séparer les données fréquemment consultées des données rarement consultées, ce qui réduit la demande de ressources dans le système SAP BW. 

Ce guide fournit des instructions pour la planification, le déploiement et la configuration de SAP BW NLS avec SAP IQ sur Azure. Ce guide est destiné à couvrir les services et fonctionnalités Azure courants qui sont pertinents pour le déploiement de SAP IQ NLS et ne couvre pas les solutions partenaires NLS. 

Ce guide ne remplace pas la documentation standard de SAP sur le déploiement NLS avec SAP IQ. Au lieu de cela, elle complète la documentation officielle sur l’installation et l’administration. 

## <a name="solution-overview"></a>Vue d’ensemble de la solution

Dans un système opérationnel SAP BW, le volume de données augmente constamment en raison des exigences commerciales et juridiques. Ce volume important de données peut affecter les performances du système et augmente le travail d’administration, ce qui entraîne la nécessité d’implémenter une stratégie d’antériorité des données. 

Si vous souhaitez conserver toutes vos données dans votre système SAP BW sans les supprimer, vous pouvez utiliser l’archivage des données. Les données sont d’abord déplacées vers l’archive ou le stockage near-line, puis supprimées du système SAP BW. Vous pouvez soit accéder directement aux données, soit les charger en fonction des besoins, en fonction de la façon dont les données ont été archivées. 

Les utilisateurs SAP BW peuvent utiliser SAP IQ en tant que solution de stockage near-line (NLS). L’adaptateur SAP IQ en tant que solution near-line est fourni avec le système SAP BW. Avec NLS implémenté, les données fréquemment utilisées sont stockées dans une base de données SAP BW en ligne (SAP HANA ou AnyDB). Les données rarement consultées sont stockées dans SAP IQ, ce qui réduit le coût de gestion des données et améliore les performances du système de SAP BW. Pour garantir la cohérence entre les données en ligne et les données near-line, les partitions archivées sont verrouillées et sont en lecture seule. 

SAP IQ prend en charge deux types d’architectures : simplex et multiplex. Dans une architecture simplex, une instance unique d’un serveur SAP IQ s’exécute sur une seule machine virtuelle. Les fichiers peuvent se trouver sur un ordinateur hôte ou sur un périphérique de stockage réseau. 

> [!Important]
> Pour la solution SAP NLS, seule l’architecture simplex est disponible et évaluée par SAP.

![Diagramme illustrant une vue d’ensemble de la solution SAP IQ.](media/sap-iq-deployment-guide/sap-iq-solution-overview.png)

Dans Azure, le serveur SAP IQ doit être implémenté sur une machine virtuelle distincte. Il n’est pas recommandé d’installer le logiciel SAP IQ sur un serveur existant qui dispose déjà d’une autre instance de base de données en cours d’exécution, car SAP IQ utilise l’intégralité du processeur et de la mémoire pour sa propre utilisation. Un serveur SAP IQ peut être utilisé pour plusieurs implémentations SAP NLS. 

## <a name="support-matrix"></a>Matrice de prise en charge

La matrice de prise en charge pour une solution SAP IQ NLS comprend les éléments suivants :

- **Système d’exploitation** : SAP IQ est certifié au niveau du système d’exploitation uniquement. Vous pouvez exécuter un système d’exploitation certifié SAP IQ sur un environnement Azure, à condition qu’ils soient compatibles pour s’exécuter sur l’infrastructure Azure. Pour plus d’informations, consultez la [note SAP 2133194](https://launchpad.support.sap.com/#/notes/2133194).

- **Compatibilité SAP BW** : le stockage near-line pour SAP IQ est disponible uniquement pour les systèmes SAP BW qui s’exécutent déjà sous Unicode. La [note SAP 1796393](https://launchpad.support.sap.com/#/notes/1796393) contient des informations sur SAP BW.

- **Stockage** : dans Azure, SAP IQ prend en charge le disque managé premium (Windows/Linux), le disque partagé Azure (Windows uniquement) et Azure NetApp Files (Linux uniquement). 

Vérifiez aussi le [Tableau de disponibilité des produits](https://userapps.support.sap.com/sap/support/pam) pour obtenir les dernières informations en date en fonction de votre version de SAP IQ. 

## <a name="sizing"></a>Dimensionnement

Le dimensionnement de SAP IQ est limité au processeur, à la mémoire et au stockage. Vous trouverez les instructions de dimensionnement générales pour SAP IQ sur Azure dans la [note SAP 1951789](https://launchpad.support.sap.com/#/notes/1951789). La recommandation de dimensionnement que vous recevez dans les instructions doit être mappée aux types de machines virtuelles Azure certifiés pour SAP. La [note SAP 1928533](https://launchpad.support.sap.com/#/notes/1928533) fournit la liste des produits SAP et des types de machines virtuelles Azure pris en charge. 

Le Guide de dimensionnement et la feuille de calcul de dimensionnement SAP IQ mentionnés dans la [note SAP 1951789](https://launchpad.support.sap.com/#/notes/1951789) ont été développés pour l’utilisation native d’une base de données SAP IQ. Étant donné qu’elles ne reflètent pas les ressources pour la planification d’une base de données SAP IQ, vous risquez de vous retrouver avec des ressources inutilisées pour SAP NLS.

## <a name="azure-resources"></a>Ressources Azure

### <a name="regions"></a>Régions

Si vous exécutez déjà vos systèmes SAP sur Azure, vous avez probablement identifié votre région. Le déploiement SAP IQ doit se trouver dans la même région que celle de votre système SAP BW pour lequel vous implémentez la solution NLS. 

Pour déterminer l’architecture de SAP IQ, vous devez vous assurer que les services requis par SAP IQ, comme Azure NetApp Files (NFS pour Linux uniquement), sont disponibles dans cette région. Pour vérifier la disponibilité du service dans votre région, vous pouvez consulter la page web [Produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/).

### <a name="availability-sets"></a>Groupes à haute disponibilité 

Pour assurer la redondance des systèmes SAP dans une infrastructure Azure, votre application doit être déployée dans des groupes à haute disponibilité ou des zones de disponibilité. Bien que vous puissiez obtenir une haute disponibilité SAP IQ à l’aide de l’architecture de multiplexage SAP IQ, l’architecture multiplex ne répond pas aux exigences de la solution NLS. 

Pour arriver à une haute disponibilité pour l’architecture SAP IQ simplex, vous devez configurer deux clusters à deux nœuds avec une solution personnalisée. Le cluster SAP IQ à deux nœuds peut être déployé dans des groupes à haute disponibilité ou des zones de disponibilité, mais le stockage Azure qui est lié aux nœuds détermine sa méthode de déploiement. Actuellement, les disques Premium partagés Azure et les Azure NetApp Files ne prennent pas en charge le déploiement par zone. Cela laisse uniquement l’option de déploiement SAP IQ dans les groupes à haute disponibilité. 

### <a name="virtual-machines"></a>Machines virtuelles

Selon le dimensionnement SAP IQ, vous devez mapper vos exigences sur les machines virtuelles Azure. Cette approche est prise en charge dans Azure pour les produits SAP. La [note SAP 1928533](https://launchpad.support.sap.com/#/notes/1928533) est un bon point de départ pour répertorier les types de machines virtuelles Azure pris en charge pour les produits SAP sous Windows et Linux. 

Outre la sélection de types de machines virtuelles strictement pris en charge, vous devez également vérifier si ceux-ci sont disponibles dans une région donnée. Vous pouvez vérifier la disponibilité des types de machine virtuelle sur la page web [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/). Pour choisir le modèle de tarification, vous pouvez consulter [Machines virtuelles Azure pour les charges de travail SAP](planning-guide.md#azure-virtual-machines-for-sap-workload). 

> [!TIP]
> Pour les systèmes de production, nous vous recommandons d’utiliser des machines virtuelles E-Series en raison du rapport entre les cœurs et la mémoire.

### <a name="storage"></a>Stockage

Le stockage Azure a plusieurs types de stockage disponibles pour les clients. Pour plus d’informations à ce sujet, consultez l’article [Quels sont les types de disque disponibles dans Azure ?](../../disks-types.md). 

Certains types de stockage dans Azure ont une utilisation limitée pour les scénarios SAP, mais d’autres types sont bien adaptés ou optimisés pour des scénarios de charges de travail SAP spécifiques. Pour plus d’informations, consultez le guide [Types de stockage Azure pour une charge de travail SAP](planning-guide-storage.md). Il met en évidence les options de stockage adaptées à SAP. 

Pour SAP IQ sur Azure, vous pouvez utiliser les types de stockage Azure suivants. Le choix dépend de votre système d’exploitation (Windows ou Linux) et de la méthode de déploiement (autonome ou haute disponibilité).

- Disques managés Azure

  Un [disque managé](../../managed-disks-overview.md) est un volume de stockage de niveau bloc qui est géré par Azure. Vous pouvez utiliser des disques managés pour le déploiement de SAP IQ simplex. Différents types de disques managés sont disponibles, mais nous vous recommandons d’utiliser des disques [SSD Premium](../../disks-types.md#premium-ssds) pour SAP IQ. 

- Disques partagés Azure

  La fonctionnalité [Disques partagés](../../disks-shared.md) est une nouvelle fonctionnalité pour disques managés Azure qui vous permet de connecter simultanément un disque managé à plusieurs machines virtuelles. Les disques managés partagés n’offrent pas en mode natif un système de fichiers entièrement managé accessible via SMB ou NFS. Vous devez utiliser un gestionnaire de cluster, comme le [cluster de basculement Windows Server](https://github.com/MicrosoftDocs/windowsserverdocs/blob/master/WindowsServerDocs/failover-clustering/failover-clustering-overview.md) (WSFC), qui gère la communication des nœuds de cluster ainsi que le verrouillage en écriture. 
  
  Pour déployer une solution à haute disponibilité pour l’architecture SAP IQ simplex sur Windows, vous pouvez utiliser des disques partagés Azure entre deux nœuds gérés par WSFC. Une architecture de déploiement SAP IQ avec disques partagés Azure est décrite dans l’article [Déployer la solution haute disponibilité SAP IQ NLS à l’aide d’un disque partagé Azure sur Windows Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-shared-disk-on-windows/ba-p/2433089).

- Azure NetApp Files

  Le déploiement de SAP IQ sur Linux peut utiliser [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) comme système de fichiers (protocole NFS) pour installer une solution autonome ou hautement disponible. Cette offre de stockage n’est pas disponible dans toutes les régions. Pour des informations à jour, consultez la page web [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/). L’architecture de déploiement SAP IQ avec Azure NetApp Files est décrite dans l’article [Déployer la solution haute disponibilité SAP IQ-NLS à l’aide d’Azure NetApp Files sur SUSE Linux Enterprise Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172).

Le tableau suivant répertorie les recommandations de chaque type de stockage en fonction du système d’exploitation : 

| Type de stockage        | Windows | Linux |
| ------------------- | ------- | ----- |
| Disques managés Azure | Oui     | Oui   |
| Disques partagés Azure  | Oui     | Non    |
| Azure NetApp Files  | Non      | Oui   |

### <a name="networking"></a>Mise en réseau

Azure fournit une infrastructure réseau qui permet le mappage de tous les scénarios qui peuvent être réalisés pour un système SAP BW qui utilise SAP IQ en tant que stockage near-line. Ces scénarios incluent la connexion à des systèmes locaux, la connexion à des systèmes dans différents réseaux virtuels et d’autres. Pour plus d’informations, consultez [Mise en réseau Microsoft Azure pour les charges de travail SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking).

## <a name="deploy-sap-iq-on-windows"></a>Déployer SAP IQ sur Windows

### <a name="server-preparation-and-installation"></a>Installation et préparation du serveur

Pour préparer les serveurs pour l’implémentation NLS avec SAP IQ sur Windows, vous pouvez obtenir les informations les plus récentes dans la [note SAP 2780668 - Premier guide SAP - Implémentation BW NLS avec SAP IQ](https://launchpad.support.sap.com/#/notes/0002780668). Il contient des informations complètes sur les conditions préalables requises pour les systèmes SAP BW, la disposition du système de fichiers SAP IQ, l’installation, les tâches de post-configuration et l’intégration SAP BW NLS avec SAP IQ.

### <a name="high-availability-deployment"></a>Déploiement de la haute disponibilité

SAP IQ prend en charge une architecture simplex et multiplex. Pour la solution NLS, seule l’architecture de serveur simplex est disponible et évaluée. Simplex est une instance unique d’un serveur SAP IQ s’exécutant sur une seule machine virtuelle. 

Techniquement, vous pouvez obtenir une haute disponibilité SAP IQ à l’aide de l’architecture de serveur multiplex, mais l’architecture multiplex ne répond pas aux exigences de la solution NLS. Pour l’architecture de serveur simplex, SAP ne fournit pas de fonctionnalités ou de procédures pour exécuter SAP IQ dans une configuration à haute disponibilité. 

Pour configurer la haute disponibilité de SAP IQ sur Windows pour l’architecture de serveur simplex, vous devez configurer une solution personnalisée, ce qui nécessite une configuration supplémentaire comme un cluster de basculement Microsoft Windows Server et des disques partagés. Une telle solution personnalisée pour SAP IQ sur Windows est décrite en détail dans [Déployer la solution haute disponibilité SAP IQ NLS à l’aide d’un disque partagé Azure sur Windows Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-shared-disk-on-windows/ba-p/2433089).

### <a name="backup-and-restore"></a>Sauvegarde et restauration

Dans Azure, vous pouvez planifier la sauvegarde de la base de données SAP IQ comme décrit dans [Administration SAP IQ : sauvegarde, restauration et récupération de données](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/5b8309b37f4e46b089465e380c24df59.html). SAP IQ fournit les types de sauvegardes de base de données suivants. Vous trouverez plus d’informations sur chaque type de sauvegarde dans [Scénarios de sauvegarde](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880dc1f84f21015af84f1a6b629dd7a.html).

- **Sauvegarde complète** : copie complète de la base de données. 
- **Sauvegarde incrémentielle** : copie toutes les transactions depuis la dernière sauvegarde de n’importe quel type. 
- **Incrémentielle depuis la sauvegarde complète** sauvegarde toutes les modifications apportées à la base de données depuis la dernière sauvegarde complète.
- **Sauvegarde virtuelle** : copie l’ensemble de la base de données, à l’exception des données de table et des métadonnées du magasin SAP IQ.

En fonction de la taille de votre base de données SAP IQ, vous pouvez planifier la sauvegarde de votre base de données à partir de n’importe quel scénario de sauvegarde. Toutefois, si vous utilisez SAP IQ avec l’interface NLS fournie par SAP, vous souhaiterez peut-être automatiser le processus de sauvegarde pour une base de données SAP IQ. L’automatisation garantit que la base de données SAP IQ peut toujours être récupérée dans un état cohérent sans perdre les données qui sont déplacées entre la base de données primaire et la base de données SAP IQ. Pour plus de détails sur l’automatisation de configuration pour le stockage near-line, reportez-vous à la [note SAP 2741824 : configuration de l’automatisation de la sauvegarde pour le magasin à froid/stockage near-line SAP IQ](https://launchpad.support.sap.com/#/notes/2741824). 

Pour une base de données SAP IQ volumineuse, vous pouvez utiliser la sauvegarde virtuelle. Pour plus d’informations, consultez [Sauvegardes virtuelles](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880672184f21015a08dceedc7d19776.html), [Présentation de la sauvegarde virtuelle dans SAP Sybase IQ](https://wiki.scn.sap.com/wiki/display/SYBIQ/Introduction+Virtual+BackUp+(+general++back+up+method+)+in+SAP+Sybase+IQ). Consultez également la [note SAP 2461985 : Comment sauvegarder une base de données SAP IQ volumineuse](https://launchpad.support.sap.com/#/notes/0002461985).

Si vous utilisez un lecteur réseau (protocole SMB) pour sauvegarder et restaurer un serveur SAP IQ sur Windows, assurez-vous d’utiliser un chemin d’accès UNC pour la sauvegarde. Trois barres obliques inverses (`\\\`) sont nécessaires lorsque vous utilisez un chemin d’accès UNC pour la sauvegarde et la restauration :

```sql
BACKUP DATABASE FULL TO '\\\sapiq.internal.contoso.net\sapiq-backup\backup\data\<filename>'
```

## <a name="deploy-sap-iq-on-linux"></a>Déployer SAP IQ sur Linux

### <a name="server-preparation-and-installation"></a>Installation et préparation du serveur

Pour préparer les serveurs pour l’implémentation NLS avec SAP IQ sur Linux, vous pouvez obtenir les informations les plus récentes dans la [note SAP 2780668 - Premier guide SAP - Implémentation BW NLS avec SAP IQ](https://launchpad.support.sap.com/#/notes/0002780668). Il contient des informations complètes sur les conditions préalables requises pour les systèmes SAP BW, la disposition du système de fichiers SAP IQ, l’installation, les tâches de post-configuration et l’intégration SAP BW NLS avec SAP IQ.

### <a name="high-availability-deployment"></a>Déploiement de la haute disponibilité

SAP IQ prend en charge une architecture simplex et multiplex. Pour la solution NLS, seule l’architecture de serveur simplex est disponible et évaluée. Simplex est une instance unique d’un serveur SAP IQ s’exécutant sur une seule machine virtuelle. 

Techniquement, vous pouvez obtenir une haute disponibilité SAP IQ à l’aide de l’architecture de serveur multiplex, mais l’architecture multiplex ne répond pas aux exigences de la solution NLS. Pour l’architecture de serveur simplex, SAP ne fournit pas de fonctionnalités ou de procédures pour exécuter SAP IQ dans une configuration à haute disponibilité.

Pour configurer la haute disponibilité SAP IQ sur Linux pour l’architecture de serveur simplex, vous devez configurer une solution personnalisée, ce qui nécessite une configuration supplémentaire, comme Pacemaker. Une telle solution personnalisée pour SAP IQ sur Linux est décrite en détail dans le [Déployer la solution haute disponibilité NLS SAP IQ à l’aide d’Azure NetApp Files sur SUSE Linux Enterprise Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172).

### <a name="backup-and-restore"></a>Sauvegarde et restauration

Dans Azure, vous pouvez planifier la sauvegarde de la base de données SAP IQ comme décrit dans [Administration SAP IQ : sauvegarde, restauration et récupération de données](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/5b8309b37f4e46b089465e380c24df59.html). SAP IQ fournit les types de sauvegardes de base de données suivants. Vous trouverez plus d’informations sur chaque type de sauvegarde dans [Scénarios de sauvegarde](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880dc1f84f21015af84f1a6b629dd7a.html).

- **Sauvegarde complète** : copie complète de la base de données. 
- **Sauvegarde incrémentielle** : copie toutes les transactions depuis la dernière sauvegarde de n’importe quel type. 
- **Incrémentielle depuis la sauvegarde complète** sauvegarde toutes les modifications apportées à la base de données depuis la dernière sauvegarde complète.
- **Sauvegarde virtuelle** : copie l’ensemble de la base de données, à l’exception des données de table et des métadonnées du magasin SAP IQ.

En fonction de la taille de votre base de données SAP IQ, vous pouvez planifier la sauvegarde de votre base de données à partir de n’importe quel scénario de sauvegarde. Toutefois, si vous utilisez SAP IQ avec l’interface NLS fournie par SAP, vous souhaiterez peut-être automatiser le processus de sauvegarde pour une base de données SAP IQ. L’automatisation garantit que la base de données SAP IQ peut toujours être récupérée dans un état cohérent sans perdre les données qui sont déplacées entre la base de données primaire et la base de données SAP IQ. Pour plus de détails sur l’automatisation de configuration pour le stockage near-line, reportez-vous à la [note SAP 2741824 : configuration de l’automatisation de la sauvegarde pour le magasin à froid/stockage near-line SAP IQ](https://launchpad.support.sap.com/#/notes/2741824). 

Pour une base de données SAP IQ volumineuse, vous pouvez utiliser la sauvegarde virtuelle. Pour plus d’informations, consultez [Sauvegardes virtuelles](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880672184f21015a08dceedc7d19776.html), [Présentation de la sauvegarde virtuelle dans SAP Sybase IQ](https://wiki.scn.sap.com/wiki/display/SYBIQ/Introduction+Virtual+BackUp+(+general++back+up+method+)+in+SAP+Sybase+IQ). Consultez également la [note SAP 2461985 : Comment sauvegarder une base de données SAP IQ volumineuse](https://launchpad.support.sap.com/#/notes/0002461985).

## <a name="disaster-recovery"></a>Récupération d'urgence

Cette section explique la stratégie pour fournir une protection de récupération d’urgence (DR) pour la solution SAP IQ NLS. Elle complète l’article [Configurer la récupération d'urgence pour SAP](../../../site-recovery/site-recovery-sap.md), qui représente les principales ressources d’une approche globale de la récupération d'urgence SAP. Le processus décrit dans cet article est un résumé. Vous devez valider les étapes exactes et effectuer un test minutieux de votre stratégie de récupération d’urgence. 

Pour SAP IQ, consultez la [note SAP 2566083](https://launchpad.support.sap.com/#/notes/0002566083), qui décrit les méthodes d’implémentation d’un environnement de récupération d’urgence en toute sécurité. Dans Azure, vous pouvez aussi utiliser [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) comme stratégie de récupération d’urgence pour SAP IQ. La stratégie de récupération d’urgence de SAP IQ dépend de la façon dont elle est déployée dans Azure et elle doit également être conforme à votre système SAP BW. 

### <a name="standalone-deployment-of-sap-iq"></a>Déploiement autonome de SAP IQ

Si vous avez installé SAP IQ en tant que système autonome qui ne dispose pas d’une redondance au niveau de l’application ou d’une haute disponibilité, mais que l’entreprise a besoin d’une installation de récupération d’urgence, tous les disques (disques gérés par Azure) attachés à la machine virtuelle sont locaux. 
  
[Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) peut être utilisé pour répliquer une machine virtuelle SAP IQ autonome sur la région secondaire. Il réplique les serveurs et tous les disques managés attachés dans la région secondaire. Ainsi, en cas de catastrophe ou de panne, vous pouvez facilement basculer vers votre environnement répliqué et continuer à travailler. Pour lancer la réplication des machines virtuelles SAP IQ vers la région de récupération d’urgence Azure, suivez les instructions dans [Répliquer une machine virtuelle vers Azure](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md). 

### <a name="highly-available-deployment-of-sap-iq"></a>Déploiement à haute disponibilité de SAP IQ

Si vous avez installé SAP IQ en tant que système hautement disponibilité où les fichiers binaires et les fichiers de base de données SAP IQ se trouvent sur un disque partagé Azure (Windows uniquement) ou sur le lecteur réseau comme Azure NetApp Files (Linux uniquement), vous devez identifier :

- Si vous avez besoin du même système SAP IQ à haute disponibilité sur le site de récupération d’urgence.
- Si une instance SAP IQ autonome répondra aux besoins de votre entreprise. 
  
Si vous avez besoin d’une instance SAP IQ autonome sur un site de récupération d’urgence, vous pouvez utiliser [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) pour répliquer la machine virtuelle SAP IQ principale sur la région secondaire. Cette opération réplique les serveurs et tous les disques managés attachés localement dans la région secondaire, mais il ne réplique pas un disque partagé Azure ou un lecteur réseau comme Azure NetApp Files. 
  
Pour copier des données à partir d’un disque partagé Azure ou d’un lecteur réseau, vous pouvez utiliser n’importe quel outil de copie de fichiers pour répliquer des données entre des régions Azure. Pour plus d’informations sur la façon de copier un volume Azure NetApp Files dans une autre région, consultez [Questions fréquentes (FAQ) sur Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region).

## <a name="next-steps"></a>Étapes suivantes

- [Configurer la reprise d’activité pour un déploiement d’application SAP multiniveau](../../../site-recovery/site-recovery-sap.md)
- [Planification et implémentation de machines virtuelles Azure pour SAP](planning-guide.md)
- [Déploiement de machines virtuelles Azure pour SAP](deployment-guide.md)
