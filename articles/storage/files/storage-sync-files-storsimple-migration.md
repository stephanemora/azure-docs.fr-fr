---
title: Migrer à partir de StorSimple vers Azure File Sync
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: fauhse
ms.subservice: files
description: StorSimple est un produit en fin de vie et Azure File Sync est la solution vers laquelle migrer. Découvrez le concept de migration et contactez-nous à l’adresse AzureFiles@microsoft.com pour une aide personnalisée sur la migration.
ms.openlocfilehash: edad4d1c6be2c39dbf8150b6ab8979ae3924fb53
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666537"
---
# <a name="storsimple-migration-to-azure-file-sync"></a>Migration depuis StorSimple vers Azure File Sync

StorSimple est un produit Microsoft abandonné. La prise en charge étendue de ce produit et de son service cloud durera jusqu’à la fin 2022.
Il est important de commencer à planifier une migration à partir de StorSimple dès à présent.

Le service Azure à long terme stratégique et par défaut vers lequel les appliances StorSimple peuvent être migrées est Azure File Sync. Il s’agit d’un service Azure mis à la disposition générale, qui offre beaucoup plus de fonctionnalités que StorSimple.

## <a name="full-cloud-side-migration-with-limited-downtime"></a>Migration complète côté cloud avec temps d’arrêt limité
Cet article décrit le concept de mise en œuvre de la migration.
Il est impératif de noter que les clients ayant besoin d’une migration à partir de StorSimple et vers Azure File Sync n’ont pas besoin de procéder de manière autonome.

> [!IMPORTANT]
> Microsoft s’engage à aider les clients lors de la migration. Contactez-nous par e-mail à l’adresse AzureFiles@microsoft.com pour obtenir un plan de migration personnalisé ainsi qu’une assistance pendant la migration.

## <a name="migration-approach"></a>Approche de migration
La migration vers Azure File Sync démarre dans le cloud avec un impact minimal sur l’infrastructure locale et des temps d’arrêt limités.
Le concept ci-dessous est destiné aux appliances StorSimple de la série 8000.
Si vous avez besoin d’effectuer une migration à partir de la série StorSimple 7000, la première étape implique une mise à niveau gratuite vers un appareil de prêt de la série 8000 correspondant de Microsoft.
Contactez-nous à l’adresse AzureFiles@microsoft.com afin que nous vous aidions à organiser un nombre approprié d’appareils de prêt.

### <a name="general-approach"></a>Approche générale
![Alt](media/storage-sync-files-storsimple-migration/storsimple-docs-overview-concept.png "Illustration de la migration côté cloud par le biais d’une appliance virtuelle temporaire et de Windows Server vers un nouveau serveur Windows local remplaçant l’appliance StorSimple locale")

1. Prenez un clone de volume de votre appareil StorSimple locale et montez-le sur une appliance virtuelle StorSimple temporaire.
2. Connectez l’appliance virtuelle via iSCSI à une machine virtuelle Azure temporaire.
3. Installez Azure File Sync sur la machine virtuelle Windows Server temporaire : une clé de Registre spécifique pour cette migration doit également être définie pour que la synchronisation soit configurée sur le serveur.
    * Déployez autant de partages de fichiers Azure qu’il y a de partages sur votre volume StorSimple. (Nous vous recommandons de déployer un partage de fichiers Azure par compte de stockage.)
    * Configurez la synchronisation entre le partage individuel sur la machine virtuelle cloud Windows Server et un partage de fichiers Azure. (mappage 1 à 1)
    * Si vous le souhaitez, ajoutez un serveur local en tant que cache de performances local, avec activation de la hiérarchisation cloud. Cette étape est nécessaire si vous souhaitez remplacer votre appliance StorSimple locale par un cache local plus riche en fonctionnalités, à technologie Windows Server et reposant sur la hiérarchisation cloud d’Azure File Sync. Votre serveur Windows local peut être un cluster ou une machine physique ou bien une machine virtuelle. Il n’est pas nécessaire d’y déployer un espace de stockage aussi grand que le jeu de données. Il a uniquement besoin d’un espace de stockage permettant la mise en cache locale des fichiers les plus fréquemment sollicités.

