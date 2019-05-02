---
title: Optimiser les performances sur les machines virtuelles de série Lsv2 Azure - stockage | Microsoft Docs
description: Découvrez comment optimiser les performances de votre solution sur les machines virtuelles de série Lsv2.
services: virtual-machines-linux
author: laurenhughes
manager: jeconnoc
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: 7be86c8934b8766217f9fca432327d254204f0c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60738942"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Optimiser les performances sur les machines virtuelles de série Lsv2

Machines virtuelles de série Lsv2 prend en charge une variété de charges de travail nécessitant des e/s élevé et le débit sur le stockage local sur un large éventail d’applications et de secteurs.  La série Lsv2 est idéale pour les Big Data, SQL, NoSQL bases de données, l’entreposage de données et grandes bases de données transactionnelles, y compris Cassandra, MongoDB, Cloudera et Redis.

La conception de la série Lsv2 Machines () optimise le processeur AMD EPYC™ 7551 afin d’améliorer les performances entre le processeur, mémoire, les périphériques NVMe et les machines virtuelles. Outre optimiser les performances de matériel, les machines virtuelles de série Lsv2 sont conçus pour fonctionner avec les besoins des systèmes d’exploitation Linux pour de meilleures performances avec le matériel et le logiciel.

Les logiciels et matériels de paramétrage a entraîné la version optimisée de [de Canonical Ubuntu 18.04 et 16.04](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer?tab=Overview)publiée en début décembre 2018 à la place de marché Azure, qui prend en charge des performances maximales sur les périphériques NVMe dans Machines virtuelles de série Lsv2.

Cet article fournit des conseils et des suggestions pour vous assurer de vos applications et charges de travail atteindre des performances maximales conçue dans les machines virtuelles. Les informations sur cette page seront continuellement mises à jour comme images Lsv2 optimisé plus sont ajoutés à la place de marché Azure.

## <a name="amd-eypc-chipset-architecture"></a>Architecture de circuit microprogrammé AMD EYPC™

Machines virtuelles de série Lsv2 utilisent des processeurs de serveur AMD EYPC™ selon la microarchitecture Zen. AMD développé infini Fabric (le cas) pour EYPC™ comme évolutives d’interconnexion pour son modèle NUMA qui pourrait être utilisée pour les communications sur matrice et sur package packages multi. Comparées aux QPI (chemin d’accès rapide d’interconnexion) et de paiement (interconnexion Ultra-Path) utilisé sur les processeurs Intel modernes monolithique lancers, architecture de puce petite NUMA de plusieurs d’AMD peut afficher les deux avantages de performances, ainsi que des défis. L’impact réel de contraintes de bande passante et latence de mémoire peut varier selon le type de charges de travail en cours d’exécution.

## <a name="tips-to-maximize-performance"></a>Conseils pour optimiser les performances

* Si vous téléchargez un Linux GuestOS personnalisé pour votre charge de travail, notez que mise en réseau accélérée est **OFF** par défaut. Si vous envisagez d’activer la mise en réseau accélérée, l’activer au moment de la création de machines virtuelles pour de meilleures performances.

* Le matériel qui alimente les machines virtuelles de série Lsv2 utilise des périphériques NVMe avec huit paires de file d’attente d’e/s (QP) s. Chaque file d’attente du périphérique d’e/s NVMe est en fait une paire : une file d’attente de soumission et une file d’attente d’achèvement. Le pilote NVMe est configuré pour optimiser l’utilisation de ces huit requêtes par seconde d’e/s en distribuant I / O dans une répétition alternée planifier. Pour obtenir des performances maximales, exécutez huit travaux par appareil pour faire correspondre.

* Évitez de mélanger NVMe des commandes d’administration (par exemple, SMART NVMe requête info, etc.) avec les commandes NVMe d’e/s au cours des charges de travail actives. Périphériques Lsv2 NVMe sont soutenues par la technologie Hyper-V NVMe Direct, qui passe en « mode lent » chaque fois que les commandes d’administration NVMe sont en attente. Lsv2 utilisateurs pourraient observer une spectaculaire des performances drop dans les performances d’e/s NVMe si cela se produit.

