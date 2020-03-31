---
title: Sauvegarde et restauration HANA sur SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Sauvegarde et restauration HANA sur SAP HANA sur Azure (grandes instances)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4384d29811d29f06422802abba5d3eb1ea5737e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72430074"
---
# <a name="backup-and-restore"></a>Sauvegarde et restauration

>[!IMPORTANT]
>Cet article n’est en aucun cas un remplacement de la documentation d’administration de SAP HANA ou des Notes SAP. Nous assumons que vous avez une bonne compréhension et une expertise approfondie des opérations et de l’administration SAP HANA, principalement de la sauvegarde, de la restauration, de la haute disponibilité et de la récupération d’urgence. Dans cet article, des captures d’écran de SAP HANA Studio sont affichées. Le contenu, la structure et la nature des écrans des outils d’administration de SAP, ainsi que les outils eux-mêmes, peuvent changer selon la version de SAP HANA.

Il est important de pratiquer les étapes et processus dans votre environnement, avec vos versions de HANA. Certains processus décrits dans cet article sont simplifiés pour une meilleure compréhension générale. Ils ne sont pas destinés à être utilisés comme des étapes détaillées pour d’éventuels manuels d’opération. Si vous souhaitez créer des manuels d’opération pour vos configurations, testez vos processus et documentez-les par rapport à vos configurations spécifiques. 

L’un des aspects les plus importants de l’utilisation des bases de données concerne la protection de celles-ci contre les événements graves. Ces événements peuvent avoir une multitude de causes, des catastrophes naturelles aux simples erreurs des utilisateurs.

La sauvegarde d’une base de données, combinée à une possibilité de restauration à un moment donné quelconque (par exemple, avant la suppression de données critiques), permet de restaurer la base de données à un état aussi proche que possible de celui qu’elle présentait avant l’interruption.

Deux types de sauvegardes doivent être effectués pour permettre la restauration :

- Sauvegardes de base de données : sauvegardes complètes, incrémentielles ou différentielles
- Sauvegardes des journaux de transactions

Vous pouvez également compléter les sauvegardes de base de données complètes exécutées au niveau application par des sauvegardes effectuées à l’aide de captures instantanées de stockage. Les captures instantanées de stockage ne remplacent pas les sauvegardes de fichier journal. Celles-ci restent importantes pour restaurer la base de données à un certain point dans le temps ou pour supprimer des journaux d’activité les transactions déjà validées. Les captures instantanées de stockage peuvent accélérer la récupération en fournissant rapidement une image de restauration par progression de la base de données. 

SAP HANA sur Azure (grandes instances) offre deux options de sauvegarde et de restauration :

- **Utilisation de vos propres méthodes de sauvegarde.** Après avoir vérifié l’espace disque requis, effectuez des sauvegardes complètes de la base de données et des journaux à l’aide de l’une des méthodes de sauvegarde de disque. Vous pouvez sauvegarder soit directement sur des volumes attachés aux unités de grande instance HANA, soit sur des configurations de partage NFS dans une machine virtuelle Azure. Dans le deuxième cas, les clients paramètrent une machine virtuelle Linux dans Azure, attachent le stockage Azure à la machine virtuelle et partagent le stockage dans celle-ci par le biais d’un serveur NFS configuré. Si vous effectuez la sauvegarde sur des volumes directement attachés aux unités de grande instance HANA, copiez les sauvegardes vers un compte de stockage Azure. Faites-le après avoir configuré une machine virtuelle Azure qui exporte les partages NFS basés sur le stockage Azure, ou bien utilisez un coffre de sauvegarde Azure ou un stockage froid Azure. 

   Une autre possibilité consiste à recourir à un outil de protection des données tiers pour stocker les sauvegardes une fois ces dernières copiées dans un compte de stockage Azure. L’utilisation de vos propres méthodes de sauvegarde peut également se révéler nécessaire pour les données qui doivent être stockées sur de plus longues périodes à des fins de conformité et d’audit. Dans tous les cas, les sauvegardes sont copiées dans des partages NFS représentés par le biais d’une machine virtuelle et d’un stockage Azure.

- **Fonctionnalité de sauvegarde et de restauration d’infrastructure.** Vous pouvez également utiliser la fonctionnalité de sauvegarde et de restauration fournie par l’infrastructure sous-jacente de SAP HANA sur Azure (grandes instances). Cette option répond à la nécessité d’effectuer des sauvegardes et des restaurations rapides. Le reste de cette section traite des fonctionnalités de sauvegarde et de restauration offertes avec les grandes instances HANA, Cette section couvre également la relation de la fonctionnalité de sauvegarde et de restauration avec la fonctionnalité de récupération d’urgence fournie par les grandes instances HANA.

