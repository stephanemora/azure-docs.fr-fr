---
title: Planification de votre infrastructure de sauvegarde de machines virtuelles dans Azure
description: Considérations importantes avant la sauvegarde de machines virtuelles dans Azure
services: backup
author: rayne-wiselman
manager: carmonm
keywords: sauvegarde de machines virtuelles
ms.service: backup
ms.topic: conceptual
ms.date: 8/29/2018
ms.author: raynew
ms.openlocfilehash: e38f245197f2b1bdb22a2866028ad10f4ec39ec1
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53343494"
---
# <a name="plan-your-vm-backup-infrastructure-in-azure"></a>Planification de votre infrastructure de sauvegarde de machines virtuelles dans Azure
Cet article formule des suggestions de performances et de ressources pour vous aider à planifier votre infrastructure de sauvegarde de machines virtuelles. Il définit également les principaux aspects du service Backup, qui peuvent être essentiels afin de vous aider à déterminer votre architecture, à planifier sa capacité et à définir des planifications. Si vous avez [préparé votre environnement](backup-azure-arm-vms-prepare.md), la planification est l’étape à suivre avant de passer à la [sauvegarde des machines virtuelles](backup-azure-arm-vms.md). Si vous avez besoin d’informations sur les machines virtuelles Azure, consultez la [Documentation sur les machines virtuelles](https://azure.microsoft.com/documentation/services/virtual-machines/).

> [!NOTE]
> Cet article peut être utilisé pour des disques managés et des disques non managés. Si vous utilisez des disques non managés, il existe certaines recommandations relatives aux comptes de stockage. Si vous utilisez [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md), vous n’avez pas à vous soucier des problèmes de performances ou d’utilisation des ressources. Azure optimise l’utilisation du stockage pour vous.
>

## <a name="how-does-azure-back-up-virtual-machines"></a>Comment Azure sauvegarde-t-il des machines virtuelles Azure ?
Lorsque le service Sauvegarde Azure lance un travail de sauvegarde à l’heure planifiée, il déclenche l’extension de sauvegarde pour prendre une capture instantanée jusqu`à une date et heure. Le service Azure Backup utilise l’extension _VMSnapshot_ dans Windows et l’extension _VMSnapshotLinux_ dans Linux. L’extension est installée lors de la première sauvegarde de machine virtuelle. Pour que l’extension soit installée, la machine virtuelle doit être en cours d’exécution. Si la machine virtuelle n’est pas en cours d’exécution, le service Sauvegarde prend un instantané du stockage sous-jacent (car aucune écriture de l’application n’a lieu pendant l’arrêt de la machine virtuelle).

Lorsque de la prise d’un instantané de machines virtuelles Windows, le service Sauvegarde se coordonne avec le service VSS pour obtenir un instantané cohérent des disques de la machine virtuelle. Si vous effectuez des sauvegardes de machines virtuelles Linux, vous pouvez écrire vos propres scripts personnalisés pour garantir la cohérence lors de la prise d’un instantané de machine virtuelle. Des détails relatifs à l’appel de ces scripts sont fournis plus loin dans cet article.

Après que le service Sauvegarde Azure a pris l’instantané, les données sont transférées vers le coffre de sauvegarde. Pour optimiser l’efficacité, le service identifie et transfère uniquement les blocs de données qui ont été modifiés depuis la sauvegarde précédente.

![Architecture de la sauvegarde des machines virtuelles Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

Une fois le transfert de données terminé, l’instantané est supprimé et un point de récupération est créé.

> [!NOTE]
> 1. Pendant le processus de sauvegarde, le service Sauvegarde Azure n’inclut pas le disque temporaire attaché à la machine virtuelle. Pour plus d’informations, voir le blog relatif au [stockage temporaire](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).
> 2. Le service Sauvegarde Azure prend un instantané au niveau du stockage et transfère cet instantané au coffre. Ne changez pas les clés de compte de stockage tant que la tâche de sauvegarde n’est pas terminée.
>

### <a name="data-consistency"></a>Cohérence des données
La sauvegarde et la restauration des données critiques d’entreprise sont compliquées par le fait qu’elles doivent être sauvegardées alors que les applications qui génèrent les données sont en cours d’exécution. Pour résoudre ce problème, Azure Backup prend en charge des sauvegardes cohérentes avec les applications Windows et les machines virtuelles Linux
#### <a name="windows-vm"></a>Machine virtuelle Windows
Azure Backup effectue la sauvegarde complète VSS sur les machines virtuelles Windows (en savoir plus sur la [sauvegarde complète VSS](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)). Pour activer les sauvegardes de copie VSS, la clé de registre suivant doit être définie sur la machine virtuelle.

```
[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
"USEVSSCOPYBACKUP"="TRUE"
```

#### <a name="linux-vms"></a>Machines virtuelles Linux

Sauvegarde Azure Backup fournit une infrastructure de script permettant de contrôler le flux de travail et l’environnement de sauvegarde. Pour garantir que les sauvegardes de machines virtuelles Linux soient cohérentes avec l’application, utilisez l’infrastructure de scripts pour créer des pré-scripts et post-scripts personnalisés. Appelez le pré-script avant de prendre l’instantané de machine virtuelle, puis appelez le post-script une fois le travail de prise d’instantané de machine virtuelle terminé. Pour plus d’informations, voir l’article concernant les [sauvegardes de machines virtuelles Linux cohérentes avec l’application](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

> [!NOTE]
> Le service Sauvegarde Azure appelle uniquement les pré-scripts et post-scripts écrits par le client. Si les pré-scripts et post-scripts s’exécutent avec succès. le service Sauvegarde Azure marque le point de récupération comme étant cohérent dans l’application. Toutefois, le client est responsable de la cohérence des applications lors de l’utilisation des scripts personnalisés.
>

Le tableau suivant décrit les types de cohérences et les conditions dans lesquelles elles se produisent.

| Cohérence | En fonction du service VSS | Explication et détails |
| --- | --- | --- |
| Cohérence des applications |Oui pour Windows|La cohérence des applications est idéale pour les charges de travail, car elle apporte les garanties suivantes :<ol><li> la machine virtuelle *démarre*. <li>Les données *ne sont pas endommagées*. <li>Il n’y a *aucune perte de données*.<li> Les données sont cohérentes vis-à-vis de l’application qui les utilise grâce à la sollicitation de l’application au moment de la sauvegarde (à l’aide de VSS) pré/post script.</ol> <li>*Machines virtuelles Windows* : la plupart des charges de travail de Microsoft ont des enregistreurs VSS qui exécutent des actions de charges de travail spécifiques relatives à la cohérence des données. Par exemple, l’enregistreur VSS SQL Server veille à ce que les écritures dans le fichier journal de transactions et la base de données soient effectuées correctement. Pour les sauvegardes de machines virtuelles IaaS Windows, la création d’un point de récupération cohérent avec l’application signifie que l’extension de sauvegarde doit appeler le flux de travail VSS et le terminer avant la prise de l’instantané de la machine virtuelle. Pour que l’instantané de machine virtuelle Azure soit précis, les enregistreurs VSS de toutes les applications de machine virtuelle Azure doivent également se terminer. (Découvrez les [principes de base du service VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) et son [fonctionnement](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx)). </li> <li> *Machines virtuelles Linux* : les clients peuvent exécuter des [pré-scripts et post-scripts personnalisés pour assurer la cohérence de l’application](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent). </li> |
| Cohérence du système de fichiers |Oui : pour les ordinateurs Windows |Il existe deux scénarios où le point de récupération peut être *cohérent avec le système de fichiers* :<ul><li>Sauvegardes de machines virtuelles Linux dans Azure, sans pré/post-script ou si le pré/post-script a échoué. <li>Échec de VSS lors de la sauvegarde de machines virtuelles Windows dans Azure.</li></ul> Dans les deux cas, la meilleure solution consiste à s’assurer que les conditions suivantes sont satisfaites : <ol><li> la machine virtuelle *démarre*. <li>Les données *ne sont pas endommagées*.<li>Il n’y a *aucune perte de données*.</ol>  Les applications doivent implémenter leur propre mécanisme de « correctif » sur les données restaurées. |
| Cohérence en cas d’incident |Non  |Cette situation est la même que lorsqu’une machine virtuelle rencontre un « incident » (via une réinitialisation matérielle ou logicielle). La cohérence en cas d’incident se produit généralement lorsque la machine virtuelle Azure est arrêtée au moment de la sauvegarde. Un point de récupération cohérent ne fournit aucune garantie de cohérence des données sur le support de stockage, que ce soit au niveau du système d’exploitation ou de l’application. Seules les données déjà présentes sur le disque au moment de la sauvegarde sont capturées et sauvegardées. <br/> <br/> Même s’il n’existe aucune garantie, le démarrage a généralement lieu et est suivi d’une procédure de vérification du disque comme chkdsk permettant de résoudre les erreurs d’endommagement. Les données ou les écritures en mémoire qui n’ont pas été transférées sur le disque sont perdues. Si une restauration de données est nécessaire, l’application suit généralement son propre mécanisme de vérification. <br><br>Par exemple, si le journal des transactions comporte des entrées qui n’existent pas dans la base de données, le logiciel de base de données effectue alors une restauration jusqu’à ce que les données soient cohérentes. Lorsque des données sont réparties sur plusieurs disques virtuels (comme des volumes fractionnés), un point de récupération cohérent après incident ne fournit aucune garantie quant à l’exactitude des données. |

## <a name="performance-and-resource-utilization"></a>Performance et utilisation des ressources
Au même titre qu’un logiciel de sauvegarde déployé sur site, il est recommandé de planifier les besoins en capacité et en consommation de ressources lors d’une sauvegarde de machines virtuelles dans Azure. Les [limites d’Azure Storage](../azure-subscription-service-limits.md#storage-limits) définissent la structuration des déploiements de machine virtuelle pour obtenir des performances maximales avec un impact minimal sur l’exécution des charges de travail.

### <a name="number-of-disks"></a>Nombre de disques
Le processus de sauvegarde tente d’exécuter un travail de sauvegarde aussi rapidement que possible. Autrement dit, il consomme un maximum de ressources. Cependant, toutes les opérations d’E/S sont limitées par le *débit cible d’un seul objet Blob*, qui est limité à 60 Mo par seconde. Afin de tenter d’accélérer l’opération, le processus de sauvegarde tente de sauvegarder chacun des disques de la machine virtuelle *en parallèle*. Si une machine virtuelle comprend quatre disques, le service tente de sauvegarder les quatre disques simultanément. Le facteur le plus important pour déterminer le trafic de sauvegarde d’un compte de stockage client est le **nombre de disques** sauvegardés.

### <a name="backup-schedule"></a>Planification de sauvegarde
Autre facteur ayant un impact sur les performances : la **planification de sauvegarde**. Configurer vos stratégies de manière à sauvegarder simultanément toutes les machines virtuelles revient à planifier un embouteillage. Le processus de sauvegarde tente de sauvegarder tous les disques en parallèle. Pour réduire le trafic de sauvegarde, sauvegardez chaque machine virtuelle à un moment différent de la journée, et évitez les sauvegardes simultanées.

## <a name="capacity-planning"></a>planification de la capacité
Téléchargez la [feuille de calcul Excel de planification des capacités des machines virtuelles](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) pour voir l’impact de vos choix en matière de planification de disques et de sauvegardes.

### <a name="backup-throughput"></a>Débit de sauvegarde
Pour chaque disque en cours de sauvegarde, Azure Backup lit les blocs sur le disque et stocke uniquement les données modifiées (sauvegarde incrémentielle). Le tableau suivant montre les valeurs moyennes de débit du service de sauvegarde. Utilisez les données suivantes pour estimer le temps nécessaire à la sauvegarde d’un disque d’une taille donnée.

| Opération de sauvegarde | Meilleur débit |
| --- | --- |
| Sauvegarde initiale |160 Mbits/s |
| Sauvegarde incrémentielle (DR) |640 Mbits/s  <br><br> Le débit diminue de manière significative si les données modifiées (qui doivent être sauvegardées) sont dispersées sur le disque.|

## <a name="total-vm-backup-time"></a>Durée de sauvegarde de machine virtuelle totale
La majeure partie du temps de sauvegarde est consacrée à la lecture et la copie des données, mais il existe d’autres opérations qui contribuent à la durée totale nécessaire à la sauvegarde d’une machine virtuelle :

* Délai nécessaire à l’ [installation ou à la mise à jour de l’extension de sauvegarde](backup-azure-arm-vms.md).
* Heure de l'instantané, qui est la durée nécessaire au déclenchement d’un instantané. Les instantanés sont déclenchés peu avant l’heure de sauvegarde planifiée.
* Délai d’attente de file d’attente. Étant donné que le service de sauvegarde traite les tâches de plusieurs clients en même temps, les données d’instantané risquent de ne pas être immédiatement copiées dans le coffre Recovery Services. Lors des périodes de charge maximale, il peut s’écouler huit heures avant que le traitement des sauvegardes ne commence. Toutefois, la durée de sauvegarde totale d’un ordinateur virtuel est inférieure à 24 heures pour des stratégies de sauvegarde quotidiennes.
Une durée totale de moins de 24 heures est possible pour les sauvegardes incrémentielles, mais pas pour les premières sauvegardes. La durée de la première sauvegarde est proportionnelle et peut dépasser 24 heures selon la taille des données et l’heure à laquelle la sauvegarde est effectuée.
* Le temps de transfert de données, le temps nécessaire pour que le service de sauvegarde calcule les modifications incrémentielles à partir d’une sauvegarde précédente de calcul et de transfère ces modifications dans l’archivage.

### <a name="why-are-backup-times-longer-than-12-hours"></a>Pourquoi les durées de sauvegarde dépassent-elles 12 heures ?

La sauvegarde consiste en deux phases : la prise d’instantané et le transfert de l’instantané vers l’archivage. Le service de sauvegarde optimise pour le stockage. Lorsque vous transférez des données d’instantané dans un coffre, le service transfère uniquement les modifications incrémentielles à partir de l’instantané précédent.  Pour déterminer les modifications incrémentielles, le service calcule la somme de contrôle des blocs. Si un bloc est modifié, le bloc est identifié comme un bloc à envoyer au coffre. Le service examine ensuite davantage chacun des blocs identifiés, à la recherche d’opportunités de réduire les données à transférer. Après avoir évalué tous les blocs modifiés, le service fusionne les modifications et les envoie au coffre. Dans certaines applications héritées, les écritures de petite taille et fragmentées ne sont pas optimales pour le stockage. Si l’instantané contient un grand nombre d’écritures de petite taille et fragmentées, le service consacre plus de temps à traiter les données écrites par les applications. Pour les applications exécutées dans la machine virtuelle, la taille du bloc d’écriture d’application recommandée est de 8 Ko minimum. Si votre application utilise un bloc de moins de 8 Ko, les performances de sauvegarde sont affectées. Pour obtenir de l’aide concernant le réglage de votre application afin d’améliorer les performances de sauvegarde, voir [Paramétrage des applications pour optimiser les performances de Stockage Azure](../virtual-machines/windows/premium-storage-performance.md). Bien que l’article sur les performances de sauvegarde utilise des exemples de stockage Premium, il est aussi applicable aux disques de stockage Standard.<br>
Il peut y avoir plusieurs raisons à une durée de sauvegarde longue :
  1. **Première sauvegarde d’un disque qui vient d’être ajouté à une machine virtuelle déjà protégée** <br>
    Si une machine virtuelle a terminé la sauvegarde initiale et effectue une sauvegarde incrémentielle. Selon la taille du nouveau disque, il peut manquer un contrat de niveau de service (SLA) de 1 jour lors de l’ajout d’un ou de nouveaux disques.
  2. **Fragmentation** <br>
    Si la charge de travail (application) en cours d’exécution sur la machine virtuelle effectue de petites écritures fragmentées, elle peut nuire aux performances de la sauvegarde. <br>
  3. **Compte de stockage surchargé** <br>
      a. Si la sauvegarde est planifiée pendant l’utilisation d’applications d’aperçu.  
      b. Si plus de 5 à 10 disques sont hébergés à partir du même compte de stockage.<br>
  4. **Mode de vérification de la cohérence**  <br>
      Pour les disques > 1 To, si la sauvegarde se produit dans le mode de vérification de la cohérence pour les raisons mentionnées ci-dessous :<br>
        a. Déplacement du disque managé dans le cadre du redémarrage de la machine virtuelle.<br>
        b. Promotion de l’instantané sur un objet blob de base.<br>

## <a name="total-restore-time"></a>Temps de restauration total

Une opération de restauration se compose de deux tâches principales : la copie des données d’un coffre vers le compte de stockage choisi, et la création de la machine virtuelle. Le temps nécessaire pour copier les données d’un coffre dépend de l’endroit où les sauvegardes sont stockées dans Azure, et de l’emplacement du compte de stockage client. Le temps nécessaire pour copier des données dépend des paramètres suivants :
* Délai d’attente de la file : étant donné que le service traite des restaurations à partir de plusieurs clients en même temps, les demandes de restauration sont mises en file d’attente.
* Heure de copie des données : les données sont copiées du coffre de sauvegarde vers le compte de stockage client. Le temps de restauration des IOPS et du débit que le service Sauvegarde Azure atteint sur le compte de stockage client sélectionné. Pour réduire le temps de copie pendant le processus de restauration, sélectionnez un compte de stockage non chargé avec d’autres lectures et écritures d’application.

## <a name="best-practices"></a>Meilleures pratiques

Nous vous suggérons de suivre les meilleures pratiques ci-dessous lors de la configuration de sauvegardes de toutes machines virtuelles :

* Ne planifiez pas de sauvegarde simultanée de plus de 10 machines virtuelles classiques à partir du même service cloud. Si vous souhaitez sauvegarder plusieurs machines virtuelles à partir du même service cloud, échelonnez les débuts de sauvegarde d’heure en heure.
* Ne planifiez pas de sauvegardes pour plus de 100 machines virtuelles à partir d’un seul coffre en même temps.
* Planifiez les sauvegardes de machines virtuelles pendant les heures creuses. Ainsi, le service Sauvegarde utilise des IOPS pour transférer les données du compte de stockage client vers le coffre de sauvegarde.
* Vérifiez que Python version 2.7 (ou supérieure) est installé sur les machines virtuelles Linux activées pour la sauvegarde.

### <a name="best-practices-for-vms-with-unmanaged-disks"></a>Meilleures pratiques pour les machines virtuelles équipées de disques non gérés

Les recommandations suivantes s’appliquent uniquement aux machines virtuelles utilisant des disques non gérés. Si vos machines virtuelles utilisent des disques managés, le service de sauvegarde assure toutes les activités de gestion du stockage.

* Veillez à appliquer une stratégie de sauvegarde aux machines virtuelles réparties entre plusieurs comptes de stockage. Veillez à limiter à 20 le nombre total de disques d’un même compte de stockage protégés par la même planification de sauvegarde. Si votre compte de stockage contient plus de 20 disques, répartissez ces machines virtuelles entre plusieurs stratégies afin d’obtenir les IOPS requises pendant la phase de transfert du processus de sauvegarde.
* Évitez de restaurer une machine virtuelle exécutée sur le stockage Premium vers le même compte de stockage. Si le processus de restauration coïncide avec l’opération de sauvegarde, cela réduira les IOPS disponibles pour la sauvegarde.
* Pour la sauvegarde de machines virtuelles Premium sur une pile de sauvegarde de machines virtuelles V1, il est recommandé d’allouer seulement 50 % de l’espace total du compte de stockage, de façon à ce que le service de sauvegarde puisse copier la capture instantanée sur le compte de stockage, puis transférer les données de celui-ci vers le coffre.


## <a name="data-encryption"></a>Chiffrement des données
Azure Backup ne chiffre pas les données dans le cadre du processus de sauvegarde. Toutefois, vous pouvez chiffrer les données dans la machine virtuelle et sauvegarder les données protégées en toute transparence (pour en savoir plus, lire [sauvegarde des données chiffrées](backup-azure-vms-encryption.md)).

## <a name="calculating-the-cost-of-protected-instances"></a>Calcul du coût des instances protégées
Les machines virtuelles Azure sauvegardées par le biais d’Azure Backup sont soumises à la [Tarification d’Azure Backup](https://azure.microsoft.com/pricing/details/backup/). Le calcul des instances protégées est basé sur la taille *réelle* de la machine virtuelle, qui est la somme de toutes les données de la machine virtuelle, à l’exception du stockage temporaire.

La tarification pour la sauvegarde des machines virtuelles n’est pas basée sur la taille maximale prise en charge pour chaque disque de données joint à la machine virtuelle. La tarification est basée sur les données réelles stockées sur le disque de données. De même, la facture du stockage de sauvegarde est basée sur la quantité de données stockées sur le service Sauvegarde Azure, qui est la somme des données réelles de chaque point de récupération.

Prenons l’exemple d’une machine virtuelle de taille standard A2 avec deux disques de données supplémentaires, d’une taille maximale de 4 To chacun. Le tableau suivant indique les données réelles stockées sur chacun de ces disques :

| Type de disque | Taille maximale | Données réelles présentes |
| --------- | -------- | ----------- |
| Disque de système d’exploitation |4095 Go |17 Go |
| Disque local / disque temporaire |135 Go |5 Go (non inclus pour la sauvegarde) |
| Disque de données 1 |4095 Go |30 Go |
| Disque de données 2 |4095 Go |0 Go |

La taille réelle de la machine virtuelle est dans ce cas 17 Go + 30 Go + 0 Go= 47 Go. Cette taille d’instance protégée (47 Go) devient la base de la facture mensuelle. La taille de l’instance protégée utilisée pour la facturation augmente à mesure qu’augmente le volume des données dans la machine virtuelle.

La facturation ne commence pas avant la fin de la première sauvegarde réussie. À partir de ce moment, la facturation du stockage et des instances protégées commence. La facturation continue tant que des données de sauvegarde sont stockées dans un coffre pour la machine virtuelle. Si vous arrêtez la protection sur la machine virtuelle, alors que des données de sauvegarde de la machine virtuelle figurent dans un coffre de sauvegarde, la facturation continue.

La facturation pour une machine virtuelle spécifiée ne s’interrompt que si la protection est levée et que toutes les données de sauvegarde sont supprimées. Lorsque la protection s’arrête, si aucun travail de sauvegarde n’est actif, la taille de la dernière sauvegarde de machine virtuelle réussie devient la taille de l’Instance protégée utilisée pour la facture mensuelle.

## <a name="questions"></a>Des questions ?
Si vous avez des questions ou si vous souhaitez que certaines fonctionnalités soient incluses, [envoyez-nous vos commentaires](https://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Étapes suivantes
* [Sauvegarde de machines virtuelles](backup-azure-arm-vms.md)
* [Gestion de la sauvegarde de machine virtuelle](backup-azure-manage-vms.md)
* [Restauration des machines virtuelles](backup-azure-arm-restore-vms.md)
* [Résoudre les problèmes de sauvegarde de machines virtuelles](backup-azure-vms-troubleshoot.md)
