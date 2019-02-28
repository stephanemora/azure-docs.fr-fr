---
title: À propos de la sauvegarde de machine virtuelle Azure
description: Découvrez la sauvegarde de machine virtuelle Azure et quelques bonnes pratiques.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: c38c457bbf428d7252cf57168685201a2ca227ba
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446798"
---
# <a name="about-azure-vm-backup"></a>À propos de la sauvegarde de machine virtuelle Azure

Cet article décrit comment le [service Sauvegarde Azure](backup-introduction-to-azure-backup.md) sauvegarde les machines virtuelles Azure.

## <a name="backup-process"></a>Processus de sauvegarde

Voici comment Sauvegarde Azure effectue une sauvegarde de machines virtuelles Azure.

1. Pour les machines virtuelles Azure sélectionnées pour la sauvegarde, le service Sauvegarde Azure lance un travail de sauvegarde conformément à la planification de sauvegarde que vous spécifiez.
2. Lors de la première sauvegarde, une extension de sauvegarde est installée sur la machine virtuelle si elle est en cours d’exécution.
    - Pour les machines virtuelles Windows, l’extension _VMSnapshot_ est installée.
    - Pour les machines virtuelles Linux, l’extension Linux _VMSnapshotLinux_ est installée.
3. Pour les machines virtuelles Windows en cours d’exécution, Sauvegarde Azure coopère avec VSS afin de prendre un instantané cohérent au niveau de l’application de la machine virtuelle.
    - Par défaut, Sauvegarde Azure effectue des sauvegardes VSS complètes.
    - Si la sauvegarde ne peut pas prendre d’instantané cohérent au niveau application, il prend un instantané cohérent au niveau fichier du stockage sous-jacent (dans la mesure où aucune écriture d’application n’a lieu quand la machine virtuelle est arrêtée).
4. Pour les machines virtuelles Linux, Sauvegarde Azure effectue une sauvegarde cohérente au niveau fichier. Pour les instantanés cohérents au niveau de l’application, vous devez personnaliser manuellement le pré-script et le post-script.
5. Une fois l’instantané pris, les données sont transférées au coffre. 
    - La sauvegarde est optimisée grâce à la sauvegarde de chaque disque de machine virtuelle en parallèle.
    - Pour chaque disque en cours de sauvegarde, Sauvegarde Azure lit les blocs sur le disque, puis identifie et transfère uniquement les blocs de données ayant changé depuis la sauvegarde précédente (données delta).
    - Une fois l’instantané pris, les données sont transférées vers le coffre.
    - Les données d’instantanés peuvent ne pas être immédiatement copiées dans le coffre. Aux heures de pointe, cela peut prendre quelques heures. La durée de sauvegarde totale d’une machine virtuelle sera inférieure à 24 heures pour des stratégies de sauvegarde quotidienne.

6. Une fois le transfert de données terminé, l’instantané est supprimé et un point de récupération est créé.

