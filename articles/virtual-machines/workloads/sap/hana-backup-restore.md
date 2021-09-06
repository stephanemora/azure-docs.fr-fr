---
title: Sauvegarde et restauration HANA sur SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Découvrez comment sauvegarder et restaurer SAP HANA sur de grandes instances HANA.
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: gwallace
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 7/02/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7b60ba5c169f660cd01f95f53fbc292a768f1b5e
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113358936"
---
# <a name="backup-and-restore-of-sap-hana-on-hana-large-instances"></a>Sauvegarde et restauration de SAP HANA sur de grandes instances HANA

>[!IMPORTANT]
>Cet article ne remplace pas la documentation d’administration de SAP HANA ni les notes SAP. Nous supposons que vous maîtrisez les opérations et l’administration SAP HANA, notamment dans les domaines de la sauvegarde, de la restauration, de la haute disponibilité et de la reprise d’activité après sinistre. Dans cet article, des captures d’écran de SAP HANA Studio sont affichées. Le contenu, la structure et la nature des écrans des outils d’administration de SAP, ainsi que les outils eux-mêmes, peuvent changer selon la version de SAP HANA.

Dans cet article, nous allons passer en revue les étapes de sauvegarde et de restauration de SAP HANA sur de grandes instances HANA, également appelées infrastructure BareMetal. 

Certains processus décrits dans cet article sont simplifiés. Ils ne sont pas conçus comme des procédures détaillées à inclure dans des manuels d’utilisation. Pour créer des manuels d’utilisation pour vos configurations, exécutez et testez vos processus avec vos versions et mises en production HANA spécifiques. Vous pouvez ensuite documenter les processus pour vos configurations.

L’un des aspects les plus importants de l’utilisation des bases de données concerne la protection de celles-ci contre les événements graves. De tels événements peuvent avoir une multitude de causes, depuis les catastrophes naturelles jusqu’aux simples erreurs des utilisateurs. La sauvegarde d’une base de données, avec la possibilité de la restaurer à n’importe quel point dans le temps, comme lorsque quelqu’un supprimait des données critiques, offre une protection fondamentale. Vous pouvez restaurer votre base de données dans un état aussi proche que possible de celui dans lequel elle était avant l’interruption.

Deux types de sauvegardes doivent être effectués pour permettre la restauration :

- Sauvegardes de base de données : sauvegardes complètes, incrémentielles ou différentielles
- Sauvegardes des journaux de transactions

Vous pouvez effectuer des sauvegardes de base de données complètes au niveau application ou des sauvegardes à l’aide de captures instantanées de stockage. Les captures instantanées de stockage ne remplacent pas les sauvegardes de fichier journal. Celles-ci restent importantes pour restaurer la base de données à un certain point dans le temps ou pour supprimer des journaux d’activité les transactions déjà validées. Les captures instantanées de stockage peuvent accélérer la récupération en fournissant rapidement une image de restauration par progression de la base de données. 

SAP HANA sur Azure (grandes instances) offre deux options de sauvegarde et de restauration :

- Vous pouvez utiliser un outil de protection des données tiers pour créer des sauvegardes. Cet outil doit être capable de créer des captures instantanées cohérentes avec l’application ou d’utiliser l’interface de sauvegarde pour diffuser plusieurs sessions sur un emplacement de sauvegarde approprié. Plusieurs outils pris en charge sont disponibles. Le choix de l’outil doit être discuté et déterminé avec l’équipe de projet pour répondre aux exigences de fenêtres de sauvegarde des clients. Il est important également de tester la procédure de sauvegarde et de restauration pendant la phase du projet.
- Vous pouvez utiliser des sauvegardes de captures instantanées de stockage avec un utilitaire fourni par Microsoft, comme décrit dans le chapitre suivant

>[!NOTE]
>Avant HANA 2.0 SPS4, il n’était pas possible de prendre des captures instantanées de base de données de conteneurs de bases de données mutualisée (plusieurs locataires).
Avec SPS4 et ultérieurs, SAP prend entièrement en charge cette fonctionnalité de capture instantanée.



## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Utiliser des captures instantanées de stockage de SAP HANA sur Azure (grandes instances)

L’infrastructure de stockage qui sous-tend SAP HANA sur Azure (grandes instances) prend en charge les captures instantanées de volumes de stockage. La prise en charge de la sauvegarde et de la restauration d’un volume est régie par les règles suivantes :

- Au lieu d’exécuter des sauvegardes de base de données complètes, le système procède à de fréquentes captures instantanées des volumes de stockage.
- Avant qu’une capture instantanée de stockage ne soit déclenchée sur un ou des volumes /hana/data, l’outil de capture instantanée (azacsnap) démarre une capture instantanée SAP HANA. Cette capture instantanée SAP HANA est le point de cohérence d’éventuelles restaurations de journaux après récupération de la capture instantanée de stockage. 
- Pour que la capture instantanée HANA réussisse, vous avez besoin d’une instance HANA active. Dans un scénario avec Réplication du système HANA (HSR), une capture instantanée de stockage n’est pas prise en charge sur un nœud secondaire actif où une capture instantanée HANA ne peut pas être effectuée.
- Une fois la capture instantanée de stockage exécutée, la capture instantanée SAP HANA est supprimée
- Des captures instantanées d’autres volumes comme /hana/shared (y compris /usr/sap) peuvent être effectuées à tout moment sans interaction avec la base de données

Les sauvegardes de fichier journal sont effectuées fréquemment et stockées dans le volume /hana/logbackups ou dans Azure. Vous pouvez déclencher une capture instantanée séparément pour le volume /hana/logbackups contenant les sauvegardes de fichier journal. Dans ce cas, vous n’avez pas besoin d’exécuter une capture instantanée de données HANA. Étant donné que tous les fichiers dans /hana/logbackup sont cohérents, du fait qu’ils sont « hors connexion », vous pouvez également les sauvegarder à tout moment dans un autre emplacement de sauvegarde pour les archiver.
Si vous devez restaurer une base de données à un point dans le temps, en cas de panne de production, l’outil azacsnap peut cloner n’importe quelle capture instantanée de données sur un nouveau volume pour récupérer la base de données (restauration par défaut) ou restaurer une capture instantanée sur le même volume de données que celui où se trouve la base de données

>[!NOTE]
> Si vous restaurez une capture instantanée plus ancienne (snaprevert) sur le volume de données d’origine, toutes les captures instantanées plus récentes créées seront supprimées. Le système de stockage le fait car les points de données pour les captures instantanées plus récents créées ne sont pas valides. Commencez toujours par restaurer la dernière capture instantanée ou, de préférence, clonez la capture instantanée sur un nouveau volume. Le processus de clonage ne supprime rien.


## <a name="storage-snapshot-considerations"></a>Considérations relatives aux captures instantanées de stockage

>[!NOTE]
>Les captures instantanées de stockage consomment un espace de stockage qui est alloué aux unités de grande instance HANA. Prendre en compte les aspects suivants concernant la planification des captures instantanées de stockage et le nombre de captures instantanées de stockage à conserver. 

Les mécanismes spécifiques des captures instantanées de stockage de SAP HANA sur Azure (grandes instances) incluent :

- Une capture instantanée de stockage spécifique (au moment précis de sa création) consomme peu de stockage.
- Étant donné que les données changent et que le contenu des fichiers de données SAP HANA évolue sur le volume de stockage, la capture instantanée doit stocker le contenu des blocs d’origine et les modifications de données.
- La taille de la capture instantanée de stockage augmente en conséquence. Plus la durée d’existence de la capture instantanée est longue, plus la capture instantanée de stockage devient volumineuse.
- Plus le volume de base de données SAP HANA fait l’objet de modifications pendant la durée de vie d’une capture instantanée de stockage, plus la capture instantanée de stockage consomme d’espace.

SAP HANA sur Azure (grandes instances) est fourni avec des tailles de volume fixes pour les volumes de données et de journaux SAP HANA. L’exécution de captures instantanées de ces volumes consomme votre espace de volume. Vous devez :

