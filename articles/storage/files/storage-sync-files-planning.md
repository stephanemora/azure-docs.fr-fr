---
title: Planification d’un déploiement Azure File Sync | Microsoft Docs
description: Découvrez les éléments à prendre en compte lors de la planification d’un déploiement Azure Files.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d2dbe29c5a348363172f57da86483ccf3fd787f0
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76046085"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planification d’un déploiement de synchronisation de fichiers Azure
Utilisez Azure File Sync pour centraliser les partages de fichiers de votre organisation dans Azure Files tout en conservant la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Azure File Sync transforme Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible dans Windows Server pour accéder à vos données localement, notamment SMB, NFS et FTPS. Vous pouvez avoir autant de caches que nécessaire dans le monde entier.

Cet article décrit les points importants à prendre en compte pour le déploiement d’Azure File Sync. Nous vous recommandons de lire aussi l’article [Planification d’un déploiement Azure Files](storage-files-planning.md). 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-file-sync-terminology"></a>Terminologie Azure File Sync
Avant de voir en détail la planification d’un déploiement Azure File Sync, il est important de bien comprendre la terminologie employée.

### <a name="storage-sync-service"></a>Service de synchronisation de stockage
Le service de synchronisation de stockage est la ressource Azure de niveau supérieur pour Azure File Sync. La ressource de service de synchronisation de stockage est un pair de la ressource de compte de stockage. Elle peut être déployée de la même façon dans des groupes de ressources Azure. Une ressource de niveau supérieur distincte de la ressource de compte de stockage est nécessaire, car le service de synchronisation de stockage peut créer des relations de synchronisation avec plusieurs comptes de stockage dans plusieurs groupes de synchronisation. Un abonnement peut avoir plusieurs ressources de service de synchronisation de stockage déployées.

### <a name="sync-group"></a>Groupe de synchronisation
Un groupe de synchronisation définit la topologie de synchronisation d’un ensemble de fichiers. Les points de terminaison dans un groupe de synchronisation sont synchronisés entre eux. Par exemple, si vous voulez gérer deux ensembles distincts de fichiers avec Azure File Sync, vous créez deux groupes de synchronisation et ajoutez des points de terminaison différents dans chacun de ces groupes. Un service de synchronisation de stockage peut héberger autant de groupes de synchronisation que nécessaire.  

### <a name="registered-server"></a>Serveur inscrit
L’objet serveur inscrit représente une relation d’approbation entre votre serveur (ou cluster) et le service de synchronisation de stockage. Vous pouvez inscrire autant de serveurs que vous souhaitez auprès d’une instance du service de synchronisation de stockage. Toutefois, un serveur (ou cluster) peut être inscrit uniquement auprès d’un seul service de synchronisation de stockage à la fois.

### <a name="azure-file-sync-agent"></a>Agent Azure File Sync
L’agent Azure File Sync est un package téléchargeable qui permet à Windows Server de rester synchronisé avec un partage de fichiers Azure. L’agent Azure File Sync a trois composants principaux : 
- **FileSyncSvc.exe** : Service en arrière-plan qui gère la supervision des changements sur les points de terminaison de serveur, ainsi que le démarrage des sessions de synchronisation dans Azure.
- **StorageSync.sys** : Filtre du système de fichiers Azure File Sync qui gère la hiérarchisation des fichiers dans Azure Files (quand la hiérarchisation cloud est activée).
- **Applets de commande de gestion PowerShell** : Applets de commande PowerShell qui vous permettent d’interagir avec le fournisseur de ressources Azure Microsoft.StorageSync. Ces composants se trouvent aux emplacements (par défaut) suivants :
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Point de terminaison de serveur
Un point de terminaison de serveur représente un emplacement spécifique sur un serveur inscrit, comme un dossier sur un volume de serveur. Plusieurs points de terminaison de serveur peuvent se trouver sur le même volume si leurs espaces de noms ne se chevauchent pas (par exemple, `F:\sync1` et `F:\sync2`). Vous pouvez configurer des stratégies de hiérarchisation cloud individuellement pour chaque point de terminaison de serveur. 

