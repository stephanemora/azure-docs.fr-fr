---
title: À propos de la sauvegarde de machine virtuelle Azure
description: Découvrez la sauvegarde de machine virtuelle Azure et quelques bonnes pratiques.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: raynew
ms.openlocfilehash: 1e80b2083a2fce90259ac0634d9e7f796f459fcd
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57880949"
---
# <a name="about-azure-vm-backup"></a>À propos de la sauvegarde de machine virtuelle Azure

Cet article décrit comment la [service Azure Backup](backup-introduction-to-azure-backup.md) sauvegarde des machines virtuelles (VM) Azure.

## <a name="backup-process"></a>Processus de sauvegarde

Voici comment Azure sauvegarde se termine une sauvegarde des machines virtuelles Azure :

1. Pour les machines virtuelles Azure qui sont sélectionnés pour la sauvegarde, sauvegarde Azure démarre un travail de sauvegarde en fonction de la planification de sauvegarde que vous spécifiez.
1. Lors de la première sauvegarde, une extension de sauvegarde est installée sur la machine virtuelle si la machine virtuelle est en cours d’exécution.
    - Pour les machines virtuelles Windows, le _VMSnapshot_ extension est installée.
    - Pour les machines virtuelles Linux, le _VMSnapshotLinux_ extension est installée.
1. Pour les machines virtuelles Windows sont en cours d’exécution, sauvegarde coordonne avec Windows VSS Volume Shadow Copy Service () pour prendre un instantané cohérent de l’application de la machine virtuelle.
    - Par défaut, la sauvegarde a des sauvegardes complètes de VSS.
    - Si la sauvegarde ne peut pas prendre un instantané cohérent au niveau de l’application, puis il prend un instantané cohérent de fichier du stockage sous-jacent (car aucune écriture de l’application se produire pendant l’arrêt de la machine virtuelle).
1. Pour les machines virtuelles Linux, sauvegarde effectue une sauvegarde cohérentes au niveau fichier. Pour les instantanés cohérents d’application, vous devez personnaliser manuellement les scripts de pré/post.
1. Une fois la sauvegarde a pris l’instantané, il transfère les données dans le coffre. 
    - La sauvegarde est optimisée par la sauvegarde de chaque disque de machine virtuelle en parallèle.
    - Pour chaque disque qui est en cours de sauvegarde, sauvegarde Azure lit les blocs sur le disque et identifie et transfère uniquement les blocs de données qui a changé (delta) depuis la sauvegarde précédente.
    - Les données d’instantanés peuvent ne pas être immédiatement copiées dans le coffre. Aux heures de pointe, cela peut prendre quelques heures. Durée de sauvegarde totale pour une machine virtuelle sera inférieure à 24 heures pour les stratégies de sauvegarde quotidiennes.

1. Lorsque le transfert de données est terminé, l’instantané est supprimé, et un point de récupération est créé.

