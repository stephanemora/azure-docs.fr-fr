---
title: Sauvegarde et restauration HANA sur SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Sauvegarde et restauration HANA sur SAP HANA sur Azure (grandes instances)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 21232e5a678d6deed920e57cd0433a3b85ca4fdc
ms.sourcegitcommit: 60606c5e9a20b2906f6b6e3a3ddbcb6c826962d6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64987910"
---
# <a name="backup-and-restore"></a>Sauvegarde et restauration

>[!IMPORTANT]
>Cet article n’est pas un remplacement pour la documentation d’administration de SAP HANA ou les Notes SAP. Nous espérons que vous avez une connaissance approfondie des et une expertise dans l’administration de SAP HANA et d’opérations, en particulier pour la sauvegarde, restauration, haute disponibilité et récupération d’urgence. Dans cet article, des captures d’écran à partir de SAP HANA Studio sont affichés. Le contenu, la structure et la nature des écrans des outils d’administration de SAP, ainsi que les outils eux-mêmes, peuvent changer selon la version de SAP HANA.

Il est important de pratiquer les étapes et processus dans votre environnement, avec vos versions de HANA. Certains processus décrits dans cet article sont simplifiés pour une meilleure compréhension générale. Ils ne sont pas destinés à être utilisés comme des étapes détaillées pour manuels d’opération éventuels. Si vous souhaitez créer des manuels d’opération pour vos configurations, tester et pratiquer vos processus et documenter vos configurations spécifiques. 

Un des aspects plus importants du fonctionnement des bases de données consiste à les protéger contre les événements catastrophiques. Ces événements peuvent avoir une multitude de causes, des catastrophes naturelles aux simples erreurs des utilisateurs.

Sauvegarde d’une base de données, avec la possibilité de restaurer à n’importe quel point dans le temps, telles qu’avant un utilisateur supprimé des données critiques, permet de restaurer à un état aussi proche que possible de celui qu’il était avant l’interruption.

Deux types de sauvegardes doivent être effectuées pour atteindre la capacité de restaurer :

- Sauvegardes de base de données : sauvegardes complètes, incrémentielles ou différentielles
- Sauvegardes des journaux de transactions

Vous pouvez également compléter les sauvegardes de base de données complètes exécutées au niveau application par des sauvegardes effectuées à l’aide de captures instantanées de stockage. Captures instantanées de stockage ne remplacent pas les sauvegardes du journal des transactions. Celles-ci restent importantes pour restaurer la base de données à un certain point dans le temps ou pour supprimer des journaux d’activité les transactions déjà validées. Captures instantanées de stockage peuvent accélérer la récupération en fournissant rapidement une image de restauration par progression de la base de données. 

SAP HANA sur Azure (grandes instances) offre deux options de sauvegarde et de restauration :

- **Faites-le vous-même (configurer en libre service).** Après que vous être assuré qu’il existe suffisamment d’espace disque, effectuer la base de données complète et sauvegardes de journal en utilisant l’une des méthodes de sauvegarde de disque suivantes. Vous pouvez sauvegarder soit directement sur des volumes attachés aux unités de grande Instance HANA ou aux partages NFS qui sont définies dans une machine virtuelle (VM). Dans le deuxième cas, les clients paramètrent une machine virtuelle Linux dans Azure, attachent le stockage Azure à la machine virtuelle et partagent le stockage dans celle-ci par le biais d’un serveur NFS configuré. Si vous effectuez la sauvegarde sur des volumes directement attachés aux unités de grande Instance HANA, copiez les sauvegardes vers un compte de stockage Azure. Pour cela une fois que vous configurez une machine virtuelle Azure qui exporte les partages NFS sont basés sur le stockage Azure. Vous pouvez également utiliser un coffre de sauvegarde Azure ou le stockage froid Azure. 

   Une autre option consiste à utiliser un outil de protection des données tierces pour stocker les sauvegardes une fois qu’ils soient copiés vers un compte de stockage Azure. La sauvegarde également peut se révéler nécessaire pour les données dont vous avez besoin pour stocker de plus longues périodes de temps à des fins d’audit et de conformité. Dans tous les cas, les sauvegardes sont copiées dans des partages NFS représentés par le biais d’une machine virtuelle et d’un stockage Azure.

- **Infrastructure et de restauration.** Vous pouvez également utiliser la sauvegarde et restaurer la fonctionnalité qui fournit l’infrastructure sous-jacente de SAP HANA sur Azure (grandes Instances). Cette option répond à la nécessité d’effectuer des sauvegardes et des restaurations rapides. Le reste de cette section traite des fonctionnalités de sauvegarde et de restauration offertes avec les grandes instances HANA, Cette section couvre également la relation ayant sauvegarde et restauration à l’incident la fonctionnalité de récupération proposées par les grandes Instances HANA.

