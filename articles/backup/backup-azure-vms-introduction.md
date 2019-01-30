---
title: À propos de la sauvegarde de machine virtuelle Azure
description: Découvrez la sauvegarde de machine virtuelle Azure et quelques bonnes pratiques.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: raynew
ms.openlocfilehash: 57d52412648cbe8a0791aa306075018a2092bf51
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54827327"
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
4. L’extension de sauvegarde prend un instantané de cohérence d’incident/cohérence de fichier au niveau du stockage.
5. Une fois l’instantané pris, les données sont transférées au coffre. Pour optimiser son efficacité, le service identifie et transfère uniquement les blocs de données qui ont été modifiés depuis la sauvegarde précédente (le delta).
5. Une fois le transfert de données terminé, l’instantané est supprimé et un point de récupération est créé.

![Architecture de la sauvegarde des machines virtuelles Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="data-encryption"></a>Chiffrement des données

Sauvegarde Azure ne chiffre pas les données dans le cadre du processus de sauvegarde. Sauvegarde Azure prend en charge la sauvegarde des machines virtuelles Azure chiffrées avec Azure Disk Encryption.

- La sauvegarde des machines virtuelles chiffrées avec une clé de chiffrement BitLocker (BEK) uniquement ou avec une BEK et une clé de chiffrement principale (KEK) est prise en charge, à la fois pour les machines virtuelles Azure gérées et non gérées.
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
        - Exécutez la commande ci-dessous à partir d'une invite de commande avec élévation des privilèges (comme administrateur) pour définir la clé de registre ci-dessus :
          ```
          REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent" /v USEVSSCOPYBACKUP /t REG_SZ /d TRUE /f
          ```
- **Machines virtuelles Linux** : Pour vérifier que vos machines virtuelles Linux respectent la cohérence des applications lors de prise d’instantanés par Sauvegarde Azure, vous pouvez utiliser le framework de pré-scripts et post-scripts Linux. Vous pouvez écrire vos propres scripts personnalisés à des fins de cohérence lors de la prise d’un instantané de machine virtuelle.
    -  Le service Sauvegarde Azure appelle uniquement les pré-scripts et post-scripts écrits par vous.
    - Si les pré-scripts et post-scripts s’exécutent correctement, le service Sauvegarde Azure marque le point de récupération comme étant cohérent dans les applications. Toutefois, vous êtes responsable de la cohérence des applications lors de l’utilisation de scripts personnalisés.
    - [Découvrez-en plus](backup-azure-linux-app-consistent.md) sur la configuration des scripts.


#### <a name="consistency-types"></a>Types de cohérence

Le tableau suivant explique les différents types de cohérence.

**Instantané** | **Détails** | **Récupération** | **Considération**
--- | --- | --- | ---
**Cohérence des applications** | Les sauvegardes cohérentes dans les applications capturent le contenu et les opérations d’E/S en attente de la mémoire. Les instantanés cohérents dans les applications utilisent l’enregistreur VSS (ou un pré/post-script pour Linux) qui garantissent la cohérence des données d’application avant une sauvegarde. | Lors de la récupération avec un instantané cohérent dans les applications, la machine virtuelle démarre. Il n’y a aucune altération ni perte des données. Les applications démarrent dans un état cohérent. | Windows : Tous les enregistreurs VSS ont réussi<br/><br/> Linux : Les pré/post-scripts sont configurés et ont réussi
**Cohérence du système de fichiers** | Les sauvegardes cohérentes dans le système de fichiers fournissent des sauvegardes cohérentes des fichiers de disque en prenant un instantané de tous les fichiers en même temps.<br/><br/> | Lors de la récupération avec un instantané cohérent dans les fichiers, la machine virtuelle démarre. Il n’y a aucune altération ni perte des données. Les applications ont besoin d’implémenter leur propre mécanisme de « correction » pour s’assurer que les données restaurées sont cohérentes. | Windows : Certains enregistreurs VSS ont échoué <br/><br/> Linux : Par défaut (si les pré/post-scripts ne sont pas configurés ou ont échoué)
**Cohérence en cas d’incident** | Une cohérence en cas d’incident se produit souvent quand une machine virtuelle Azure s’arrête au moment de la sauvegarde.  Seules les données déjà présentes sur le disque au moment de la sauvegarde sont capturées et sauvegardées.<br/><br/> Un point de récupération cohérent en cas d’incident ne garantit pas la cohérence des données pour le système d’exploitation ou l’application. | Il n’y a aucune garantie, mais généralement la machine virtuelle démarre, puis effectue une vérification des disques afin de corriger les erreurs dues à des altérations. Toutes les données en mémoire ou écritures qui n’ont pas été transférées vers un disque sont perdues. Les applications implémentent leur propre vérification des données. Par exemple, pour une application de base de données, si un journal des transactions comporte des entrées qui ne figurent pas dans la base de données, le logiciel de base de données tourne jusqu’à ce que les données soient cohérentes. | La machine virtuelle est arrêtée


## <a name="service-and-subscription-limits"></a>Limites du service et des abonnements

Sauvegarde Azure présente plusieurs limites en termes d’abonnements et de coffres.

[!INCLUDE [azure-backup-limits](../../includes/azure-backup-limits.md)]


## <a name="backup-performance"></a>Performances de sauvegarde

### <a name="disk-considerations"></a>Considérations liées aux disques

Une opération de sauvegarde optimise en sauvegardant parallèlement chacun des disques de la machine virtuelle. Par exemple, si une machine virtuelle comprend quatre disques, le service tente de sauvegarder les quatre disques simultanément. Pour chaque disque en cours de sauvegarde, Azure Backup lit les blocs sur le disque et stocke uniquement les données modifiées (sauvegarde incrémentielle).


### <a name="scheduling-considerations"></a>Considérations liées à la planification

La planification de la sauvegarde affecte les performances.

- Si vous configurez des stratégies de sorte que toutes les machines virtuelles soient sauvegardées en même temps, vous planifiez un embouteillage, car le processus de sauvegarde tente de sauvegarder tous les disques en parallèle.
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

- **Sauvegarde initiale d’un disque qui vient d’être ajouté à une machine virtuelle déjà protégée** : Si une machine virtuelle fait l’objet d’une sauvegarde incrémentielle et qu’un nouveau disque est ajouté à cette machine, la durée de sauvegarde peut dépasser 24 heures, car le disque nouvellement ajouté doit subir un réplication initiale en même temps que la réplication différentielle des disques existants.
- **Fragmentation** : Le produit de sauvegarde analyse les modifications incrémentielles entre deux opérations de sauvegarde. Les opérations de sauvegarde sont plus rapides quand les modifications sont regroupées dans un même emplacement sur le disque que quand elles sont éparpillées. 
- **Évolution** : Une évolution quotidienne (pour la réplication incrémentielle) de plus de 200 Go par disque peut prendre plus de 8 heures. Si une machine virtuelle possède plusieurs disques, et si la sauvegarde de l’un d’eux prend plus de temps, cela peut avoir une incidence sur l’opération de sauvegarde globale (ou entraîner un échec). 
- **Mode Comparaison de somme de contrôle** : Le mode Comparaison de somme de contrôle est relativement plus lent que le mode optimisé utilisé par un point de récupération instantané. Si vous utilisez déjà un point de récupération instantané et avez supprimé les instantanés de niveau 1, la sauvegarde bascule en mode Comparaison de somme de contrôle, qui a effet que l’opération de sauvegarde prend plus de 24 heures (ou échoue).

## <a name="restore-considerations"></a>Considérations liées à la restauration

Une opération de restauration se compose de deux tâches principales : copie des données d’un coffre vers le compte de stockage choisi et création de la machine virtuelle. Le temps nécessaire pour copier les données d’un coffre dépend de l’endroit où les sauvegardes sont stockées dans Azure, et de l’emplacement du compte de stockage. Le temps nécessaire pour copier des données dépend des paramètres suivants :

- **Temps d’attente en file d’attente** : étant donné que le service traite les restaurations à partir de plusieurs comptes de stockage en même temps, les demandes de restauration sont mises en file d’attente.
- **Temps de copie des données** : les données sont copiées du coffre vers le compte de stockage. La durée de restauration dépend des IOPS et du débit du compte de stockage sélectionné que le service Sauvegarde Azure utilise. Pour réduire le temps de copie pendant le processus de restauration, sélectionnez un compte de stockage non chargé avec d’autres lectures et écritures d’application.

## <a name="best-practices"></a>Bonnes pratiques

Nous vous suggérons de suivre ces pratiques pour configurer des sauvegardes de machines virtuelles :

- Envisagez de modifier l’heure de stratégie par défaut (par exemple, si votre heure de stratégie par défaut est 24:00 heures, envisagez de l’incrémenter de quelques minutes) lorsque les instantanés de données sont pris pour s’assurer que les ressources sont utilisées de manière optimale.
- Pour une sauvegarde de machine virtuelle Premium sur un point de récupération non instantané, la fonctionnalité alloue environ 50 % de l’espace total du compte de stockage. Le service de sauvegarde requiert cet espace pour copier l’instantané vers le même compte de stockage et le transférer vers le coffre.
- Pour restaurer des machines virtuelles à partir d’un seul coffre, il est vivement recommandé d’utiliser des  [comptes de stockage v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)  différents pour s’assurer que le compte de stockage cible ne soit pas limité. Par exemple, chaque machine virtuelle doit avoir compte de stockage distincts(si 10 machines virtuelles sont restaurées, utilisez 10 comptes de stockage différents).
- Les restaurations à partir de la couche de stockage de niveau 1 (instantané) sont effectuées en quelques minutes (dans la mesure où il s’agit du même compte de stockage), par opposition à la couche de stockage de niveau 2 (coffre) qui peut prendre des heures. Nous vous recommandons d’utiliser la fonctionnalité [Restauration instantanée](backup-instant-restore-capability.md) pour accélérer les restaurations quand des données sont disponibles au niveau 1 (sachant que, si les données doit être restaurées à partir du coffre, cela prendra de temps).
- La limite du nombre de disques par compte de stockage dépend de la lourdeur des disques auxquels accèdent les applications s’exécutant sur la machine virtuelle IaaS. Vérifiez si plusieurs disques sont hébergés sur un seul compte de stockage. En règle générale, si plus de 5 disques sont présents sur un compte de stockage, équilibrez la charge en déplaçant certains disques vers des comptes de stockage distincts.

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

- [Découvrez-en plus](../virtual-machines/windows/premium-storage-performance.md) sur le paramétrage des applications pour optimiser les performances du stockage Azure. Cet article se concentre sur le stockage Premium, mais il s’applique également aux disques de stockage standard.
- [Prenez en main](backup-azure-arm-vms-prepare.md) la sauvegarde en examinant la prise en charge et les limitations des machines virtuelles, en créant un coffre, puis en préparant des machines virtuelles à la sauvegarde.