Vous pouvez créer un point de terminaison de serveur via un point de montage. Notez que les points de montage dans le point de terminaison sont ignorés.  

Vous pouvez créer un point de terminaison de serveur sur le volume système, mais il existe deux limites si vous procédez ainsi :
* La hiérarchisation cloud ne peut pas être activée.
* La restauration d’espace de noms rapide (où le système désactive rapidement l’espace de noms complet, puis commence à rappeler le contenu) n’est pas effectuée.


> [!Note]  
> Seuls les volumes non amovibles sont pris en charge.  Les lecteurs mappés à partir d’un partage distant ne sont pas pris en charge pour un chemin d’accès au point de terminaison du serveur.  En outre, un point de terminaison de serveur peut se trouver sur le volume système Windows, bien que la hiérarchisation cloud ne soit pas prise en charge sur le volume système.

Si vous ajoutez un emplacement de serveur contenant un ensemble de fichiers comme point de terminaison de serveur à un groupe de synchronisation, ces fichiers sont fusionnés avec les autres fichiers déjà présents sur les autres points de terminaison dans le groupe de synchronisation.

### <a name="cloud-endpoint"></a>Point de terminaison cloud
Un point de terminaison cloud est un partage de fichiers Azure qui fait partie d’un groupe de synchronisation. L’intégralité du partage de fichiers Azure se synchronise. Un partage de fichiers Azure peut être membre d’un seul point de terminaison cloud. Un partage de fichiers Azure peut donc être membre d’un seul groupe de synchronisation. Si vous ajoutez un partage de fichiers Azure contenant un ensemble de fichiers existants comme point de terminaison cloud à un groupe de synchronisation, ces fichiers sont fusionnés avec les autres fichiers déjà présents sur les autres points de terminaison dans le groupe de synchronisation.

