---
title: Comprendre la hiérarchisation Cloud d’Azure File Sync | Microsoft Docs
description: Découvrez la hiérarchisation Cloud, une fonctionnalité Azure File Sync facultative. Les fichiers fréquemment utilisés sont mis en cache localement sur le serveur ; les autres sont hiérarchisées sur Azure Files.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 06b82bf78c7a4d66e2c4da6d053f8ab77c400e00
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258714"
---
# <a name="cloud-tiering-overview"></a>Vue d’ensemble de la hiérarchisation cloud
La hiérarchisation Cloud, fonctionnalité facultative d’Azure File Sync, diminue la quantité de stockage local requise tout en conservant les performances d’un serveur de fichiers local.

Lorsqu’elle est activée, cette fonctionnalité stocke uniquement les fichiers fréquemment consultés (à chaud) sur votre serveur local. Les fichiers rarement consultés (à froid) sont fractionnés en espace de noms (structure de fichiers et de dossiers) et en contenu de fichier. L’espace de noms est stocké localement et le contenu du fichier est stocké dans un partage de fichiers Azure dans le Cloud. 

Quand un utilisateur ouvre un fichier hiérarchisé, Azure File Sync rappelle les données du fichier depuis le partage de fichiers dans Azure.

## <a name="how-cloud-tiering-works"></a>Fonctionnement de la hiérarchisation Cloud

### <a name="cloud-tiering-policies"></a>Stratégies de hiérarchisation Cloud
Lorsque vous activez la hiérarchisation Cloud, vous pouvez définir deux stratégies pour informer Azure File Sync quand vous souhaitez hiérarchiser les fichiers à froid : la **stratégie d’espace libre du volume** et la **stratégie de date**. 

#### <a name="volume-free-space-policy"></a>Stratégie d’espace libre du volume
La **stratégie d’espace libre du volume** indique à Azure File Sync de hiérarchiser les fichiers froids dans le Cloud lorsqu’une certaine quantité d’espace est libre sur votre disque local. 

Par exemple, si la capacité de votre disque local est de 200 Gio et que vous souhaitez disposer d’au moins 40 Go toujours libres, vous devez définir la stratégie d’espace libre du volume sur 20 %. L’espace libre du volume s’applique au niveau du volume plutôt qu’au niveau des répertoires individuels ou des points de terminaison de serveur. 

#### <a name="date-policy"></a>Stratégie de date
Avec la **stratégie de date**, les fichiers froids sont hiérarchisés dans le Cloud s’ils n’ont pas été consultés (c’est-à-dire lus ou modifiés) pendant x jours. Par exemple, si vous remarquez que les fichiers qui ont passé plus de 15 jours sans être consultés sont archivés, vous devez définir la stratégie de date sur 15 jours. 

Pour obtenir plus d’exemples sur la façon dont les stratégies de date et d’espace libre du volume fonctionnent ensemble, consultez [Choisir les stratégies de hiérarchisation Cloud Azure File Sync](file-sync-choose-cloud-tiering-policies.md).

