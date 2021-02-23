---
title: Objectifs de performance et d’extensibilité d'Azure Files
description: Obtenez plus d’informations sur les objectifs d’extensibilité et de performances pour Azure Files, y compris la capacité, le taux de demandes et les limites de bande passante entrante et sortante.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6ef255d78d3dd3ff6fcc5eba7aad522018185299
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518893"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Objectifs de performance et d’extensibilité d'Azure Files
[Azure Files](storage-files-introduction.md) offre des partages de fichiers entièrement gérés dans le cloud, accessibles à l’aide des protocoles SMB et de système de fichiers NFS. Cet article présente les objectifs de performance et d’extensibilité pour Azure Files et Azure File Sync.

Les objectifs de performance et d’extensibilité répertoriés ici sont des objectifs haut de gamme, mais ils peuvent être affectés par d'autres variables de votre déploiement. Par exemple, le débit d’un fichier peut également être limité par votre bande passante réseau disponible, et pas seulement par les serveurs qui hébergent vos partages de fichiers Azure. Nous vous recommandons vivement de tester votre modèle d’utilisation pour déterminer si l’extensibilité et les performances d'Azure Files répondent à vos besoins. Nous sommes résolus à augmenter ces limites au fil du temps. 

## <a name="azure-files-scale-targets"></a>Objectifs de mise à l’échelle Azure Files
Les partages de fichiers Azure sont déployés dans des comptes de stockage, qui sont des objets de niveau supérieur représentant un pool partagé de stockage. Ce pool de stockage peut être utilisé pour déployer plusieurs partages de fichiers. Trois catégories doivent donc être prises en compte : les comptes de stockage, les partages de fichiers Azure et les fichiers.

### <a name="storage-account-scale-targets"></a>Objectifs de mise à l'échelle d'un compte de stockage
Azure prend en charge plusieurs types de comptes de stockage pour différents scénarios de stockage que les clients peuvent rencontrer, mais il existe deux principaux types de comptes de stockage pour Azure Files. Le type de compte de stockage que vous devez créer varie selon que vous souhaitez créer un partage de fichiers Standard ou un partage de fichiers Premium : 

- **Comptes de stockage version 2 universels (GPv2)**  : Les comptes de stockage GPv2 vous permettent de déployer des partages de fichiers Azure sur du matériel Standard/sur disque dur (HDD). En plus de stocker les partages de fichiers Azure, les comptes de stockage GPv2 peuvent stocker d’autres ressources de stockage, telles que des conteneurs d’objets blob, des files d’attente ou des tables. Les partages de fichiers peuvent être déployés sur le niveau Transaction optimisée (par défaut), le niveau d’accès froid ou le niveau d’accès chaud.

- **Comptes de stockage FileStorage** : Les comptes de stockage FileStorage vous permettent de déployer des partages de fichiers Azure sur du matériel Premium/sur disque SSD. Les comptes FileStorage peuvent uniquement être utilisés pour stocker des partages de fichiers Azure. Aucune autre ressource de stockage (conteneurs d’objets blob, files d’attente, tables, etc.) ne peut être déployée dans un compte FileStorage.

| Attribut | Comptes de stockage GPv2 (Standard) | Comptes de stockage FileStorage (Premium) |
|-|-|-|
| Nombre de comptes de stockage par région par abonnement | 250 | 250 |
| Capacité maximale du compte de stockage | 5 Pio<sup>1</sup> | 100 Tio (approvisionné) |
| Nombre maximal de partages de fichiers | Illimité | Illimité, la taille totale approvisionnée de tous les partages doit être inférieure à la capacité maximale du compte de stockage |
| Taux maximal de requêtes simultanées | 20 000 IOPS<sup>1</sup> | 100 000 E/S par seconde |
| Entrée maximale | <ul><li>États-Unis/Europe : 10 GBP/s<sup>1</sup></li><li>Autres régions (LRS/ZRS) : 10 GBP/s<sup>1</sup></li><li>Autres régions (GRS) : 5 GBP/s<sup>1</sup></li></ul> | 4,136 Mio/s |
| Sortie maximale | 50 GBP/s<sup>1</sup> | 6,204 Mio/s |
| Nombre maximal de règles de réseau virtuel | 200 | 200 |
| Nombre maximal de règles d’adresse IP | 200 | 200 |
| Opérations de lecture de gestion | 800 toutes les 5 minutes | 800 toutes les 5 minutes |
| Opérations d’écriture de gestion | 10 par seconde/1 200 par heure | 10 par seconde/1 200 par heure |
| Opérations de liste de gestion | 100 toutes les 5 minutes | 100 toutes les 5 minutes |