> [!NOTE]
>   La technologie de capture instantanée utilisée par l’infrastructure sous-jacente des grandes instances HANA a une dépendance sur les captures instantanées SAP HANA. À ce jour, les captures instantanées SAP HANA ne fonctionnent pas avec plusieurs locataires de conteneurs de bases de données SAP HANA mutualisées. Si seul un locataire est déployé, les captures instantanées de SAP HANA fonctionnent, et cette méthode est utilisable.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Utiliser des captures instantanées de stockage de SAP HANA sur Azure (grandes instances)

L’infrastructure de stockage qui sous-tend SAP HANA sur Azure (grandes instances) prend en charge les captures instantanées de volumes de stockage. La prise en charge de la sauvegarde et de la restauration d’un volume est régie par les règles suivantes :

- Au lieu d’exécuter des sauvegardes de base de données complètes, le système procède à de fréquentes captures instantanées des volumes de stockage.
- Lorsqu’une capture instantanée est déclenchée sur les volumes /hana/data et /hana/shared (/usr/sap compris), la technologie de capture instantanée lance une capture instantanée SAP HANA avant d’exécuter la capture instantanée de stockage. Cette capture instantanée SAP HANA est le point d’installation d’éventuelles restaurations de journaux après récupération de la capture instantanée de stockage. Pour qu’une capture instantanée HANA réussisse, vous avez besoin d’une instance HANA active. Dans un scénario HSR, une capture instantanée de stockage n’est pas prise en charge sur un nœud secondaire actif où une capture instantanée HANA n’est pas possible.
- Une fois la capture instantanée de stockage exécutée, la capture instantanée SAP HANA est supprimée.
- Les sauvegardes de fichier journal sont effectuées fréquemment et stockées dans le volume /hana/logbackups ou dans Azure. Vous pouvez déclencher une capture instantanée séparément pour le volume /hana/logbackups contenant les sauvegardes de fichier journal. Dans ce cas, vous n’avez pas besoin d’exécuter une capture instantanée HANA.
- Si vous devez restaurer une base de données à un point dans le temps, contactez le support technique de Microsoft Azure (pour une interruption de production) ou SAP HANA sur Azure pour solliciter une restauration à partir d’une capture instantanée de stockage donnée (par exemple, une restauration planifiée d’un système bac à sable à son état d’origine).
- La capture instantanée SAP HANA incluse dans la capture instantanée de stockage constitue un point de décalage pour l’application des sauvegardes de fichier journal qui ont été exécutées et stockées après la création de la capture instantanée de stockage.
- Ces sauvegardes de fichier journal sont effectuées pour restaurer la base de données à un point spécifique dans le temps.

Vous avez la possibilité d’effectuer des captures instantanées de stockage ciblant trois classes de volumes différentes :

- Une capture instantanée combinée sur les volumes /hana/data et /hana/shared (/usr/sap compris). Cette capture instantanée nécessite la création d’une capture instantanée de SAP HANA en préparation de la capture instantanée de stockage. La capture instantanée de SAP HANA permet de garantir que la base de données est dans un état cohérent du point de vue du stockage. Pour le processus de restauration, c’est un point de configuration.
- Une capture instantanée distincte sur /hana/logbackups.
- Une partition du système d’exploitation.

