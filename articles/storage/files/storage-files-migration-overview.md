---
title: Migrer vers des partages de fichiers Azure
description: Découvrez les migrations vers les partages de fichiers Azure et trouvez votre guide de migration.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 903ce52120fce7c23c6a3754498b81fc6fc2430f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247314"
---
# <a name="migrate-to-azure-file-shares"></a>Migrer vers des partages de fichiers Azure

Cet article aborde les aspects de base de la migration vers les partages de fichiers Azure.

Outre les concepts de base de la migration, cet article contient une liste de guides de migration existants et individualisés. Ces guides de migration vous aident à déplacer vos fichiers vers des partages de fichiers Azure et sont organisés en fonction de l’endroit où vos données résident actuellement et du modèle de déploiement (cloud uniquement ou hybride) sur lequel vous envisagez de passer.

## <a name="migration-basics"></a>Notions de base de la migration

Il existe plusieurs types de stockage cloud dans Azure. Un aspect fondamental de la migration de fichiers vers Azure consiste à déterminer l’option de stockage Azure adaptée à vos données.

Les partages de fichiers Azure sont idéaux pour les données de fichiers à usage général. De fait, tout ce que vous utilisez avec un partage SMB ou NFS local. Avec [Azure File Sync](storage-sync-files-planning.md), vous pouvez éventuellement mettre en cache le contenu de plusieurs partages de fichiers Azure sur plusieurs serveurs Windows en local.

Si une application est en cours d’exécution sur un serveur local, le stockage des fichiers dans les partages de fichiers Azure peut être adapté, mais cela dépend de l’application. Vous pouvez transférer l’application pour qu’elle s’exécute dans Azure et utiliser des partages de fichiers Azure comme stockage partagé. Vous pouvez également envisager d’utiliser des [disques Azure](../../virtual-machines/windows/managed-disks-overview.md) dans ce scénario. Pour les applications qui se trouvent dans le cloud et qui ne dépendent pas de l’accès SMB ou de l’accès aux données ou aux accès partagé, le stockage d’objets comme [les objets blob Azure](../blobs/storage-blobs-overview.md), constitue souvent le meilleur choix.

La clé de toute migration consiste à capturer toutes les correspondances de fichiers applicables lors de la migration de vos fichiers de leur emplacement de stockage actuel vers Azure. Pour choisir la bonne capacité de stockage Azure, vous devez également tenir compte de la fidélité de l’option de stockage Azure et de celle requise par votre scénario. Traditionnellement, les données de fichiers à usage général dépendent des métadonnées de fichier. Les données d’application ne le sont peut-être pas. Les fichiers comprennent deux composants de base :

- **Flux de données** : le flux de données enregistre le contenu du fichier.
- **Métadonnées du fichier** : Les métadonnées de fichier comportent plusieurs sous-composants :
   * Attributs du fichier : lecture seule, par exemple.
   * Autorisations du fichier : *autorisations NTFS* ou *listes de contrôle d’accès de fichiers et de dossiers*.
   * Horodatages : en particulier, les horodatages de *création* et de *dernière modification*.
   * Autre flux de données : espace pour stocker de grandes quantités de propriétés non standard.

La fidélité des fichiers dans une migration peut donc être définie comme la capacité à stocker toutes les informations de fichier applicables sur la source, la possibilité de les transférer avec l’outil de migration et la capacité à les stocker sur le stockage cible de la migration.