<sup>1</sup> Les comptes de stockage universels version 2 prennent en charge de plus grandes limites de gestion et de plus grandes limites d’entrée par requête. Pour demander une augmentation des limites de compte, contactez le [support Azure](https://azure.microsoft.com/support/faq/).

### <a name="azure-file-share-scale-targets"></a>Cibles de mise à l’échelle du partage de fichiers Azure
| Attribut | Partages de fichiers Standard<sup>1</sup> | Partages de fichiers Premium |
|-|-|-|
| Taille minimale d'un partage de fichiers | Pas de minimum | 100 Gio (approvisionné) |
| Unité d’augmentation/de réduction de taille approvisionnée | N/A | 1 Gio |
| Taille maximale d’un partage de fichiers | <ul><li>100 Tio, avec la fonctionnalité de partage de fichiers volumineux activée<sup>2</sup></li><li>5 Tio, par défaut</li></ul> | 100 Tio |
| Nombre maximal de fichiers dans un partage de fichiers | Aucune limite | Aucune limite |
| Taux de requêtes maximal (IOPS max.) | <ul><li>10 000, avec la fonctionnalité de partage de fichiers volumineux activée<sup>2</sup></li><li>1 000 ou 100 requêtes par 100 ms, par défaut</li></ul> | <ul><li>IOPS de base : 400 + 1 IOPS par Gio, jusqu’à 100 000</li><li>IOPS en mode rafale : Max (4 000,3 x IOPS par Gio), jusqu’à 100 000</li></ul> |
| Entrée maximale pour un partage de fichier unique | <ul><li>Jusqu’à 300 Mio/s, avec la fonctionnalité de partage de fichiers volumineux activée<sup>2</sup></li><li>Jusqu’à 60 Mio/s, par défaut</li></ul> | 40 Mio/s + 0,04 * Gio configurés |
| Sortie maximale pour un partage de fichier unique | <ul><li>Jusqu’à 300 Mio/s, avec la fonctionnalité de partage de fichiers volumineux activée<sup>2</sup></li><li>Jusqu’à 60 Mio/s, par défaut</li></ul> | 60 Mio/s + 0,06 * Gio configurés |
| Nombre maximal d’instantanés de partage | 200 instantanés | 200 instantanés |
| Longueur maximale du nom de l’objet (répertoires et fichiers) | 2 048 caractères | 2 048 caractères |
| Nombre maximal de caractères des composants de nom de chemin d’accès (dans le chemin d’accès \A\B\C\D, chaque lettre est un composant) | 255 caractères | 255 caractères |
| Limite de liaison codée en dur (NFS uniquement) | N/A | 178 |
| Nombre maximal de canaux SMB Multichannel | N/A | 4 |
| Nombre maximal de stratégies d’accès stockées par partage de fichiers | 5 | 5 |

<sup>1</sup> Les limites pour les partages de fichiers standard s’appliquent aux trois niveaux disponibles pour les partages de fichiers standard : transaction optimisée, chaud et froid.

<sup>2</sup> Par défaut, la taille des partages de fichiers standard est de 5 Tio. Consultez [Activer et créer des partages de fichiers volumineux](./storage-files-how-to-create-large-file-share.md) pour savoir comment effectuer un scale-up des partages de fichiers jusqu’à 100 Tio.

### <a name="file-scale-targets"></a>Objectifs de mise à l'échelle de fichier
| Attribut | Fichiers dans les partages de fichiers standard  | Fichiers dans les partages de fichiers Premium  |
|-|-|-|
| Taille maximale du fichier | 4 Tio | 4 Tio |
| Taux maximal de requêtes simultanées | 1 000 E/S par seconde | Jusqu’à 8 000<sup>1</sup> |
| Entrée maximale pour un fichier | 60 Mio/s | 200 Mio/s (jusqu’à 1 Gio/s avec la version préliminaire de SMB Multichannel)<sup>2</sup>|
| Sortie maximale pour un fichier | 60 Mio/s | 300 Mio/s (jusqu’à 1 Gio/s avec la version préliminaire de SMB Multichannel)<sup>2</sup> |
| Nombre maximal de handles simultanés | 2 000 handles | 2 000 handles  |

<sup>1 S’applique aux opérations d’E/S en lecture et écriture (généralement des tailles d’E/S plus petites ou égales à 64 Kio). Le nombre d’opérations sur les métadonnées autres que les lectures et les écritures peut être inférieur.</sup>
<sup>2 Soumis aux limites de réseau de la machine, bande passante disponible, tailles d’I/O, profondeur de la file d'attente et autres facteurs. Pour plus d’informations, consultez [Performances de SMB Multichannel](./storage-files-smb-multichannel-performance.md).</sup>

## <a name="azure-file-sync-scale-targets"></a>Objectifs de mise à l’échelle d’Azure File Sync
Le tableau suivant indique les limites de tests réalisés par Microsoft, ainsi que les cibles constituant des limites matérielles :

| Ressource | Cible | Limite inconditionnelle |
|----------|--------------|------------|
| Services de synchronisation de stockage par région | 100 services de synchronisation de stockage | Oui |
| Groupes de synchronisation par service de synchronisation de stockage | 200 groupes de synchronisation | Oui |
| Serveurs inscrits par le service de synchronisation de stockage | 99 serveurs | Oui |
| Points de terminaison cloud par groupe de synchronisation | 1 point de terminaison cloud | Oui |
| Points de terminaison de serveur par groupe de synchronisation | 100 points de terminaison de serveur | Oui |
| Points de terminaison de serveur par serveur | 30 points de terminaison de serveur | Oui |
| Objets du système de fichiers (répertoires et fichiers) par groupe de synchronisation | 100 millions d’objets | Non |
| Nombre maximal d’objets de système de fichiers (répertoires et fichiers) dans un répertoire | 5 millions d’objets | Oui |
| Taille maximale du descripteur de sécurité d’objet (répertoires et fichiers) | 64 Kio | Oui |
| Taille du fichier | 100 Gio | Non |
| Taille minimale d’un fichier à hiérarchiser | V9 et versions ultérieures : en fonction de la taille de cluster du système de fichiers (le double de cette taille). Par exemple, si la taille du cluster de système de fichiers est de 4 Kio, la taille de fichier minimale sera de 8 Kio.<br> V8 et versions antérieures : 64 Kio  | Oui |

> [!Note]  
> Un point de terminaison Azure File Sync peut augmenter la taille d’un partage de fichiers Azure. Si la limite de taille du partage de fichiers Azure est atteinte, la synchronisation ne fonctionne pas.

### <a name="azure-file-sync-performance-metrics"></a>Métriques de performances Azure File Sync
Étant donné que l’agent Azure File Sync s’exécute sur un ordinateur Windows Server qui se connecte aux partages de fichiers Azure, les performances de synchronisation réelles dépendent de plusieurs facteurs dans votre infrastructure : Windows Server et la configuration de disque sous-jacente, la bande passante réseau entre le serveur et le stockage Azure, la taille des fichiers, la taille totale du jeu de données et l’activité sur le jeu de données. Comme Azure File Sync fonctionne au niveau du fichier, les caractéristiques de performances d’une solution Azure File Sync est exprimée de façon optimale en nombre d’objets (fichiers et répertoires) traités par seconde.

Pour Azure File Sync, les performances sont essentielles dans deux phases :

1. **Approvisionnement initial unique** : pour optimiser les performances au moment de l’approvisionnement initial, consultez [Intégrer Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) pour plus d’informations sur un déploiement optimal.
2. **Synchronisation continue** : une fois que les données sont initialement approvisionnées dans les partages de fichiers Azure, Azure File Sync synchronise plusieurs points de terminaison.

Pour vous aider à planifier votre déploiement pour chacune des phases, voici les résultats observés durant le test interne sur un système avec une configuration

| Configuration système | Détails |
|-|-|
| UC | 64 cœurs virtuels avec cache L3 64 MiB |
| Mémoire | 128 Go |
| Disque | Disques SAS avec RAID 10 et cache protégé par batterie |
| Réseau | Réseau 1 Gbit/s |
| Charge de travail | Serveur de fichiers à usage général|

| Provisionnement initial unique  | Détails |
|-|-|
| Nombre d’objets | 25 millions d’objets |
| Taille du jeu de données| ~4,7 Tio |
| Taille de fichier moyenne | ~200 Kio (plus gros fichier : 100 Gio) |
| Énumération initiale des modifications cloud | 20 objets par seconde  |
| Débit de chargement | 20 objets par seconde par groupe de synchronisation |
| Débit de téléchargement d’espace de noms | 400 objets par seconde |

### <a name="initial-one-time-provisioning"></a>Provisionnement initial unique
**Énumération initiale des modifications cloud** : Lors de la création d’un groupe de synchronisation, l’énumération initiale des modifications cloud est la première étape qui s’exécutera. Dans ce processus, le système énumère tous les éléments du partage de fichiers Azure. Pendant ce processus, il n’y aura aucune activité de synchronisation, c’est-à-dire qu’aucun élément ne sera téléchargé du point de terminaison cloud vers le point de terminaison de serveur et qu’aucun élément ne sera chargé du point de terminaison de serveur vers le point de terminaison cloud. L’activité de synchronisation reprendra une fois l’énumération initiale des modifications cloud terminée.
Le taux de performances est de 20 objets par seconde. Les clients peuvent estimer le temps nécessaire pour effectuer l’énumération initiale des modifications cloud en déterminant le nombre d’éléments dans le partage cloud et en utilisant les formules suivantes pour obtenir la durée en jours. 

   **Durée (en jours) de l’énumération initiale des modifications cloud = (Nombre d’objets dans le point de terminaison cloud)/(20 * 60 * 60 * 24)**

**Débit de téléchargement d’espace de noms** : Lorsqu’un nouveau point de terminaison de serveur est ajouté à un groupe de synchronisation existant, l’agent Azure File Sync ne télécharge aucun contenu de fichier à partir du point de terminaison cloud. Il synchronise d’abord l’espace de noms complet, puis déclenche un rappel en arrière-plan pour télécharger les fichiers dans leur intégralité ou, si la hiérarchisation cloud est activée, sur la stratégie de hiérarchisation de cloud définie sur le point de terminaison.

| Synchronisation continue  | Détails  |
|-|--|
| Nombre d’objets synchronisés| 125 000 objets (variation ~1 %) |
| Taille du jeu de données| 50 GiB |
| Taille de fichier moyenne | ~ 500 Kio |
| Débit de chargement | 20 objets par seconde par groupe de synchronisation |
| Débit de téléchargement complet* | 60 objets par seconde |

* Si la hiérarchisation cloud est activée, vous devez avoir de meilleures performances, car seules certaines données de fichier sont téléchargées. Azure File Sync télécharge uniquement les données des fichiers mis en cache quand elles changent sur un point de terminaison. Pour les fichiers hiérarchisés ou nouvellement créés, l’agent ne télécharge pas les données de fichier et, à la place, synchronise uniquement l’espace de noms sur tous les points de terminaison de serveur. L’agent prend également en charge les téléchargements partiels de fichiers hiérarchisés à mesure qu’ils sont consultés par l’utilisateur. 

> [!Note]  
> Les nombres ci-dessus ne sont pas une indication des performances que vous allez rencontrer. Les performances réelles dépendent de plusieurs facteurs comme indiqué au début de cette section.

En règle générale pour votre déploiement, gardez ces quelques points à l’esprit :

- Le débit d’objets est proportionnel au nombre de groupes de synchronisation sur le serveur. Si vous fractionnez les données en plusieurs groupes de synchronisation sur un serveur, vous obtenez un meilleur débit qui est également limité par le serveur et le réseau.
- Le débit d’objets est inversement proportionnel au débit de MiB par seconde. Pour les plus petits fichiers, le débit est plus élevé en termes de nombre d’objets traités par seconde, mais inférieur en termes de MiB par seconde. À l’inverse, pour les plus gros fichiers, moins d’objets sont traités par seconde, mais le débit de MiB par seconde est supérieur. Le débit de MiB par seconde est limité par les objectifs d’échelle d’Azure Files.

## <a name="see-also"></a>Voir aussi
- [Planification d’un déploiement Azure Files](storage-files-planning.md)
- [Planification d’un déploiement de synchronisation de fichiers Azure](storage-sync-files-planning.md)