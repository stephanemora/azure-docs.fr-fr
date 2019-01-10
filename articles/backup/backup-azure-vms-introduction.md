---
title: À propos de la sauvegarde de machine virtuelle Azure
description: Découvrez la sauvegarde de machine virtuelle Azure et quelques bonnes pratiques.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: raynew
ms.openlocfilehash: 9a80671a72f059e24a8cebc5de803af9261ad829
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53743950"
---
# <a name="about-azure-vm-backup"></a>À propos de la sauvegarde de machine virtuelle Azure

Cet article décrit comment le [service Sauvegarde Azure](backup-introduction-to-azure-backup.md) sauvegarde les machines virtuelles Azure.

## <a name="backup-process"></a>Processus de sauvegarde

Voici comment Sauvegarde Azure effectue une sauvegarde de machines virtuelles Azure.

1. Pour les machines virtuelles Azure sélectionnées pour la sauvegarde, le service Sauvegarde Azure lance un travail de sauvegarde conformément à la planification de sauvegarde que vous spécifiez.
2. Le service déclenche l’extension de sauvegarde.
    - Les machines virtuelles Windows utilisent l’extension _VMSnapshot_.
    - Les machines virtuelles Linux utilisent l’extension _VMSnapshotLinux_.
    - L’extension est installée lors de la première sauvegarde de machine virtuelle.
    - Pour que l’extension soit installée, la machine virtuelle doit être en cours d’exécution.
    - Si la machine virtuelle n’est pas en cours d’exécution, le service Sauvegarde prend un instantané du stockage sous-jacent (car aucune écriture de l’application n’a lieu pendant l’arrêt de la machine virtuelle).
4. L’extension de sauvegarde prend un instantané cohérent dans les applications au niveau du stockage.
5. Une fois l’instantané pris, les données sont transférées au coffre. Pour optimiser son efficacité, le service identifie et transfère uniquement les blocs de données qui ont été modifiés depuis la sauvegarde précédente (le delta).
5. Une fois le transfert de données terminé, l’instantané est supprimé et un point de récupération est créé.