* Les utilisateurs de Lsv2 ne doivent pas compter informations sur les appareils NUMA (toutes les 0) signalées à partir de la machine virtuelle pour les lecteurs de données pour déterminer l’affinité NUMA pour leurs applications. La méthode recommandée pour de meilleures performances consiste à répartir des charges de travail sur les processeurs si possible.

* La profondeur de file d’attente pris en charge maximale par paire de file d’attente d’e/s pour les périphériques NVMe de machine virtuelle Lsv2 est 1024 (Visual Studio. Amazon i3 QD 32 limite). Les utilisateurs Lsv2 doivent limiter leurs charges de travail de benchmarking (synthétiques) à la profondeur de file d’attente 1024 ou plus basse à ne pas déclencher de conditions complète de file d’attente, ce qui peuvent réduire les performances.

## <a name="utilizing-local-nvme-storage"></a>Utilisation du stockage NVMe local

Stockage local sur le disque de NVMe to 1,92 sur toutes les machines virtuelles de Lsv2 est éphémère. Les données sur le disque local de NVMe persistera lors du redémarrage standard réussi de la machine virtuelle. Les données ne seront pas conservées sur le NVMe si la machine virtuelle est redéployée, désallouée ou supprimée. Données ne seront pas conservées si un autre problème provoque la machine virtuelle ou du matériel, qu'il est en cours d’exécution, pour devenir défectueux. Lorsque cela se produit, toutes les données sur l’ancien hôte sont en toute sécurité effacées.

Il y aura également cas lorsque la machine virtuelle doit être déplacé vers un autre ordinateur hôte, par exemple, lors d’une opération de maintenance planifiée. Opérations de maintenance planifiée et certaines défaillances matérielles peuvent être anticipées avec [événements planifiés](scheduled-events.md). Événements planifiés doivent être utilisés pour rester informé sur toute maintenance prédite et les opérations de récupération.

Dans le cas où un événement de maintenance planifiée nécessite la machine virtuelle pour être recréé sur un hôte avec des disques locaux vides, les données devez doit être resynchronisée (là encore, avec toutes les données sur l’ancien hôte effacé en toute sécurité). Cela se produit, car les machines virtuelles de série Lsv2 ne pas prennent en charge la migration dynamique sur le disque local de NVMe.

Il existe deux modes pour la maintenance planifiée.

### <a name="standard-vm-customer-controlled-maintenance"></a>Maintenance de contrôlés par le client standard de machine virtuelle

- La machine virtuelle est déplacée vers un hôte mis à jour pendant une fenêtre de 30 jours.
- Données de stockage local Lsv2 peuvent être perdues, donc recommandées de sauvegarder les données avant l’événement.

### <a name="automatic-maintenance"></a>Maintenance automatique

- Se produit si le client n’exécute pas de maintenance contrôlés par le client, ou en cas de procédures d’urgence comme un événement de zéro jour de sécurité.
- Destiné à conserver les données client, mais il existe un petit risque d’un blocage de la machine virtuelle ou un redémarrage.
- Données de stockage local Lsv2 peuvent être perdues, donc recommandées de sauvegarder les données avant l’événement.

Pour tous les événements à venir de service, utilisez le processus de maintenance contrôlée pour sélectionner une heure la plus pratique à vous pour la mise à jour. Avant l’événement, vous pouvez sauvegarder vos données dans le stockage premium. Une fois l’événement de maintenance est terminée, vous pouvez revenir à vos données sur le stockage de NVMe local des machines virtuelles Lsv2 actualisé.

Les scénarios qui conservent les données sur les disques NVMe locaux sont les suivantes :