> [!NOTE]
>   La technologie de capture instantanée est utilisée par l’infrastructure sous-jacente des grandes Instances HANA a une dépendance sur les captures instantanées SAP HANA. À ce stade, les captures instantanées SAP HANA ne fonctionnent pas conjointement avec plusieurs locataires de conteneurs de base de données mutualisées SAP HANA. Si seul un locataire est déployé, les captures instantanées SAP HANA ne fonctionnent pas et vous pouvez utiliser cette méthode.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Utiliser des captures instantanées de stockage de SAP HANA sur Azure (grandes Instances)

L’infrastructure de stockage qui sous-tend SAP HANA sur Azure (grandes instances) prend en charge les captures instantanées de volumes de stockage. La prise en charge de la sauvegarde et de la restauration d’un volume est régie par les règles suivantes :

- Au lieu d’exécuter des sauvegardes de base de données complètes, le système procède à de fréquentes captures instantanées des volumes de stockage.
- Lorsqu’une capture instantanée est déclenchée sur/Hana/Data et/Hana/Shared, qui inclut la technologie de capture instantanée, les volumes, / usr/SAP lance une capture instantanée avant de SAP HANA, il exécute la capture instantanée de stockage. Cette capture instantanée SAP HANA est le point d’installation d’éventuelles restaurations de journaux après récupération de la capture instantanée de stockage. Pour une capture instantanée HANA réussisse, vous avez besoin d’une instance HANA active. Dans un scénario HSR, une capture instantanée de stockage n’est pas pris en charge sur un nœud secondaire actuel où une capture instantanée HANA ne peut pas être effectuée.
- Une fois la capture instantanée de stockage est correctement exécutée, la capture instantanée SAP HANA est supprimée.
- Les sauvegardes de fichier journal sont effectuées fréquemment et stockées dans le volume /hana/logbackups ou dans Azure. Vous pouvez déclencher une capture instantanée séparément pour le volume /hana/logbackups contenant les sauvegardes de fichier journal. Dans ce cas, vous n’avez pas besoin exécuter une capture instantanée HANA.
- Si vous devez restaurer une base de données à un certain point dans le temps, pour une interruption de production, cette prise en charge de Microsoft Azure ou solliciter SAP HANA lors de la restauration Azure pour certaines captures instantanées de stockage. (par exemple, une restauration planifiée d’un système bac à sable à son état d’origine).
- La capture instantanée SAP HANA qui est incluse dans la capture instantanée de stockage est un point de décalage pour l’application des sauvegardes de journal des transactions qui ont exécuté et ont été stockées après la capture instantanée de stockage.
- Ces sauvegardes de fichier journal sont effectuées pour restaurer la base de données à un point spécifique dans le temps.

Vous pouvez effectuer des captures instantanées de stockage ciblant trois classes de volumes :

- Une capture instantanée combinée sur/hana/data et/hana/shared, qui inclut/usr/SAP. Cette capture instantanée nécessite la création d’une capture instantanée de SAP HANA en préparation de la capture instantanée de stockage. La capture instantanée SAP HANA garantit que la base de données est dans un état cohérent à partir d’un point de vue du stockage. Pour le processus de restauration, qui est un point pour configurer l’authentification.
- Une capture instantanée distincte sur /hana/logbackups.
- Une partition du système d’exploitation.

