---
title: Planification d’un déploiement Azure File Sync | Microsoft Docs
description: Planifiez un déploiement avec Azure File Sync, un service qui vous permet de mettre en cache plusieurs partages de fichiers Azure sur un serveur Windows local ou une machine virtuelle cloud.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: b106c82e3755fbd0e02f12a769d80ce4761cf026
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285856"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planification d’un déploiement de synchronisation de fichiers Azure

:::row:::
    :::column:::
        [![Interview et démo de présentation d’Azure File Sync - cliquez pour lancer la lecture](./media/storage-sync-files-planning/azure-file-sync-interview-video-snapshot.png)](https://www.youtube.com/watch?v=nfWLO7F52-s)
    :::column-end:::
    :::column:::
        Azure File Sync est un service qui permet de mettre en cache plusieurs partages de fichiers Azure sur un serveur Windows local ou une machine virtuelle cloud. 
        
        Cet article vous présente les concepts et fonctionnalités d’Azure File Sync. Une fois que vous êtes familiarisé avec Azure File Sync, vous pouvez consulter le [Guide de déploiement Azure File Sync](storage-sync-files-deployment-guide.md) pour tester ce service.        
    :::column-end:::
:::row-end:::

Les fichiers sont stockés sur le cloud dans les [partages de fichiers Azure](storage-files-introduction.md). Les partages de fichiers Azure peuvent être utilisés de deux façons : en montant directement ces partages de fichiers Azure serverless (SMB), ou en mettant en cache les partages de fichiers Azure en local avec Azure File Sync. L'option de déploiement que vous choisissez détermine les aspects à prendre en compte lors de la planification de votre déploiement. 

- **Montage direct d'un partage de fichiers Azure** : étant donné qu'Azure Files fournit un accès SMB, vous pouvez monter des partages de fichiers Azure localement ou dans le cloud à l'aide du client SMB standard disponible sous Windows, macOS et Linux. Dans la mesure où les partages de fichiers Azure sont serverless, vous n'avez aucun serveur de fichiers ou appareil NAS à gérer lors des déploiements liés à des scénarios de production. Concrètement, cela signifie que vous n'avez aucun correctif logiciel à appliquer ni aucun disque physique à remplacer. 

- **Mise en cache d'un partage de fichiers Azure localement à l'aide d'Azure File Sync** : Azure File Sync vous permet de centraliser les partages de fichiers de votre organisation dans Azure Files, tout en conservant la flexibilité, le niveau de performance et la compatibilité d'un serveur de fichiers local. Azure File Sync transforme une instance Windows Server locale (ou cloud) en cache rapide de votre partage de fichiers Azure. 

## <a name="management-concepts"></a>Concepts de gestion
Un déploiement Azure File Sync repose sur les trois objets de gestion suivants :

- **Partage de fichiers Azure** : un partage de fichiers Azure est un partage de fichiers cloud serverless qui fournit le *point de terminaison cloud* d'une relation de synchronisation Azure File Sync. Les fichiers d'un partage de fichiers Azure sont directement accessibles via le protocole SMB ou FileREST, mais lorsque le partage de fichiers Azure est utilisé avec Azure File Sync, il est préférable de privilégier un accès aux fichiers via le cache de Windows Server car le mécanisme de détection des modifications d'Azure Files est moins efficace que celui de Windows Server. Par conséquent, les modifications directement apportées au partage de fichiers Azure mettent du temps à se propager jusqu'aux points de terminaison de serveur.
- **Point de terminaison de serveur** : emplacement de l'instance de Windows Server qui est synchronisé avec un partage de fichiers Azure. Il peut s'agir d'un dossier spécifique ou de la racine d'un volume. Plusieurs points de terminaison de serveur peuvent coexister sur un même volume si leurs espaces de noms ne se chevauchent pas.
- **Groupe de synchronisation** : objet qui définit la relation de synchronisation entre un **point de terminaison cloud**, ou un partage de fichiers Azure, et un point de terminaison de serveur. Les points de terminaison dans un groupe de synchronisation sont synchronisés entre eux. Par exemple, si vous voulez gérer deux ensembles distincts de fichiers avec Azure File Sync, vous créez deux groupes de synchronisation et ajoutez des points de terminaison différents dans chacun de ces groupes.

### <a name="azure-file-share-management-concepts"></a>Concepts de gestion des partages de fichiers Azure
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Concepts de gestion d'Azure File Sync
Les groupes de synchronisation sont déployés dans des **services de synchronisation de stockage** ; il s'agit d'objets de niveau supérieur qui inscrivent les serveurs à utiliser avec Azure File Sync et contiennent les relations des groupes de synchronisation. La ressource de service de synchronisation de stockage est un pair de la ressource de compte de stockage. Elle peut être déployée de la même façon dans des groupes de ressources Azure. Un service de synchronisation de stockage peut créer des groupes de synchronisation contenant des partages de fichiers Azure répartis sur différents comptes de stockage et différentes instances inscrites de Windows Server.

Pour créer un groupe de synchronisation dans un service de synchronisation de stockage, vous devez préalablement inscrire une instance de Windows Server auprès du service de synchronisation de stockage. L'objet **serveur inscrit** qui est alors créé représente une relation d'approbation entre votre serveur, ou cluster, et le service de synchronisation de stockage. Pour inscrire un service de synchronisation de stockage, vous devez d'abord installer l'agent Azure File Sync sur le serveur. Un serveur ou un cluster ne peut être inscrit qu'auprès d'un seul service de synchronisation de stockage à la fois.

Un groupe de synchronisation contient un point de terminaison cloud, ou un partage de fichiers Azure, et au moins un point de terminaison de serveur. L'objet point de terminaison de serveur contient les paramètres de configuration de la fonctionnalité de **hiérarchisation cloud**, qui fournit la fonctionnalité de mise en cache d'Azure File Sync. Pour la synchronisation avec un partage de fichiers Azure, le compte de stockage contenant le partage de fichiers Azure doit se trouver dans la même région Azure que le service de synchronisation de stockage.

> [!Important]  
> Vous pouvez apporter des modifications à l’espace de noms d’un point de terminaison cloud ou d’un point de terminaison de serveur du groupe de synchronisation et faire en sorte que vos fichiers soient synchronisés avec les autres points de terminaison du groupe. Si vous apportez une modification au point de terminaison cloud (partage de fichiers Azure) directement, cette modification doit être détectée au préalable par un travail de détection des modifications Azure File Sync. Un travail de détection des modifications est lancé pour un point de terminaison cloud toutes les 24 heures uniquement. Pour plus d’informations, consultez [Questions fréquentes (FAQ) sur Azure Files](storage-files-faq.md#afs-change-detection).

### <a name="consider-the-count-of-storage-sync-services-needed"></a>Prise en compte du nombre de services de synchronisation de stockage nécessaires
Dans une section précédente, nous avons abordé la ressource principale à configurer pour Azure File Sync : un *service de synchronisation de stockage*. Un serveur Windows ne peut être inscrit qu’à un seul service de synchronisation de stockage. Par conséquent, il est souvent préférable de déployer un seul service de synchronisation de stockage et d’y inscrire tous les serveurs. 

Ne créez plusieurs services de synchronisation de stockage que dans les cas suivants :
* Vous possédez des ensembles distincts de serveurs qui ne doivent jamais s’échanger de données. Dans ce cas, concevez le système de façon à exclure certains ensembles de serveurs à synchroniser avec un partage de fichiers Azure déjà utilisé comme point de terminaison cloud dans un groupe de synchronisation au sein d’un autre service de synchronisation de stockage. En d’autres termes, les serveurs Windows inscrits auprès d’un autre service de synchronisation de stockage ne peuvent pas se synchroniser avec le même partage de fichiers Azure.
* Vous avez besoin de plus de serveurs inscrits ou de groupes de synchronisation que ne peut en prendre en charge un seul service de synchronisation de stockage. Pour plus d’informations, consultez [Cibles de mise à l’échelle Azure File Sync](storage-files-scale-targets.md#azure-file-sync-scale-targets).

## <a name="plan-for-balanced-sync-topologies"></a>Planification de topologies de synchronisation équilibrée
Avant de déployer des ressources, il est important de planifier ce que vous allez synchroniser sur un serveur local et avec quel partage de fichiers Azure. Le fait de créer un plan vous aidera à déterminer le nombre de comptes de stockage, de partages de fichiers Azure et de ressources de synchronisation dont vous aurez besoin. Ces considérations sont toujours pertinentes, même si vos données ne résident pas sur un serveur Windows ou sur le serveur que vous souhaitez utiliser à long terme. La [section migration](#migration) peut vous aider à déterminer les chemins de migration adaptés à votre situation.

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="windows-file-server-considerations"></a>Considérations relatives aux serveurs de fichiers Windows
Pour activer la fonctionnalité de synchronisation sur Windows Server, vous devez installer l'agent téléchargeable Azure File Sync. L’agent Azure File Sync fournit deux composants principaux : `FileSyncSvc.exe`, le service Windows en arrière-plan chargé de la supervision des modifications sur les points de terminaison de serveur et du lancement des sessions de synchronisation, et `StorageSync.sys`, un filtre de système de fichiers qui permet la hiérarchisation cloud et une récupération d’urgence rapide.  

### <a name="operating-system-requirements"></a>Système d'exploitation requis
Azure File Sync est pris en charge avec les versions suivantes de Windows Server :

| Version | Références prises en charge | Options de déploiement prises en charge |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datacenter, Standard et IoT | Complète et Minimale |
| Windows Server 2016 | Datacenter, Standard et Storage Server | Complète et Minimale |
| Windows Server 2012 R2 | Datacenter, Standard et Storage Server | Complète et Minimale |

Nous prévoyons d’ajouter la prise en charge de versions ultérieures de Windows Server quand elles seront disponibles.

> [!Important]  
> Nous vous recommandons de mettre régulièrement à jour tous les serveurs que vous utilisez avec Azure File Sync en installant les dernières mises à jour disponibles sur Windows Update. 

### <a name="minimum-system-resources"></a>Ressources système minimum
Azure File Sync requiert un serveur, physique ou virtuel, avec au moins un processeur et un minimum de 2 Gio de mémoire.

> [!Important]  
> Si le serveur est exécuté sur une machine virtuelle sur laquelle la mémoire dynamique est activée, la machine virtuelle doit être configurée avec un minimum de 2048 Mio de mémoire.

Pour la plupart des charges de travail de production, nous déconseillons de configurer un serveur de synchronisation Azure File Sync en se contentant de la configuration minimale requise. Pour plus d'informations, consultez [Ressources système recommandées](#recommended-system-resources).

### <a name="recommended-system-resources"></a>Ressources système recommandées
Comme pour toute fonctionnalité ou application serveur, les ressources système requises pour Azure File Sync dépendent de l'échelle de déploiement. Un déploiement sur serveur important nécessite des ressources système importantes. Pour Azure File Sync, l'échelle est déterminée par le nombre d'objets répartis sur les points de terminaison de serveur et par l'évolution sur le jeu de données. Un même serveur peut avoir des points de terminaison de serveur dans plusieurs groupes de synchronisation, et le nombre d'objets répertoriés dans le tableau suivant représente l'espace de noms complet auquel un serveur est attaché. 

Par exemple, le point de terminaison de serveur A avec 10 millions d'objets + le point de terminaison de serveur B avec 10 millions d'objets = 20 millions d'objets. Pour cet exemple de déploiement, nous recommandons 8 UC, 16 Gio de mémoire pour l'état stable et (si possible) 48 Gio de mémoire pour la migration initiale.
 
Les données d’espace de noms sont stockées en mémoire pour des raisons de performance. Pour cette raison, les espaces de noms plus grands nécessitent davantage de mémoire pour maintenir de bonnes performances, et une évolution plus importante requiert davantage d’UC pour le traitement. 
 
Dans le tableau suivant, nous avons fourni la taille de l'espace de noms, ainsi qu'une conversion en capacité pour les partages de fichiers à usage général standard, sachant que la taille de fichier moyenne est 512 Kio. Si les tailles de vos fichiers sont plus petites, envisagez d’ajouter de la mémoire supplémentaire pour obtenir la même capacité. Basez la configuration de votre mémoire sur la taille de l’espace de noms.

| Taille de l’espace de noms - fichiers et répertoires (millions)  | Capacité type (Tio)  | Cœurs de processeur  | Mémoire recommandée (Gio) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (synchronisation initiale)/2 (évolution classique)      |
| 5        | 2.3     | 2        | 16 (synchronisation initiale)/4 (évolution classique)    |
| 10       | 4,7     | 4        | 32 (synchronisation initiale)/8 (évolution classique)   |
| 30       | 14,0    | 8        | 48 (synchronisation initiale)/16 (évolution classique)   |
| 50       | 23,3    | 16       | 64 (synchronisation initiale)/32 (évolution classique)  |
| 100*     | 46,6    | 32       | 128 (synchronisation initiale)/32 (évolution classique)  |

\*Toute synchronisation de plus de 100 millions de fichiers et répertoires est actuellement déconseillée. Il s'agit d'une limite conditionnelle basée sur les seuils que nous avons testés. Pour plus d’informations, voir [Objectifs de performance et d’extensibilité d’Azure Files](storage-files-scale-targets.md#azure-file-sync-scale-targets).

> [!TIP]
> La synchronisation initiale d’un espace de noms est une opération intensive et nous vous recommandons d’allouer davantage de mémoire jusqu’à ce que la synchronisation initiale soit terminée. Cela n’est pas obligatoire, mais peut accélérer la synchronisation initiale. 
> 
> L’évolution standard est que 0,5 % de l’espace de noms change chaque jour. Pour des niveaux d’évolution plus élevés, envisagez d’ajouter davantage d’UC. 

- Un volume attaché localement formaté avec le système de fichiers NTFS.

### <a name="evaluation-cmdlet"></a>Applet de commande d’évaluation
Avant de déployer Azure File Sync, vous devez évaluer s’il est compatible avec votre système à l’aide de l’applet de commande d’évaluation d’Azure File Sync. Cette cmdlet recherche les problèmes potentiels liés au système de fichiers et au jeu de données, comme des caractères ou une version de système d’exploitation non pris en charge. Ses vérifications couvrent la plupart des fonctionnalités mentionnées ci-dessous, mais pas toutes. Nous vous conseillons de lire attentivement le reste de cette section pour garantir le bon déroulement de votre déploiement. 

Vous pouvez installer l’applet de commande d’évaluation en installant le module Az PowerShell en suivant ces instructions : [Installez et configurez Azure PowerShell](/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Usage  
Vous pouvez appeler l’outil d’évaluation de différentes manières : vous pouvez effectuer les vérifications du système, les vérifications du jeu de données, ou les deux. Pour effectuer à la fois les vérifications du système et les vérifications du jeu de données : 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Pour tester uniquement votre jeu de données :
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Pour tester uniquement la configuration requise :
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name> -SkipNamespaceChecks
```
 
Pour afficher les résultats au format CSV :
```powershell
$validation = Invoke-AzStorageSyncCompatibilityCheck C:\DATA
$validation.Results | Select-Object -Property Type, Path, Level, Description, Result | Export-Csv -Path C:\results.csv -Encoding utf8
```

### <a name="file-system-compatibility"></a>Compatibilité du système de fichiers
Azure File Sync n'est pris en charge que sur les volumes NTFS en attachement direct. Sur Windows Server, le stockage en attachement direct, ou DAS, signifie que le système d'exploitation Windows Server est propriétaire du système de fichiers. Le DAS peut être fourni en attachant physiquement des disques au serveur de fichiers, en attachant des disques virtuels à une machine virtuelle de serveur de fichiers (comme une machine virtuelle hébergée par Hyper-V), ou même via ISCSI.

Seuls les volumes NTFS sont pris en charge ; ReFS, FAT, FAT32 et autres systèmes de fichiers ne sont pas pris en charge.

Le tableau suivant présente l'état d'interopérabilité des fonctionnalités du système de fichiers NTFS : 

| Fonctionnalité | État de la prise en charge | Notes |
|---------|----------------|-------|
| Listes ACL | entièrement prise en charge | Les listes de contrôle d'accès discrétionnaire de type Windows sont conservées par Azure File Sync et appliquées par Windows Server sur les points de terminaison de serveur. Les listes de contrôle d'accès peuvent également être appliquées lors du montage direct du partage de fichiers Azure, mais cela nécessite une configuration supplémentaire. Pour plus d'informations, consultez la [section Identité](#identity). |
| Liens physiques | Ignoré | |
| Liens symboliques | Ignoré | |
| Points de montage | Partiellement pris en charge | Les points de montage peuvent être la racine d’un point de terminaison de serveur, mais ils sont ignorés s’ils se trouvent dans un espace de noms du point de terminaison de serveur. |
| Jonctions | Ignoré | Par exemple, les dossiers DfrsrPrivate de système de fichiers DFS et DFSRoots. |
| Points d’analyse | Ignoré | |
| Compression NTFS | entièrement prise en charge | |
| Fichiers partiellement alloués | entièrement prise en charge | Les fichiers partiellement alloués sont synchronisés (ils ne sont pas bloqués), mais ils sont synchronisés dans le cloud sous forme de fichiers complets. Si le contenu d’un fichier change dans le cloud (ou sur un autre serveur), le fichier n’est plus partiellement alloué quand le changement est téléchargé. |
| Autres flux de données | Conservés, mais pas synchronisés | Par exemple, les balises de classification créées par l’infrastructure de classification des fichiers ne sont pas synchronisées. Les balises de classification qui existent sur des fichiers à chacun des points de terminaison du serveur ne sont pas affectées. |

<a id="files-skipped"></a>Azure File Sync ignore également certains fichiers temporaires et dossiers système :

| Fichier/Dossier | Remarque |
|-|-|
| pagefile.sys | Fichier spécifique au système |
| Desktop.ini | Fichier spécifique au système |
| thumbs.db | Fichier temporaire pour les miniatures |
| ehthumbs.db | Fichier temporaire pour les miniatures multimédia |
| ~$\*.\* | Fichier temporaire Office |
| \*.tmp | Fichier temporaire |
| \*.laccdb | Accès au fichier de verrouillage de base de données|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Fichier de synchronisation interne|
| \\System Volume Information | Dossier spécifique au volume |
| $RECYCLE.BIN| Dossier |
| \\SyncShareState | Dossier de synchronisation |

### <a name="failover-clustering"></a>Clustering de basculement
Le clustering de basculement Windows Server est pris en charge par Azure File Sync pour l’option de déploiement « Serveur de fichiers pour une utilisation générale ». Le clustering de basculement n’est pas pris en charge sur le « serveur de fichiers avec montée en puissance parallèle pour les données d’application » (SOFS) ou sur les volumes partagés de cluster (CSV).

> [!Note]  
> L’agent Azure File Sync doit être installé sur chaque nœud d’un cluster de basculement pour que la synchronisation fonctionne correctement.

### <a name="data-deduplication"></a>Déduplication des données
**Windows Server 2016 et Windows Server 2019**   
La déduplication des données est prise en charge indépendamment du fait que la hiérarchisation cloud soit activée ou désactivée sur un ou plusieurs points de terminaison de serveur du volume pour Windows Server 2016 et Windows Server 2019. Le fait d’activer la déduplication des données sur un volume pour lequel la hiérarchisation cloud est activée vous permet de mettre en cache plus de fichiers en local sans avoir à provisionner plus de stockage. 

Quand la déduplication des données est activée sur un volume où la hiérarchisation cloud est activée, les fichiers optimisés par la déduplication à l’emplacement du point de terminaison de serveur sont hiérarchisés d’une façon similaire à un fichier normal, en fonction des paramètres de stratégie de hiérarchisation cloud. Une fois que les fichiers optimisés par la déduplication ont été hiérarchisés, le travail de nettoyage de la mémoire de la déduplication des données s’exécute automatiquement pour récupérer de l’espace disque en supprimant les blocs inutiles qui ne sont plus référencés par d’autres fichiers sur le volume.

Notez que les économies faites sur les volumes s’appliquent seulement au serveur ; vos données dans le partage de fichiers Azure ne sont pas dédupliquées.

> [!Note]  
> Pour prendre en charge la déduplication des données sur des volumes en ayant activé la hiérarchisation cloud sur Windows Server 2019, la mise à jour Windows [KB4520062](https://support.microsoft.com/help/4520062) doit être installé et la version 9.0.0.0 (ou supérieure) de l’agent Azure File Sync est requise.

**Windows Server 2012 R2**  
Azure File Sync ne prend pas en charge la déduplication des données et la hiérarchisation cloud sur le même volume sur Windows Serveur 2012 R2. Si la déduplication des données est activée sur un volume, la hiérarchisation cloud doit être désactivée. 

**Remarques**
- Si la déduplication des données est installée avant d’installer l’agent Azure File Sync, un redémarrage est nécessaire pour prendre en charge la déduplication des données et la hiérarchisation cloud sur le même volume.
- Si la déduplication des données est activée sur un volume une fois la hiérarchisation cloud activée, la tâche d’optimisation de la déduplication initiale permettra d’optimiser les fichiers qui ne sont pas déjà hiérarchisés dans le volume et aura l’impact suivant sur la hiérarchisation cloud :
    - La stratégie d’espace disponible continuera à hiérarchiser les fichiers en fonction de l’espace disponible sur le volume à l’aide de la carte thermique.
    - La stratégie de date ignorera la hiérarchisation des fichiers qui ont été éligibles pour la hiérarchisation en raison de l’accès de la tâche d’optimisation de la déduplication aux fichiers.
- En ce qui concerne les tâches d’optimisation de la déduplication en cours, la hiérarchisation cloud avec la stratégie de date sera retardée par le paramètre [MinimumFileAgeDays](/powershell/module/deduplication/set-dedupvolume) de déduplication des données, si le fichier n’est pas déjà hiérarchisé. 
    - Exemple : Si la valeur du paramètre MinimumFileAgeDays est de sept jours et la valeur de la stratégie de date de la hiérarchisation cloud est de 30 jours, la stratégie de date hiérarchisera les fichiers après 37 jours.
    - Remarque : Une fois un fichier hiérarchisé par Azure File Sync, la tâche d’optimisation de la déduplication ignorera le fichier.
- Si un serveur exécutant Windows Server 2012 R2 avec l’agent Azure File Sync installé est mis à niveau vers Windows Server 2016 ou Windows Server 2019, les étapes suivantes doivent être effectuées pour prendre en charge la déduplication des données et la hiérarchisation cloud sur le même volume :  
    - Désinstallez l’agent Azure File Sync pour Windows Server 2012 R2 et redémarrez le serveur.
    - Téléchargez l’agent Azure File Sync pour la nouvelle version du système d’exploitation serveur (Windows Server 2016 ou Windows Server 2019).
    - Installez l’agent Azure File Sync et redémarrez le serveur.  
    
    Remarque : Les paramètres de configuration Azure File Sync sur le serveur sont conservés lorsque l’agent est désinstallé et réinstallé.

### <a name="distributed-file-system-dfs"></a>Système de fichiers DFS
Azure File Sync prend en charge l’interopérabilité avec les espaces de noms DFS (DFS-N) et la réplication DFS (DFS-R).

**Espaces de noms DFS (DFS-N)**  : Azure File Sync est entièrement pris en charge sur les serveurs DFS-N. Vous pouvez installer l’agent Azure File Sync sur un ou plusieurs membres DFS-N pour synchroniser des données entre les points de terminaison de serveur et le point de terminaison cloud. Pour plus d’informations, consultez [Vue d’ensemble des espaces de noms DFS](/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Réplication DFS (DFS-R)**  : Comme DFS-R et Azure File Sync sont deux solutions de réplication, dans la plupart des cas, nous recommandons de remplacer DFS-R par Azure File Sync. Il existe cependant plusieurs scénarios où vous souhaiterez utiliser DFS-R et Azure File Sync :

- Vous migrez d’un déploiement de DFS-R vers un déploiement d’Azure File Sync. Pour plus d’informations, consultez [Migrer un déploiement de la réplication DFS (DFS-R) vers Azure File Sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Tous les serveurs locaux ayant besoin d’une copie de vos données de fichiers ne peuvent pas être connectés directement à Internet.
- Les serveurs de succursale consolident les données sur un serveur hub pour lequel vous souhaitez utiliser Azure File Sync.

Pour qu’Azure File Sync et DFS-R fonctionnent côte à côte :

1. La hiérarchisation cloud d’Azure File Sync doit être désactivée sur les volumes avec des dossiers répliqué DFS-R.
2. Les points de terminaison de serveur ne doivent pas être configurés sur des dossiers de réplication DFS-R en lecture seule.

Pour plus d’informations, consultez [Vue d’ensemble de la réplication DFS](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj127250(v=ws.11)).

### <a name="sysprep"></a>Sysprep
L’utilisation de sysprep sur un serveur sur lequel l’agent Azure File Sync est installé n’est pas prise en charge et peut produire des résultats inattendus. L’installation de l’agent et l’inscription du serveur doivent être effectués après avoir déployé l’image du serveur et terminé la mini-configuration de sysprep.

### <a name="windows-search"></a>Recherche Windows
Si la hiérarchisation cloud est activée sur un point de terminaison de serveur, les fichiers qui sont hiérarchisés sont ignorés et ne sont pas indexés par Windows Search. Les fichiers non hiérarchisés sont indexés correctement.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Autres solutions de gestion hiérarchique du stockage (HSM)
Aucune autre solution HSM ne doit être utilisée avec Azure File Sync.

## <a name="performance-and-scalability"></a>Performances et extensibilité

Étant donné que l’agent Azure File Sync s’exécute sur un ordinateur Windows Server qui se connecte aux partages de fichiers Azure, les performances de synchronisation réelles dépendent de plusieurs facteurs dans votre infrastructure : Windows Server et la configuration de disque sous-jacente, la bande passante réseau entre le serveur et le stockage Azure, la taille des fichiers, la taille totale du jeu de données et l’activité sur le jeu de données. Comme Azure File Sync fonctionne au niveau du fichier, les caractéristiques de performances d’une solution Azure File Sync est exprimée de façon optimale en nombre d’objets (fichiers et répertoires) traités par seconde.

Les modifications apportées au partage de fichiers Azure avec le portail Azure ou SMB ne sont pas immédiatement détectées et répliquées comme le sont des modifications apportées au point de terminaison de serveur. Azure Files n’a pas encore de notifications ou journalisation des modifications. Il n’existe donc aucun moyen de lancer automatiquement une session de synchronisation lorsque des fichiers sont modifiés. Sur Windows Server, Azure File Sync utilise la [journalisation du nombre de séquences de mise à jour de Windows](/windows/win32/fileio/change-journals) pour lancer automatiquement une session de synchronisation en cas de modification des fichiers.

Pour détecter les modifications apportées au partage de fichiers Azure, Azure File Sync a une tâche planifiée appelée tâche de détection des modifications. Une tâche de détection des modifications énumère tous les fichiers inclus dans le partage de fichiers, puis les compare à la version de synchronisation de ces fichiers. Lorsque la tâche de détection des modifications détermine que des fichiers ont changé, Azure File Sync lance une session de synchronisation. La tâche de détection des modifications est lancée toutes les 24 heures. Étant donné que la tâche de détection des modifications fonctionne en énumérant chaque fichier dans le partage de fichiers Azure, elle prend plus de temps dans les espaces de noms de grande taille que dans les plus petits. Pour des espaces de noms de grande taille, plus de 24 heures peuvent être nécessaires pour déterminer les fichiers qui ont été modifiés.

Pour plus d’informations, consultez [Métriques de niveau de performance Azure file Sync](storage-files-scale-targets.md#azure-file-sync-performance-metrics) et [Cibles de mise à l’échelle Azure File Sync](storage-files-scale-targets.md#azure-file-sync-scale-targets).

## <a name="identity"></a>Identité
Azure File Sync fonctionne avec votre identité AD standard sans aucune configuration particulière en plus de la configuration de la synchronisation. En utilisant Azure File Sync, vous vous attendez probablement à ce que la plupart des accès passent par les serveurs de mise en cache Azure File Sync plutôt que par le partage de fichiers Azure. Comme les points de terminaison de serveur se trouvent sur Windows Server, et que Windows Server prend en charge AD et les listes de contrôle d’accès de type Windows depuis longtemps, la seule chose à faire est de s’assurer que les serveurs de fichiers Windows inscrits auprès du service de synchronisation de stockage sont joints au domaine. Azure File Sync stocke les listes de contrôle d'accès sur les fichiers du partage de fichiers Azure et les réplique sur tous les points de terminaison de serveur.

Même si les modifications apportées directement au partage de fichiers Azure mettent plus longtemps à se synchroniser avec les points de terminaison de serveur au sein du groupe de synchronisation, vous pouvez également vous assurer qu'il vous est possible d'appliquer vos autorisations AD sur votre partage de fichiers directement dans le cloud. Pour ce faire, vous devez effectuer une jonction de domaine de votre compte de stockage à votre instance locale d'AD, tout comme vos serveurs de fichiers Windows sont joints à un domaine. Pour en savoir plus sur la jonction de domaine de votre compte de stockage à une instance d'Active Directory détenue par le client, consultez [Présentation d'Azure Files Active Directory](storage-files-active-directory-overview.md).

> [!Important]  
> La jonction de domaine de votre compte de stockage à Active Directory n'est pas obligatoire pour déployer Azure File Sync. Il s'agit d'une étape totalement facultative qui permet au partage de fichiers Azure d'appliquer des listes de contrôle d'accès locales lorsque les utilisateurs procèdent à un montage direct du partage de fichiers Azure.

## <a name="networking"></a>Mise en réseau
L'agent Azure File Sync communique avec votre service de synchronisation de stockage et votre partage de fichiers Azure à l'aide du protocole Azure File Sync REST et du protocole FileREST, qui utilisent tous les deux HTTPS sur le port 443. SMB n'est jamais utilisé pour charger ou télécharger des données entre votre instance de Windows Server et le partage de fichiers Azure. Dans la mesure où la plupart des organisations autorisent le trafic HTTPS sur le port 443 pour visiter la plupart des sites web, aucune configuration réseau spéciale n'est généralement requise dans le cadre du déploiement d'Azure File Sync.

En fonction de la stratégie de votre organisation ou d'exigences réglementaires particulières, vous pouvez avoir besoin d'une communication plus restrictive avec Azure. C'est pourquoi Azure File Sync propose différents mécanismes pour vous permettre de configurer la mise en réseau. En fonction de vos besoins, vous pouvez :

- Canaliser la synchronisation et le trafic de chargement/téléchargement des fichiers sur votre VPN ExpressRoute ou Azure 
- Utiliser les fonctionnalités Azure Files et Azure Networking telles que les points de terminaison de service et les points de terminaison privés
- Configurer Azure File Sync de manière à prendre en charge votre proxy dans votre environnement
- Limiter l'activité du réseau à partir d'Azure File Sync

Pour en savoir plus sur Azure File Sync et la mise en réseau, consultez [Considérations relatives à la mise en réseau Azure File Sync](storage-sync-files-networking-overview.md).

## <a name="encryption"></a>Chiffrement
Lorsque vous utilisez Azure File Sync, vous devez prendre en compte trois couches de chiffrement différentes : le chiffrement sur le stockage au repos de Windows Server, le chiffrement en transit entre l'agent Azure File Sync et Azure, et le chiffrement au repos de vos données sur le partage de fichiers Azure. 

### <a name="windows-server-encryption-at-rest"></a>Chiffrement au repos de Windows Server 
Sur Windows Server, deux stratégies de chiffrement des données fonctionnent généralement avec Azure File Sync : le chiffrement sous le système de fichiers, de manière à chiffrer le système de fichiers et toutes les données qui y sont écrites, et le chiffrement au sein du format de fichier lui-même. Ces méthodes ne s'excluent pas mutuellement ; si besoin, elles peuvent être utilisées ensemble dans la mesure où l'objet du chiffrement est différent.

Pour assurer le chiffrement sous le système de fichiers, Windows Server fournit la boîte de réception BitLocker. BitLocker est entièrement transparent pour Azure File Sync. L'utilisation d'un mécanisme de chiffrement comme BitLocker permet d'empêcher l'exfiltration physique des données de votre centre de données local en cas de vol des disques et d'empêcher le chargement indépendant d'un système d'exploitation non autorisé pour effectuer des lectures/écritures non autorisées sur vos données. Pour en savoir plus sur BitLocker, consultez [Présentation de BitLocker](/windows/security/information-protection/bitlocker/bitlocker-overview).

Les produits tiers qui fonctionnent de la même façon que BitLocker, en ce sens qu’ils se trouvent sous le volume NTFS, doivent également fonctionner de manière totalement transparente avec Azure File Sync. 

L'autre méthode principale de chiffrement des données consiste à chiffrer le flux de données du fichier lorsque l'application enregistre ce dernier. Certaines applications peuvent effectuer cette opération en mode natif, mais ce n'est généralement pas le cas. Azure Information Protection (AIP)/Azure Rights Management Services (Azure RMS)/Active Directory RMS sont des exemples de méthodes de chiffrement du flux de données du fichier. L'utilisation d'un mécanisme de chiffrement comme AIP/RMS permet d'empêcher l'exfiltration des données de votre partage de fichiers si quelqu'un venait à les copier à un autre emplacement, comme une clé USB, ou à les envoyer par e-mail à une personne non autorisée. Lorsque le flux de données d'un fichier est chiffré au sein du format de fichier, ce fichier reste chiffré sur le partage de fichiers Azure. 

Azure File Sync n'interagit pas avec NTFS EFS (NTFS Encrypted File System) ou des solutions de chiffrement tierces situées au-dessus du système de fichiers mais en dessous du flux de données du fichier. 

### <a name="encryption-in-transit"></a>Chiffrement en transit

> [!NOTE]
> Le service Azure File Sync supprimera la prise en charge des protocoles TLS 1.0 et 1.1 le 1er août 2020. Toutes les versions prises en charge de l’agent Azure File Sync utilisent déjà le protocole TLS 1.2 par défaut. L’utilisation d’une version antérieure de TLS peut se produire si le protocole TLS 1.2 a été désactivé sur votre serveur ou si un proxy est utilisé. Si vous utilisez un proxy, nous vous recommandons de vérifier la configuration du proxy. Les régions de service Azure File Sync ajoutées après le 1/5/2020 prennent uniquement en charge le protocole TLS 1.2, et la prise en charge des protocoles TLS 1.0 et 1.1 sera supprimée des régions existantes le 1er août 2020.  Pour plus d’informations, consultez le [guide de résolution des problèmes](storage-sync-files-troubleshoot.md#tls-12-required-for-azure-file-sync).

L'agent Azure File Sync communique avec votre service de synchronisation de stockage et votre partage de fichiers Azure à l'aide du protocole Azure File Sync REST et du protocole FileREST, qui utilisent tous deux HTTPS sur le port 443. Azure File Sync n'envoie pas de requêtes non chiffrées sur HTTP. 

Les comptes de stockage Azure contiennent un commutateur permettant d'exiger le chiffrement en transit, qui est activé par défaut. Même si le commutateur est désactivé au niveau du compte de stockage, ce qui signifie que des connexions non chiffrées à vos partages de fichiers Azure sont possibles, Azure File Sync utilisera toujours des canaux chiffrés pour accéder à votre partage de fichiers.

La principale raison justifiant de désactiver le chiffrement en transit pour le compte de stockage est la nécessité de prendre en charge une application héritée qui doit être exécutée sur un système d'exploitation plus ancien, tel que Windows Server 2008 R2 ou une ancienne distribution Linux, communiquant directement avec un partage de fichiers Azure. Si l'application héritée communique avec le cache Windows Server du partage de fichiers, le basculement de ce paramètre n'aura aucun effet. 

Nous recommandons vivement de vérifier que le chiffrement des données en transit est activé.

Pour plus d’informations sur le chiffrement en transit, voir [ Exiger un transfert sécurisé dans Stockage Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="azure-file-share-encryption-at-rest"></a>Chiffrement au repos du partage de fichiers Azure
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Niveaux de stockage
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Activer les partages de fichiers Standard pour couvrir jusqu'à 100 Tio
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Disponibilité régionale
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Disponibilité régionale d'Azure File Sync

Pour connaître la disponibilité régionale, consultez [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

Les régions suivantes vous obligent à demander l’accès à Stockage Azure avant de pouvoir utiliser Azure File Sync avec elles :

- France Sud
- Afrique du Sud Ouest
- Émirats arabes unis Centre

Pour demander l’accès à ces régions, suivez le processus décrit dans [ce document](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="redundancy"></a>Redondance
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> Les options de stockage géoredondant et de stockage géoredondant interzone permettent de basculer manuellement le stockage vers la région secondaire. Cette opération est réservée aux situations d'urgence liées à l'utilisation d'Azure File Sync en raison de la probabilité accrue de perte de données. En cas de sinistre, si vous souhaitez procéder à un basculement manuel du stockage, vous devez déposer une demande de support auprès de Microsoft pour qu'Azure File Sync reprenne la synchronisation avec le point de terminaison secondaire.

## <a name="migration"></a>Migration
Si vous disposez déjà d’un serveur de fichiers Windows 2012 R2 (ou version ultérieure), Azure File Sync peut y être installé directement sans qu’il soit nécessaire de déplacer les données vers un nouveau serveur. Si vous envisagez de migrer vers un nouveau serveur de fichiers Windows dans le cadre de l’adoption d’Azure File Sync, ou si vos données sont actuellement situées sur un stockage NAS (Network-Attached Storage), il existe plusieurs approches possibles de migration pour utiliser Azure File Sync avec ces données. Tout dépend de l’emplacement de vos données. 

Pour des instructions détaillées relatives à votre scénario, consultez l’article [Vue d’ensemble d’Azure File Sync et de la migration de partages de fichiers Azure](storage-files-migration-overview.md).

## <a name="antivirus"></a>Antivirus
Du fait que les antivirus analysent les fichiers pour détecter la présence éventuelle de code malveillant connu, ils peuvent provoquer le rappel de fichiers hiérarchisés, occasionnant ainsi des frais de sortie conséquents. Dans les versions 4.0 et ultérieures de l'agent Azure File Sync, les fichiers hiérarchisés sont dotés de l'attribut Windows sécurisé FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS. Nous vous recommandons de contacter l'éditeur de votre logiciel pour savoir comment configurer la solution de manière à ignorer la lecture des fichiers dotés cet attribut (la plupart le font automatiquement). 

Les solutions antivirus internes de Microsoft, Windows Defender et System Center Endpoint Protection (SCEP), ignorent automatiquement la lecture des fichiers dotés de cet attribut. Nous les avons testés et y avons détecté un problème mineur : lorsque vous ajoutez un serveur à un groupe de synchronisation existant, les fichiers de moins de 800 octets sont rappelés (téléchargés) sur le nouveau serveur. Ces fichiers resteront sur le nouveau serveur et ne seront pas hiérarchisés, car ils ne respectent pas les exigences de taille de niveau (> à 64 Ko).

> [!Note]  
> Les fournisseurs de logiciels antivirus peuvent vérifier la compatibilité entre leur produit et Azure File Sync en utilisant la [suite de tests de compatibilité des antivirus avec Azure File Sync](https://www.microsoft.com/download/details.aspx?id=58322), qui est disponible en téléchargement dans le Centre de téléchargement Microsoft.

## <a name="backup"></a>Sauvegarde 
Si la hiérarchisation cloud est activée, vous ne devez pas utiliser de solutions qui sauvegardent directement le point de terminaison du serveur ou une machine virtuelle sur laquelle se trouve le point de terminaison de serveur. La hiérarchisation cloud entraîne le stockage d’un seul sous-ensemble de vos données sur le point de terminaison du serveur, avec le jeu de données complet résidant dans votre partage de fichiers Azure. Selon la solution de sauvegarde utilisée, les fichiers hiérarchisés soit sont ignorés et ne sont pas sauvegardés (parce qu’ils ont l’attribut FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS défini), soit sont rappelés sur le disque, ce qui entraîne des frais de sortie élevés. Nous vous recommandons d’utiliser une solution de sauvegarde cloud pour sauvegarder le partage de fichiers Azure directement. Pour plus d’informations, consultez [À propos de la sauvegarde des partages de fichiers Azure](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) ou contactez votre fournisseur de sauvegarde pour déterminer s’il prend en charge la sauvegarde des partages de fichiers Azure.

Si vous préférez utiliser une solution de sauvegarde locale, les sauvegardes doivent être effectuées sur un serveur dans le groupe de synchronisation pour lequel la hiérarchisation cloud est désactivée. Lorsque vous effectuez une restauration, utilisez les options de restauration au niveau du volume ou au niveau du fichier. Les fichiers restaurés en utilisant l’option de restauration au niveau du fichier seront synchronisés avec tous les points de terminaison dans le groupe de synchronisation et les fichiers existants seront remplacés par la version restaurée à partir de la sauvegarde.  Les restaurations au niveau du volume ne remplaceront pas les versions plus récentes des fichiers dans le partage de fichiers Azure ou d’autres points de terminaison serveur.

> [!WARNING]
> Si vous devez utiliser Robocopy /B avec un agent Azure File Sync s’exécutant sur le serveur source ou le serveur cible, effectuez une mise à niveau vers l’agent Azure File Sync version v12.0 ou ultérieure. L’utilisation de Robocopy /B avec des versions d’agent inférieures à v12.0 entraînera un endommagement des fichiers hiérarchisés durant la copie.

> [!Note]  
> La restauration complète peut engendrer des résultats inattendus et n’est pas prise en charge actuellement.

> [!Note]  
> Grâce à la version 9 de l’agent Azure File Sync, les captures instantanées VSS (notamment l’onglet Versions précédentes) sont à présent prises en charge sur les volumes avec hiérarchisation cloud activée. Toutefois, vous devez activer la compatibilité des versions précédentes via PowerShell. [Découvrez comment](storage-sync-files-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service).

## <a name="data-classification"></a>Classification des données
Si vous avez installé un logiciel de classification des données, l’activation de la hiérarchisation cloud peut entraîner un coût accru pour deux raisons :

1. Lorsque la hiérarchisation cloud est activée, vos fichiers les plus fréquemment utilisés sont mis en cache localement, et les moins fréquemment utilisés sont hiérarchisés dans le partage de fichiers Azure dans le cloud. Si votre classification des données analyse régulièrement tous les fichiers du partage de fichiers, les fichiers hiérarchisés dans le cloud doivent être rappelés chaque fois que l’analyse est effectuée. 

2. Si le logiciel de classification des données utilise les métadonnées du flux de données d’un fichier, celui-ci doit être entièrement rappelé afin que le logiciel puisse voir la classification. 

Ces augmentations du nombre de rappels et de la quantité de données rappelées peuvent augmenter les coûts.

## <a name="azure-file-sync-agent-update-policy"></a>Stratégie de mise à jour de l’agent Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Étapes suivantes
* [Prenez en compte les paramètres de pare-feu et de proxy](storage-sync-files-firewall-and-proxy.md)
* [Planification d’un déploiement Azure Files](storage-files-planning.md)
* [Déployer Azure Files](./storage-how-to-create-file-share.md)
* [Déployer Azure File Sync](storage-sync-files-deployment-guide.md)
* [Superviser Azure File Sync](storage-sync-files-monitoring.md)