![Architecture de la sauvegarde des machines virtuelles Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encrypting-azure-vm-backups"></a>Chiffrement des sauvegardes de machine virtuelle Azure

Quand vous sauvegardez des machines virtuelles Azure avec Sauvegarde Azure, ces machines sont chiffrées au repos avec SSE (Storage Service Encryption). Sauvegarde Azure peut également sauvegarder des machines virtuelles Azure chiffrées avec ADE (Azure Disk Encryption).


**Chiffrement** | **Détails** | **Support**
--- | --- | ---
**ADE** | ADE chiffre les disques de système d’exploitation et de données des machines virtuelles Azure.<br/><br/> ADE s’intègre soit aux clés de chiffrement BitLocker (BEK) protégées dans un coffre de clés en tant que secrets, soit aux clés de chiffrement principales (KEK) Azure Key Vault. | Sauvegarde Azure prend en charge la sauvegarde des machines virtuelles Azure managées et non managées, chiffrées avec une BEK uniquement ou avec une BEK et une KEK.<br/><br/> La BEK et la KEK sont sauvegardées et chiffrées.<br/><br/> Dans la mesure où la KEK et la BEK sont sauvegardées, les utilisateurs disposant d’autorisations peuvent si nécessaire restaurer les clés et les secrets dans le coffre de clés et récupérer la machine virtuelle chiffrée.<br/><br/> Ni Azure ni les utilisateurs non autorisés ne peuvent lire les clés et les secrets chiffrés.
**SSE** | Avec SSE, le stockage Azure chiffre automatiquement les données au repos avant de les stocker, puis les déchiffre avant de les récupérer. | Sauvegarde Azure utilise SSE pour chiffrer au repos les machines virtuelles Azure.

- La sauvegarde des machines virtuelles chiffrées avec une clé de chiffrement BitLocker (BEK) uniquement ou avec une BEK et une clé de chiffrement principale (KEK) est prise en charge, à la fois pour les machines virtuelles Azure managées et non managées.
- Les données de la BEK (secrets) et de la KEK (clés) sont sauvegardées et chiffrées. Ces données ne peuvent être lues et utilisées que si elles sont restaurées dans le coffre de clés par des utilisateurs autorisés. Ni Azure ni les utilisateurs non autorisés ne peuvent lire ou utiliser les clés et les secrets chiffrés.
- Étant donné que la BEK est également sauvegardée, s’ils la perdent, les utilisateurs autorisés peuvent la restaurer dans le coffre de clés pour récupérer la machine virtuelle chiffrée. 
- Seuls les utilisateurs disposant du niveau d’autorisations approprié peuvent sauvegarder et restaurer des machines virtuelles chiffrées, ainsi que des clés et des secrets.



## <a name="taking-snapshots"></a>Prise d’instantanés

Sauvegarde Azure prend des instantanés conformément à la planification de sauvegarde. 

- **Machines virtuelles Windows** : pour les machines virtuelles Windows, le service Sauvegarde se coordonne avec le service VSS pour prendre un instantané cohérent au niveau application des disques de la machine virtuelle.
    - Par défaut, Sauvegarde Azure effectue des sauvegardes VSS complètes. [Plus d’informations](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)
    - Pour changer le paramètre afin que Sauvegarde Azure effectue des sauvegardes de copie VSS, définissez la clé de Registre suivante à partir d’une invite de commandes : **REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent" /v USEVSSCOPYBACKUP /t REG_SZ /d TRUE /f**.
- **Machines virtuelles Linux** : pour prendre des instantanés cohérents au niveau application de machines virtuelles Linux, utilisez le framework pré-script et post-script de Linux pour écrire vos propres scripts afin de garantir la cohérence.
    -  Sauvegarde Azure appelle uniquement les pré-scripts et post-scripts que vous avez écrits.
    - Si les pré-scripts et post-scripts s’exécutent correctement, le service Sauvegarde Azure marque le point de récupération comme étant cohérent dans les applications. Toutefois, vous êtes responsable de la cohérence des applications lors de l’utilisation de scripts personnalisés.
    - [Découvrez-en plus](backup-azure-linux-app-consistent.md) sur la configuration des scripts.


### <a name="snapshot-consistency"></a>Cohérence des instantanés

Le tableau suivant explique les différents types de cohérence.

**Instantané** | **Détails** | **Récupération** | **Considération**
--- | --- | --- | ---
**Cohérence des applications** | Les sauvegardes cohérentes dans les applications capturent le contenu et les opérations d’E/S en attente de la mémoire. Les instantanés cohérents dans les applications utilisent l’enregistreur VSS (ou un pré/post-script pour Linux) qui garantissent la cohérence des données d’application avant une sauvegarde. | Lors de la récupération avec un instantané cohérent dans les applications, la machine virtuelle démarre. Il n’y a aucune altération ni perte des données. Les applications démarrent dans un état cohérent. | Windows : Tous les enregistreurs VSS ont réussi<br/><br/> Linux : Les pré/post-scripts sont configurés et ont réussi
**Cohérence du système de fichiers** | Les sauvegardes cohérentes dans le système de fichiers fournissent des sauvegardes cohérentes des fichiers de disque en prenant un instantané de tous les fichiers en même temps.<br/><br/> | Lors de la récupération avec un instantané cohérent dans les fichiers, la machine virtuelle démarre. Il n’y a aucune altération ni perte des données. Les applications ont besoin d’implémenter leur propre mécanisme de « correction » pour s’assurer que les données restaurées sont cohérentes. | Windows : Certains enregistreurs VSS ont échoué <br/><br/> Linux : Par défaut (si les pré/post-scripts ne sont pas configurés ou ont échoué)
**Cohérence en cas d’incident** | Une cohérence en cas d’incident se produit souvent quand une machine virtuelle Azure s’arrête au moment de la sauvegarde.  Seules les données déjà présentes sur le disque au moment de la sauvegarde sont capturées et sauvegardées.<br/><br/> Un point de récupération cohérent en cas d’incident ne garantit pas la cohérence des données pour le système d’exploitation ou l’application. | Il n’y a aucune garantie, mais généralement la machine virtuelle démarre, puis effectue une vérification des disques afin de corriger les erreurs dues à des altérations. Toutes les données en mémoire ou écritures qui n’ont pas été transférées vers un disque sont perdues. Les applications implémentent leur propre vérification des données. Par exemple, pour une application de base de données, si un journal des transactions comporte des entrées qui ne figurent pas dans la base de données, le logiciel de base de données tourne jusqu’à ce que les données soient cohérentes. | La machine virtuelle est arrêtée


## <a name="restore-considerations"></a>Considérations liées à la restauration 



**Considération** | **Détails**
--- | ---
**Disque** | Les disques d’une machine virtuelle sont sauvegardés en parallèle. Par exemple, si une machine virtuelle comprend quatre disques, le service tente de sauvegarder les quatre disques simultanément. La sauvegarde est incrémentielle (seules les données ayant changé sont sauvegardées).
**Planification** |  Pour réduire le trafic de sauvegarde, sauvegardez chaque machine virtuelle à un moment différent de la journée, sans chevauchement. La sauvegarde simultanée de machines virtuelles provoque des embouteillages.
**Préparation des sauvegardes** | Tenez compte du temps de préparation des sauvegardes. Celui-ci comprend l’installation ou la mise à jour de l’extension de sauvegarde ainsi que le déclenchement d’un instantané conformément à la planification de sauvegarde.
**Transfert de données** | Temps nécessaire au service de sauvegarde pour calculer les changements incrémentiels par rapport à la sauvegarde précédente.<br/><br/> Pour déterminer les changements dans une sauvegarde incrémentielle, le service calcule la somme de contrôle du bloc. Si un bloc est modifié, il est identifié en vue d’être envoyé dans le coffre. Le service explore au niveau du détail les blocs identifiés pour tenter de réduire les données à transférer. Au terme de l’évaluation de tous blocs ayant changé, les changements sont transférés dans le coffre.<br/><br/> Il peut y avoir un décalage entre la prise de l’instantané et sa copie dans le coffre.<br/><br/> Aux heures de pointe, le traitement des sauvegardes peut prendre jusqu’à huit heures. La durée de la sauvegarde quotidienne d’une machine virtuelle est inférieure à 24 heures.
**Sauvegarde initiale** | Bien qu’une durée totale de moins de 24 heures soit possible pour les sauvegardes incrémentielles, cela peut ne pas être le cas pour la première sauvegarde. Le temps nécessaire dépendra de la taille des données et du moment auquel est réalisée la sauvegarde.
**File d’attente de restauration** | Sauvegarde Azure traite les travaux de restauration de plusieurs comptes de stockage en même temps, et les demandes de restauration sont mises en file d’attente.
**Copie pendant la restauration** | Durant le processus de restauration, les données sont copiées du coffre vers le compte de stockage.<br/><br/> La durée de la restauration dépend des opérations d’E/S par seconde (IOPS) et du débit du compte de stockage.<br/><br/> Pour réduire le temps de copie, sélectionnez un compte de stockage non chargé avec d’autres lectures et écritures d’application.


### <a name="backup-performance"></a>Performances de sauvegarde

Ces scénarios courants peuvent affecter la durée de sauvegarde :

- Ajout d’un nouveau disque à une machine virtuelle Azure protégée : si une machine virtuelle fait l’objet d’une sauvegarde incrémentielle et qu’un nouveau disque est ajouté, la sauvegarde peut durer plus de 24 heures en raison de la réplication initiale du nouveau disque en plus de la réplication delta des disques existants.
- Disques fragmentés : les opérations de sauvegarde sont plus rapides quand les changements de disque sont colocalisés. Si les changements sont dispersés et fragmentés sur un disque, la sauvegarde est plus lente. 
- Activité du disque : si l’activité quotidienne de disques protégés faisant l’objet d’une sauvegarde incrémentielle dépasse les 200 Go, la sauvegarde peut prendre du temps (plus de huit heures). 
- Versions de Sauvegarde : la dernière version de Sauvegarde, appelée Restauration instantanée, utilise un processus plus performant que la comparaison de la somme de contrôle pour évaluer les changements. Si vous utilisez la dernière version et que vous avez supprimé un instantané de sauvegarde, la sauvegarde utilise alors la comparaison de la somme de contrôle et prend plus de 24 heures (ou échoue).

## <a name="best-practices"></a>Bonnes pratiques 
Nous vous suggérons de suivre ces pratiques pour configurer des sauvegardes de machines virtuelles :

- Envisagez de modifier l’heure de planification par défaut définie dans une stratégie. Par exemple, si l’heure par défaut d’une stratégie est minuit, songez à l’incrémenter de quelques minutes pour optimiser l’utilisation des ressources.
- Pour une sauvegarde de machine virtuelle Premium sur un point de récupération non instantané, la fonctionnalité alloue environ 50 % de l’espace total du compte de stockage. Le service de sauvegarde requiert cet espace pour copier l’instantané vers le même compte de stockage et le transférer vers le coffre.
- Pour restaurer des machines virtuelles à partir d’un seul coffre, il est vivement recommandé d’utiliser des [comptes de stockage v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) différents pour éviter que le compte de stockage cible ne soit limité. Par exemple, chaque machine virtuelle doit avoir compte de stockage distincts(si 10 machines virtuelles sont restaurées, utilisez 10 comptes de stockage différents).
- Les restaurations à partir de la couche de stockage de niveau 1 (instantané) sont effectuées en quelques minutes (dans la mesure où il s’agit du même compte de stockage), par opposition à la couche de stockage de niveau 2 (coffre) qui peut prendre des heures. Nous vous recommandons d’utiliser la fonctionnalité [Restauration instantanée](backup-instant-restore-capability.md) pour accélérer les restaurations quand des données sont disponibles au niveau 1 (sachant que, si les données doit être restaurées à partir du coffre, cela prendra de temps).
- La limite du nombre de disques par compte de stockage dépend de la lourdeur des disques auxquels accèdent les applications s’exécutant sur la machine virtuelle IaaS. Vérifiez si plusieurs disques sont hébergés sur un seul compte de stockage. En règle générale, si plus de 5 disques sont présents sur un compte de stockage, équilibrez la charge en déplaçant certains disques vers des comptes de stockage distincts.

## <a name="backup-costs"></a>Coûts de sauvegarde

Les machines virtuelles Azure sauvegardées avec Sauvegarde Azure sont soumises aux [tarifs de Sauvegarde Azure](https://azure.microsoft.com/pricing/details/backup/).

- La facturation ne commence pas avant la fin de la première sauvegarde réussie. À partir de ce moment, la facturation du stockage et des machines virtuelles protégées commence.
- La facturation continue tant que des données de sauvegarde sont stockées dans un coffre pour la machine virtuelle. Si vous arrêtez la protection d’une machine virtuelle alors que les données de sauvegarde de cette machine sont présentes dans un coffre, la facturation continue.
- La facturation pour une machine virtuelle spécifiée ne s’interrompt que si la protection est levée et que toutes les données de sauvegarde sont supprimées.
- Quand la protection s’arrête et qu’il n’y a aucun travail de sauvegarde actif, la taille de la dernière sauvegarde de machine virtuelle réussie devient la taille de l’instance protégée utilisée pour la facture mensuelle.
- Le calcul des instances protégées est basé sur la taille *réelle* de la machine virtuelle, qui est la somme de toutes les données de la machine virtuelle, à l’exception du stockage temporaire.
- La tarification est basée sur les données réelles stockées sur le disque de données.
- La tarification pour la sauvegarde des machines virtuelles n’est pas basée sur la taille maximale prise en charge pour chaque disque de données attaché à la machine virtuelle.
- De même, la facture du stockage de sauvegarde est basée sur la quantité de données stockées sur le service Sauvegarde Azure, qui est la somme des données réelles de chaque point de récupération.

Prenons l’exemple d’une machine virtuelle de taille standard A2 avec deux disques de données supplémentaires, d’une taille maximale de 4 To chacun. Le tableau suivant indique les données réelles stockées sur chacun de ces disques :

**Disque** | **Taille maximale** | **Données réelles présentes**
--- | --- | ---
Disque de système d’exploitation | 4095 Go | 17 Go 
Disque local/temporaire | 135 Go | 5 Go (non inclus pour la sauvegarde) 
Disque de données 1 | 4095 Go | 30 Go 
Disque de données 2 | 4095 Go | 0 Go 

- La taille réelle de la machine virtuelle est dans ce cas 17 Go + 30 Go + 0 Go= 47 Go.
- Cette taille d’instance protégée (47 Go) devient la base de la facture mensuelle.
- La taille de l’instance protégée utilisée pour la facturation augmente proportionnellement à la quantité de données dans la machine virtuelle.


## <a name="next-steps"></a>Étapes suivantes

À présent, [préparez](backup-azure-arm-vms-prepare.md) la sauvegarde de la machine virtuelle Azure.
