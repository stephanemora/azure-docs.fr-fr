---
title: Fonctionnement des instantanés Azure NetApp Files | Microsoft Docs
description: Explique le fonctionnement des instantanés Azure NetApp Files, y compris les méthodes de création d’instantanés, les méthodes de restauration des instantanés, l’utilisation des instantanés dans les paramètres de réplication entre régions.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: b-juche
ms.openlocfilehash: beadd250ec4472b894f0f474b1057ad44cf474ed
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133512"
---
# <a name="how-azure-netapp-files-snapshots-work"></a>Fonctionnement des instantanés Azure NetApp Files

Cet article explique le fonctionnement des instantanés Azure NetApp Files. La technologie des instantanés Azure NetApp Files offre une stabilité, une évolutivité et une récupération plus rapide, sans impact sur les performances. La technologie des instantanés Azure NetApp Files fournit une base pour les solutions de protection des données, notamment les restaurations de fichiers uniques, les restaurations de volumes et les clones, ainsi que la réplication entre régions. 

Pour obtenir des instructions sur l’utilisation des instantanés de volume, consultez [Gérer les instantanés avec Azure NetApp Files](azure-netapp-files-manage-snapshots.md). Pour plus d’informations sur la gestion des instantanés dans la réplication entre les régions, consultez [Configuration requise et considérations pour la réplication entre régions](cross-region-replication-requirements-considerations.md).

## <a name="what-volume-snapshots-are"></a>Que sont les instantanés de volume ?  

Un instantané Azure NetApp Files est une image de système de fichiers (volume) à une date et heure. Il peut servir de sauvegarde en ligne. Vous pouvez utiliser un instantané pour [créer un volume](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume), [restaurer un fichier](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client) ou [rétablir un volume](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert). Dans les données d’application spécifiques stockées sur des volumes Azure NetApp Files, des étapes supplémentaires peuvent être nécessaires pour garantir la cohérence des applications.  

Les instantanés à faible surcharge sont rendues possibles grâce aux fonctionnalités uniques de la technologie de virtualisation de volume qui fait partie d’Azure NetApp Files. À l’instar d’une base de données, cette couche utilise des pointeurs vers les blocs de données réels sur le disque. Toutefois, contrairement à une base de données, elle ne réécrit pas les blocs existants ; elle écrit les données mises à jour dans un nouveau bloc et modifie le pointeur. Un instantané Azure NetApp Files manipule simplement les pointeurs de bloc, en créant une vue « figée » en lecture seule d’un volume qui permet aux applications d’accéder à des versions antérieures de fichiers et de hiérarchies de répertoires sans programmation particulière. Les blocs de données réels ne sont pas copiés. Dès lors, les instantanés sont efficaces dans laps de temps nécessaire à leur création. Ils sont quasi instantanés, quelle que soit la taille du volume. Les instantanés sont également efficaces dans l’espace de stockage. Ils ne consomment pas d’espace et seuls les blocs delta entre les instantanés et le volume actif sont conservés. 

Les diagrammes suivants illustrent les concepts :  

![Diagrammes montrant les concepts clés des instantanés](../media/azure-netapp-files/snapshot-concepts.png)

Dans les diagrammes, un instantané est créé dans la figure 1a. Dans la figure 1b, les données modifiées sont écrites dans un *nouveau bloc* et le pointeur est mis à jour. Toutefois, le pointeur d’instantané pointe toujours vers le *bloc précédemment écrit*, ce qui vous donne une vue active et historique des données. Un autre instantané est créé dans la figure 1c. À présent, vous avez accès à trois générations de données (données actives, instantané 2 et instantané 1, par ordre d’antériorité), sans nécessiter l’espace de volume requis par trois copies complètes. 

Un instantané crée uniquement une copie des métadonnées de volume (*table inode*). La création ne prend que quelques secondes, quelle que soit la taille du volume, la capacité utilisée ou le niveau d’activité sur le volume. Ainsi, la création d’un instantané de volume de 100 Tio requiert le même laps de temps que la création d’un instantané de volume de 100 Gio. Une fois l’instantané créé, les modifications apportées aux fichiers de données sont reflétées dans la version active des fichiers, comme d’habitude.

