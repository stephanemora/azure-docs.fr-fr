---
title: Migrer des données de la gamme StorSimple 5000-7000 vers Azure File Sync | Microsoft Docs
description: Décrit comment migrer des données de la gamme StorSimple 5000-7000 vers Azure File Sync (AFS).
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: b46e9ee8fc3e14981a01cc2425a8ce55d06c5a9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "65150736"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-azure-file-sync"></a>Migrer des données d’un appareil StorSimple 5000-7000 vers Azure File Sync

> [!IMPORTANT]
> La fin du support de la série StorSimple 5000/7000 interviendra le 9 juillet 2019. Nous recommandons aux clients de la série StorSimple 5000/7000 de migrer vers l’une des alternatives décrites dans le document.

La migration des données désigne le processus de déplacement des données d’un emplacement de stockage à un autre. Cela implique de réaliser une copie exacte des données actuelles d’une organisation d’un appareil à l’autre (de préférence sans interrompre ou désactiver les applications actives), puis de rediriger toutes les activités d’entrées/de sorties vers le nouvel appareil. 

En juillet 2019, les appareils de stockage de la gamme StorSimple 5000 et 7000 auront atteint leur durée de vie. Cela signifie que Microsoft ne sera plus en mesure de prendre en charge les matériels et logiciels associés à la gamme StorSimple 5000/7000 après juillet 2019. Les clients qui utilisent ces appareils doivent migrer leurs données StorSimple vers d’autres solutions de stockage hybride dans Azure. Cet article concerne la migration des données d’un appareil de la gamme StorSimple 5000/7000 vers Azure File Sync (AFS).

## <a name="intended-audience"></a>Public concerné

Cet article est destiné aux professionnels des technologies de l’information et aux travailleurs du savoir ayant pour responsabilité le déploiement et la gestion des appareils de la gamme StorSimple 5000/7000 dans le centre de données. Les clients utilisant leurs appareils StorSimple pour les charges de travail du serveur de fichiers (avec Windows Server) peuvent trouver ce chemin de migration particulièrement attrayant. Si vous estimez que les fonctionnalités d’Azure File Sync sont adaptées pour votre organisation, cet article peut vous aider à comprendre comment passer de StorSimple à ces solutions.

## <a name="migration-considerations"></a>Considérations relatives à la migration

Ce processus fonctionne pour les clients ayant configuré un partage de fichiers Windows à l’aide d’un volume StorSimple pour le stockage. La migration des données de StorSimple 5000/7000 à Azure File Sync implique la conversion de cet emplacement de partage de fichiers vers un [point de terminaison de serveur](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning), puis l’utilisation en local des lecteurs attachés en tant que point de terminaison supplémentaire, qui deviendra par la suite votre nouvel emplacement. 

Au cours de la migration vers AFS, les points suivants doivent être pris en compte :