### <a name="windows-server-data-deduplication"></a>Déduplication des données Windows Server
La déduplication des données est prise en charge sur les volumes pour lesquels la hiérarchisation Cloud est activée à partir de Windows Server 2016. Pour plus d’informations, consultez [Planification d’un déploiement Azure File Sync](file-sync-planning.md#data-deduplication).

### <a name="cloud-tiering-heatmap"></a>Carte thermique de hiérarchisation Cloud
Azure File Sync surveille l’accès aux fichiers (opérations de lecture et d’écriture) au fil du temps et, en fonction de la fréquence et de la date d’accès, attribue un score thermique à chaque fichier. Il utilise ces scores pour générer un « carte thermique » de votre espace de noms sur chaque point de terminaison de serveur. Cette carte thermique est une liste de tous les fichiers de synchronisation dans un emplacement où la hiérarchisation Cloud est activée, classée par score de chaleur. Les fichiers fréquemment consultés qui ont été récemment ouverts sont considérés comme chauds, tandis que les fichiers qui ont été horodatés et n’ont pas été utilisés pendant un certain temps sont considérés comme froids. 

Pour déterminer la position relative d’un fichier individuel sur cette carte thermique, le système utilise la valeur maximale de l’un de ses timestamp, dans cet ordre : MAX (heure du dernier accès, heure de la dernière modification, heure de création). 

En règle générale, l’heure du dernier accès est suivie et disponible. Toutefois, lorsqu’un nouveau point de terminaison de serveur est créé, avec la hiérarchisation cloud activée, le temps passé est insuffisant pour observer l’accès des fichiers. En l’absence d’heure de dernier accès, l’heure de la dernière modification est utilisée pour évaluer la position relative sur la carte thermique.  

La stratégie de date fonctionne de la même façon. Sans heure de dernier accès, la stratégie de date tiendra compte de l’heure de la dernière modification. Si celle-ci est indisponible, la date de création d’un fichier est la valeur prise en compte. Au fil du temps, le système observera plus de requêtes d’accès aux fichiers et finira par utiliser l’heure de dernier accès suivie automatiquement.

> [!Note]
> La hiérarchisation cloud ne dépend pas de la fonctionnalité NTFS pour le suivi de l’heure du dernier accès. La fonctionnalité NTFS est désactivée par défaut et, en raison de considérations liées aux performances, nous ne vous recommandons pas d’activer manuellement cette fonctionnalité. La hiérarchisation Cloud suit séparément l’heure du dernier accès.

### <a name="proactive-recalling"></a>Rappel proactif

Quand un fichier est créé ou modifié, vous pouvez rappeler proactivement un fichier aux serveurs que vous indiquez. Le rappel proactif a pour effet de rendre le fichier (nouveau ou modifié) disponible en vue de son utilisation sur chaque serveur que vous avez indiqué. 

Par exemple, une société internationale a des filiales aux États-Unis et en Inde. Le matin (heure des États-Unis), les travailleurs de l’information créent un nouveau dossier et de nouveaux fichiers pour un tout nouveau projet et travaillent dessus toute la journée. Azure File Sync synchronisera le dossier et les fichiers sur le partage de fichiers Azure (point de terminaison cloud). Les travailleurs de l’information en Inde continuent de travailler sur le projet dans leur fuseau horaire. Lorsqu’ils arrivent le matin, le serveur local avec Azure File Sync en Inde doit disposer de ces nouveaux fichiers localement, afin que l’équipe en Inde puisse travailler efficacement à partir d’un cache local. L’activation de ce mode empêche l’accès initial au fichier d’être plus lent en raison du rappel à la demande et permet au serveur de rappeler de manière proactive les fichiers dès qu’ils ont été modifiés ou créés dans le partage de fichiers Azure.

Si les fichiers rappelés sur le serveur ne sont pas nécessaires localement, le rappel inutile peut augmenter le trafic de sortie et les coûts. Activez le rappel proactif quand vous savez que le préremplissage d’un cache de serveur avec des changements récents depuis le Cloud aura des effets positifs sur les utilisateurs ou les applications en utilisant les fichiers sur ce serveur. 

L’activation du rappel proactif peut également entraîner une augmentation de l’utilisation de la bande passante sur le serveur et peut entraîner la hiérarchisation d’autres contenus relativement nouveaux sur le serveur local en raison de l’augmentation des fichiers rappelés. Hiérarchiser trop tôt peut cependant entraîner davantage de rappels si les fichiers en cours de hiérarchisation sont considérés comme étant chauds par les serveurs. 

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Image indiquant le comportement de téléchargement d’un partage de fichiers Azure pour un point de terminaison de serveur actuellement appliqué et un bouton permettant d’ouvrir un menu permettant de le modifier.":::

Pour plus d’informations sur le rappel proactif, consultez [Déployer Azure File Sync](file-sync-deployment-guide.md#proactively-recall-new-and-changed-files-from-an-azure-file-share).

## <a name="tiered-vs-locally-cached-file-behavior"></a>Comportement d’un fichier mis en cache localement vs. hiérarchisé

La hiérarchisation Cloud est la séparation entre l’espace de noms (l’arborescence de fichiers et de dossiers, ainsi que les propriétés de fichier) et le contenu du fichier. 

#### <a name="tiered-file"></a>Fichier hiérarchisé

Pour les fichiers hiérarchisés, la taille sur le disque est égale à zéro, car le contenu du fichier lui-même n’est pas stocké localement. Quand un fichier est hiérarchisé, le filtre du système de fichiers Azure File Sync (StorageSync.sys) remplace le fichier local par un pointeur (ou point d’analyse). Le point d’analyse représente une URL vers le fichier dans le partage de fichiers Azure Files. Un fichier hiérarchisé a l’attribut « offline » (hors connexion), et son attribut FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS est défini dans le système de fichiers NTFS de façon à ce que des applications tierces puissent identifier sûrement des fichiers hiérarchisés.   

![Capture d’écran des propriétés d’un fichier lorsqu’il est hiérarchisé (espace de noms uniquement).](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-2.png)    

#### <a name="locally-cached-file"></a>Fichier mis en cache localement

D’un autre côté, pour un fichier stocké sur un serveur de fichiers local, la taille sur le disque est environ égale à la taille logique du fichier, car le fichier entier (attributs de fichier + contenu du fichier) est stocké localement.     

![Capture d’écran des propriétés d’un fichier lorsqu’il est hiérarchisé (espace de noms + contenu du fichier).](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-1.png) 

Un fichier peut également être partiellement hiérarchisé (ou partiellement rappelé). Dans un fichier partiellement hiérarchisé, seule une partie du fichier est stockée sur le disque. Vous avez peut-être partiellement rappelé des fichiers sur votre volume si les fichiers sont partiellement lus par les applications qui prennent en charge l’accès en continu aux fichiers. Voici quelques exemples : les lecteurs multimédias et les utilitaires zip. Azure File Sync est efficace et rappelle uniquement les informations demandées à partir du partage de fichiers Azure connecté.

> [!NOTE]
> La Taille représente la taille complète du fichier. La Taille sur le disque représente la taille du flux de fichier stocké sur le disque.

## <a name="next-steps"></a>Étapes suivantes

* [Choisir des stratégies de hiérarchisation Cloud Azure File Sync](file-sync-choose-cloud-tiering-policies.md)
* [Planification d’un déploiement de synchronisation de fichiers Azure](file-sync-planning.md)