## <a name="minimizing-downtime"></a>Minimisation des temps d’arrêt
Après l’étape 3 ci-dessus, l’appliance StorSimple locale est toujours activement utilisée par les utilisateurs et les applications. Ainsi, le jeu de fichiers synchronisés à partir du clone de volume initial est légèrement obsolète à la fin de la synchronisation.
L’approche visant à réduire les temps d’arrêt consiste à répéter la synchronisation à partir du processus de clonage de volume, de telle sorte que la synchronisation se termine plus rapidement à chaque itération, les modifications entre les clones de volume étant moins nombreuses à chaque fois.
Vous pouvez répéter ce processus jusqu’à ce que la synchronisation à partir d’un clone de volume puisse se terminer dans le laps de temps que vous trouvez acceptable pour les temps d’arrêt.
Une fois que c’est le cas, empêchez les utilisateurs et les applications d’apporter des modifications à votre appliance StorSimple. Le temps d’arrêt commence.
Prenez un autre clone de volume et laissez-le se synchroniser avec le ou les serveurs connectés.
Établissez l’accès de vos utilisateurs et applications à votre nouveau serveur Windows reposant sur Azure File Sync.
Envisagez de déployer ou d’ajuster un espace de noms DFS pour que le passage de l’ancienne appliance StorSimple vers votre nouveau serveur Windows soit transparent du point de vue des applications et des utilisateurs.
Votre migration est terminée.

## <a name="migration-goal"></a>Objectif de la migration
Une fois la migration terminée, vous pouvez déprovisionner l’appliance virtuelle StorSimple temporaire et la machine virtuelle Azure.

En outre, l’appliance locale StorSimple peut être déprovisionnée, car vos utilisateurs et applications accèdent déjà au serveur Windows à la place.
L’image ci-dessous illustre la configuration obtenue. Un déploiement Azure File Sync standard comprend un certain nombre de partages de fichiers Azure et de serveurs Windows connectés à ces derniers via Azure File Sync. N’oubliez pas qu’un même serveur peut connecter différents dossiers locaux à différents partages de fichiers en même temps.
En outre, un même partage de fichiers Azure peut se synchroniser sur de nombreux serveurs différents, option intéressante si vous avez besoin de mettre en cache des données au niveau des filiales. Vérifiez également si vous pouvez optimiser vos stratégies de hiérarchisation cloud pour une utilisation plus efficace de votre espace de stockage local.

![Alt](media/storage-sync-files-storsimple-migration/storsimple-docs-goal.PNG "Illustration montrant l’objectif une fois la migration terminée. Elle illustre un certain nombre de partages de fichiers qui se synchronisent sur un serveur Windows local, avec accès des utilisateurs et des applications aux fichiers dans le cloud ou sur le serveur Windows.")

## <a name="next-steps"></a>Étapes suivantes
Familiarisez-vous avec Azure Files et Azure File Sync. Pour réussir une migration, il est important de comprendre la terminologie et le modèle de déploiement Azure File Sync. Des informations plus détaillées sont disponibles pour chaque étape dans cet article de présentation. Veillez à contacter Microsoft pour obtenir une aide personnalisée lors de la planification et de l’exécution de votre migration.

> [!IMPORTANT]
> Microsoft s’engage à aider les clients lors de la migration. Contactez-nous par e-mail à l’adresse AzureFiles@microsoft.com pour obtenir un plan de migration personnalisé ainsi qu’une assistance pendant la migration.

## <a name="additional-resources"></a>Ressources supplémentaires
Azure File Sync, en tant que service cible, contient deux documents fondamentaux que nous vous recommandons de lire si vous débutez avec Azure File Sync.
* [Azure File Sync - Vue d’ensemble](storage-sync-files-planning.md)
* [Azure File Sync - Guide de déploiement](storage-sync-files-deployment-guide.md)

Azure Files est un service de stockage dans Azure, qui offre des partages de fichiers en tant que service. Vous n’avez pas besoin de payer ou de gérer une machine virtuelle ou un stockage de machines virtuelles associé.
* [Azure Files - Vue d’ensemble ](storage-files-introduction.md)
* [Azure Files - Guide de déploiement d’un partage de fichiers Azure](storage-how-to-create-file-share.md)