- Planifier les captures instantanées de stockage.
- Surveiller la consommation d’espace des volumes de stockage. 
- Gérer le nombre de captures instantanées stockées. 

Vous pouvez désactiver les captures instantanées de stockage lorsque vous importez des masses de données ou apportez d’autres modifications importantes à la base de données HANA. 


Les sections ci-après fournissent diverses informations concernant l’exécution de ces captures instantanées et des recommandations générales :

- Bien que le matériel puisse prendre en charge 255 captures instantanées par volume, il est recommandé de rester très en deçà de ce seuil. La recommandation est de 250 maximum.
- Avant d’effectuer des captures instantanées de stockage, surveillez l’espace libre.
- Réduisez le nombre de captures instantanées de stockage en fonction de l’espace libre. Vous pouvez réduire le nombre de captures instantanées à conserver ou étendre les volumes. Vous pouvez commander plus de stockage par unités d’un téraoctet.
- Lorsque vous exécutez des tâches telles que le déplacement de données dans SAP HANA avec des outils de migration de la plateforme SAP (R3load) ou la restauration des bases de données SAP HANA à partir de sauvegardes, désactivez les captures instantanées de stockage sur le volume /hana/data. 
- Dans le cadre de réorganisations plus vastes des tables SAP HANA, évitez les captures instantanées de stockage dans la mesure du possible.
- Les captures instantanées de stockage constituent une condition préalable pour tirer parti des fonctionnalités de récupération d’urgence de SAP HANA sur Azure (grandes instances).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Conditions préalables pour tirer profit des captures instantanées de stockage en libre service

Lisez la documentation [Qu’est-ce que l’outil Azure Application Consistent Snapshot Tool ?](../../../azure-netapp-files/azacsnap-introduction.md)

Deux méthodes d’implémentation de cet outil sont possibles. 
1)  Localement sur le serveur de base de données
2)  À distance sur une machine virtuelle de « sauvegarde »

Si vous créez une machine virtuelle de sauvegarde, assurez-vous que le dernier client HANA est installé sur cette machine virtuelle. Avec cette méthode, azacsnap doit être capable d’ouvrir une connexion de base de données distante à une instance HANA en cours d’exécution sur une autre machine virtuelle.
Pour pouvoir accéder au stockage, vous devez demander à l’équipe de Support Microsoft de se servir d’un utilisateur de stockage et d’une clé SSH. Sans cette clé SSH et l’utilisateur, il n’est pas possible de créer des captures instantanées.

## <a name="download-and-set-up-azacsnap"></a>Télécharger et configurer azacsnap

Pour configurer des captures instantanées de stockage avec de grandes instances HANA, commencez par télécharger et installer l’outil azacsnap, comme décrit dans [Bien démarrer avec l’outil Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-get-started.md)

Azacsnap crée un utilisateur appelé azacsnap par défaut. Si vous préférez utiliser un autre nom, vous pouvez le spécifier au cours de l’installation. Pour plus d’informations, consultez la documentation ci-dessus.

## <a name="subsequent-next-steps"></a>Étapes suivantes
Suivez la documentation d’azacsnap pour :

- [Installer l’outil Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-installation.md)
- [Configurer l’outil Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-cmd-ref-configure.md)
- [Tester l’outil Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-cmd-ref-test.md)
- [Effectuer une sauvegarde à l’aide de l’outil Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-cmd-ref-backup.md)
- [Obtenir des détails avec l’outil Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-cmd-ref-details.md)
- [Supprimer avec l’outil Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-cmd-ref-delete.md)
- [Restaurer avec l’outil Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-cmd-ref-restore.md)
- [Récupération d’urgence avec l’outil Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-disaster-recovery.md)
- [Dépanner l'outil Azure Application Consistent Snapshot](../../../azure-netapp-files/azacsnap-troubleshoot.md)
- [Conseils et astuces pour utiliser l’outil Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-tips.md)


## <a name="next-steps"></a>Étapes suivantes

Lisez l’article [Qu’est-ce que l’outil Azure Application Consistent Snapshot Tool ?](../../../azure-netapp-files/azacsnap-introduction.md)

  



