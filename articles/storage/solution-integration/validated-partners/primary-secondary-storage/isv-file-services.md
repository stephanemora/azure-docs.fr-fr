---
title: Considérations sur l’exécution de services de fichiers ISV dans Azure
titleSuffix: Azure Storage
description: Guide simple sur différentes options ISV lors de l’exécution de services de fichiers dans Azure
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 05/24/2021
ms.service: storage
ms.subservice: common
ms.openlocfilehash: a880e64a710638561eb171b2d7bb14973ef58224
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111969763"
---
# <a name="running-isv-file-services-in-azure"></a>Exécution de services de fichiers ISV dans Azure

Azure offre différentes options de stockage pour les données de fichiers. Les services natifs Azure sont les suivants :
- [Azure Files](https://azure.microsoft.com/services/storage/files/) – Partages de fichiers complètement managés dans le cloud, accessibles via les protocoles SMB et NFS standard du secteur. Azure Files offre deux types différents (Standard et Premium) avec des caractéristiques de performances différentes.
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) – Partages de fichiers complètement managés dans le cloud, conçus pour répondre aux performances requises des applications métier d’entreprise. Azure NetApp Files offrent plusieurs niveaux de service avec des limitations de performances différentes (Standard, Premium et Ultra).
- [Stockage Blob Azure](https://azure.microsoft.com/services/storage/blobs/) – Plateforme de stockage d’objets à grande échelle pour le stockage de données non structurées. Le Stockage Blob Azure offre deux types différents (Standard et Premium) avec des caractéristiques de performances différentes. 
  
Plusieurs articles décrivent les différences et les recommandations documentant la sélection du service de fichiers natif. Pour en savoir plus :
- Notre guide de migration décrit l’[organigramme de base](../../../common/storage-migration-overview.md#choose-a-target-storage-service)
- [Comparaison détaillée entre Azure Files et Azure NetApp Files](../../../files/storage-files-netapp-comparison.md)

De nombreuses solutions d’éditeurs de logiciels indépendants (ISV, Independent Software Vendor) peuvent fournir des services de fichiers dans Azure. Cet article aborde deux sujets :
- il fournit des considérations générales sur la sélection des services de fichiers ;
- il présente brièvement les différences existant entre les solutions ISV.
  
La liste complète des solutions ISV vérifiées est disponible sur la page [Partenaires de stockage principal et secondaire du Stockage Azure](./partner-overview.md).

## <a name="considerations"></a>Considérations

Au moment d’opérer la sélection de la meilleure solution pour l’exécution de services de fichiers dans Azure, plusieurs réflexions importantes sont à prendre en considération.

### <a name="basic-considerations"></a>Considérations de base

La préoccupation en matière de fonctionnalités de base passe par l’examen des fonctionnalités les plus courantes des plateformes de stockage sous-jacentes. Cette étape est effectuée initialement pour explorer les solutions qui fournissent les fonctionnalités de base dont nous avons besoin pour satisfaire à nos exigences. Les fonctionnalités de base importantes varient d’un cas à l’autre. Les plus courantes sont la prise en charge des protocoles, la taille des espaces de noms, la hiérarchisation automatique, le chiffrement, la prise en charge de WORM et l’authentification. Vous pouvez consulter la liste des fonctionnalités de base que nos solutions ISV prennent en charge dans [Comparaison des fonctionnalités](#isv-solutions-comparison).

### <a name="performance-considerations"></a>Considérations relatives aux performances

Une fois que vous avez sélectionné la solution qui répond aux fonctionnalités de base, les performances nécessaires doivent être étudiées. L’estimation de l’adéquation des performances est liée à trois caractéristiques de performances de base :
  - Latence
  - Bande passante
  - IOPS ou TPS

L’importance des caractéristiques des performances de base dépend de la concurrence des charges de travail (nombre de requêtes en parallèle) et de la taille des blocs (taille des requêtes).

| Type de charge de travail | Recommandations |
| -------------------- | --------------- |
| **Concurrence faible** | La latence est la considération la plus critique. Plus la latence est faible, plus les performances sont élevées. Dans les charges de travail à faible concurrence, les limites d’IOPS et de bande passante sont rarement franchies |
| **Concurrence élevée** | L’impact de la latence est beaucoup plus faible en raison d’une forte concurrence. Les IOPS et/ou la bande passante doivent être prises en compte. |
| **Taille des blocs** | Pour les charges de travail dotées de petites tailles de bloc, les limites d’IOPS sont plus importantes à prendre en compte, tandis que les limites de bande passante sont plus importantes pour les charges de travail pourvues de grandes tailles de bloc. |

Toute charge de travail de stockage peut être décrite en gardant ces caractéristiques à l’esprit. Par exemple, les charges de travail OLTP présentent généralement une concurrence élevée et de petites tailles de bloc. Les charges de travail HPC révèlent généralement une concurrence élevée, mais la taille des blocs peut varier, passant de la petite à la grande taille. 
    
Certaines règles générales sont toujours recommandées :
  - Sélection du protocole : si possible, utilisez SMB3 avec la prise en charge de Multichannel, ou NFSv3 avec la prise en charge de nconnect, car de meilleures performances seront obtenues. Évitez d’utiliser des protocoles hérités à la fois de considérations relatives aux performances, mais aussi à la sécurité. Notez également que les clients peuvent être aussi ajustés, et qu’il est recommandé d’optimiser les performances.
  - Réseau : si le type de machine virtuelle les prend en charge, utilisez les performances réseau accélérées. La latence s’en trouvera réduite et les performances en bénéficieront toujours. 
  - Type de machine virtuelle : sélectionnez le type de machine virtuelle qui convient le mieux à la charge de travail. Si la charge de travail comporte un petit nombre de clients, l’exécution de services de fichiers dans un plus petit nombre de machines virtuelles de grande taille est mieux adaptée. En revanche, un grand nombre de clients peut tirer parti de l’exécution de services de fichiers dans un plus grand nombre de machines virtuelles, de tailles plus petites.
  - Pour des charges de travail peu concurrentielles et de petite taille de bloc, explorez les solutions qui :
    - utilisent des disques managés (disques SSD Premium ou Ultra) ou
    - disposent d’un algorithme de mise en cache approprié.
  - Pour les charges de travail très concurrentielles et de taille de bloc volumineuse, explorez les solutions qui utilisent le Stockage Blob Azure en tant que serveur back-end

## <a name="isv-solutions-overview-and-example-use-cases"></a>Solutions ISV : vue d’ensemble et exemples de cas d’usage

Cet article compare plusieurs solutions ISV qui fournissent des services de fichiers dans Azure.

| Solution | Vue d’ensemble | Exemples de cas d’utilisation |
| -------- | ----------- | ----------------- |
| **Nasuni** | **UniFS** est un service de fichiers d’entreprise avec une alternative de cloud plus simple, économique, reposant sur Microsoft Azure | - Stockage de fichiers principal <br> - Partages de fichiers par service <br> - Gestion centralisée des fichiers <br> - Collaboration sur plusieurs sites avec verrouillage de fichier global <br> - Windows Virtual Desktop <br> - Partages de fichiers de travail/VDI à distance |
| **NetApp** | **Cloud Volumes ONTAP (CVO)** optimise vos coûts de stockage cloud et les performances tout en améliorant la protection des données, la sécurité et la conformité. Comprend une gestion, une disponibilité et une durabilité des données de qualité professionnelle | - Applications métier <br> - Bases de données relationnelles et NoSQL <br> - Big Data et Analytics <br> - Données persistantes pour les conteneurs <br> - Pipelines CI/CD <br> - Reprise d’activité pour les solutions NetApp locales |
| **Panzura**| **CloudFS** est un système de fichiers global d’entreprise hybride qui permet d’accéder au même jeu de données, localement ou dans le cloud | - Remplacement NAS de l’entreprise <br> - Collaboration globale <br> - Accès natif cloud aux données non structurées pour Analytics, IA/ML. |
| **Tiger Technology** | **Tiger Bridge** est une solution logicielle de gestion de données. Fournit la hiérarchisation entre un système de fichiers NTFS et un Stockage Blob Azure ou des disques managés Azure. Crée un seul espace de noms avec verrouillage de fichier local. | - Analytics <br> - Archivage cloud <br> - Protection continue des données (CDP) <br> - Reprise d’activité pour les serveurs Windows <br> - Synchronisation et collaboration sur plusieurs sites <br> - Workflows à distance (VDI) |
| **XenData** | **Cloud File Gateway** crée un système de fichiers global extrêmement évolutif à l’aide de serveurs de fichiers Windows | - Partage global de fichiers scientifiques et techniques <br> - Montage vidéo collaboratif |

## <a name="isv-solutions-comparison"></a>Comparaison des solutions ISV

### <a name="supported-protocols"></a>Protocoles pris en charge

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **SMB 2.1**                                         | Oui                  | Oui                            | Oui                       | Oui                   | Oui                   |
| **SMB 3.0**                                         | Oui                  | Oui                            | Oui                       | Oui                   | Oui                   |
| **SMB 3.1**                                         | Oui                  | Oui                            | Oui                       | Oui                   | Oui                   |
| **NFS v3**                                          | Oui                  | Oui                            | Oui                       | Oui                   | Oui                   |
| **NFS v4.1**                                        | Oui                  | Oui                            | Oui                       | Oui                   | Oui                   |
| **iSCSI**                                           | Non                   | Oui                            | Non                        | Oui                   | Non                    |

### <a name="supported-services-for-persistent-storage"></a>Services pris en charge pour le stockage persistant

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **Disques managés**                                   | Non                   | Oui                            | Oui                       | Oui                   | Non                    |
| **Disques non managés**                                 | Non                   | Non                             | Non                        | Oui                   | Non                    |
| **Objets blob de blocs de Stockage Azure**                       | Oui                  | Oui (hiérarchisation)                  | Oui                       | Oui                   | Oui                   |
| **Objets blob de pages de Stockage Azure**                        | Non                   | Oui (pour HA)                   | Oui                       | Non                    | Non                    |
| **Prise en charge du niveau d’archivage Azure**                      | Non                   | Non                             | Oui                       | Oui                   | Oui                   |
| **Fichiers accessibles dans un format non opaque**           | Non                   | Non                             | Non                        | Oui                   | Oui                   |

### <a name="extended-features"></a>Fonctionnalités étendues

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **Environnement d’exploitation**                           | UniFS                | ONTAP                          | PFOS                      | Windows Server        | Windows Server              |
| **Haute disponibilité**                               | Oui                  | Oui                            | Oui                       | Oui (nécessite une installation)  | Oui                   |
| **Basculement automatique entre les nœuds du cluster** | Oui                  | Oui                            | Oui                       | Oui (cluster Windows) | oui (cluster Windows) |
| **Basculement automatique entre les zones de disponibilité**    | Oui                  | Non                             | Oui                       | Oui (cluster Windows) | oui (cluster Windows) |
| **Basculement automatique entre les régions**               | Oui (avec prise en charge de Nasuni)| Non                         | Non                        | Oui (cluster Windows) | oui (cluster Windows) |
| **Prise en charge des captures instantanées**                                | Oui                  | Oui                            | Oui                       | Oui                   | Non                    |
| **Prise en charge des instantanés de cohérence**                     | Oui                  | Oui                            | Oui                       | Oui                   | Non                    |
| **Sauvegarde intégrée**                               | Oui                  | Oui (Module complémentaire)                   | Non                        | Oui                   | Oui                   |
| **Contrôle de version**                                      | Oui                  | Oui                            | Non                        | Oui                   | Oui                   |
| **Restauration au niveau du fichier**                              | Oui                  | Oui                            | Oui                       | Oui                   | Oui                   |
| **Restauration au niveau du volume**                            | Oui                  | Oui                            | Oui                       | Oui                   | Oui                   |
| **WORM**                                            | Oui                  | Oui                            | Non                        | Oui                   | Non                    |
| **Hiérarchisation automatique**                               | Oui                  | Oui                            | Non                        | Oui                   | Oui                   |
| **Verrouillage de fichier global**                             | Oui                  | Oui (NetApp Global File Cache) | Oui                       | Oui                   | Oui                   |
| **Agrégation d’espaces de noms sur les sources back-end**      | Oui                  | Oui                            | Non                        | Oui                   | Oui                   |
| **Mise en cache des données actives**                          | Oui                  | Oui                            | Oui                       | Oui                   | Oui                   |
| **Modes de mise en cache pris en charge**                         | Dernier récemment utilisé (LRU), épinglé manuellement | dernier récemment utilisé (LRU)                            | Dernier récemment utilisé (LRU), épinglé manuellement      | dernier récemment utilisé (LRU)                   | dernier récemment utilisé (LRU)                   |
| **Chiffrement au repos**                              | Oui                  | Oui                            | Oui                       | Oui                   | Non                    |
| **Déduplication**                                  | Oui                  | Oui                            | Oui                       | Non                    | Non                    |
| **Compression**                                     | Oui                  | Oui                            | Oui                       | Non                    | Non                    |

### <a name="authentication-sources"></a>Sources d’authentification

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **Prise en charge d’Azure Active Directory**                                | Oui (via ADDS)       | Oui (via ADDS)                 | Oui (via ADDS)            | Oui (via ADDS)        | Oui (via ADDS)        |
| **Prise en charge d’Active Directory**                        | Oui                  | Oui                            | Oui                       | Oui                   | Oui                   |
| **Prise en charge de LDAP**                                    | Oui                  | Oui                            | Non                        | Non                    | Oui                   |

### <a name="management"></a>Gestion

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **REST API**                                        | Oui                  | Oui                            | Oui                       | Oui                   | Non                    |
| **GUI web**                                         | Oui                  | Oui                            | Oui                       | Non                    | Non                    |

### <a name="scalability"></a>Extensibilité

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **Nombre maximal de nœuds dans un seul cluster**     | 100                  | 2 (HA)                         | Testé jusqu’à 60 nœuds    | N/A                 | N/A                 |
| **Nombre maximal de volumes**                       | 800                  | 1 024                           | Illimité                 | N/A                 | 1                     |
| **Nombre maximal d’instantanés**                     | Illimité            | Illimité                      | Illimité                 | N/A                 | N/A                 |
| **Taille maximale d’un seul espace de noms**              | Illimité            | Dépend de l’infrastructure      | Illimité                 | N/A                 | N/A                 |

### <a name="licensing"></a>Licence

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **BYOL**                                            | Oui                  | Oui                            | Oui                       | Oui                   | Oui                   |
| **Éligible à Azure Benefit**                          | Non                   | Oui                            | Oui                       | Non                    | Non                    |
| **Modèle de déploiement (IaaS, SaaS)**                   | IaaS                 | IaaS                           | IaaS                      | IaaS                  | IaaS                  |

### <a name="other-features"></a>Autres fonctionnalités

**Nasuni**
- [Place de marché](https://azuremarketplace.microsoft.com/marketplace/apps/nasunicorporation.nasuni-nea-90-prod?tab=Overview)
- Peut utiliser des disques managés pour la mise en cache des données
- Gestion centralisée de toutes les appliances de périphérie
- Historique de contrôle de version de fichier illimité
- Taille illimitée du système de fichiers
- Taille de fichier illimitée
- Accès aux fichiers via HTTPS, API REST et FTP
- Équilibrage de la charge de plusieurs systèmes de stockage avec Azure Load Balancer
- Chiffrement avec des clés gérées par le client
- Prend en charge les outils d’audit et de sécurité tiers (par exemple, Varonis, Stealthbits)
- Services professionnels et déploiement de White glove

**NetApp**
- [Place de marché](https://azuremarketplace.microsoft.com/marketplace/apps/netapp.netapp-ontap-cloud?tab=Overview)
- Économies de déduplication transmises au client par le biais d’une consommation d’infrastructure réduite

**Panzura**
- [Place de marché](https://azuremarketplace.microsoft.com/marketplace/apps/panzura-file-system.panzura-freedom-filer)
- Verrouillage au niveau de l’octet (plusieurs opérations lecture/écriture simultanées s’ouvrent)

**Tiger Technology**
- Invisible pour les applications
- Restauration partielle
- Intégration de Windows Shell (superposition, menu contextuel, feuille de propriétés)
- Prise en charge des annulations de suppression et des suppressions réversibles Azure
- Option permettant d’appliquer des renommages à la cible cloud
- Écriture partielle dans des objets
- Protection contre les ransomwares
- Synchronisation et collaboration sur plusieurs sites

**XenData**
- Le service Cosmos DB permet la synchronisation rapide de plusieurs passerelles, y compris des fichiers propriétaires spécifiques à l’application pour la collaboration globale 
- Chaque passerelle dispose d’un contrôle extrêmement précis du contenu mis en cache localement
- Prend en charge le streaming vidéo et les restaurations partielles de fichiers
- Prend en charge les chargements d’Azure Data Box
- Chiffrement fourni par le Stockage Blob Azure

## <a name="next-steps"></a>Étapes suivantes

En savoir plus :

- [Disques Azure](../../../../virtual-machines/managed-disks-overview.md)
- [Stockage Blob Azure](https://azure.microsoft.com/services/storage/blobs/)
- [Partenaires vérifiés pour le stockage principal et secondaire](./partner-overview.md)
- [Vue d’ensemble de la migration du stockage](../../../common/storage-migration-overview.md)