Pour obtenir les scripts de capture instantanée plus récente et la documentation, consultez [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Lorsque vous téléchargez le package de script de capture instantanée à partir de [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0), vous obtenez trois fichiers. Un des fichiers est documenté dans un fichier PDF pour les fonctionnalités fournies. Après avoir téléchargé l’ensemble d’outils, suivez les instructions dans « obtenir les outils de capture instantanée. »

## <a name="storage-snapshot-considerations"></a>Considérations relatives aux captures instantanées de stockage

>[!NOTE]
>Captures instantanées de stockage utilisent l’espace de stockage qui est alloué aux unités de grande Instance HANA. Prendre en compte les aspects suivants de la planification des captures instantanées de stockage et combien instantanés de stockage à conserver. 

Les mécanismes spécifiques des captures instantanées de stockage de SAP HANA sur Azure (grandes instances) incluent :

- Une capture instantanée de stockage spécifique au point dans le temps quand il est consomme peu de stockage.
- En tant que les données changent et que le contenu de données SAP HANA modification des fichiers sur le volume de stockage, la capture instantanée doit stocker le contenu des blocs d’origine et les modifications de données.
- La taille de la capture instantanée de stockage augmente en conséquence. Plus la durée d’existence de la capture instantanée est longue, plus la capture instantanée de stockage devient volumineuse.
- Plus le volume de base de données SAP HANA fait l’objet de modifications pendant la durée de vie d’une capture instantanée de stockage, plus la capture instantanée de stockage consomme d’espace.

SAP HANA sur Azure (grandes instances) est fourni avec des tailles de volume fixes pour les volumes de données et de journaux SAP HANA. Effectuer des captures instantanées de ces volumes consomme votre espace de volume. Vous devez :

- Déterminer quand planifier les captures instantanées de stockage.
- Surveiller la consommation d’espace des volumes de stockage. 
- Gérer le nombre de captures instantanées stockées. 

Vous pouvez désactiver les captures instantanées de stockage lorsque vous importez des masses de données ou apportez d’autres modifications importantes à la base de données HANA. 


Les sections suivantes fournissent des informations pour l’exécution de ces captures instantanées et incluent des recommandations générales :

- Bien que le matériel peut prendre en charge 255 captures instantanées par volume, vous souhaitez rester bien en deçà de ce nombre. La recommandation est inférieur ou égal à 250.
- Avant d’effectuer des captures instantanées du stockage, surveillez l’espace libre.
- Réduisez le nombre de captures instantanées de stockage en fonction de l’espace libre. Vous pouvez réduire le nombre de captures instantanées à conserver ou étendre les volumes. Vous pouvez commander du stockage supplémentaire par unités d’un téraoctet.
- Lorsque vous exécutez des tâches telles que le déplacement de données dans SAP HANA avec des outils de migration de la plateforme SAP (R3load) ou la restauration des bases de données SAP HANA à partir de sauvegardes, désactivez les captures instantanées de stockage sur le volume /hana/data. 
- De réorganisations plus vastes des tables SAP HANA, évitez si possible les captures instantanées de stockage.
- Les captures instantanées de stockage constituent une condition préalable pour tirer parti des fonctionnalités de récupération d’urgence de SAP HANA sur Azure (grandes instances).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Conditions préalables pour tirer profit des captures instantanées de stockage en libre service

Pour vous assurer que le script de capture instantanée s’exécute correctement, assurez-vous que Perl est installé sur le système d’exploitation Linux sur le serveur de grandes Instances HANA. Perl est préinstallé sur votre unité de grande Instance HANA. Pour vérifier la version de Perl, utilisez la commande ci-dessous :

`perl -v`

![L’exécution de cette commande copie la clé publique.](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Configuration des captures instantanées de stockage

Pour configurer les captures instantanées de stockage avec grandes Instances HANA, procédez comme suit.
1. Assurez-vous que Perl est installé sur le système d’exploitation Linux sur le serveur de grandes instances HANA.
1. Modifiez /etc/ssh/ssh\_config en ajoutant la ligne _MACs hmac-sha1_.
1. Créer un compte d’utilisateur de sauvegarde SAP HANA sur le nœud principal pour chaque instance SAP HANA que vous exécutez, le cas échéant.
1. Installez le client SAP HANA HDB sur tous les serveurs de grandes instances SAP HANA.
1. Sur le premier serveur de grandes instances SAP HANA de chaque région, créez une clé publique pour accéder à l’infrastructure de stockage sous-jacente qui contrôle la création de captures instantanées.
1. Copiez les scripts et le fichier de configuration de [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) dans l’emplacement de **hdbsql** dans l’installation de SAP HANA.
1. Modifiez le fichier *HANABackupDetails.txt* en fonction des spécifications appropriées du client.

Procurez-vous les derniers scripts d’instantané et la documentation associée à partir de [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Pour connaître les étapes répertoriées précédemment, consultez [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

### <a name="consideration-for-mcod-scenarios"></a>Considération des scénarios MCOD
Si vous exécutez un [scénario MCOD](https://launchpad.support.sap.com/#/notes/1681092) avec plusieurs instances SAP HANA sur une unité de grande Instance HANA, vous avez des volumes de stockage distincts pour chacune des instances SAP HANA. Pour plus d’informations sur MDC et d’autres considérations, consultez « Points importants à retenir » dans [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Étape 1 : Installer le client SAP HANA HDB

Le système d’exploitation Linux installé dans SAP HANA sur Azure (grandes Instances) inclut les dossiers et scripts nécessaires à l’exécution des captures instantanées de stockage SAP HANA pour la sauvegarde et de reprise après sinistre à des fins de récupération. Vérifiez s’il existe des versions plus récentes dans [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). La version plus récente des scripts est 4.0. Des scripts différents peuvent avoir différentes versions mineures dans la même version majeure.

Il vous incombe d’installer le client SAP HANA HDB sur les unités de grande Instance HANA pendant l’installation de SAP HANA.

### <a name="step-2-change-the-etcsshsshconfig"></a>Étape 2 : Modifier la configuration /etc/ssh/ssh\_

Cette étape est décrite dans « Activer la communication avec le stockage » dans [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


### <a name="step-3-create-a-public-key"></a>Étape 3 : Créer une clé publique

Pour activer l’accès aux interfaces de capture instantanée de stockage de votre locataire de grande Instance HANA, établissez une procédure de connexion via une clé publique. 

Sur le premier serveur SAP HANA sur Azure (grandes Instances) dans votre client, créez une clé publique pour accéder à l’infrastructure de stockage. Avec une clé publique, un mot de passe n’est pas requis pour se connecter à des interfaces de capture instantanée de stockage. Vous ne devez également mettre à jour les informations d’identification de mot de passe avec une clé publique. 

Pour générer une clé publique, consultez « Activer la communication avec le stockage » dans [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


### <a name="step-4-create-an-sap-hana-user-account"></a>Étape 4 : Créer un compte d'utilisateur SAP HANA

Pour démarrer la création de captures instantanées SAP HANA, créez un compte d’utilisateur dans SAP HANA utilisables par les scripts de capture instantanée de stockage. Créez un compte d’utilisateur SAP HANA dans SAP HANA Studio à cette fin. L’utilisateur doit être créé sous le SYSTEMDB et *pas* sous la base de données SID MDC. Dans l’environnement de conteneur unique, l’utilisateur est créé dans la base de données client. Ce compte doit avoir **administrateur de sauvegarde** et **en lecture catalogue** des privilèges. 

Pour configurer et utiliser un compte d’utilisateur, consultez « Activer la communication avec SAP HANA » dans [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Étape 5 : Autoriser le compte d'utilisateur SAP HANA

Dans cette étape, vous autorisez le compte d’utilisateur SAP HANA que vous avez créé afin que les scripts n’aient à envoyer des mots de passe lors de l’exécution. La commande SAP HANA `hdbuserstore` permet la création d’une clé utilisateur SAP HANA. La clé est stockée sur un ou plusieurs nœuds SAP HANA. Cette clé utilisateur permet à l’utilisateur d’accéder à SAP HANA sans avoir à gérer les mots de passe à partir du processus d’utilisation de scripts décrit plus loin dans cet article.

>[!IMPORTANT]
>Exécutez ces commandes de configuration avec le même contexte utilisateur exécutées dans les commandes de l’instantané. Sinon, les commandes de capture instantanée ne fonctionnent pas correctement.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Étape 6 : Obtenir les scripts de capture instantanée, configurer les captures instantanées, et tester la configuration et la connectivité

Téléchargez la version la plus récente des scripts depuis [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). La façon dont les scripts sont installés modifié avec la version 4.0 des scripts. Pour plus d’informations, consultez « Activer la communication avec SAP HANA » dans [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Pour la séquence exacte de commandes, consultez « Installation facile des outils de capture instantanée (par défaut) » dans [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Nous recommandons l’utilisation de l’installation par défaut. 

Pour mettre à niveau à partir de la version 3.x vers 4.0, consultez « Mise à niveau une installation existante » dans [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Pour désinstaller l’ensemble de 4.0 Outils, voir « La désinstallation des outils de capture instantanée » dans [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

N’oubliez pas d’exécuter les étapes décrites dans « Terminer l’installation des outils de capture instantanée » dans [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

L’objectif des différents scripts et fichiers car ils vous avez installés est décrit dans « Quels sont ces outils de capture instantanée ? » dans [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Avant de configurer les outils de capture instantanée, assurez-vous également configuré les paramètres et les emplacements de sauvegarde HANA correctement. Pour plus d’informations, consultez « Configuration de HANA SAP » dans [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

La configuration de l’ensemble d’outils de capture instantanée est décrite dans « Fichier de configuration - HANABackupCustomerDetails.txt » dans [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

#### <a name="test-connectivity-with-sap-hana"></a>Tester la connectivité avec SAP HANA

Après avoir placé toutes les données de configuration dans le fichier *HANABackupCustomerDetails.txt*, vérifiez si les configurations des données d’instance HANA sont correctes. Utilisez le script `testHANAConnection`, qui est indépendant d’une configuration Scale-up ou Scale-out SAP HANA.

Pour plus d’informations, consultez « Vérifiez la connectivité avec SAP HANA - testHANAConnection » dans [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

#### <a name="test-storage-connectivity"></a>Tester la connectivité du stockage

L’étape de test suivante consiste à vérifier la connectivité vers le stockage basé sur les données que vous placez dans le *HANABackupCustomerDetails.txt* fichier de configuration. Exécutez ensuite une capture instantanée de test. Avant d’exécuter le `azure_hana_backup` commande, vous devez exécuter ce test. Pour la séquence de commandes pour ce test, consultez « Vérifiez la connectivité avec le stockage - testStorageSnapshotConnection » » dans [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Une fois que la connexion aux interfaces de machine virtuelle de stockage est établie, le script passe à la phase 2 et crée une capture instantanée de test, La sortie est indiquée ici pour une configuration de montée en puissance de trois nœuds de SAP HANA.

Si la capture instantanée de test s’exécute correctement avec le script, vous pouvez planifier les captures instantanées de stockage réel. Si elle n’est pas réussie, identifiez le problème avant de vous déplacez vers le bas. La capture instantanée de test doit être conservée jusqu’à ce que les premières captures instantanées réelles soient effectuées.


### <a name="step-7-perform-snapshots"></a>Étape 7 : Effectuer des captures instantanées

Une fois les étapes de préparation terminées, vous pouvez commencer à configurer et planifier les captures instantanées de stockage réel. Le script à planifier fonctionne avec les configurations de SAP HANA avec montée en puissance et montée en puissance parallèle. Pour une exécution périodique et régulière de la planification du script de sauvegarde, planifiez le script à l’aide de l’utilitaire cron. 

Pour la syntaxe de commande exacte et des fonctionnalités, consultez « Effectuer instantané de sauvegarde - azure_hana_backup » dans [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

Lorsque le script `azure_hana_backup` s’exécute, il crée le stockage de capture instantané dans le code suivant trois phases :

1. Il s’exécute une capture instantanée SAP HANA.
1. Il s’exécute une capture instantanée de stockage.
1. Il supprime la capture instantanée SAP HANA qui a été créée avant l’exécution de la capture instantanée de stockage.

Pour exécuter le script, vous devez l’appeler à partir du dossier exécutable HDB dans lequel il a été copié. 

La période de rétention est administrée avec le nombre de captures instantanées soumis en tant que paramètre lorsque vous exécutez le script. La quantité de temps qui est couverte par les captures instantanées de stockage est une fonction de la période d’exécution et le nombre de captures instantanées soumis en tant que paramètre lors de l’exécution du script. 

Si le nombre de captures instantanées conservées dépasse le nombre qui sont nommées en tant que paramètre dans l’appel du script, l’ancienne capture instantanée de stockage de la même étiquette est supprimée avant l’exécution d’un nouvel instantané. Le nombre que vous indiquez en tant que dernier paramètre de l’appel correspond au nombre que vous pouvez utiliser pour contrôler le nombre de captures instantanées qui sont conservées. Avec ce numéro, vous pouvez également contrôler, indirectement, l’espace disque qui est utilisé pour les captures instantanées. 


## <a name="snapshot-strategies"></a>Stratégies de capture instantanée
La fréquence des captures instantanées des différents types varie selon que vous utilisez ou non la fonctionnalité de récupération d’urgence des grandes instances HANA. Cette fonctionnalité repose sur les captures instantanées de stockage, qui peuvent avoir des recommandations spéciales pour la fréquence et les périodes d’exécution des captures instantanées de stockage. 

Dans les considérations et recommandations ci-après, nous supposons que vous n’utilisez *pas* la fonctionnalité de récupération d’urgence proposée par les grandes instances HANA. À la place, vous utilisez les captures instantanées de stockage comme un moyen d’obtenir des sauvegardes et de pouvoir fournir une récupération jusqu’à une date et heure couvrant les 30 derniers jours. Selon les limites du nombre de captures instantanées et de l’espace, tenez compte des exigences suivantes :

- Temps de récupération pour la récupération jusqu’à une date et heure.
- Espace utilisé.
- Objectifs de point et de délai de récupération pour une récupération d’urgence potentielle après un incident.
- Exécution éventuelles de sauvegardes de base de données complètes HANA pour les disques. Chaque fois qu’une sauvegarde de base de données complète est effectuée pour les disques ou pour l’interface **backint**, l’exécution des captures instantanées de stockage échoue. Si vous projetez d’exécuter des sauvegardes de base de données complètes reposant sur des captures instantanées de stockage, assurez-vous que l’exécution des captures instantanées de stockage est désactivée pendant cette période.
- Le nombre de captures instantanées par volume, qui est limité à 250.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Si vous n’utilisez pas la fonctionnalité de récupération d’urgence des grandes Instances HANA, la période de capture instantanée est moins fréquente. Dans ce cas, effectuez les captures instantanées combinées sur/Hana/Data et/Hana/Shared, qui inclut/usr/SAP, de 12 heures ou 24 heures. Conservez les captures instantanées pendant un mois. Cela vaut également pour les captures instantanées du volume de sauvegarde de journal. L’exécution de sauvegardes de fichier journal SAP HANA sur le volume de sauvegarde de journal se produit dans les 5 minutes à 15 minutes.

Les captures instantanées de stockage planifiées sont les mieux exécutées avec cron. Utiliser le même script pour toutes les sauvegardes et les besoins de récupération d’urgence. Modifiez les entrées du script en fonction des différentes heures de sauvegarde demandées. Ces captures instantanées sont toutes planifiées différemment dans cron selon leur durée d’exécution. Il peut être toutes les heures, toutes les 12 heures, tous les jours, ou les hebdomadaire. 

L’exemple suivant montre une planification cron dans/etc/crontab :
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
Dans l’exemple précédent, une capture instantanée horaire combinée couvre les volumes qui stockent les /hana/shared/SID, qui inclut/usr/SAP, les emplacements/Hana/Data. Utilisez ce type de capture instantanée pour une récupération plus rapide pendant les deux derniers jours. Il existe également un plan quotidien de capture instantanée sur ces volumes. Par conséquent, vous avez deux jours de couverture par les captures instantanées horaires, ainsi que quatre semaines couvertes par les captures instantanées quotidiennes. Le volume de sauvegarde du journal des transactions également est sauvegardé quotidiennement. Ces sauvegardes sont conservées pendant quatre semaines. 

Comme vous le voir dans la troisième ligne de crontab, la sauvegarde du journal des transactions HANA est planifiée pour s’exécuter toutes les 5 minutes. Les heures de début des différents travaux cron qui exécutent des captures instantanées de stockage sont décalées. De cette façon, les captures instantanées ne s’exécutent à la fois à un certain point dans le temps. 

Dans l’exemple suivant, vous effectuez une capture instantanée combinée qui couvre les volumes contenant/Hana/Data et le /hana/shared/SID, qui inclut/usr/SAP, emplacements sur une base horaire. Vous conservez ces captures instantanées pendant deux jours. Les captures instantanées des volumes de sauvegarde de journal des transactions s’exécutent sur une base de 5 minutes et sont conservées pendant quatre heures. Comme avant, la sauvegarde du fichier journal des transactions HANA est planifiée pour exécuter toutes les 5 minutes. 

La capture instantanée du volume de sauvegarde de fichier journal s’effectue dans un délai de 2 minutes après le démarrage de la sauvegarde du fichier journal. Dans des circonstances normales, la sauvegarde de fichier journal SAP HANA se termine dans ces 2 minutes. Une fois encore, le volume contenant le numéro d’unité logique de démarrage est sauvegardé une fois par jour par une capture instantanée de stockage et est conservé pendant quatre semaines.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

Le graphique suivant illustre les séquences de l’exemple précédent. Le numéro d’unité logique de démarrage est exclue.

![Relation entre les sauvegardes et les captures instantanées](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA effectue des écritures régulières sur le volume /hana/log pour documenter les modifications validées dans la base de données. Régulièrement, SAP HANA écrit un point de sauvegarde dans le volume /hana/data. Comme spécifié dans crontab, une sauvegarde de fichier journal SAP HANA s’exécute toutes les 5 minutes. 

Vous consultez également qu’une capture instantanée SAP HANA s’exécute toutes les heures suite au déclenchement d’une capture instantanée sur les volumes/Hana/Data et /hana/shared/SID de stockage combinée. Après que la capture instantanée HANA a réussi, le stockage combiné d’instantané s’exécute. Comme indiqué dans crontab, la capture instantanée de stockage sur le volume/Hana/logbackup s’exécute toutes les 5 minutes, environ 2 minutes après la sauvegarde de journal de transactions HANA.

> 

>[!IMPORTANT]
> L’utilisation de captures instantanées de stockage pour les sauvegardes SAP HANA n’est précieuse que quand ces captures sont effectuées conjointement avec des sauvegardes de fichier journal SAP HANA. Ces sauvegardes de fichier journal doivent couvrir les périodes entre les captures instantanées de stockage. 

Si vous vous êtes engagé auprès des utilisateurs à assurer une récupération jusqu’à 30 jours, vous devez disposer des éléments suivants :

- Accéder à une capture instantanée de stockage combinée sur/Hana/Data et /hana/shared/SID est 30 derniers jours, dans les cas extrêmes. 
- Vérifiez que les sauvegardes de fichier journal contiguës couvrent la période entre des captures instantanées de stockage combinées. La capture instantanée la plus ancienne du volume de sauvegarde de fichier journal doit donc remonter à 30 jours, Ce n’est pas le cas si vous copiez les sauvegardes du journal des transactions vers un autre partage NFS qui se trouve sur le stockage Azure. Auquel cas, vous pourriez récupérer les anciennes sauvegardes de fichier journal, de ce partage NFS.

Pour bénéficier des captures instantanées de stockage et la réplication de stockage éventuelle des sauvegardes de fichier journal, modifiez l’emplacement auquel SAP HANA écrit les sauvegardes du journal des transactions. Vous pouvez effectuer ce changement dans HANA Studio. 

Bien que SAP HANA sauvegarde automatiquement des segments de journal complets, spécifiez un intervalle de sauvegarde de journal pour être déterministe. Cela est particulièrement vrai lorsque vous utilisez l’option de récupération d’urgence, car vous souhaitez généralement exécuter des sauvegardes de journal avec une période déterministe. Dans le cas suivant, 15 minutes est défini en tant que l’intervalle de sauvegarde de journal.

![Planifier les sauvegardes de journaux d’activité SAP HANA dans SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Vous pouvez également choisir des sauvegardes plus fréquemment que toutes les 15 minutes. La valeur paramétrée est souvent inférieure avec la fonctionnalité de récupération d’urgence des grandes instances HANA. Certains clients effectuent des sauvegardes de fichier journal toutes les 5 minutes.

Si la base de données n’a jamais été sauvegardée, la dernière étape consiste à effectuer une sauvegarde de base de données basée sur un fichier afin de créer une entrée de sauvegarde unique devant figurer dans le catalogue de sauvegarde. Le cas contraire, SAP HANA ne peut pas lancer vos sauvegardes de journaux spécifié.

![Créer une sauvegarde basée sur un fichier pour créer une entrée de sauvegarde unique](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Après l’exécution de vos premières captures instantanées de stockage qui ont réussi, supprimer la capture instantanée de test qui s’exécutait à l’étape 6. Pour plus d’informations, consultez « Instantanés de test de Remove - removeTestStorageSnapshot » dans [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Surveiller le nombre et la taille des captures instantanées sur le volume de disque

Sur un volume de stockage, vous pouvez surveiller le nombre de captures instantanées et l’espace de stockage consommé par ces captures. La commande `ls` n’affiche pas le répertoire des captures instantanées ni les fichiers. La commande de système d’exploitation Linux `du` donne des détails sur ces captures instantanées de stockage, car celles-ci sont stockées sur les mêmes volumes. Utilisez la commande avec les options suivantes :

- `du –sh .snapshot`: cette option fournit le nombre total de captures instantanées dans le répertoire de captures instantanées.
- `du –sh --max-depth=1`: cette option répertorie toutes les captures instantanées enregistrées dans le dossier **.snapshot** et la taille de chaque capture instantanée.
- `du –hc`: cette option fournit la taille totale de toutes les captures instantanées.

Utilisez ces commandes pour vous assurer que les captures instantanées qui sont créées et stockées ne consomment pas la totalité du stockage sur les volumes.

>[!NOTE]
>Les captures instantanées de la LUN de démarrage ne sont pas visibles avec les commandes précédentes.

### <a name="get-details-of-snapshots"></a>Obtenir les détails des captures instantanées
Pour obtenir plus d’informations sur les captures instantanées, utilisez le script `azure_hana_snapshot_details`. S’il existe un serveur actif dans l’emplacement de récupération d’urgence, vous pouvez exécuter ce script dans n’importe quel emplacement. Le script fournit les informations ventilées suivantes pour chaque volume contenant des captures instantanées : 
   * Taille de toutes les captures instantanées dans un volume
   * Chaque capture instantanée dans ce volume inclut les informations suivantes : 
      - Nom de la capture instantanée 
      - Heure de création 
      - Taille de la capture instantanée
      - Fréquence de la capture instantanée
      - ID de sauvegarde HANA associé à cette capture instantanée, le cas échéant

Pour la syntaxe des commandes et des sorties, consultez « Instantanés de liste - azure_hana_snapshot_details » dans [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Réduire le nombre de captures instantanées sur un serveur

Comme expliqué précédemment, vous pouvez réduire le nombre de captures instantanées que vous stockez une certaine étiquette. Les deux derniers paramètres de la commande permettant de lancer une capture instantanée correspondent à l’étiquette et au nombre de captures instantanées à conserver.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

Dans l’exemple précédent, l’étiquette de l’instantané est **dailyhana**. Le nombre de captures instantanées portant cette étiquette qui doivent être conservées est **28**. Afin de faire face au problème de la consommation d’espace disque, vous pouvez vouloir réduire le nombre de captures instantanées stockées. Un moyen simple de réduire le nombre de captures instantanées à 15, par exemple, consiste à exécuter le script avec le dernier paramètre défini sur **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Si vous exécutez le script avec ce paramètre, le nombre d’instantanés, qui inclut la nouvelle capture instantanée de stockage, est 15. Les 15 captures instantanées les plus récentes sont conservées, tandis que les 15 captures instantanées plus anciennes sont supprimées.

 >[!NOTE]
 > Ce script réduit le nombre de captures instantanées uniquement s’il existe des captures instantanées datant de plus d’une heures. Le script ne supprime pas les captures instantanées qui datent inférieur à une heures. Ces restrictions sont associées à la fonctionnalité de récupération d’urgence disponible en option.

Si vous ne souhaitez plus conserver un ensemble de captures instantanées avec le préfixe de sauvegarde **dailyhana** dans les exemples de syntaxe, exécutez le script avec **0** comme valeur de rétention. Toutes les captures instantanées qui correspondent à cette étiquette sont alors supprimées. Suppression de toutes les captures instantanées peut affecter les fonctionnalités de la fonctionnalité de récupération d’urgence de grandes Instances HANA.

L’autre possibilité pour supprimer certaines captures instantanées consiste à utiliser le script `azure_hana_snapshot_delete`. Ce script est conçu pour supprimer une capture instantanée ou un ensemble de captures instantanées à l’aide de l’ID de sauvegarde HANA tel que figurant dans HANA Studio ou du nom de la capture instantanée. À l’heure actuelle, l’ID de sauvegarde est uniquement lié aux captures instantanées créées pour le type de capture instantanée **hana**. Sauvegardes du type de capture instantanée **journaux** et **démarrage** n’effectuent pas une solution SAP HANA d’instantané, donc il n’existe aucun ID de sauvegarde pour ces captures instantanées. Si le nom de la capture instantanée est entré, le script recherche sur les différents volumes toutes les captures instantanées qui correspondent au nom entré. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Pour plus d’informations sur le script, consultez « Supprimer un instantané - azure_hana_snapshot_delete » dans [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Exécutez le script en tant qu’utilisateur **racine**.

>[!IMPORTANT]
>S’il existe des données qui existent uniquement dans la capture instantanée que vous voulez supprimer, une fois que l’instantané est supprimé, que les données sont définitivement perdues.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Restauration au niveau du fichier à partir d’une capture instantanée de stockage

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
Vous pouvez accéder aux captures instantanées **hana** et **logs** directement sur les volumes dans le répertoire **.snapshot**. Il existe un sous-répertoire pour chaque capture instantanée. Copier chaque fichier dans l’état qu'où il se trouvait au moment de l’instantané à partir de ce sous-répertoire dans la structure de répertoire réelle. 

Dans la version actuelle du script, il est *aucune* restaurer le script fourni pour la restauration de capture instantanée en libre-service. Restauration de capture instantanée peut être effectuée dans le cadre des scripts de récupération d’urgence en libre-service sur le site de récupération d’urgence pendant le basculement. Pour restaurer un instantané de votre choix à partir des instantanés existants disponibles, vous devez contacter l’équipe des opérations Microsoft en ouvrant une demande de service.

>[!NOTE]
>Un fichier unique restauration ne fonctionne pas pour les instantanés de la LUN indépendant du type des unités de grande Instance HANA de démarrage. Le **.snapshot** directory n’est pas exposé dans le numéro d’unité logique de démarrage. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Récupération jusqu’à la capture instantanée HANA la plus récente

Dans un scénario de production vers le bas, le processus de récupération à partir d’une capture instantanée de stockage peut être démarré en tant qu’un incident client auprès du Support Microsoft Azure. Il est la plus haute urgence si les données ont été supprimées dans un système de production et la seule façon de le récupérer consiste à restaurer la base de données de production.

Dans un cas différent, une récupération jusqu’à une date et heure peut ne pas avoir de caractère d’urgence et être planifiée plusieurs jours à l’avance. Vous pouvez planifier cette récupération avec SAP HANA sur Azure au lieu de déclencher un indicateur de priorité élevée. Par exemple, que vous souhaitiez mettre à niveau le logiciel SAP en appliquant un nouveau package. et vouloir ensuite revenir à une capture instantanée correspondant à l’état du système avant sa mise à niveau avec le package d’améliorations.

Avant de soumettre la demande, vous devez effectuer certaines tâches de préparation. L’équipe SAP HANA sur Azure peut ensuite traiter la demande et fournir les volumes restaurés. Vous pourrez ensuite restaurer la base de données HANA à l’aide des captures instantanées.

Pour les possibilités pour l’obtention d’une capture instantanée restaurée avec le nouvel ensemble d’outils, consultez « Comment restaurer un instantané » dans [guide de récupération manuelle de SAP HANA sur Azure à partir d’une capture instantanée de stockage](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

Pour préparer la demande, procédez comme suit.

1. Déterminez la capture instantanée à restaurer. Sauf indication contraire, seul le volume hana/data est restauré. 

1. Arrêtez l’instance HANA.

   ![Arrêter l’instance HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Démontez les volumes de données sur chaque nœud de base de données HANA. Si les volumes de données sont toujours montés sur le système d’exploitation, la restauration de la capture instantanée échoue.

   ![Démonter les volumes de données sur chaque nœud de base de données HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Ouvrez une demande de support Azure et inclure des instructions sur la restauration d’une capture instantanée spécifique :

   - Pendant la restauration : SAP HANA sur Azure Service peut vous demander de participer à une téléconférence pour coordonner, vérifiez et confirmez que la capture instantanée de stockage correcte est restaurée. 

   - Après la restauration : SAP HANA sur Azure Service vous informe lorsque la capture instantanée de stockage est restaurée.

1. Une fois le processus de restauration terminé, remontez tous les volumes de données.

   ![Remonter tous les volumes de données](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Une autre possibilité pour obtenir des fichiers de données SAP HANA récupérées à partir d’une capture instantanée de stockage, par exemple, est décrite dans l’étape 7 dans [guide de récupération manuelle de SAP HANA sur Azure à partir d’une capture instantanée de stockage](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

Pour restaurer à partir d’une sauvegarde d’instantanés, consultez [guide de récupération manuelle de SAP HANA sur Azure à partir d’une capture instantanée de stockage](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf). 

>[!Note]
>Si votre instantané a été restaurée par les opérations de Microsoft, vous n’avez pas besoin de l’étape 7.


### <a name="recover-to-another-point-in-time"></a>Récupération jusqu’à un autre point dans le temps
Pour restaurer à un certain point dans le temps, consultez « Récupérer la base de données pour le point suivant dans le temps » dans [guide de récupération manuelle de SAP HANA sur Azure à partir d’une capture instantanée de stockage](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf). 


## <a name="next-steps"></a>Étapes suivantes
- Consultez [principes de la récupération d’urgence et la préparation](hana-concept-preparation.md).