- La machine virtuelle est en cours d’exécution et sain.
- La machine virtuelle est redémarrée en place (par vous ou Azure).
- La machine virtuelle est suspendue (arrêté sans désallocation).
- La majorité de la maintenance planifiée, les opérations de traitement.

Les scénarios qui effacement en toute sécurité des données pour protéger le client :

- La machine virtuelle est redéployée, arrêtée (désallouée,) ou de suppression (par vous).
- La machine virtuelle devient non intègre et qu’il a au service sont réparées vers un autre nœud en raison d’un problème matériel.
- Un petit nombre de la maintenance des opérations de maintenance planifiée qui nécessite la machine virtuelle pour être réaffectée à un autre ordinateur hôte pour la maintenance.

Pour en savoir plus sur les options de sauvegarde de données dans le stockage local, consultez [sauvegarde et récupération d’urgence pour les disques Azure IaaS](backup-and-disaster-recovery-for-azure-iaas-disks.md).

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

* **Démarrage d’un déploiement de machines virtuelles de série Lsv2 ?**  
   Beaucoup comme toute autre machine virtuelle, utilisez le [Portal](quick-create-portal.md), [Azure CLI](quick-create-cli.md), ou [PowerShell](quick-create-powershell.md) pour créer une machine virtuelle.

* **Une défaillance de disque unique NVMe entraîne toutes les machines virtuelles sur l’ordinateur hôte échoue ?**  
   Si une défaillance de disque est détectée sur le nœud de matériel, le matériel est en état d’échec. Lorsque cela se produit, toutes les machines virtuelles sur le nœud sont automatiquement désallouées et déplacés vers un nœud sain. Pour les machines virtuelles de série Lsv2, cela signifie que les données du client sur le nœud défaillant sont effacées de façon sécurisée et doivent être recréée par le client sur le nouveau nœud. Comme indiqué, avant la migration dynamique est disponible sur Lsv2, les données sur le nœud défaillant proactive va avec les machines virtuelles comme ils sont transférés vers un autre nœud.

* **Je dois apporter des ajustements à rq_affinity pour les performances ?**  
   Le paramètre rq_affinity est un ajustement mineur lorsque vous utilisez les opérations d’entrée/sortie maximales absolues par seconde (IOPS). Une fois que tout le reste fonctionne bien, puis essayez de définir rq_affinity à 0 pour voir si cela a son importance.

* **Je dois modifier les paramètres de blk_mq ?**  
   RHEL/CentOS 7.x utilise automatiquement blk-mq pour les périphériques NVMe. Aucune des modifications de configuration ou les paramètres ne sont nécessaires. Le paramètre scsi_mod.use_blk_mq est uniquement pour SCSI et a été utilisé pendant la version préliminaire Lsv2, car les périphériques NVMe étaient visibles dans les machines virtuelles invitées en tant que périphériques SCSI. Actuellement, les périphériques NVMe sont visibles en tant que périphériques NVMe, donc le paramètre de blk-mq SCSI est sans importance.

* **Je dois modifier « fio » ?**  
   Pour obtenir le nombre maximal d’IOPS avec un outil tel que « fio » dans les tailles L64v2 et de la machine virtuelle L80v2 des mesures de performance, la valeur est « rq_affinity » 0 sur chaque appareil NVMe.  Par exemple, cette ligne de commande définit « rq_affinity » à zéro pour tous les périphériques NVMe 10 dans une machine virtuelle L80v2 :

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   Notez également que les meilleures performances sont obtenue lors de l’e/s est effectuée directement à chacun des périphériques NVMe brutes avec aucun ne partitionnement, aucun système de fichiers, pas de RAID 0 config, etc. Avant de commencer une session de test, vérifiez la configuration est dans un état connu/nettoyer de nouveau en exécutant `blkdiscard` sur chacun des périphériques NVMe.
   
## <a name="next-steps"></a>Étapes suivantes

* Consultez les spécifications pour toutes les [optimisés pour les performances de stockage des machines virtuelles](sizes-storage.md) sur Azure