Pour garantir le bon fonctionnement de votre migration, identifiez [le meilleur outil de copie selon vos besoins](#migration-toolbox) et associez une cible de stockage à votre source.

En tenant compte des informations précédentes, il est clair que le stockage cible pour les fichiers à usage général dans Azure est : [Partages de fichiers Azure](storage-files-introduction.md) : Par rapport au stockage d’objets dans les objets blob Azure, les métadonnées de fichier peuvent être stockées en mode natif sur des fichiers dans un partage de fichiers Azure.

Les partages de fichiers Azure préservent également la hiérarchie des fichiers et des dossiers. De plus :
* Les autorisations NTFS peuvent être stockées sur des fichiers et des dossiers, car ils sont locaux.
* Les utilisateurs Active Directory (ou Azure AD DS) peuvent accéder en mode natif aux partages de fichiers Azure. 
    Ils peuvent utilisent leur identité actuelle et obtenir un accès en fonction des autorisations du partage, ainsi que des listes de contrôle d’accès des fichiers et des dossiers. Ce comportement est similaire à celui des utilisateurs qui se connectent à un partage de fichiers local.
*  L’autre flux de données est l’aspect principal de la fidélité des fichiers qui ne peut actuellement pas être stocké sur un fichier dans un partage de fichiers Azure.
   Il est conservé localement quand Azure File Sync est impliqué.

* [En savoir plus sur l’authentification AD pour les partages de fichiers Azure](storage-files-identity-auth-active-directory-enable.md).
* [En savoir plus sur l’authentification Azure Active Directory Domain Services (Azure AD DS) pour les partages de fichiers Azure](storage-files-identity-auth-active-directory-domain-service-enable.md)

## <a name="migration-guides"></a>Guides de migration

Le tableau suivant répertorie les guides de migration détaillés.

Navigation :
1. Recherchez la ligne du système source sur lequel vos fichiers se trouvent actuellement.
2. Déterminez si vous ciblez un déploiement hybride dans lequel vous utilisez Azure File Sync pour mettre en cache le contenu d’un ou plusieurs partages de fichiers Azure en local ou si vous souhaitez utiliser des partages de fichiers Azure directement dans le cloud. Sélectionnez la colonne cible qui reflète votre décision.
3. À l’intersection de la source et de la cible, la cellule répertorie les scénarios de migration disponibles. Sélectionnez l’un d’entre eux pour accéder directement au guide de migration détaillé.

Les scénarios sans lien n’ont pas encore de guide de migration. Consultez régulièrement cette table pour découvrir les mises à jour. Les nouveaux guides seront publiés quand ils seront disponibles.

| Source | Cible : </br>Déploiement hybride | Cible : </br>Déploiement cloud uniquement |
|:---|:--|:--|
| | Combinaison d’outils :| Combinaison d’outils : |
| Windows Server 2012 R2 et ultérieur | <ul><li>[Azure File Sync](storage-sync-files-deployment-guide.md)</li><li>[Azure File Sync + DataBox](storage-sync-offline-data-transfer.md)</li><li>Service de migration de stockage + Azure File Sync</li></ul> | <ul><li>Azure File Sync</li><li>Azure File Sync + DataBox</li><li>Service de migration de stockage + Azure File Sync</li><li>RoboCopy</li></ul> |
| Windows Server 2012 et versions ultérieures | <ul><li>Azure File Sync + DataBox</li><li>Service de migration de stockage + Azure File Sync</li></ul> | <ul><li>Service de migration de stockage + Azure File Sync</li><li>RoboCopy</li></ul> |
| NAS (Network Attached Storage) | <ul><li>[Azure File Sync + RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Linux/Samba | <ul><li>[RoboCopy + Azure File Sync](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| StorSimple 8100/8600 | <ul><li>[Azure File Sync + appliance virtuelle 8020](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple 1200 | <ul><li>[Azure File Sync](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>Boîte à outils de migration

### <a name="file-copy-tools"></a>Outils de copie de fichiers

Il existe plusieurs outils de copie de fichiers Microsoft et non-Microsoft. Pour pouvoir sélectionner le bon outil de copie de fichiers pour votre scénario de migration, vous devez tenir compte de trois questions fondamentales :

* L’outil de copie prend-il en charge l’emplacement source et l’emplacement cible pour une copie de fichier donnée ? 
    * Prend-il en charge le chemin d’accès réseau et/ou les protocoles disponibles (par exemple REST/SMB/NFS) vers et depuis les emplacements de stockage source et cible ?
* L’outil de copie préserve-t-il la fidélité de fichier nécessaire qui est prise en charge par l’emplacement source/cible ? Dans certains cas, votre stockage cible ne prend pas en charge le même niveau de fidélité que votre source. Vous avez déjà décidé que le stockage cible était suffisant pour répondre à vos besoins. Par conséquent, l’outil de copie doit uniquement correspondre aux capacités de fidélité des fichiers de la cible.
* L’outil de copie dispose-t-il de fonctionnalités qui s’adaptent à ma stratégie de migration ? 
    * Par exemple, considérez si certaines des options vous permettent de réduire le temps d’arrêt. Voici une bonne question à se poser : Puis-je exécuter cette copie plusieurs fois sur le même emplacement auquel des utilisateurs accèdent de façon active ? Si la réponse est oui, vous pouvez réduire considérablement le temps d’arrêt. Comparez ceci à la situation dans laquelle vous ne pouvez démarrer la copie que lorsque la source cesse de changer, afin de garantir une copie complète.

Le tableau suivant classe les outils Microsoft et leurs aptitudes actuelles pour les partages de fichiers Azure :

| Recommandé | Outil | Prend en charge les partages de fichiers Azure | Préserve la fidélité des fichiers |
| :-: | :-- | :---- | :---- |
|![Oui, recommandé.](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | Pris en charge. Les partages de fichiers Azure peuvent être montés comme lecteurs réseau. | Fidélité totale* |
|![Oui, recommandé.](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure File Sync | Intégré en natif dans les partages de fichiers Azure. | Fidélité totale* |
|![Oui, recommandé.](media/storage-files-migration-overview/circle-green-checkmark.png)| Service de migration de stockage (SMS) | Pris en charge de façon indirecte. Les partages de fichiers Azure peuvent être montés comme lecteurs réseau sur un serveur cible SMS. | Fidélité totale* |
|![Pas entièrement recommandé.](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure DataBox | Pris en charge. | Ne copie pas les métadonnées. [Peut être utilisé en association avec Azure File Sync](storage-sync-offline-data-transfer.md). |
|![Option non recommandée.](media/storage-files-migration-overview/circle-red-x.png)| AzCopy | Pris en charge. | Ne copie pas les métadonnées. |
|![Option non recommandée.](media/storage-files-migration-overview/circle-red-x.png)| Explorateur de stockage Azure | Pris en charge. | Ne copie pas les métadonnées. |
|![Option non recommandée.](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Pris en charge. | Ne copie pas les métadonnées. |
|||||

*\* Fidélité complète : respecte ou dépasse les fonctionnalités du partage de fichiers Azure.*

### <a name="migration-helper-tools"></a>Outils d’assistance à la migration

Cette section répertorie les outils qui permettent de planifier et d’exécuter les migrations.

* **RoboCopy, de Microsoft Corporation**

    L’un des outils de copie les plus adaptés aux migrations de fichiers, fourni avec Microsoft Windows. En raison des nombreuses options de cet outil, la [documentation de RoboCopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) est une source à consulter.

* **TreeSize, de JAM Software GmbH**

    Azure File Sync est principalement mis à l’échelle avec le nombre d’éléments (fichiers et dossiers) et moins, avec la quantité totale de Tio. Cet outil peut être utilisé pour déterminer le nombre de fichiers et de dossiers sur vos volumes Windows Server. Il peut aussi être utilisé pour créer une perspective avant le [déploiement d’Azure File Sync](storage-sync-files-deployment-guide.md), mais également après, lorsque la hiérarchisation cloud est engagée et que vous souhaitez voir non seulement le nombre d’éléments, mais également dans quels répertoires votre cache serveur est le plus utilisé.
    Cet outil (version 4.4.1 testée) est compatible avec les fichiers cloud hiérarchisés. Il n’entraîne pas le rappel des fichiers hiérarchisés pendant son fonctionnement normal.


## <a name="next-steps"></a>Étapes suivantes

1. Créez un plan pour le déploiement des partages de fichiers Azure (cloud uniquement ou hybride) dont vous avez besoin.
2. Passez en revue la liste des guides de migration disponibles pour trouver le guide détaillé qui correspond à votre source et votre déploiement de partages de fichiers Azure.

Vous trouverez plus d’informations sur les technologies Azure Files dans cet article :

* [Présentation des partages de fichiers Azure](storage-files-introduction.md)
* [Planification d’un déploiement de synchronisation de fichiers Azure](storage-sync-files-planning.md)
* [Azure File Sync : hiérarchisation cloud](storage-sync-cloud-tiering.md)