![Architecture de la sauvegarde des machines virtuelles Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Chiffrement des sauvegardes de machines virtuelles Azure

Quand vous sauvegardez des machines virtuelles Azure avec Sauvegarde Azure, ces machines sont chiffrées au repos avec SSE (Storage Service Encryption). Sauvegarde Azure peut également sauvegarder des machines virtuelles Azure qui sont chiffrées à l’aide d’Azure Disk Encryption.

**Chiffrement** | **Détails** | **Support**
--- | --- | ---
**Azure Disk Encryption** | Azure Disk Encryption chiffre les disques du système d’exploitation et de données pour les machines virtuelles Azure.<br/><br/> Azure Disk Encryption s’intègre avec des clés de chiffrement BitLocker (BEKs), qui sont sauvegardées dans un coffre de clés en tant que secrets. Azure Disk Encryption s’intègre également avec des clés de chiffrement à clé Azure Key Vault (Kek). | Sauvegarde Azure prend en charge la sauvegarde des machines virtuelles Azure managées et chiffrées avec BEKs uniquement ou avec BEKs ainsi que des clés.<br/><br/> BEKs et clés sont sauvegardées et chiffrés.<br/><br/> Étant donné que les clés et BEKs sont sauvegardées, les utilisateurs disposant des autorisations nécessaires peuvent restaurer des clés et secrets vers le coffre de clés si nécessaire. Ces utilisateurs peuvent également récupérer la machine virtuelle chiffrée.<br/><br/> Impossible de lire les secrets et clés chiffrées par les utilisateurs non autorisés ou par Azure.
**SSE** | Avec SSE, stockage Azure fournit le chiffrement au repos en chiffrant automatiquement les données avant de les stocker. Stockage Azure également déchiffre les données avant de les récupérer. | Sauvegarde Azure utilise SSE pour le chiffrement au repos des machines virtuelles Azure.

Managé et les machines virtuelles Azure, sauvegarde prend en charge les machines virtuelles chiffrées avec BEKs uniquement ou les machines virtuelles chiffrées avec BEKs ainsi que des clés.

Sauvegardé BEKs (secrets) les clés (clés) sont chiffrés. Ils peuvent lues et utilisées uniquement quand ils sont restaurés vers le coffre de clés par les utilisateurs autorisés. Les utilisateurs non autorisés, ni Azure capable de lire ou utiliser les clés sauvegardées ou secrets.

BEKs sont également sauvegardées. Par conséquent, si les BEKs sont perdus, les utilisateurs autorisés à restaurer les BEKs au coffre de clés et récupérer les machines virtuelles chiffrées. Seuls les utilisateurs avec le niveau nécessaire d’autorisations peuvent sauvegarder et restaurer des machines virtuelles chiffrées ou des clés et des secrets.

## <a name="snapshot-creation"></a>Création d’instantanés

Sauvegarde Azure prend des captures instantanées en fonction de la planification de sauvegarde. 

- **Machines virtuelles Windows :** Pour les machines virtuelles Windows, le service de sauvegarde se coordonne avec VSS pour prendre un instantané cohérent de l’application de disques de machine virtuelle.

  - Par défaut, Sauvegarde Azure effectue des sauvegardes VSS complètes. [Plus d’informations](https://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)
  - Pour modifier le paramètre afin que la sauvegarde Azure effectue des sauvegardes de copie VSS, définissez la clé de Registre suivante à partir d’une invite de commandes :

    **REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent" /v USEVSSCOPYBACKUP /t REG_SZ /d TRUE /f**

- **Machines virtuelles Linux :** Pour prendre des instantanés cohérents au niveau de l’application des machines virtuelles Linux, utilisez Linux pré-script et post-script dans le framework pour écrire vos propres scripts personnalisés pour garantir la cohérence.

  - Sauvegarde Azure appelle uniquement les scripts de pré/post écrits par vous.
  - Si les pré-scripts et post-scripts s’exécutent correctement, Azure Backup marque le point de récupération comme cohérente avec les applications. Toutefois, lorsque vous utilisez des scripts personnalisés, vous êtes responsable de la cohérence des applications.
  - [En savoir plus](backup-azure-linux-app-consistent.md) sur la façon de configurer des scripts.

### <a name="snapshot-consistency"></a>Cohérence des instantanés

Le tableau suivant décrit les différents types de cohérence de l’instantané :

**Instantané** | **Détails** | **Récupération** | **Considération**
--- | --- | --- | ---
**Cohérence des applications** | Les sauvegardes cohérentes dans les applications capturent le contenu et les opérations d’E/S en attente de la mémoire. Instantanés cohérents au niveau de l’application utilisent un enregistreur VSS (ou des scripts de pré/post pour Linux) pour garantir la cohérence des données de l’application avant une sauvegarde se produit. | Lorsque vous récupérez une machine virtuelle avec un instantané cohérent au niveau de l’application, la machine virtuelle démarre. Il n’y a aucune altération ni perte des données. Les applications démarrent dans un état cohérent. | Windows : Tous les enregistreurs VSS ont réussi<br/><br/> Linux : Les pré/post-scripts sont configurés et ont réussi
**Système de fichiers cohérent** | Sauvegardes cohérentes de système de fichiers assurer la cohérence en prenant un instantané de tous les fichiers en même temps.<br/><br/> | Lorsque vous récupérez une machine virtuelle avec un instantané cohérent du système de fichiers, la machine virtuelle démarre. Il n’y a aucune altération ni perte des données. Les applications ont besoin d’implémenter leur propre mécanisme de « correction » pour s’assurer que les données restaurées sont cohérentes. | Windows : Certains enregistreurs VSS ont échoué <br/><br/> Linux : Par défaut (si les scripts de pré/post ne sont pas configurés ou échec)
**Cohérence en cas d’incident** | Instantanés cohérents d’incident se produisent généralement si une machine virtuelle Azure s’arrête au moment de la sauvegarde. Seules les données déjà présentes sur le disque au moment de la sauvegarde sont capturées et sauvegardées.<br/><br/> Un point de récupération cohérent en cas d’incident ne garantit pas la cohérence des données pour le système d’exploitation ou l’application. | Bien qu’il n’y a aucune garantie, la machine virtuelle démarre généralement, puis démarre une vérification de disque pour résoudre les erreurs de corruption. N’importe quel données en mémoire ou les opérations d’écriture qui n’ont pas été transférées sur le disque avant l’incident sont perdues. Les applications implémentent leur propre vérification des données. Par exemple, une application de base de données peut utiliser son journal des transactions pour la vérification. Si le journal des transactions comporte des entrées qui ne figurent pas dans la base de données, le logiciel de base de données restaure les transactions jusqu'à ce que les données sont cohérentes. | La machine virtuelle est arrêtée

## <a name="backup-and-restore-considerations"></a>Considérations relatives à la sauvegarde et de restauration 

**Considération** | **Détails**
--- | ---
**Disque** | Sauvegarde des disques de machine virtuelle est parallèle. Par exemple, si une machine virtuelle comprend quatre disques, le service de sauvegarde tente de sauvegarder toutes les quatre disques en parallèle. La sauvegarde est incrémentielle (seules les données ayant changé sont sauvegardées).
**Planification** |  Pour réduire le trafic de sauvegarde, sauvegarder des machines virtuelles différentes à différents moments de la journée et assurez-vous que les heures ne se chevauchent pas. La sauvegarde simultanée de machines virtuelles provoque des embouteillages.
**Préparation des sauvegardes** | N’oubliez pas le temps nécessaire pour préparer la sauvegarde. Le temps de préparation inclut l’installation ou de la mise à jour de l’extension de sauvegarde et d’une capture instantanée en fonction de la planification de sauvegarde.
**Transfert de données** | Prendre en compte le temps nécessaire pour la sauvegarde Azure identifier les modifications incrémentielles à partir de la sauvegarde précédente.<br/><br/> Dans une sauvegarde incrémentielle, sauvegarde Azure détermine les modifications en calculant la somme de contrôle du bloc. Si un bloc est modifié, elle est marquée pour le transfert dans le coffre. Le service analyse des blocs identifiés pour tenter de réduire la quantité de données à transférer. Après avoir évalué tous les blocs modifiés, sauvegarde Azure transfère les modifications apportées dans le coffre.<br/><br/> Il peut y avoir un décalage entre la prise de l’instantané et sa copie dans le coffre.<br/><br/> Aux heures de pointe, il peut prendre jusqu'à huit heures pour les sauvegardes à traiter. La durée de la sauvegarde quotidienne d’une machine virtuelle est inférieure à 24 heures.
**Sauvegarde initiale** | Bien que la durée totale de sauvegarde pour les sauvegardes incrémentielles est inférieure à 24 heures, ce qui peut être pas le cas pour la première sauvegarde. Le temps nécessaire pour la sauvegarde initiale dépend de la taille des données et lors du traitement de la sauvegarde.
**File d’attente de restauration** | Azure Backup traite des restaurations à partir de plusieurs comptes de stockage en même temps, et il place les demandes de restauration dans une file d’attente.
**Copie pendant la restauration** | Durant le processus de restauration, les données sont copiées du coffre vers le compte de stockage.<br/><br/> Le temps de restauration total varie selon les opérations d’e/s par seconde (IOPS) et le débit du compte de stockage.<br/><br/> Pour réduire le temps de copie, sélectionnez un compte de stockage non chargé avec d’autres lectures et écritures d’application.

### <a name="backup-performance"></a>Performances de sauvegarde

Ces scénarios courants peuvent affecter le temps de sauvegarde total :

- **Ajout d’un disque à une machine virtuelle Azure protégée :** Si une machine virtuelle est en cours de sauvegarde incrémentielle et un nouveau disque est ajouté, l’heure de sauvegarde augmente. La durée totale de sauvegarde peut durer plus de 24 heures en raison de la réplication initiale du nouveau disque, ainsi que la réplication delta de disques existants.
- **Disques fragmentés :** Opérations de sauvegarde sont plus rapides lorsque les modifications de disque sont contiguës. Si les changements sont dispersés et fragmentés sur un disque, la sauvegarde est plus lente. 
- **Activité du disque :** Si protégé des disques qui sont en cours de sauvegarde incrémentielle ont un taux d’activité quotidienne de plus de 200 Go, la sauvegarde peut prendre beaucoup de temps (plus de huit heures). 
- **Versions de sauvegarde :** La dernière version de la sauvegarde (connu en tant que la version de la restauration instantanée) utilise un processus plus optimisé que la comparaison de la somme de contrôle pour identifier les modifications. Mais si vous êtes à l’aide de la restauration instantanée et que vous avez supprimé un instantané de sauvegarde, la sauvegarde bascule vers la comparaison de la somme de contrôle. Dans ce cas, l’opération de sauvegarde sera excéder 24 heures (ou échec).

## <a name="best-practices"></a>Bonnes pratiques

Lorsque vous configurez les sauvegardes de machines virtuelles, nous vous suggérons ces pratiques :

- Modifier les heures de planification par défaut qui sont définies dans une stratégie. Par exemple, si la durée par défaut dans la stratégie est 12:00 AM, incrémentez le minutage de plusieurs minutes afin que les ressources sont utilisées de manière optimale.
- Sauvegarde des machines virtuelles qui utilisent le stockage premium, nous vous recommandons d’exécuter la dernière version de sauvegarde Azure ([la restauration instantanée](backup-instant-restore-capability.md)). Si vous n’exécutez pas la version la plus récente, sauvegarde alloue environ 50 % de l’espace de stockage total. Le service de sauvegarde nécessite cet espace pour copier la capture instantanée pour le compte de stockage et pour son transfert vers le coffre.
- Si vous restaurez des machines virtuelles à partir d’un seul coffre, nous recommandons vivement d’utiliser différents [les comptes de stockage à usage général v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) pour vous assurer que le compte de stockage cible ne limité. Par exemple, chaque machine virtuelle doit avoir un autre compte de stockage. Par exemple, si 10 machines virtuelles sont restaurés, utilisez 10 différents comptes de stockage.
- Les restaurations à partir d’une couche de stockage à usage général v1 (snapshot) s’effectuera en quelques minutes, car l’instantané est dans le même compte de stockage. Restaurations à partir de la couche de stockage à usage général v2 (coffre) peuvent prendre des heures. Dans les cas où les données sont disponibles dans le stockage à usage général v1, nous vous recommandons d’utiliser le [la restauration instantanée](backup-instant-restore-capability.md) fonctionnalité des restaurations plus rapides. (Si les données doivent être restaurées à partir d’un coffre, puis il prendra plus de temps.)
- La limite du nombre de disques par compte de stockage est relatif à la façon dont les disques sont accédés par les applications qui s’exécutent sur une infrastructure en tant que service (IaaS) de machine virtuelle. En règle générale, si les disques de 5 à 10 ou plus sont présentes sur un seul compte de stockage, équilibrer la charge en déplaçant certains disques pour séparer les comptes de stockage.

## <a name="backup-costs"></a>Coûts de sauvegarde

Les machines virtuelles Azure sauvegardées avec Sauvegarde Azure sont soumises aux [tarifs de Sauvegarde Azure](https://azure.microsoft.com/pricing/details/backup/).

Facturation ne commence pas avant la fin de la première sauvegarde réussie. À partir de ce moment, la facturation du stockage et des machines virtuelles protégées commence. La facturation continue tant que les données de sauvegarde pour la machine virtuelle sont stockées dans un coffre. Si vous arrêtez la protection d’une machine virtuelle alors que les données de sauvegarde de cette machine sont présentes dans un coffre, la facturation continue.

La facturation pour une machine virtuelle spécifiée ne s’interrompt que si la protection est levée et que toutes les données de sauvegarde sont supprimées. Quand la protection s’arrête et qu’il n’y a aucun travail de sauvegarde actif, la taille de la dernière sauvegarde de machine virtuelle réussie devient la taille de l’instance protégée utilisée pour la facture mensuelle.

Le calcul de taille d’instance protégée est basé sur le *réel* taille de la machine virtuelle. Taille de la machine virtuelle est la somme de toutes les données dans la machine virtuelle, à l’exclusion du stockage temporaire. Tarification est basée sur les données réelles qui a stocké sur les disques de données, pas sur la valeur maximale prise en charge de taille pour chaque disque de données qui est attaché à la machine virtuelle.

De même, la facture de stockage de sauvegarde est basée sur la quantité de données stockées dans la sauvegarde Azure, qui est la somme des données réelles dans chaque point de récupération.

Par exemple, prendre une machine virtuelle A2 Standard-en taille réelle qui a deux disques de données supplémentaires avec une taille maximale de 4 To. Le tableau suivant présente les données réelles stockées sur chacun de ces disques :

**Disque** | **Taille maximale** | **Données réelles présentes**
--- | --- | ---
Disque de système d’exploitation | 4095 Go | 17 Go
Disque local/temporaire | 135 Go | 5 Go (non inclus pour la sauvegarde)
Disque de données 1 | 4095 Go | 30 Go
Disque de données 2 | 4095 Go | 0 Go

La taille réelle de la machine virtuelle est dans ce cas 17 Go + 30 Go + 0 Go= 47 Go. Cette taille d’instance protégée (47 Go) devient la base de la facture mensuelle. Que la quantité de données dans la machine virtuelle augmente, la taille d’instance protégée utilisée pour la facturation augmente pour faire correspondre.

## <a name="next-steps"></a>Étapes suivantes

Maintenant, [préparer pour la sauvegarde de machine virtuelle Azure](backup-azure-arm-vms-prepare.md).