Dans l’interlude, les blocs de données pointés à partir d’un instantané restent stables et immuables. En raison de la nature de la « redirection en écriture » des volumes Azure NetApp Files, un instantané n’entraîne aucune surcharge des performances et ne consomme pas d’espace. Vous pouvez stocker jusqu’à 255 instantanés par volume au fil du temps. Tous sont accessibles sous forme de versions en lecture seule et en ligne des données, et consomment ainsi moins de capacité que le nombre de blocs modifiés entre chaque instantané. Les blocs modifiés sont stockés dans le volume actif. Les blocs pointés dans des instantanés sont conservés (en lecture seule) dans le volume afin d’être uniquement réaffectés lorsque tous les pointeurs (dans le volume actif et les instantanés) ont été effacés. Dès lors, l’utilisation du volume augmente au fil du temps, moyennant de nouveaux blocs de données ou des blocs de données (modifiés) conservés dans des instantanés.

 Le diagramme suivant montre les instantanés d’un volume et l’espace utilisé au fil du temps : 

![Diagramme montrant les instantanés d’un volume et l’espace utilisé au fil du temps](../media/azure-netapp-files/snapshots-used-space-over-time.png)

Un instantané de volume enregistrant uniquement les modifications de bloc depuis le dernier instantané, il offre les avantages clés suivants :