![Architecture de la sauvegarde des machines virtuelles Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="data-encryption"></a>Chiffrement des données

Sauvegarde Azure ne chiffre pas les données dans le cadre du processus de sauvegarde. Sauvegarde Azure prend en charge la sauvegarde des machines virtuelles Azure chiffrées avec Azure Disk Encryption.

- La sauvegarde des machines virtuelles chiffrées avec une clé de chiffrement Bitlocker (BEK) uniquement ou avec une BEK et une clé de chiffrement principale (KEK) est prise en charge, à la fois pour les machines virtuelles Azure gérées et non gérées.
- Les BEK (secrets) et KEK (clés) sauvegardées sont chiffrées afin de pouvoir être lues et utilisées uniquement quand les utilisateurs autorisés les restaurent dans le coffre de clés.
- Étant donné que la BEK est également sauvegardée, s’ils la perdent, les utilisateurs autorisés peuvent la restaurer dans le coffre de clés pour récupérer la machine virtuelle chiffrée. Les clés et secrets des machines virtuelles chiffrées sont sauvegardés sous forme chiffrée, si bien que ni les utilisateurs non autorisés, ni Azure ne peuvent lire ou utiliser les clés et secrets sauvegardés. Seuls les utilisateurs disposant du niveau d’autorisations approprié peuvent sauvegarder et restaurer des machines virtuelles chiffrées, ainsi que des clés et des secrets.

## <a name="snapshot-consistency"></a>Cohérence des instantanés

Quand des applications sont en cours d’exécution, Sauvegarde Azure prend des instantanés en veillant à la cohérence des applications.

- **Machines virtuelles Windows** : Pour les machines virtuelles Windows, le service Sauvegarde se coordonne avec le service VSS pour obtenir un instantané cohérent des disques de la machine virtuelle.
    - Par défaut, Sauvegarde Azure effectue des sauvegardes VSS complètes. [Plus d’informations](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)
    - Pour modifier le paramètre afin que Sauvegarde Azure effectue des sauvegardes de copie VSS, définissez la clé de Registre suivante :
        ```
        [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
        ""USEVSSCOPYBACKUP"="TRUE"
        ```
- **Machines virtuelles Linux** : Pour vérifier que vos machines virtuelles Linux respectent la cohérence des applications lors de prise d’instantanés par Sauvegarde Azure, vous pouvez utiliser le framework de pré-scripts et post-scripts Linux. Vous pouvez écrire vos propres scripts personnalisés à des fins de cohérence lors de la prise d’un instantané de machine virtuelle.
    -  Le service Sauvegarde Azure appelle uniquement les pré-scripts et post-scripts écrits par vous.
    - Si les pré-scripts et post-scripts s’exécutent correctement, le service Sauvegarde Azure marque le point de récupération comme étant cohérent dans les applications. Toutefois, vous êtes responsable de la cohérence des applications lors de l’utilisation de scripts personnalisés.
    - [Découvrez-en plus](backup-azure-linux-app-consistent.md) sur la configuration des scripts.


#### <a name="consistency-types"></a>Types de cohérence

Le tableau suivant explique les différents types de cohérence.

**Instantané** | **Basé sur le service VSS** | **Détails** | **Récupération**
--- | --- | --- | ---
**Cohérence des applications** | Oui (Windows uniquement) | Les sauvegardes cohérentes dans les applications capturent le contenu et les opérations d’E/S en attente de la mémoire. Les instantanés cohérents dans les applications utilisent l’enregistreur VSS (ou un pré/post-script pour Linux) qui garantissent la cohérence des données d’application avant une sauvegarde. | Lors de la récupération avec un instantané cohérent dans les applications, la machine virtuelle démarre. Il n’y a aucune altération ni perte des données. Les applications démarrent dans un état cohérent.
**Cohérence du système de fichiers** | Oui (Windows uniquement) |  Les sauvegardes cohérentes dans le système de fichiers fournissent des sauvegardes cohérentes des fichiers de disque en prenant un instantané de tous les fichiers en même temps.<br/><br/> Les points de récupération de sauvegarde Azure sont cohérents dans les fichiers pour :<br/><br/> - les sauvegardes de machines virtuelles Linux qui n’ont pas de pré/post-scripts ou qui ont des scripts qui ont échoué ;<br/><br/> - les sauvegardes de machine virtuelle Windows où VSS a échoué. | Lors de la récupération avec un instantané cohérent dans les fichiers, la machine virtuelle démarre. Il n’y a aucune altération ni perte des données. Les applications ont besoin d’implémenter leur propre mécanisme de « correction » pour veiller à ce que les données restaurées sont cohérentes.
**Cohérence en cas d’incident** | Non  | Une cohérence en cas d’incident se produit souvent quand une machine virtuelle Azure s’arrête au moment de la sauvegarde.  Seules les données déjà présentes sur le disque au moment de la sauvegarde sont capturées et sauvegardées.<br/><br/> Un point de récupération cohérent en cas d’incident ne garantit pas la cohérence des données pour le système d’exploitation ou l’application. | Il n’y a aucune garantie, mais généralement la machine virtuelle démarre, puis effectue une vérification des disques afin de corriger les erreurs dues à des altérations. Toutes les données en mémoire ou écritures qui n’ont pas été transférées vers un disque sont perdues. Les applications implémentent leur propre vérification des données. Par exemple, pour une application de base de données, si un journal des transactions comporte des entrées qui ne figurent pas dans la base de données, le logiciel de base de données tourne jusqu’à ce que les données soient cohérentes.


## <a name="service-and-subscription-limits"></a>Limites du service et des abonnements

Sauvegarde Azure présente plusieurs limites en termes d’abonnements et de coffres.

[!INCLUDE [azure-backup-limits](../../includes/azure-backup-limits.md)]


## <a name="backup-performance"></a>Performances de sauvegarde

### <a name="disk-considerations"></a>Considérations liées aux disques

Sauvegarde Azure essaie de s’exécuter aussi rapidement que possible, en consommant autant de ressources que possible.

- Pour essayer d’accélérer l’opération, le processus de sauvegarde tente de sauvegarder chacun des disques de la machine virtuelle en parallèle.
- Par exemple, si une machine virtuelle comprend quatre disques, le service tente de sauvegarder les quatre disques simultanément.
- Le facteur le plus important pour déterminer le trafic de sauvegarde d’un compte de stockage client est le nombre de disques sauvegardés.
- Toutes les opérations d’E/S sont limitées par le *débit cible d’un seul objet blob*, qui est limité à 60 Mo par seconde.
- Pour chaque disque en cours de sauvegarde, Azure Backup lit les blocs sur le disque et stocke uniquement les données modifiées (sauvegarde incrémentielle). Vous pouvez utiliser les valeurs de débit moyennes ci-dessous pour estimer le temps nécessaire à la sauvegarde d’un disque d’une taille donnée.

    **opération** | **Meilleur débit**
    --- | ---
    Sauvegarde initiale | 160 Mbits/s |
    Sauvegarde incrémentielle | 640 Mbits/s  <br><br> Le débit diminue considérablement si les données delta sont dispersées sur le disque.|



### <a name="scheduling-considerations"></a>Considérations liées à la planification

La planification de la sauvegarde affecte les performances.

- Si vous configurez des stratégies de sorte à ce que toutes les machines virtuelles soient sauvegardées en même temps, vous planifiez un embouteillage, car le processus de sauvegarde tente de sauvegarder tous les disques en parallèle.
- Pour réduire le trafic de sauvegarde, sauvegardez chaque machine virtuelle à un moment différent de la journée, et évitez les sauvegardes simultanées.


### <a name="time-considerations"></a>Considérations de durée

La majeure partie du temps de sauvegarde est consacrée à la lecture et la copie des données, mais il existe d’autres opérations qui contribuent à la durée totale nécessaire à la sauvegarde d’une machine virtuelle :

- **Installer l’extension de sauvegarde** : temps nécessaire à l’installation ou à la mise à jour de l’extension de sauvegarde.
- **Déclencher un instantané** : temps nécessaire pour déclencher un instantané. Les instantanés sont déclenchés peu avant l’heure de sauvegarde planifiée.
- **Temps d’attente en file d’attente** : étant donné que le service de sauvegarde traite les travaux de plusieurs comptes de stockage clients en même temps, les données d’instantané risquent de ne pas être immédiatement copiées dans le coffre Recovery Services. Lors des périodes de charge maximale, il peut s’écouler huit heures avant que le traitement des sauvegardes ne commence. Toutefois, la durée de sauvegarde totale d’un ordinateur virtuel est inférieure à 24 heures pour des stratégies de sauvegarde quotidiennes.
- **Sauvegarde initiale** : Bien qu’une durée totale de moins de 24 heures soit possible pour les sauvegardes incrémentielles, cela peut ne pas être le cas pour la première sauvegarde. Le temps nécessaire dépendra de la taille des données et du moment auquel est réalisée la sauvegarde.
- **Temps de transfert des données** : temps nécessaire pour que le service de sauvegarde calcule les modifications incrémentielles à partir d’une sauvegarde précédente et transfère ces modifications au coffre.

### <a name="factors-affecting-backup-time"></a>Facteurs affectant la durée de sauvegarde

La sauvegarde comporte deux phases : la prise d’instantanés et le transfert des instantanés vers le coffre. Le service de sauvegarde optimise pour le stockage.

- Lorsque vous transférez des données d’instantané dans un coffre, le service transfère uniquement les modifications incrémentielles à partir de l’instantané précédent.
- Pour déterminer les modifications incrémentielles, le service calcule la somme de contrôle des blocs.
    - Si un bloc est modifié, le bloc est identifié comme un bloc à envoyer au coffre.
    - Le service examine davantage chacun des blocs identifiés, à la recherche d’opportunités de réduire les données à transférer.
    - Après avoir évalué tous les blocs modifiés, le service fusionne les modifications et les envoie au coffre.

Les situations qui peuvent affecter la durée de sauvegarde sont les suivantes :


- **Sauvegarde initiale d’un disque qui vient d’être ajouté à une machine virtuelle déjà protégée** : si une machine virtuelle fait l’objet d’une sauvegarde incrémentielle, quand vous ajoutez un nouveau disque, alors la sauvegarde peut manquer le contrat SLA d’un jour, selon la taille du nouveau disque.
- **Application fragmentée** : si une application n’est pas correctement configurée, elle peut ne pas être optimale pour le stockage.
    - Si l’instantané contient un grand nombre d’écritures de petite taille et fragmentées, le service consacre plus de temps à traiter les données écrites par les applications.
    - Pour les applications exécutées dans la machine virtuelle, la taille du bloc d’écriture d’application recommandée est de 8 Ko minimum. Si votre application utilise un bloc de moins de 8 Ko, les performances de sauvegarde sont affectées.
- **Compte de stockage surchargé** : une sauvegarde peut être planifiée quand l’application est en cours d’exécution en production ou si plus de cinq à dix disques sont hébergés à partir du même compte de stockage.
- **Mode de vérification de la cohérence** : Pour les disques supérieurs à 1 To, la sauvegarde peut être en mode de vérification de la cohérence pour deux raisons :
    - Déplacement du disque managé dans le cadre du redémarrage de la machine virtuelle.
    - Promotion de l’instantané sur un objet blob de base.


## <a name="restore-considerations"></a>Considérations liées à la restauration

Une opération de restauration se compose de deux tâches principales : copie des données d’un coffre vers le compte de stockage choisi et création de la machine virtuelle. Le temps nécessaire pour copier les données d’un coffre dépend de l’endroit où les sauvegardes sont stockées dans Azure, et de l’emplacement du compte de stockage. Le temps nécessaire pour copier des données dépend des paramètres suivants :

- **Temps d’attente en file d’attente** : étant donné que le service traite les restaurations à partir de plusieurs comptes de stockage en même temps, les demandes de restauration sont mises en file d’attente.
- **Temps de copie des données** : les données sont copiées du coffre vers le compte de stockage. La durée de restauration dépend des IOPS et du débit du compte de stockage sélectionné que le service Sauvegarde Azure utilise. Pour réduire le temps de copie pendant le processus de restauration, sélectionnez un compte de stockage non chargé avec d’autres lectures et écritures d’application.

## <a name="best-practices"></a>Bonnes pratiques

Nous vous suggérons de suivre ces pratiques pour configurer des sauvegardes de machines virtuelles :

- Ne planifiez pas de sauvegardes pour plus de 100 machines virtuelles à partir d’un seul coffre en même temps.
- Planifiez les sauvegardes de machines virtuelles pendant les heures creuses. Ainsi, le service Sauvegarde utilise des IOPS pour transférer les données du compte de stockage vers le coffre.
- Si vous sauvegardez des disques managés, le service Sauvegarde Azure gère le stockage. Si vous sauvegardez des disques non managés :
    - Veillez à appliquer une stratégie de sauvegarde aux machines virtuelles réparties entre plusieurs comptes de stockage.
    - Veillez à limiter à 20 le nombre de disques d’un même compte de stockage protégés par la même planification de sauvegarde.
    - Si votre compte de stockage contient plus de 20 disques, répartissez ces machines virtuelles entre plusieurs stratégies afin d’obtenir les IOPS requises pendant la phase de transfert du processus de sauvegarde.
    - Évitez de restaurer une machine virtuelle exécutée sur le stockage Premium vers le même compte de stockage. Si le processus de restauration coïncide avec l’opération de sauvegarde, cela réduira les IOPS disponibles pour la sauvegarde.
    - Pour la sauvegarde de machines virtuelles Premium sur une pile de sauvegarde de machines virtuelles V1, il est recommandé d’allouer seulement 50 % de l’espace total du compte de stockage, de façon à ce que le service de sauvegarde puisse copier la capture instantanée sur le compte de stockage, puis transférer les données de celui-ci vers le coffre.
- Il est recommandé d’utiliser différents comptes de stockage au lieu d’utiliser les mêmes comptes de stockage pour restaurer des machines virtuelles à partir d’un seul coffre. Ainsi, vous évitez le phénomène de limitation et la restauration réussit à 100 % avec des performances optimales.
- Les restaurations à partir de la couche de stockage de niveau 1 s’effectuent en quelques minutes tandis que celles de niveau 2 prennent plusieurs heures. Nous vous recommandons d’utiliser la [fonctionnalité du point de récupération instantané](backup-upgrade-to-vm-backup-stack-v2.md) pour obtenir des restaurations plus rapides. Cette dernière s’applique uniquement aux machines virtuelles Azure gérées.


## <a name="backup-costs"></a>Coûts de sauvegarde

Les machines virtuelles Azure sauvegardées avec Sauvegarde Azure sont soumises aux [tarifs de Sauvegarde Azure](https://azure.microsoft.com/pricing/details/backup/).

- La facturation ne commence pas avant la fin de la première sauvegarde réussie. À partir de ce moment, la facturation du stockage et des instances protégées commence.
- La facturation continue tant que des données de sauvegarde sont stockées dans un coffre pour la machine virtuelle. Si vous arrêtez la protection sur la machine virtuelle, alors que des données de sauvegarde de la machine virtuelle figurent dans un coffre de sauvegarde, la facturation continue.
- La facturation pour une machine virtuelle spécifiée ne s’interrompt que si la protection est levée et que toutes les données de sauvegarde sont supprimées.
- Lorsque la protection s’arrête, si aucun travail de sauvegarde n’est actif, la taille de la dernière sauvegarde de machine virtuelle réussie devient la taille de l’Instance protégée utilisée pour la facture mensuelle.
- Le calcul des instances protégées est basé sur la taille *réelle* de la machine virtuelle, qui est la somme de toutes les données de la machine virtuelle, à l’exception du stockage temporaire.
- La tarification est basée sur les données réelles stockées sur le disque de données.
- La tarification pour la sauvegarde des machines virtuelles n’est pas basée sur la taille maximale prise en charge pour chaque disque de données joint à la machine virtuelle.
- De même, la facture du stockage de sauvegarde est basée sur la quantité de données stockées sur le service Sauvegarde Azure, qui est la somme des données réelles de chaque point de récupération.

Prenons l’exemple d’une machine virtuelle de taille standard A2 avec deux disques de données supplémentaires, d’une taille maximale de 4 To chacun. Le tableau suivant indique les données réelles stockées sur chacun de ces disques :

| Type de disque | Taille maximale | Données réelles présentes |
| --------- | -------- | ----------- |
| Disque de système d’exploitation |4095 Go |17 Go |
| Disque local / disque temporaire |135 Go |5 Go (non inclus pour la sauvegarde) |
| Disque de données 1 |4095 Go |30 Go |
| Disque de données 2 |4095 Go |0 Go |

- La taille réelle de la machine virtuelle est dans ce cas 17 Go + 30 Go + 0 Go= 47 Go.
- Cette taille d’instance protégée (47 Go) devient la base de la facture mensuelle.
- La taille de l’instance protégée utilisée pour la facturation augmente à mesure qu’augmente le volume des données dans la machine virtuelle.


## <a name="next-steps"></a>Étapes suivantes

Après avoir examiné le processus de sauvegarde et les considérations relatives aux performances, effectuez les étapes suivantes :

- Téléchargez la [feuille de calcul Excel de planification de la capacité](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) pour effectuer des calculs de planification de sauvegarde et de disques.
- [Découvrez-en plus](../virtual-machines/windows/premium-storage-performance.md) sur le paramétrage des applications pour optimiser les performances du stockage Azure. Cet article se concentre sur le stockage Premium, mais il s’applique également aux disques de stockage standard.
- [Prenez en main](backup-azure-arm-vms-prepare.md) la sauvegarde en examinant la prise en charge et les limitations des machines virtuelles, en créant un coffre, puis en préparant des machines virtuelles à la sauvegarde.