Pour vous procurer les derniers scripts de capture instantanée et la documentation associée, consultez [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Lorsque vous téléchargez le package de script de capture instantanée à partir de [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md), vous obtenez trois fichiers. Un des fichiers est documenté dans un fichier PDF concernant les fonctionnalités fournies. Après avoir téléchargé l’ensemble d’outils, suivez les instructions de la section « Get the snapshot tools » (Obtenir les outils de capture instantanée).

## <a name="storage-snapshot-considerations"></a>Considérations relatives aux captures instantanées de stockage

>[!NOTE]
>Les captures instantanées de stockage consomment un espace de stockage qui est alloué aux unités de grande instance HANA. Prendre en compte les aspects suivants concernant la planification des captures instantanées de stockage et le nombre de captures instantanées de stockage à conserver. 

Les mécanismes spécifiques des captures instantanées de stockage de SAP HANA sur Azure (grandes instances) incluent :

- Une capture instantanée de stockage spécifique (au moment précis de sa création) consomme peu de stockage.
- Étant donné que les données changent et que le contenu des fichiers de données SAP HANA évolue sur le volume de stockage, la capture instantanée doit stocker le contenu des blocs d’origine et les modifications de données.
- La taille de la capture instantanée de stockage augmente en conséquence. Plus la durée d’existence de la capture instantanée est longue, plus la capture instantanée de stockage devient volumineuse.
- Plus le volume de base de données SAP HANA fait l’objet de modifications pendant la durée de vie d’une capture instantanée de stockage, plus la capture instantanée de stockage consomme d’espace.

SAP HANA sur Azure (grandes instances) est fourni avec des tailles de volume fixes pour les volumes de données et de journaux SAP HANA. Effectuer des captures instantanées de ces volumes consomme votre espace de volume. Vous devez :

- Planifier les captures instantanées de stockage.
- Surveiller la consommation d’espace des volumes de stockage. 
- Gérer le nombre de captures instantanées stockées. 

Vous pouvez désactiver les captures instantanées de stockage lorsque vous importez des masses de données ou apportez d’autres modifications importantes à la base de données HANA. 


Les sections ci-après fournissent diverses informations concernant l’exécution de ces captures instantanées et des recommandations générales :

- Bien que le matériel puisse prendre en charge 255 captures instantanées par volume, il est recommandé de rester très en deçà de ce seuil. La recommandation est de 250 maximum.
- Avant d’effectuer des captures instantanées du stockage, surveillez l’espace libre.
- Réduisez le nombre de captures instantanées de stockage en fonction de l’espace libre. Vous pouvez réduire le nombre de captures instantanées à conserver ou étendre les volumes. Vous pouvez commander du stockage supplémentaire par unités d’un téraoctet.
- Lorsque vous exécutez des tâches telles que le déplacement de données dans SAP HANA avec des outils de migration de la plateforme SAP (R3load) ou la restauration des bases de données SAP HANA à partir de sauvegardes, désactivez les captures instantanées de stockage sur le volume /hana/data. 
- Dans le cadre de réorganisations plus vastes des tables SAP HANA, évitez les captures instantanées de stockage dans la mesure du possible.
- Les captures instantanées de stockage constituent une condition préalable pour tirer parti des fonctionnalités de récupération d’urgence de SAP HANA sur Azure (grandes instances).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Conditions préalables pour tirer profit des captures instantanées de stockage en libre service

Pour vérifier que le script de capture instantanée s’exécute correctement, vérifiez que Perl est installé sur le système d’exploitation Linux sur le serveur de grandes instances HANA. Perl est préinstallé sur votre unité de grande instance HANA. Pour vérifier la version de Perl, utilisez la commande ci-dessous :

`perl -v`

![L’exécution de cette commande copie la clé publique.](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Configuration des captures instantanées de stockage

Les étapes pour configurer des captures instantanées de stockage avec de grandes instances HANA sont les suivantes.
1. Assurez-vous que Perl est installé sur le système d’exploitation Linux sur le serveur de grandes instances HANA.
1. Modifiez /etc/ssh/ssh\_config en ajoutant la ligne _MACs hmac-sha1_.
1. Créez un compte d’utilisateur de sauvegarde SAP HANA sur le nœud principal de chaque instance SAP HANA que vous exécutez, le cas échéant.
1. Installez le client SAP HANA HDB sur tous les serveurs de grandes instances SAP HANA.
1. Sur le premier serveur de grandes instances SAP HANA de chaque région, créez une clé publique pour accéder à l’infrastructure de stockage sous-jacente qui contrôle la création de captures instantanées.
1. Copiez les scripts et le fichier de configuration de [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) dans l’emplacement de **hdbsql** dans l’installation de SAP HANA.
1. Modifiez le fichier *HANABackupDetails.txt* en fonction des spécifications appropriées du client.

Procurez-vous les derniers scripts d’instantané et la documentation associée à partir de [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). Pour connaître les étapes répertoriées précédemment, consultez les [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

### <a name="consideration-for-mcod-scenarios"></a>Considération des scénarios MCOD
Si vous exécutez un [scénario MCOD](https://launchpad.support.sap.com/#/notes/1681092) avec plusieurs instances SAP HANA sur une unité de grande instance HANA, vous obtenez des volumes de stockage distincts pour chacune des instances SAP HANA. Pour plus d’informations sur MDC et d’autres considérations, consultez les points importants à retenir concernant les [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Étape 1 : Installer le client SAP HANA HDB

Le système d’exploitation Linux installé dans SAP HANA sur Azure (grandes instances) inclut les dossiers et scripts nécessaires pour exécuter des captures instantanées de stockage SAP HANA à des fins de sauvegarde et de récupération d’urgence. Vérifiez s’il existe des versions plus récentes dans [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). 

Il vous incombe d’installer le client SAP HANA HDB sur les unités de grandes instances HANA quand vous installez SAP HANA.

### <a name="step-2-change-the-etcsshssh_config"></a>Étape 2 : Modifier la configuration /etc/ssh/ssh\_

Cette étape est décrite dans la procédure d’activation de la communication avec le stockage concernant les [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-3-create-a-public-key"></a>Étape 3 : Créer une clé publique

Pour activer l’accès aux interfaces de capture instantanée de stockage de votre locataire de grandes instances HANA, connectez-vous à l’aide d’une clé publique. 

Sur le premier serveur SAP HANA sur Azure (grandes instances) de votre locataire, créez une clé publique pour accéder à l’infrastructure de stockage. Cette clé publique garantit qu’aucun mot de passe n’est requis pour la connexion aux interfaces de capture instantanée de stockage Vous n’avez pas non plus à mettre à jour les informations d’identification de mot de passe avec une clé publique. 

Pour générer une clé publique, consultez la procédure d’activation de la communication avec le stockage concernant les [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-4-create-an-sap-hana-user-account"></a>Étape 4 : Créer un compte d'utilisateur SAP HANA

Pour lancer la création de captures instantanées SAP HANA, créez dans SAP HANA un compte d’utilisateur que les scripts de capture instantanée de stockage peuvent utiliser. Créez un compte d’utilisateur SAP HANA dans SAP HANA Studio à cette fin. L’utilisateur doit être créé sous le SYSTEMDB et *non* sous la base de données SID pour MDC. Dans l’environnement de conteneur unique, l’utilisateur est créé dans la base de données locataire. Ce compte doit disposer des privilèges **Backup Admin** (administration des sauvegardes) et **Catalog Read** (lecture du catalogue). 

Pour configurer et utiliser un compte d’utilisateur, consultez la procédure d’activation de la communication avec SAP HANA dans [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Étape 5 : Autoriser le compte d'utilisateur SAP HANA

Dans cette étape, vous autorisez le compte d’utilisateur SAP HANA que vous avez créé, afin que les scripts n’aient pas besoin d’envoyer des mots de passe au moment de l’exécution. La commande SAP HANA `hdbuserstore` permet la création d’une clé utilisateur SAP HANA. La clé est stockée sur un ou plusieurs nœuds SAP HANA. Cette clé utilisateur permet à l’utilisateur d’accéder à SAP HANA sans avoir à gérer les mots de passe à partir du processus d’utilisation de scripts décrit plus loin dans cet article.

>[!IMPORTANT]
>Exécutez ces commandes de configuration avec le même contexte utilisateur que les commandes de capture instantanée. Sinon, les commandes de capture instantanée ne fonctionnent pas correctement.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Étape 6 : Obtenir les scripts de capture instantanée, configurer les captures instantanées, et tester la configuration et la connectivité

Téléchargez la version la plus récente des scripts depuis [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1). La méthode d’installation des scripts a changé avec la version 4.1 des scripts. Pour plus d’informations, consultez la procédure d’activation de la communication avec SAP HANA concernant les [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Pour connaître la séquence exacte de commandes, consultez la procédure d’installation facile des outils de capture instantanée (par défaut) pour les [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Nous recommandons l’utilisation de l’installation par défaut. 

Pour effectuer une mise à niveau à partir de la version 3.x vers 4.1, consultez la procédure de mise à niveau d’une installation existante concernant les [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Pour désinstaller l’ensemble d’outils 4.1, consultez la procédure de désinstallation des [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

N’oubliez pas d’exécuter les étapes permettant de terminer l’installation des dans [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

L’objectif des différents scripts et fichiers une fois installés est décrit dans la section « What are these snapshot tools » (Quels sont ces outils de capture instantanée ?) relative aux [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Avant de configurer les outils de capture instantanée, vérifiez également que vous avez correctement configuré les paramètres et les emplacements de sauvegarde HANA. Pour plus d’informations, consultez la configuration SAP HANA concernant les [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

La configuration de l’ensemble d’outils de capture instantanée est décrite dans la section « Fichier de configuration - HANABackupCustomerDetails.txt » relative aux [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-connectivity-with-sap-hana"></a>Tester la connectivité avec SAP HANA

Après avoir placé toutes les données de configuration dans le fichier *HANABackupCustomerDetails.txt*, vérifiez si les configurations des données d’instance HANA sont correctes. Utilisez le script `testHANAConnection`, qui est indépendant d’une configuration Scale-up ou Scale-out SAP HANA.

Pour plus d’informations, consultez la procédure de vérification de la connectivité avec SAP HANA concernant les [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-storage-connectivity"></a>Tester la connectivité du stockage

L’étape de test suivante consiste à vérifier la connectivité au stockage en fonction des données que vous avez placées dans le fichier de configuration *HANABackupCustomerDetails.txt*. Exécutez ensuite une capture instantanée de test. Avant d’exécuter la commande `azure_hana_backup`, vous devez exécuter ce test. Pour connaître la séquence de commandes pour ce test, consultez la procédure de vérification de la connectivité avec le stockage - testStorageSnapshotConnection concernant les [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Une fois que la connexion aux interfaces de machine virtuelle de stockage est établie, le script passe à la phase 2 et crée une capture instantanée de test, comme indiqué ici pour une configuration de SAP HANA à trois nœuds avec scale-out parallèle.

Si la capture instantanée de test est correctement exécutée avec le script, vous pouvez planifier les captures instantanées de stockage réelles. Si elle a échoué, identifiez le problème avant de poursuivre. La capture instantanée de test doit être conservée jusqu’à ce que les premières captures instantanées réelles soient effectuées.


### <a name="step-7-perform-snapshots"></a>Étape 7 : Effectuer des captures instantanées

Une fois toutes les étapes de préparation effectuées, vous pouvez commencer à configurer et à planifier les captures instantanées du stockage réel. Le script à planifier fonctionne avec les configurations de SAP HANA avec montée en puissance et montée en puissance parallèle. Pour une exécution périodique et régulière de la planification du script de sauvegarde, planifiez le script à l’aide de l’utilitaire cron. 

Pour connaître la syntaxe de commande exacte et les fonctionnalités, consultez la procédure de sauvegarde d’une capture instantanée - azure_hana_backup concernant les [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

Lorsque le script `azure_hana_backup` est exécuté, il crée la capture instantanée de stockage selon les trois phases suivantes :

1. Il crée une capture instantanée SAP HANA.
1. Il crée une capture instantanée de stockage.
1. Il supprime la capture instantanée SAP HANA créée avant l’exécution de la capture instantanée de stockage.

Pour exécuter le script, appelez-le depuis le dossier de l’exécutable HDB dans lequel il a été copié. 

La période de rétention est administrée avec le nombre de captures instantanées soumis en tant que paramètre lorsque vous exécutez le script. La durée couverte par les captures instantanées de stockage dépend de deux facteurs : la période d’exécution et le nombre de captures instantanées envoyées en tant que paramètre lors de l’exécution du script. 

Si le nombre de captures instantanées conservées dépasse le nombre spécifié en tant que paramètre dans l’appel du script, la plus ancienne capture instantanée de stockage portant la même étiquette est supprimée avant l’exécution d’une nouvelle capture instantanée. Le nombre que vous indiquez en tant que dernier paramètre de l’appel correspond au nombre que vous pouvez utiliser pour contrôler le nombre de captures instantanées qui sont conservées. Ce nombre vous permet également de contrôler indirectement l’espace disque utilisé pour les captures instantanées. 


## <a name="snapshot-strategies"></a>Stratégies de capture instantanée
La fréquence des captures instantanées des différents types varie selon que vous utilisez ou non la fonctionnalité de récupération d’urgence des grandes instances HANA. Cette fonctionnalité repose sur les captures instantanées de stockage, qui peuvent avoir des recommandations spéciales pour la fréquence et les périodes d’exécution des captures instantanées de stockage. 

Dans les considérations et recommandations ci-après, nous supposons que vous n’utilisez *pas* la fonctionnalité de récupération d’urgence proposée par les grandes instances HANA. À la place, vous utilisez les captures instantanées de stockage comme un moyen d’obtenir des sauvegardes et de pouvoir fournir une récupération jusqu’à une date et heure couvrant les 30 derniers jours. Étant donné les limites liées au nombre de captures instantanées et à l’espace, tenez compte des exigences suivantes :

- Temps de récupération pour la récupération jusqu’à une date et heure.
- Espace utilisé.
- Objectifs de point et de délai de récupération pour une récupération d’urgence potentielle après un incident.
- Exécution éventuelles de sauvegardes de base de données complètes HANA pour les disques. Chaque fois qu’une sauvegarde de base de données complète est effectuée pour les disques ou pour l’interface **backint**, l’exécution des captures instantanées de stockage échoue. Si vous prévoyez d’exécuter des sauvegardes de base de données complètes reposant sur des captures instantanées de stockage, assurez-vous que l’exécution des captures instantanées de stockage est désactivée pendant cette période.
- Nombre de captures instantanées par volume, qui est limité à 250.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Si vous n’utilisez pas la fonctionnalité de récupération d’urgence des grandes instances HANA, la période de capture instantanée est moins fréquente. Dans ces cas, effectuez les captures instantanées combinées sur /hana/data et /hana/shared (/usr/sap compris) toutes les 12 ou 24 heures. Conservez les captures instantanées pendant un mois. Il en va de même pour les captures instantanées du volume de sauvegarde de fichier journal. Les sauvegardes de fichier journal SAP HANA sont exécutées toutes les 5 à 15 minutes en fonction du volume de sauvegarde de fichier journal.

Les captures instantanées de stockage planifiées sont les mieux exécutées avec cron. Utilisez le même script pour la totalité des sauvegardes et des besoins en matière de récupération d’urgence. Modifiez les entrées du script en fonction des différentes heures de sauvegarde demandées. Ces captures instantanées sont toutes planifiées différemment dans cron selon leur durée d’exécution : toutes les heures, toutes les 12 heures, une fois par jour ou une fois par semaine. 

Voici un exemple de planification cron dans /etc/crontab :
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
Dans l’exemple précédent, une capture instantanée horaire combinée couvre les volumes contenant les emplacements /hana/data et /hana/shared/SID (/usr/sap compris). Utilisez ce type de capture instantanée pour une récupération plus rapide pendant les deux derniers jours. En outre, il existe une capture instantanée quotidienne sur ces volumes. Vous avez ainsi deux jours couverts par les captures instantanées horaires, ainsi que quatre semaines couvertes par les captures instantanées quotidiennes. Le volume de sauvegarde de fichier journal est également sauvegardé une fois par jour. Ces sauvegardes sont conservées pendant quatre semaines. 

Comme le montre la troisième ligne de crontab, la sauvegarde du journal des transactions HANA est planifiée pour s’exécuter toutes les 5 minutes. Les heures de début des différents travaux cron qui exécutent des captures instantanées de stockage sont décalées. De cette façon, les captures instantanées ne s’exécutent pas toutes à la fois à un moment donné. 

Dans l’exemple suivant, vous effectuez une capture instantanée combinée qui couvre les volumes contenant les emplacements /hana/data et /hana/shared/SID (/usr/sap compris) toutes les heures. Vous conservez ces captures instantanées pendant deux jours. Les captures instantanées des volumes de sauvegarde de fichier journal s’exécutent toutes les 5 minutes et sont conservées pendant 4 heures. Comme avant, la sauvegarde du fichier journal HANA est planifiée pour s’exécuter toutes les 5 minutes. 

La capture instantanée du volume de sauvegarde de fichier journal s’effectue dans un délai de 2 minutes après le démarrage de la sauvegarde du fichier journal. Normalement, la sauvegarde du fichier journal SAP HANA doit se terminer dans cet intervalle de 2 minutes. Une fois encore, le volume contenant le numéro d’unité logique de démarrage est sauvegardé une fois par jour par une capture instantanée de stockage et est conservé pendant quatre semaines.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

Le graphique suivant illustre les séquences de l’exemple précédent. Le numéro d’unité logique de démarrage est exclu.

![Relation entre les sauvegardes et les captures instantanées](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA effectue des écritures régulières sur le volume /hana/log pour documenter les modifications validées dans la base de données. Régulièrement, SAP HANA écrit un point de sauvegarde dans le volume /hana/data. Comme spécifié dans crontab, une sauvegarde de fichier journal SAP HANA s’exécute toutes les 5 minutes. 

Une capture instantanée SAP HANA est également exécutée toutes les heures suite au déclenchement d’une capture instantanée de stockage combinée sur les volumes /hana/data et /hana/shared/SID. Une fois que la capture instantanée HANA a réussi, la capture instantanée de stockage combinée est exécutée. Comme indiqué dans crontab, la capture instantanée de stockage sur le volume /hana/logbackup s’exécute toutes les 5 minutes, environ 2 minutes après la sauvegarde de fichier journal HANA.

> 

>[!IMPORTANT]
> L’utilisation de captures instantanées de stockage pour les sauvegardes SAP HANA n’est précieuse que quand ces captures sont effectuées conjointement avec des sauvegardes de fichier journal SAP HANA. Ces sauvegardes de fichier journal doivent couvrir les périodes entre les captures instantanées de stockage. 

Si vous vous êtes engagé auprès des utilisateurs à assurer une récupération jusqu’à 30 jours, vous devez disposer des éléments suivants :

- Dans des cas extrêmes, accédez à une capture instantanée de stockage combinée sur les volumes /hana/data et /hana/shared/SID remontant à 30 jours. 
- Vérifiez que les sauvegardes de fichier journal contiguës couvrent la période entre des captures instantanées de stockage combinées. La capture instantanée la plus ancienne du volume de sauvegarde de fichier journal doit donc remonter à 30 jours, ce qui n’est pas le cas si vous copiez les sauvegardes de journal dans un autre partage NFS situé sur le Stockage Azure. Auquel cas, vous pourriez récupérer les anciennes sauvegardes de fichier journal, de ce partage NFS.

Pour tirer parti des captures instantanées de stockage et de la réplication de stockage éventuelle des sauvegardes de fichier journal, modifiez l’emplacement où SAP HANA écrit les sauvegardes de fichier journal. Vous pouvez effectuer ce changement dans HANA Studio. 

Bien que SAP HANA sauvegarde automatiquement des segments de journal complets, indiquez un intervalle de sauvegarde de fichier journal de manière à ce qu’il soit déterministe. Cela est particulièrement vrai si vous utilisez l’option de récupération d’urgence, car on souhaite en général exécuter des sauvegardes de fichier journal selon une période déterministe. Dans le cas suivant, 15 minutes sont définies comme intervalle de sauvegarde du fichier journal.

![Planifier les sauvegardes de journaux d’activité SAP HANA dans SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Vous pouvez également choisir d’effectuer des sauvegardes plus fréquemment que toutes les 15 minutes. La valeur paramétrée est souvent inférieure avec la fonctionnalité de récupération d’urgence des grandes instances HANA. Certains clients effectuent des sauvegardes de fichier journal toutes les 5 minutes.

Si la base de données n’a jamais été sauvegardée, la dernière étape consiste à effectuer une sauvegarde de base de données basée sur un fichier afin de créer une entrée de sauvegarde unique devant figurer dans le catalogue de sauvegarde. Dans le cas contraire, SAP HANA ne peut pas lancer vos sauvegardes de journaux spécifiées.

![Créer une sauvegarde basée sur un fichier pour créer une entrée de sauvegarde unique](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Après avoir exécuté correctement vos premières captures instantanées de stockage, supprimez la capture instantanée de test qui a été exécutée à l’étape 6. Pour plus d’informations, consultez la procédure de suppression des captures instantanées de test - removeTestStorageSnapshot concernant les [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Surveiller le nombre et la taille des captures instantanées sur le volume de disque

Sur un volume de stockage, vous pouvez surveiller le nombre de captures instantanées et l’espace de stockage consommé par ces captures. La commande `ls` n’affiche pas le répertoire des captures instantanées ni les fichiers. La commande du système d’exploitation Linux `du` donne des détails sur ces captures instantanées de stockage, car elles sont stockées sur les mêmes volumes. Utilisez la commande avec les options suivantes :

- `du –sh .snapshot`: cette option fournit le nombre total de captures instantanées dans le répertoire de captures instantanées.
- `du –sh --max-depth=1`: cette option répertorie toutes les captures instantanées enregistrées dans le dossier **.snapshot** et la taille de chaque capture instantanée.
- `du –hc`: cette option fournit la taille totale de toutes les captures instantanées.

Utilisez ces commandes pour vous assurer que les captures instantanées créées et stockées ne consomment pas la totalité du stockage sur les volumes.

>[!NOTE]
>Les captures instantanées du numéro d’unité logique de démarrage ne sont pas visibles avec les commandes précédentes.

### <a name="get-details-of-snapshots"></a>Obtenir les détails des captures instantanées
Pour obtenir plus d’informations sur les captures instantanées, utilisez le script `azure_hana_snapshot_details`. Vous pouvez exécuter ce script dans n’importe quel emplacement à la condition qu’un serveur actif se trouve à l’emplacement de la récupération d’urgence. Le script fournit les informations ventilées suivantes pour chaque volume contenant des captures instantanées : 
   * Taille de toutes les captures instantanées dans un volume
   * Chaque capture instantanée dans ce volume inclut les informations suivantes : 
      - Nom de la capture instantanée 
      - Heure de création 
      - Taille de la capture instantanée
      - Fréquence de la capture instantanée
      - ID de sauvegarde HANA associé à cette capture instantanée, le cas échéant

Pour connaître la syntaxe des commandes et des sorties, découvrez comment obtenir la liste des captures instantanées (azure_hana_snapshot_details) concernant les [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Réduire le nombre de captures instantanées sur un serveur

Comme expliqué précédemment, vous pouvez réduire le nombre de captures instantanées stockées portant une certaine étiquette. Les deux derniers paramètres de la commande permettant de lancer une capture instantanée correspondent à l’étiquette et au nombre de captures instantanées à conserver.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

Dans l’exemple précédent, l’étiquette de l’instantané est **dailyhana**. Le nombre de captures instantanées portant cette étiquette à conserver est de **28**. Afin de faire face au problème de la consommation d’espace disque, vous pouvez vouloir réduire le nombre de captures instantanées stockées. Un moyen simple de réduire le nombre de captures instantanées à 15, par exemple, consiste à exécuter le script en définissant le dernier paramètre sur la valeur **15** :

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Si vous exécutez le script défini avec ce paramètre, le nombre total de captures instantanées, y compris la nouvelle capture instantanée de stockage, est 15. Les 15 captures instantanées les plus récentes sont conservées, tandis que les 15 captures instantanées plus anciennes sont supprimées.

 >[!NOTE]
 > Ce script ne réduit le nombre de captures instantanées que s’il existe des captures instantanées datant de plus d’une heure. Le script ne supprime pas les captures instantanées datant de moins d’une heure. Ces restrictions sont associées à la fonctionnalité de récupération d’urgence disponible en option.

Si vous ne souhaitez plus conserver un ensemble de captures instantanées portant le préfixe de sauvegarde **dailyhana** dans les exemples de syntaxe, exécutez le script avec **0** comme valeur de rétention. Toutes les captures instantanées correspondant à cette étiquette sont alors supprimées. La suppression de toutes les captures instantanées a une incidence sur la fonctionnalité de récupération d’urgence des grandes instances HANA.

L’autre possibilité pour supprimer certaines captures instantanées consiste à utiliser le script `azure_hana_snapshot_delete`. Ce script est conçu pour supprimer une capture instantanée ou un ensemble de captures instantanées à l’aide de l’ID de sauvegarde HANA tel que figurant dans HANA Studio ou du nom de la capture instantanée. À l’heure actuelle, l’ID de sauvegarde est uniquement lié aux captures instantanées créées pour le type de capture instantanée **hana**. Les sauvegardes de capture instantanée de type **logs** ou **boot** n’effectuent pas une capture instantanée SAP HANA, et il n’existe donc aucun ID de sauvegarde pour ces captures instantanées. Si le nom de la capture instantanée est entré, le script recherche sur les différents volumes toutes les captures instantanées qui correspondent au nom entré. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Pour plus d’informations sur le script, consultez la procédure de suppression d’un instantané - azure_hana_snapshot_delete concernant les [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Exécutez le script en tant qu’utilisateur **root**.

>[!IMPORTANT]
>Si des données n’existent que dans la capture instantanée que vous envisagez de supprimer, la suppression de celle-ci entraîne la perte définitive de ces données.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Restauration au niveau du fichier à partir d’une capture instantanée de stockage

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
Vous pouvez accéder aux captures instantanées **hana** et **logs** directement sur les volumes dans le répertoire **.snapshot**. Il existe un sous-répertoire pour chaque capture instantanée. Copiez chaque fichier dans l’état où il se trouvait au moment de la capture instantanée, depuis ce sous-répertoire vers l’arborescence. 

Dans la version actuelle du script, il n’existe *aucun* script de restauration fourni pour la restauration de capture instantanée en libre-service. La restauration de capture instantanée est possible dans le cadre des scripts de récupération d’urgence en libre-service sur le site de récupération d’urgence pendant le basculement. Pour restaurer la capture instantanée de votre choix à partir des captures instantanées disponibles, vous devez contacter l’équipe des opérations Microsoft en ouvrant une demande de service.

>[!NOTE]
>La restauration d’un fichier unique ne fonctionne pas pour les captures instantanées du numéro d’unité logique de démarrage indépendant du type d’unités de grande instance HANA. Le répertoire **.snapshot** n’est pas exposé dans le numéro d’unité logique de démarrage. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Récupération jusqu’à la capture instantanée HANA la plus récente

En cas d’interruption de la production, vous pouvez lancer le processus de récupération à partir d’une capture instantanée de stockage en signalant un incident client auprès du support technique de Microsoft Azure. Le problème est de nature urgente si des données sont supprimées d’un système de production et si le seul moyen de les récupérer est de restaurer la base de données de production.

Dans un cas différent, une récupération jusqu’à une date et heure peut ne pas avoir de caractère d’urgence et être planifiée plusieurs jours à l’avance. Vous pouvez planifier cette récupération avec SAP HANA sur Azure au lieu de signaler un problème prioritaire. Par exemple, vous pourriez planifier la mise à niveau du logiciel SAP en appliquant un nouveau package d’améliorations, et vouloir ensuite revenir à une capture instantanée correspondant à l’état du système avant sa mise à niveau avec le package d’améliorations.

Avant de soumettre la demande, vous devez effectuer certaines tâches de préparation. L’équipe SAP HANA sur Azure peut alors traiter la demande et fournir les volumes restaurés. Vous pourrez ensuite restaurer la base de données HANA à l’aide des captures instantanées.

Pour connaître les possibilités d’obtention d’une capture instantanée restaurée avec le nouvel ensemble d’outils, consultez la procédure de restauration d’un instantané dans le [guide de récupération manuelle de SAP HANA sur Azure à partir d’une capture instantanée de stockage](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Pour préparer la demande, procédez comme suit.

1. Déterminez la capture instantanée à restaurer. Sauf indication contraire, seul le volume hana/data est restauré. 

1. Arrêtez l’instance HANA.

   ![Arrêter l’instance HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Démontez les volumes de données sur chaque nœud de base de données HANA. Si les volumes de données sont toujours montés sur le système d’exploitation, la restauration de la capture instantanée échoue.

   ![Démonter les volumes de données sur chaque nœud de base de données HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Ouvrez une demande de support Azure et incluez des instructions sur la restauration d’une capture instantanée spécifique :

   - Pendant la restauration : le service SAP HANA sur Azure peut vous demander de participer à une téléconférence de coordination, de vérification et de confirmation afin de vérifier que la capture instantanée de stockage est restaurée correctement. 

   - Après la restauration : le service SAP HANA sur Azure vous informe lorsque la capture instantanée de stockage est restaurée.

1. Une fois le processus de restauration terminé, remontez tous les volumes de données.

   ![Remonter tous les volumes de données](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Une autre possibilité pour obtenir des fichiers de données SAP HANA récupérés à partir d’une capture instantanée de stockage, par exemple, est décrite à l’étape 7 du [guide de récupération manuelle de SAP HANA sur Azure à partir d’une capture instantanée de stockage](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Pour effectuer une restauration à partir d’une sauvegarde de captures instantanées, consultez le [guide de récupération manuelle de SAP HANA sur Azure à partir d’une capture instantanée de stockage](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

>[!Note]
>Si votre capture instantanée a été restaurée par des opérations Microsoft, vous n’avez pas besoin de l’étape 7.


### <a name="recover-to-another-point-in-time"></a>Récupération jusqu’à un autre point dans le temps
Pour effectuer une restauration à un certain point dans le temps, consultez la procédure de récupération de base de données pour le point suivant dans le temps, dans le [guide de récupération manuelle de SAP HANA sur Azure à partir d’une capture instantanée de stockage](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


## <a name="next-steps"></a>Étapes suivantes
- Voir [Disaster Recovery principles and preparation](hana-concept-preparation.md) (Principes de récupération d’urgence et préparation).