* Les instantanés sont ***efficaces en termes de stockage** _.   
    Les instantanés consomment un espace de stockage minimal, car ils ne copient pas les blocs de données de l’ensemble du volume. Deux instantanés créés dans l’ordre diffèrent uniquement en fonction des blocs ajoutés ou modifiés dans l’intervalle de temps qui les sépare. Ce comportement de bloc incrémentiel limite la consommation de capacité de stockage associée. De nombreuses implémentations d’instantanés consomment des volumes de stockage correspondant au système de fichiers actif, ce qui augmente les besoins en capacité de stockage. En fonction des taux de modification quotidiens _block-level*, les instantanés Azure NetApp Files consomment plus ou moins de capacité, mais uniquement sur les données modifiées. La consommation quotidienne moyenne des instantanés est comprise entre 1 et 5 % de la capacité de volume utilisée pour de nombreux volumes d’application, ou jusqu’à 20 à 30 % pour les volumes tels que les volumes de base de données SAP HANA. Veillez à [surveiller l’utilisation des volumes et des instantanés](azure-netapp-files-metrics.md#volumes) en termes de consommation de la capacité d’instantané par rapport au nombre d’instantanés créés et gérés.   

* Les instantanés sont ***rapides à créer, répliquer, restaurer ou cloner** _.   
    La création, la réplication, la restauration ou le clonage d’un instantané ne prend que quelques secondes, quelle que soit la taille du volume et le niveau des activités. Vous pouvez créer un instantané de volume [à la demande](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume). Vous pouvez également utiliser [des stratégies d’instantanés](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) pour spécifier le moment où Azure NetApp Files doit créer automatiquement un instantané et le nombre d’instantanés à conserver pour un volume.  La cohérence des applications peut être obtenue en orchestrant les instantanés avec la couche d’application, par exemple, à l’aide de l’[outil AzAcSnap](azacsnap-introduction.md) pour SAP HANA.

_ Les instantanés n’ont aucun impact sur les ***performances du volume** _.   
    En raison de la nature de « redirection en écriture » de la technologie sous-jacente, le stockage ou la conservation des instantanés Azure NetApp Files n’a aucun impact sur les performances, même avec une activité intensive des données. La plupart du temps, la suppression d’un instantané n’a que peu, voire pas d’impact sur les performances. 

_ Les instantanés offrent une ***évolutivité** _ car ils peuvent être créés fréquemment et nombre d’entre eux peuvent être conservés.   
    Les volumes Azure NetApp Files prennent en charge jusqu’à 255 instantanés. La possibilité de stocker un grand nombre d’instantanés à faible impact, souvent créés, augmente la probabilité que la version de données souhaitée puisse être récupérée correctement.

_ Les instantanés fournissent une ***visibilité à l’utilisateur** _ et la _*_possibilité de récupérer des fichiers_*_.   
Les performances élevées, l’évolutivité et la stabilité de la technologie d’instantanés d’Azure NetApp Files offrent à l’utilisateur une solution de sauvegarde en ligne idéale à des fins de récupération. Les instantanés peuvent être rendus accessibles par l’utilisateur à des fins de restauration des fichiers, des répertoires ou des volumes. Des solutions supplémentaires vous permettent de copier des sauvegardes vers un stockage hors connexion ou de procéder à une [réplication entre régions](cross-region-replication-introduction.md) à des fins de conservation ou de récupération d’urgence.

## <a name="ways-to-create-snapshots"></a>Créer des instantanés   

Vous pouvez utiliser différentes méthodes pour créer et gérer des instantanés :

_ Manuellement (à la demande) à l’aide de ce qui suit :   
    * [Portail Azure](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume), [API REST](/rest/api/netapp/snapshots), [Azure CLI](/cli/azure/netappfiles/snapshot) ou outils [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshot)
    * Scripts (voir les [exemples](azure-netapp-files-solution-architectures.md#sap-tech-community-and-blog-posts))

* De manière automatisée à l’aide de ce qui suit :
    * Stratégies d’instantanés, via le [portail Azure](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies), l’[API REST](/rest/api/netapp/snapshotpolicies), [Azure CLI](/cli/azure/netappfiles/snapshot/policy)ou les outils [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshotpolicy)
    * Outil Application Consistent Snapshot comme [AzAcSnap](azacsnap-introduction.md)

## <a name="how-volumes-and-snapshots-are-replicated-cross-region-for-dr"></a>Réplication entre régions de volumes et d’instantanés à des fins de récupération d’urgence  

Azure NetApp Files prend en charge la [réplication entre régions](cross-region-replication-introduction.md) à des fins de récupération d’urgence (DR). La réplication entre régions Azure NetApp Files utilise la technologie SnapMirror. Seuls les blocs modifiés sont envoyés sur le réseau dans un format fiable et compressé. Une fois la réplication entre régions initiée entre les volumes, le contenu entier des volumes (à savoir, les blocs de données stockés réels) est transféré une seule fois. Cette opération est appelée *transfert de base*. Après le transfert initial, seuls les blocs modifiés (capturés dans les instantanés) sont transférés. Il en résulte un réplica 1:1 asynchrone du volume source, incluant tous les instantanés. Ce comportement suit un mécanisme de réplication complet et incrémentiel à l’infini. Cette technologie limite le volume de données nécessaire à la réplication dans les différentes régions, réduisant ainsi le coût de transfert des données. Elle réduit également le temps de réplication. Vous pouvez obtenir un objectif de point de récupération (RPO) plus petit, car davantage d’instantanés peuvent être créés et transférés plus fréquemment moyennant des transferts de données limités. En outre, il n’est plus nécessaire de disposer de mécanismes de réplication basés sur l’hôte, ce qui limite les coûts en termes de machines virtuelles et de licences logicielles.

Le diagramme suivant illustre le trafic d’instantanés dans les scénarios de réplication entre régions : 

![Diagramme illustrant le trafic d’instantanés dans les scénarios de réplication entre régions](../media/azure-netapp-files/snapshot-traffic-cross-region-replication.png)

## <a name="ways-to-restore-data-from-snapshots"></a>Restaurer des données à partir d’instantanés  

La technologie d’instantanés Azure NetApp Files améliore considérablement la fréquence et la fiabilité des sauvegardes. Elle n’affecte que très peu les performances et peut être mise en place en toute sécurité sur un volume actif. Les instantanés Azure NetApp Files permettent des restaurations quasi instantanées, sécurisées et pouvant être gérées par l’utilisateur. Cette section décrit les différentes méthodes permettant d’accéder aux données ou de les restaurer à partir d’instantanés Azure NetApp Files.

### <a name="restoring-files-or-directories-from-snapshots"></a>Restauration de fichiers ou de répertoires à partir d’instantanés 

Si la [visibilité du chemin d’instantané](azure-netapp-files-manage-snapshots.md#edit-the-hide-snapshot-path-option) n’est pas définie sur `hidden`, les utilisateurs peuvent directement accéder aux instantanés à des fins de récupération suite à une suppression accidentelle, une corruption ou une modification de leurs données. La sécurité des fichiers et des répertoires est conservée dans l’instantané, et les instantanés sont, par conception, en lecture seule. Dès lors, la restauration est aussi simple que sécurisée. 

Le diagramme suivant illustre l’accès d’un fichier ou d’un répertoire à un instantané : 

![Diagramme illustrant l’accès d’un fichier ou d’un répertoire à un instantané](../media/azure-netapp-files/snapshot-file-directory-access.png)

Dans le diagramme, l’instantané 1 consomme uniquement les blocs delta entre le volume actif et le moment où l’instantané est créé. Cela étant, lorsque vous accédez à l’instantané via le chemin d’accès d’instantané du volume, les données *s’affichent* comme s’il s’agissait de la capacité du volume complet au moment de la création de l’instantané. En accédant aux dossiers d’instantanés, les utilisateurs peuvent restaurer eux-mêmes les données en copiant les fichiers et les répertoires à partir d’un instantané de leur choix.

De même, les instantanés des volumes de réplication entre régions cibles sont accessibles en lecture seule à des fins de récupération des données dans la région de récupération d’urgence.  

Le diagramme suivant illustre l’accès aux instantanés dans les scénarios de réplication entre régions : 

![Diagramme montrant l’accès aux instantanés dans une réplication entre régions](../media/azure-netapp-files/snapshot-access-cross-region-replication.png)

Consultez [Restaurer un fichier à partir d’un instantané à l’aide d’un client](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client) pour en savoir plus sur la restauration de fichiers ou de répertoires individuels à partir d’instantanés.

### <a name="restoring-cloning-a-snapshot-to-a-new-volume"></a>Restauration (clonage) d’un instantané sur un nouveau volume

Vous pouvez restaurer des instantanés Azure NetApp Files sur un volume distinct indépendant. Cette opération est quasi instantanée, quelle que soit la taille du volume et la capacité consommée. Le volume nouvellement créé est presque immédiatement accessible, tandis que le volume réel et les blocs de données d’instantanés sont copiés. En fonction de la taille et de la capacité du volume, ce processus peut nécessiter un certain temps durant lequel le volume parent et l’instantané ne peuvent pas être supprimés. Toutefois, le volume est accessible au terme de création initiale, tandis que le processus de copie est en cours en arrière-plan. Cette fonctionnalité permet de créer rapidement des volumes à des fins de récupération de données ou de clonage de volumes pour le test et le développement. Compte tenu de la nature du processus de copie des données, la consommation du pool de capacité de stockage double au terme de la restauration, et le nouveau volume affiche la capacité active complète de l’instantané d’origine. Une fois ce processus terminé, le volume est indépendant et dissocié du volume d’origine, et les volumes sources et l’instantané peuvent être gérés ou supprimés indépendamment du nouveau volume.

Le diagramme suivant illustre un nouveau volume créé en restaurant (clonant) un instantané :   

![Diagramme montrant un nouveau volume créé en restaurant un instantané](../media/azure-netapp-files/snapshot-restore-clone-new-volume.png)

Les mêmes opérations peuvent être effectuées sur des instantanés répliqués vers un volume de récupération d’urgence (DR). Tout instantané peut être restauré sur un nouveau volume, même si la réplication entre régions est active ou en cours. Cette fonctionnalité permet de créer des environnements de test et de développement non disruptifs dans une région de récupération d’urgence, en plaçant les données à utiliser, plutôt que d’utiliser les volumes répliqués à des fins de récupération d’urgence uniquement. Ce cas d’usage permet d’isoler le test et le développement de la production et d’éliminer tout impact sur les environnements de production.  

Le diagramme suivant illustre la restauration d’un volume (clonage) à l’aide de l’instantané du volume cible de récupération d’urgence pendant la réplication entre régions :  

![Diagramme illustrant la restauration d’un volume à l’aide de l’instantané du volume cible de récupération d’urgence](../media/azure-netapp-files/snapshot-restore-clone-target-volume.png)

Consultez [Restaurer un instantané sur un nouveau volume](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume) pour en savoir plus sur les opérations de restauration de volume.

### <a name="restoring-reverting-a-snapshot-in-place"></a>Restauration (rétablissement) d’un instantané sur place

Dans certains cas, le nouveau volume consommant la capacité de stockage, la création d’un nouveau volume à partir d’un instantané peut ne pas être nécessaire ou appropriée. Pour récupérer rapidement après une altération des données (par exemple, les altérations de bases de données ou les attaques par ransomware), il peut s’avérer plus approprié de restaurer un instantané au sein du volume lui-même. Cette opération peut être effectuée à l’aide de la fonctionnalité de restauration d’instantané Azure NetApp Files. Cette fonctionnalité vous permet de restaurer rapidement un volume à l’état dans lequel il se trouvait lors de la prise d’un instantané particulier. Dans la plupart des cas, le rétablissement d’un volume est beaucoup plus rapide que la restauration de fichiers individuels, d’un instantané vers le système de fichiers actif, notamment pour les volumes particulièrement importants. 

Le rétablissement d’un instantané de volume est quasi instantané et ne prend que quelques secondes, même pour les volumes les plus importants. Les métadonnées du volume actif (*table inode*) sont remplacées par les métadonnées de l’instantané à partir de l’heure de création de l’instantané, ce qui permet de restaurer le volume à ce point spécifique dans le temps. Aucun bloc de données ne doit être copié pour que la restauration soit prise en compte. Dès lors, la gestion de l’espace est mieux optimisée par rapport à la restauration d’un instantané sur un nouveau volume. 

Le diagramme suivant montre une restauration de volume vers un instantané antérieur :  

![Diagramme montrant une restauration de volume vers un instantané antérieur](../media/azure-netapp-files/snapshot-volume-revert.png)

> [!IMPORTANT]
> Les données du système de fichiers écrites et instantanés actifs créés après la capture de l’instantané sélectionné seront perdus. L’opération de restauration de l’instantané remplacera toutes les données du volume ciblé par les données de l’instantané sélectionné. Vous devez prêter attention au contenu de l’instantané et à la date de création lorsque vous sélectionnez un instantané. Vous ne pouvez pas annuler l’opération de restauration de l’instantané.  

Consultez [Restaurer un volume à l’aide de la restauration d’instantané](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert) pour en savoir plus sur l’utilisation de cette fonctionnalité.

## <a name="how-snapshots-are-deleted"></a>Suppression des instantanés 

Les instantanés consomment de la capacité de stockage. Par conséquent, ils ne sont généralement pas conservés indéfiniment. Pour la protection, la conservation et la récupération des données, un certain nombre d’instantanés (créés à différents moments) sont généralement conservés en ligne pendant un certains laps de temps en fonction des exigences en matière de RPO, RTO et de contrat SLA. Toutefois, les instantanés plus anciens n’ont pas besoin d’être conservés sur le service de stockage et devront peut-être être supprimés pour libérer de l’espace. Les instantanés peuvent à tout moment être supprimés (pas nécessairement dans leur ordre de création) par un administrateur. 

> [!IMPORTANT]
> L’opération de suppression d’un instantané est irréversible. Vous devez conserver les copies du volume hors connexion à des fins de protection et de conservation des données. 

Lorsqu’un instantané est supprimé, tous les pointeurs de cet instantané vers des blocs de données existants sont supprimés. Lorsqu’un bloc de données n’a plus de pointeurs pointant vers lui (moyennant le volume actif ou d’autres instantanés de volume), le bloc de données est renvoyé à l’espace libre du volume pour une utilisation ultérieure. Par conséquent, la suppression des instantanés libère généralement plus de capacité dans un volume que la suppression des données du volume actif, car les blocs de données sont souvent capturés dans des instantanés créés précédemment. 

Le diagramme suivant illustre l’effet sur la consommation de stockage de la suppression de l’instantané 3 d’un volume :  

![Diagramme illustrant l’effet sur la consommation de stockage de la suppression de l’instantané](../media/azure-netapp-files/snapshot-delete-storage-consumption.png)

Veillez à [surveiller la consommation des volumes et des instantanés](azure-netapp-files-metrics.md#volumes) et à comprendre comment l’application, le volume actif et la consommation des instantanés interagissent. 

Consultez [Supprimer des instantanés](azure-netapp-files-manage-snapshots.md#delete-snapshots) pour en savoir plus sur la manière de gérer la suppression des instantanés. Consultez [Gérer les stratégies d’instantanés](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) pour savoir comment automatiser ce processus.

## <a name="next-steps"></a>Étapes suivantes

* [Gérer les instantanés avec Azure NetApp Files](azure-netapp-files-manage-snapshots.md)
* [Surveiller les métriques de volume et d’instantané](azure-netapp-files-metrics.md#volumes)
* [Résoudre les problèmes de stratégies d’instantanés](troubleshoot-snapshot-policies.md)
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Vidéo sur les instantanés Azure NetApp Files](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Instantané NetApp - Bibliothèque vidéo NetApp](https://tv.netapp.com/detail/video/2579133646001/snapshot)