1. Actuellement, Azure Files a une restriction de 5 To/partage. Vous pouvez contourner cette restriction en utilisant Azure File Sync avec des données réparties sur plusieurs partages de fichiers Azure. Pour plus d’informations, consultez le [modèle de croissance des données pour le déploiement d’Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-planning).
2. Cette migration télécharge le jeu de données principal sur un appareil local, car la copie des données est effectuée à partir de l’appareil local. Vérifiez que vous avez suffisamment de bande passante pour prendre en charge ce transfert.
3. Ce processus ne conserve pas les captures instantanées qui ont déjà été créées. Seules les données principales sont migrées. En outre, le processus ne conserve pas les modèles de bande passante ou les stratégies de sauvegarde associés. [Utilisez Sauvegarde Azure](https://docs.microsoft.com/azure/backup/backup-azure-files) pour configurer les stratégies de sauvegarde après la migration des données sur le partage de fichiers Azure.
4. StorSimple fournit le matériel premier tiers. Toutefois, avec Azure Files/Azure File Sync, vous utilisez votre propre matériel Windows Server local en tant que cache local. Vous devez vous assurer que vous disposez de la capacité de stockage suffisante pour conserver le jeu de données de votre choix en local. Pour plus d’informations sur la hiérarchisation et la définition de la cible d’espace disponible, consultez l’article expliquant comment [créer un point de terminaison de serveur lors du déploiement d’Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal). 
5. Consultez la [tarification pour Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/) comme elle diffère de StorSimple. AFS ne dispose pas de fonctionnalités de déduplication et de compression comme StorSimple.

## <a name="migration-prerequisites"></a>Prérequis pour la migration

Vous trouverez ci-dessous les prérequis pour la migration d’appareils d’ancienne génération StorSimple de la gamme 5000 ou 7000 vers Azure File Sync. Avant de commencer, assurez-vous de satisfaire les exigences suivantes :

- Vous avez accès à un appareil de la gamme StorSimple 5000/7000 exécutant une version logicielle v2.1.1.518 ou ultérieure. Les versions antérieures ne sont pas prises en charge. L’angle supérieur droit de l’interface utilisateur web de votre appareil StorSimple doit afficher la version du logiciel en cours d’exécution.  
    Si votre appareil n’exécute pas la version v2.1.1.518, mettez à niveau votre système vers la version minimale requise. Pour des instructions détaillées, consultez le [guide de mise à niveau du système vers v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
- Vérifiez s’il existe des travaux de sauvegarde actifs en cours d’exécution sur l’appareil source. Cela inclut notamment les tâches sur l’hôte de la console de protection des données StorSimple. Patientez jusqu’à ce que les tâches en cours soient entièrement terminées. 
- Accédez à un hôte Windows Server connecté à votre appareil de la gamme StorSimple 5000-7000. L’hôte doit exécuter une version de Windows Server prise en charge comme décrit dans [Interopérabilité d’Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning).
- Les volumes StorSimple sont montés sur l’hôte et contiennent des partages de fichiers.
- L’hôte a une capacité de stockage local suffisante pour contenir vos données en cache local.
- L’accès au niveau propriétaire à l’abonnement Azure que vous allez utiliser pour déployer Azure File Sync. Vous pouvez rencontrer des problèmes lors de la création d’un point de terminaison cloud pour votre groupe de synchronisation si vous n’avez pas d’autorisations de niveau propriétaire ou administrateur.
- Accédez à un [compte de stockage à usage général v2](https://docs.microsoft.com/azure/storage/common/storage-account-overview) avec un partage de fichiers Azure vers lequel vous souhaitez effectuer la synchronisation. Pour plus d’informations, consultez la rubrique [Création d’un compte de stockage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) .
  - L’article [Créer un partage de fichiers via le portail Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).

## <a name="migration-process"></a>Processus de migration

La migration des données de la gamme StorSimple 5000-7000 vers AFS est un processus en deux étapes :
1.  Répliquez les données du serveur de fichiers local où les volumes StorSimple sont montés sur un partage Azure Files.  La réplication est effectuée via un agent AFS que vous installez.
2.  Mettez sous tension l’appareil StorSimple. Les disques locaux font ensuite office de cache local.

### <a name="migration-steps"></a>Étapes de la migration

Procédez comme suit pour migrer le partage de fichiers Windows configuré sur les volumes StorSimple vers un partage Azure File Sync. 
1.  Effectuez ces étapes sur le même hôte Windows Server que celui où les volumes StorSimple sont montés ou utilisez un autre système. 
    - [Préparez Windows Server pour une utilisation avec Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#prepare-windows-server-to-use-with-azure-file-sync).
    - [Installez l’agent Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#install-the-azure-file-sync-agent).
    - [Déployez le service de synchronisation de stockage](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#deploy-the-storage-sync-service). 
    - [Inscrivez Windows Server au service de synchronisation de stockage](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#register-windows-server-with-storage-sync-service). 
    - [Créez un groupe de synchronisation et un point de terminaison cloud](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#create-a-sync-group-and-a-cloud-endpoint). Des groupes de synchronisation doivent être créés pour chaque partage de fichiers Windows à migrer depuis l’hôte.
    - [Créez un point de terminaison de serveur](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal#create-a-server-endpoint). Pour le chemin d’accès, spécifiez celui du volume StorSimple qui contient vos données de partage de fichiers. Par exemple, si le volume StorSimple est le lecteur `J`, et si vos données se trouvent dans `J:/<myafsshare>`, ajoutez ce chemin d’accès en tant que point de terminaison de serveur. Laissez l’élément **Hiérarchisation** défini sur **Désactivée**.
2.  Attendez la fin de la synchronisation du serveur de fichiers. Pour chaque serveur dans un groupe de synchronisation donné, vérifiez que :
    - Les horodatages de la dernière tentative de synchronisation pour le chargement téléchargement sont récents.
    - L’état est affiché en vert pour le chargement et le téléchargement.
    - Le champ **Activité de synchronisation** indique un nombre minime ou nul de fichiers restant à synchroniser.
    - Le champ **Fichiers non synchronisés** présente la valeur 0 pour le chargement et pour le téléchargement.
    Pour savoir quand la synchronisation du serveur est terminée, consultez [Résoudre les problèmes de synchronisation de fichiers Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#how-do-i-know-if-my-servers-are-in-sync-with-each-other). La synchronisation peut prendre entre plusieurs heures et plusieurs jours en fonction de la taille de vos données et de la bande passante. Une fois la synchronisation terminée, toutes vos données se trouvent en sécurité dans le partage de fichiers Azure. 
3.  Accédez aux partages sur les volumes StorSimple. Sélectionnez un partage, cliquez avec le bouton droit et choisissez **Propriétés**. Notez que les autorisations de partage se trouvent sous **Sécurité**. Ces autorisations devront être appliquées manuellement au nouveau partage lors d’une étape ultérieure.
4.  Les étapes suivantes varient en fonction de si vous utilisez le même hôte Windows Server ou un hôte différent.

    Ignorez cette étape et passez à l’étape suivante si vous utilisez un autre hôte Windows Server. Si vous utilisez le même serveur de fichiers Windows pour AFS, un temps d’arrêt de quelques minutes a lieu. 
    - **Début du temps d’arrêt** : supprimez le point de terminaison de serveur que vous avez créé au cours de *l’étape 1F*. 
    - Créez un point de terminaison de serveur avec le chemin d’accès où vous souhaitez que les données qui s’y trouvent aillent.
    - Une fois que le point de terminaison de serveur affiche l’état « Sain » (cela peut prendre quelques minutes), vous verrez les données dans ce nouvel emplacement. Vous pouvez désormais configurer votre hôte Windows Server pour traiter les fichiers à partir de ce nouvel emplacement. - **Fin du temps d’arrêt**.
5.  Si vous utilisez un autre serveur de fichiers Windows pour Azure File Sync, il n’y aura aucun temps d’arrêt. 
    - Ajoutez un autre point de terminaison de serveur au chemin d’accès du stockage local que vous êtes prêt à utiliser en tant que cache à la place de l’appareil StorSimple. 
    - Vous serez en mesure de voir les fichiers dans le nouveau serveur après quelques minutes. Vous êtes libre de basculer de votre appareil StorSimple à ce nouvel emplacement sur l’hôte à tout moment.

    > [!TIP] 
    > Envisagez de configurer ce nouveau partage de fichiers avec le même nom et le même chemin d’accès que celui qu’il remplace afin d’éviter toute perturbation. Si vous utilisez DFS-N, vous devrez peut-être apporter des modifications à la configuration.
6.  Reconfigurez les autorisations de partage comme indiqué dans *l’étape 3*.

Si vous rencontrez des problèmes lors de la migration de données, veuillez [contacter le Support Microsoft](storsimple-8000-contact-microsoft-support.md). 



## <a name="next-steps"></a>Étapes suivantes

Si AFS n’est pas la solution idéale pour vous, découvrez comment [migrer des données d’un appareil de la gamme StorSimple 5000-7000 vers un appareil de la gamme 8000](storsimple-8000-migrate-from-5000-7000.md).