> [!Important]  
> Azure File Sync prend en charge les modifications directes dans le partage de fichiers Azure. Toutefois, les modifications apportées au partage de fichiers Azure doivent d’abord être détectées par un travail de détection des modifications Azure File Sync. Un travail de détection des modifications est lancé pour un point de terminaison cloud toutes les 24 heures uniquement. Par ailleurs, les modifications apportées à un partage de fichiers Azure via le protocole REST ne mettent pas à jour l’heure de dernière modification de SMB et ne sont pas visibles comme des modifications par la synchronisation. Pour plus d’informations, consultez [Questions fréquentes (FAQ) sur Azure Files](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Hiérarchisation cloud 
La hiérarchisation cloud est une fonctionnalité facultative d’Azure File Sync, qui met en cache sur le serveur local les fichiers faisant l’objet d’accès fréquents, tous les autres fichiers étant hiérarchisés sur Azure Files en fonction de paramètres de stratégie. Pour plus d’informations, voir [Compréhension de la hiérarchisation Cloud](storage-sync-cloud-tiering.md).

## <a name="azure-file-sync-system-requirements-and-interoperability"></a>Configuration requise et interopérabilité d’Azure File Sync 
Cette section traite de la configuration requise et de l’interopérabilité de l’agent Azure File Sync avec les fonctionnalités et rôles Windows Server, ainsi qu’avec des solutions tierces.

### <a name="evaluation-cmdlet"></a>Applet de commande d’évaluation
Avant de déployer Azure File Sync, vous devez évaluer s’il est compatible avec votre système à l’aide de l’applet de commande d’évaluation d’Azure File Sync. Cette cmdlet recherche les problèmes potentiels liés au système de fichiers et au jeu de données, comme des caractères ou une version de système d’exploitation non pris en charge. Ses vérifications couvrent la plupart des fonctionnalités mentionnées ci-dessous, mais pas toutes. Nous vous conseillons de lire attentivement le reste de cette section pour garantir le bon déroulement de votre déploiement. 

Vous pouvez installer l’applet de commande d’évaluation en installant le module Az PowerShell en suivant ces instructions : [Installez et configurez Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

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
    Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
Pour afficher les résultats au format CSV :
```powershell
    $errors = Invoke-AzStorageSyncCompatibilityCheck […]
    $errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="system-requirements"></a>Configuration requise
- Un serveur fonctionnant avec l’une des versions de système d’exploitation suivantes :

    | Version | Références prises en charge | Options de déploiement prises en charge |
    |---------|----------------|------------------------------|
    | Windows Server 2019 | Datacenter et Standard | Complète et Minimale |
    | Windows Server 2016 | Datacenter et Standard | Complète et Minimale |
    | Windows Server 2012 R2 | Datacenter et Standard | Complète et Minimale |
    | Windows Server IoT 2019 pour le stockage| Datacenter et Standard | Complète et Minimale |
    | Windows Storage Server 2016| Datacenter et Standard | Complète et Minimale |
    | Windows Storage Server 2012 R2| Datacenter et Standard | Complète et Minimale |

    Nous prévoyons d’ajouter la prise en charge de versions ultérieures de Windows Server quand elles seront disponibles.

    > [!Important]  
    > Nous vous recommandons de mettre régulièrement à jour tous les serveurs que vous utilisez avec Azure File Sync en installant les dernières mises à jour disponibles sur Windows Update. 

- Un serveur avec un minimum de 2 Gio de mémoire.

    > [!Important]  
    > Si le serveur s’exécute sur une machine virtuelle où la mémoire dynamique est activée, la machine virtuelle doit être configurée avec un minimum de 2 048 Mio de mémoire.
    
- Un volume attaché localement formaté avec le système de fichiers NTFS.

### <a name="file-system-features"></a>Fonctionnalités du système de fichiers

| Fonctionnalité | État de la prise en charge | Notes |
|---------|----------------|-------|
| Listes ACL | entièrement prise en charge | Les listes ACL Windows sont conservées par Azure File Sync et appliquées par Windows Server sur les points de terminaison de serveur. Les listes ACL Windows ne sont pas (encore) prises en charge par Azure Files si les fichiers sont accessibles directement dans le cloud. |
| Liens physiques | Ignoré | |
| Liens symboliques | Ignoré | |
| Points de montage | Partiellement pris en charge | Les points de montage peuvent être la racine d’un point de terminaison de serveur, mais ils sont ignorés s’ils se trouvent dans un espace de noms du point de terminaison de serveur. |
| Jonctions | Ignoré | Par exemple, les dossiers DfrsrPrivate de système de fichiers DFS et DFSRoots. |
| Points d’analyse | Ignoré | |
| Compression NTFS | entièrement prise en charge | |
| Fichiers partiellement alloués | entièrement prise en charge | Les fichiers partiellement alloués sont synchronisés (ils ne sont pas bloqués), mais ils sont synchronisés dans le cloud sous forme de fichiers complets. Si le contenu d’un fichier change dans le cloud (ou sur un autre serveur), le fichier n’est plus partiellement alloué quand le changement est téléchargé. |
| Autres flux de données | Conservés, mais pas synchronisés | Par exemple, les balises de classification créées par l’infrastructure de classification des fichiers ne sont pas synchronisées. Les balises de classification qui existent sur des fichiers à chacun des points de terminaison du serveur ne sont pas affectées. |

> [!Note]  
> Seuls les volumes NTFS sont pris en charge. ReFS, FAT, FAT32 et les autres systèmes de fichiers ne sont pas pris en charge.

### <a name="files-skipped"></a>Fichiers ignorés

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
La déduplication des données est prise en charge sur les volumes avec hiérarchisation cloud sur Windows Server 2016 et Windows Server 2019. Le fait d’activer la déduplication des données sur un volume pour lequel la hiérarchisation cloud est activée vous permet de mettre en cache plus de fichiers en local sans avoir à provisionner plus de stockage. 

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
- En ce qui concerne les tâches d’optimisation de la déduplication en cours, la hiérarchisation cloud avec la stratégie de date sera retardée par le paramètre [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) de déduplication des données, si le fichier n’est pas déjà hiérarchisé. 
    - Exemple : Si la valeur du paramètre MinimumFileAgeDays est de sept jours et la valeur de la stratégie de date de la hiérarchisation cloud est de 30 jours, la stratégie de date hiérarchisera les fichiers après 37 jours.
    - Remarque : Une fois un fichier hiérarchisé par Azure File Sync, la tâche d’optimisation de la déduplication ignorera le fichier.
- Si un serveur exécutant Windows Server 2012 R2 avec l’agent Azure File Sync installé est mis à niveau vers Windows Server 2016 ou Windows Server 2019, les étapes suivantes doivent être effectuées pour prendre en charge la déduplication des données et la hiérarchisation cloud sur le même volume :  
    - Désinstallez l’agent Azure File Sync pour Windows Server 2012 R2 et redémarrez le serveur.
    - Téléchargez l’agent Azure File Sync pour la nouvelle version du système d’exploitation serveur (Windows Server 2016 ou Windows Server 2019).
    - Installez l’agent Azure File Sync et redémarrez le serveur.  
    
    Remarque : Les paramètres de configuration Azure File Sync sur le serveur sont conservés lorsque l’agent est désinstallé et réinstallé.

### <a name="distributed-file-system-dfs"></a>Système de fichiers DFS
Azure File Sync prend en charge l’interopérabilité avec les espaces de noms DFS (DFS-N) et la réplication DFS (DFS-R).

**Espaces de noms DFS (DFS-N)**  : Azure File Sync est entièrement pris en charge sur les serveurs DFS-N. Vous pouvez installer l’agent Azure File Sync sur un ou plusieurs membres DFS-N pour synchroniser des données entre les points de terminaison de serveur et le point de terminaison cloud. Pour plus d’informations, consultez [Vue d’ensemble des espaces de noms DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Réplication DFS (DFS-R)**  : Comme DFS-R et Azure File Sync sont deux solutions de réplication, dans la plupart des cas, nous recommandons de remplacer DFS-R par Azure File Sync. Il existe cependant plusieurs scénarios où vous souhaiterez utiliser DFS-R et Azure File Sync :

- Vous migrez d’un déploiement de DFS-R vers un déploiement d’Azure File Sync. Pour plus d’informations, consultez [Migrer un déploiement de la réplication DFS (DFS-R) vers Azure File Sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Tous les serveurs locaux ayant besoin d’une copie de vos données de fichiers ne peuvent pas être connectés directement à Internet.
- Les serveurs de succursale consolident les données sur un serveur hub pour lequel vous souhaitez utiliser Azure File Sync.

Pour qu’Azure File Sync et DFS-R fonctionnent côte à côte :

1. La hiérarchisation cloud d’Azure File Sync doit être désactivée sur les volumes avec des dossiers répliqué DFS-R.
2. Les points de terminaison de serveur ne doivent pas être configurés sur des dossiers de réplication DFS-R en lecture seule.

Pour plus d’informations, consultez [Vue d’ensemble de la réplication DFS](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Sysprep
L’utilisation de sysprep sur un serveur sur lequel l’agent Azure File Sync est installé n’est pas prise en charge et peut produire des résultats inattendus. L’installation de l’agent et l’inscription du serveur doivent être effectués après avoir déployé l’image du serveur et terminé la mini-configuration de sysprep.

### <a name="windows-search"></a>Recherche Windows
Si la hiérarchisation cloud est activée sur un point de terminaison de serveur, les fichiers qui sont hiérarchisés sont ignorés et ne sont pas indexés par Windows Search. Les fichiers non hiérarchisés sont indexés correctement.

### <a name="antivirus-solutions"></a>Solutions antivirus
Du fait que les antivirus analysent les fichiers pour détecter la présence éventuelle de code malveillant connu, ils peuvent provoquer le rappel de fichiers hiérarchisés. Dans les versions 4.0 et ultérieures de l'agent Azure File Sync, les fichiers hiérarchisés sont dotés de l'attribut Windows sécurisé FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS. Nous vous recommandons de contacter l'éditeur de votre logiciel pour savoir comment configurer la solution de manière à ignorer la lecture des fichiers dotés cet attribut (la plupart le font automatiquement). 

Les solutions antivirus internes de Microsoft, Windows Defender et System Center Endpoint Protection (SCEP), ignorent automatiquement la lecture des fichiers dotés de cet attribut. Nous les avons testés et y avons détecté un problème mineur : lorsque vous ajoutez un serveur à un groupe de synchronisation existant, les fichiers de moins de 800 octets sont rappelés (téléchargés) sur le nouveau serveur. Ces fichiers resteront sur le nouveau serveur et ne seront pas hiérarchisés, car ils ne respectent pas les exigences de taille de niveau (> à 64 Ko).

> [!Note]  
> Les fournisseurs de logiciels antivirus peuvent vérifier la compatibilité entre leur produit et Azure File Sync en utilisant la [suite de tests de compatibilité des antivirus avec Azure File Sync](https://www.microsoft.com/download/details.aspx?id=58322), qui est disponible en téléchargement dans le Centre de téléchargement Microsoft.

### <a name="backup-solutions"></a>Solutions de sauvegarde
Tout comme les solutions antivirus, les solutions de sauvegarde peuvent provoquer le rappel de fichiers hiérarchisés. Pour sauvegarder le partage de fichiers Azure, nous vous recommandons d’utiliser une solution de sauvegarde cloud au lieu d’un produit de sauvegarde locale.

Si vous utilisez une solution de sauvegarde sur site, les sauvegardes doivent être effectuées sur un serveur dans le groupe de synchronisation qui a la hiérarchisation cloud désactivée. Lorsque vous effectuez une restauration, utilisez les options de restauration au niveau du volume ou au niveau du fichier. Les fichiers restaurés en utilisant l’option de restauration au niveau du fichier seront synchronisés avec tous les points de terminaison dans le groupe de synchronisation et les fichiers existants seront remplacés par la version restaurée à partir de la sauvegarde.  Les restaurations au niveau du volume ne remplaceront pas les versions plus récentes des fichiers dans le partage de fichiers Azure ou d’autres points de terminaison serveur.

> [!Note]  
> La restauration complète peut engendrer des résultats inattendus et n’est pas prise en charge actuellement.

> [!Note]  
> Grâce à la version 9 de l’agent Azure File Sync, les captures instantanées VSS (notamment l’onglet Versions précédentes) sont à présent prises en charge sur les volumes avec hiérarchisation cloud activée. Toutefois, vous devez activer la compatibilité des versions précédentes via PowerShell. [Découvrez comment](storage-files-deployment-guide.md).

### <a name="encryption-solutions"></a>Solutions de chiffrement
La prise en charge des solutions de chiffrement dépend de la façon dont elles sont implémentées. Azure File Sync est connu pour fonctionner avec les solutions suivantes :

- Chiffrement BitLocker
- Azure Information Protection, Azure Rights Management Services (Azure RMS) et Active Directory RMS

Azure File Sync est connu pour ne pas fonctionner avec les solutions suivantes :

- Système de fichiers chiffrés NTFS (EFS)

En règle générale, Azure File Sync prend en charge l’interopérabilité avec les solutions de chiffrement qui se trouvent sous le système de fichiers, comme BitLocker, et avec les solutions qui sont implémentées dans le format de fichier, comme Azure Information Protection. Aucune interopérabilité spécifique n’a été prévue pour les solutions qui se trouvent au-dessus du système de fichiers (par exemple, le système de fichiers NTFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Autres solutions de gestion hiérarchique du stockage (HSM)
Aucune autre solution HSM ne doit être utilisée avec Azure File Sync.

## <a name="region-availability"></a>Disponibilité des régions
Azure File Sync est disponible uniquement dans les régions suivantes :

| Région | Emplacement du centre de données |
|--------|---------------------|
| Australie Est | Nouvelle-Galles du Sud |
| Sud-Australie Est | Victoria |
| Brésil Sud | État de Sao Paulo |
| Centre du Canada | Toronto |
| Est du Canada | Québec |
| Inde centrale | Pune |
| USA Centre | Iowa |
| Asie Est | Hong Kong (R.A.S.) |
| USA Est | Virginie |
| USA Est 2 | Virginie |
| France Centre | Paris |
| France Sud* | Marseille |
| Centre de la Corée | Séoul |
| Corée du Sud | Busan |
| Japon Est | Tokyo, Saitama |
| OuJapon Est | Osaka |
| Centre-Nord des États-Unis | Illinois |
| Europe Nord | Irlande |
| Afrique du Sud Nord | Johannesbourg |
| Afrique du Sud Ouest* | Le Cap |
| États-Unis - partie centrale méridionale | Texas |
| Inde Sud | Chennai |
| Asie Sud-Est | Singapour |
| Sud du Royaume-Uni | London |
| Ouest du Royaume-Uni | Cardiff |
| Gouvernement des États-Unis – Arizona | Arizona |
| Gouvernement des États-Unis – Texas | Texas |
| Gouvernement américain - Virginie | Virginie |
| Émirats arabes unis Nord | Dubaï |
| Émirats arabes unis Centre* | Abu Dhabi |
| Europe Ouest | Pays-bas |
| Centre-USA Ouest | Wyoming |
| USA Ouest | Californie |
| USA Ouest 2 | Washington |

Azure File Sync prend en charge la synchronisation uniquement avec un partage de fichiers Azure qui est situé dans la même région que le service de synchronisation de stockage.

Pour les régions marquées d’un astérisque, vous devez contacter le support Azure pour demander l’accès au stockage Azure dans ces régions. Le processus est décrit dans [ce document](https://azure.microsoft.com/global-infrastructure/geographies/).

### <a name="azure-disaster-recovery"></a>Reprise d’activité après sinistre Azure
Pour une protection contre la perte d’une région Azure, Azure File Sync s’intègre à l’option de [redondance du stockage géoredondant](../common/storage-redundancy-grs.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (GRS). Le stockage GRS fonctionne en utilisant la réplication de blocs asynchrone entre le stockage dans la région primaire, avec laquelle vous interagissez normalement, et le stockage dans la région secondaire associée. En cas de sinistre entraînant la mise hors connexion temporaire ou définitive d'une région Azure, Microsoft bascule le stockage vers la région associée. 

> [!Warning]  
> Si vous utilisez votre partage de fichiers Azure comme point de terminaison cloud sur un compte de stockage GRS, vous ne devez pas initier de basculement de compte de stockage. Cela provoquera en effet un arrêt de la synchronisation et pourra entraîner une perte inattendue de données dans le cas de fichiers nouvellement hiérarchisés. En cas de perte d’une région Azure, Microsoft déclenchera le basculement du compte de stockage en employant une méthode compatible avec Azure File Sync.

Pour prendre en charge l’intégration du basculement entre le stockage géoredondant et Azure File Sync, toutes les régions Azure File Sync sont associées à une région secondaire qui correspond à la région secondaire utilisée par le stockage. Ces associations sont les suivantes :

| Région primaire      | Région jumelée      |
|---------------------|--------------------|
| Australie Est      | Sud-Australie Est|
| Sud-Australie Est | Australie Est     |
| Brésil Sud        | États-Unis - partie centrale méridionale   |
| Centre du Canada      | Est du Canada        |
| Est du Canada         | Centre du Canada     |
| Inde centrale       | Inde Sud        |
| USA Centre          | USA Est 2          |
| Asie Est           | Asie Sud-Est     |
| USA Est             | USA Ouest            |
| USA Est 2           | USA Centre         |
| France Centre      | France Sud       |
| France Sud        | France Centre     |
| Japon Est          | OuJapon Est         |
| OuJapon Est          | Japon Est         |
| Centre de la Corée       | Corée du Sud        |
| Corée du Sud         | Centre de la Corée      |
| Europe Nord        | Europe Ouest        |
| Centre-Nord des États-Unis    | États-Unis - partie centrale méridionale   |
| Afrique du Sud Nord  | Afrique du Sud Ouest  |
| Afrique du Sud Ouest   | Afrique du Sud Nord |
| États-Unis - partie centrale méridionale    | Centre-Nord des États-Unis   |
| Inde Sud         | Inde centrale      |
| Asie Sud-Est      | Asie Est          |
| Sud du Royaume-Uni            | Ouest du Royaume-Uni            |
| Ouest du Royaume-Uni             | Sud du Royaume-Uni           |
| Gouvernement des États-Unis – Arizona      | Gouvernement des États-Unis – Texas       |
| US Gov Iowa         | Gouvernement américain - Virginie    |
| Gouvernement américain - Virginie      | Gouvernement des États-Unis – Texas       |
| Europe Ouest         | Europe Nord       |
| Centre-USA Ouest     | USA Ouest 2          |
| USA Ouest             | USA Est            |
| USA Ouest 2           | Centre-USA Ouest    |

## <a name="azure-file-sync-agent-update-policy"></a>Stratégie de mise à jour de l’agent Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="recommended-azure-file-sync-machine-configuration"></a>Configuration d’ordinateur Azure File Sync recommandée

La configuration d’ordinateur Azure File Sync requise est déterminée par le nombre d’objets dans l’espace de noms et l’évolution du jeu de données. Un serveur unique peut être attaché à plusieurs groupes de synchronisation et le nombre d’objets figurant dans le tableau suivant constitue l’espace de noms complet auquel un serveur est attaché. Par exemple, le point de terminaison de serveur A avec 10 millions d’objets + le point de terminaison de serveur B avec 10 millions d’objets = 20 millions d’objets. Pour cet exemple de déploiement, nous recommandons 8 UC, 16 Gio de mémoire pour l’état stable et (si possible) 48 Gio de mémoire pour la migration initiale.
 
Les données d’espace de noms sont stockées en mémoire pour des raisons de performance. Pour cette raison, les espaces de noms plus grands nécessitent davantage de mémoire pour maintenir de bonnes performances, et une évolution plus importante requiert davantage d’UC pour le traitement. 
 
Dans le tableau suivant, nous avons fourni la taille de l’espace de noms, ainsi qu’une conversion en capacité pour les partages de fichiers à usage général standard, où la taille de fichier moyenne est 512 Kio. Si les tailles de vos fichiers sont plus petites, envisagez d’ajouter de la mémoire supplémentaire pour obtenir la même capacité. Basez la configuration de votre mémoire sur la taille de l’espace de noms.

| Taille de l’espace de noms - fichiers et répertoires (millions)  | Capacité type (Tio)  | Cœurs de processeur  | Mémoire recommandée (Gio) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (synchronisation initiale)/2 (évolution classique)      |
| 5        | 2.4     | 2        | 16 (synchronisation initiale)/4 (évolution classique)    |
| 10       | 4.8     | 4        | 32 (synchronisation initiale)/8 (évolution classique)   |
| 30       | 14.3    | 8        | 48 (synchronisation initiale)/16 (évolution classique)   |
| 50       | 23.8    | 16       | 64 (synchronisation initiale)/32 (évolution classique)  |
| 100*     | 47.7   | 32       | 128 (synchronisation initiale)/32 (évolution classique)  |

\*Plus de 100 millions de fichiers et de répertoires n’ont pas été testés. Il s’agit d’une limite non stricte.

> [!TIP]
> La synchronisation initiale d’un espace de noms est une opération intensive et nous vous recommandons d’allouer davantage de mémoire jusqu’à ce que la synchronisation initiale soit terminée. Cela n’est pas obligatoire, mais peut accélérer la synchronisation initiale. 
> 
> L’évolution standard est que 0,5 % de l’espace de noms change chaque jour. Pour des niveaux d’évolution plus élevés, envisagez d’ajouter davantage d’UC. 

## <a name="next-steps"></a>Étapes suivantes
* [Prenez en compte les paramètres de pare-feu et de proxy](storage-sync-files-firewall-and-proxy.md)
* [Planification d’un déploiement Azure Files](storage-files-planning.md)
* [Déployer Azure Files](storage-files-deployment-guide.md)
* [Déployer Azure File Sync](storage-sync-files-deployment-guide.md)
* [Superviser Azure File Sync](storage-sync-files-monitoring.md)
