---
title: Comparaison des outils de migration de Stockage Azure – Données non structurées
description: Fonctionnalités de base et comparaison entre les outils utilisés pour la migration des données non structurées
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 15daeb0e6bf320a0727d8e6ea502063a30e67ad0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106230897"
---
# <a name="comparison-matrix"></a>Matrice de comparaison

La matrice de comparaison suivante montre les fonctionnalités de base des différents outils qui peuvent être utilisés pour la migration de données non structurées. 

## <a name="supported-azure-services"></a>Services Azure pris en charge

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Nom de la solution**  | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview)              | [Mobilité des données et migration](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestion intelligente des données](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Prise en charge d’Azure Files (tous les niveaux)** | Oui                          | Oui                      | Oui            | Oui                            |
| **Prise en charge d’Azure NetApp Files**      | Non                           | Oui                      | Oui            | Oui                            |
| **Prise en charge des niveaux chaud/froid des objets blob Azure**   | Non                           | Oui (par le biais de la préversion NFS)    | Oui            | Oui                            |
| **Prise en charge du niveau Archive des objets blob Azure** | Non                           | Non                       | Non             | Oui (en tant que destination de la migration) |
| **Prise en charge d’Azure Data Lake Storage** | Non                           | Non                       | Non             | Non                             |
| **Sources prises en charge**      | Windows Server 2012 R2 et versions ultérieures | Systèmes de fichiers NAS et cloud | Tout NAS et S3 | NAS, Blob, S3                  |

## <a name="supported-protocols-source--destination"></a>Protocoles pris en charge (source/destination)

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Nom de la solution**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilité des données et migration](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestion intelligente des données](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **SMB 2.1**       | Oui | Oui | Oui | Oui |
| **SMB 3.0**       | Oui | Oui | Oui | Oui |
| **SMB 3.1**       | Oui | Oui | Oui | Oui |
| **NFS v3**        | Non  | Oui | Oui | Oui |
| **NFS v4.1**      | Non  | Oui | Non  | Oui |
| **API REST Blob** | Non  | Non  | Oui | Oui |
| **S3**            | Non  | Oui | Oui | Oui |

## <a name="extended-features"></a>Fonctionnalités étendues

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Nom de la solution**  | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilité des données et migration](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestion intelligente des données](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Remappage UID/SID**                   | Non  | Oui                        | Oui | Non                             |
| **Remappage protocole ACL**                | Non  | Non                         | Non  | Non                             |
| **Prise en charge DFS**                           | Oui | Oui                        | Oui | Oui                            |
| **Prise en charge de la limitation**                    | Oui | Oui                        | Oui | Oui                            |
| **Exclusions de modèles de fichiers**               | Non  | Oui                        | Oui | Oui (à l’aide de la fonctionnalité de copie) |
| **Prise en charge des attributs de fichier sélectifs** | Oui | Oui                        | Oui | Oui (pour les attributs étendus)  |
| **Supprimer les propagations**                   | Oui | Oui                        | Oui | Oui                            |
| **Suivre les jonctions NTFS**                 | Non  | Oui                        | Non  | Oui                            |
| **Écraser le propriétaire SMB et le propriétaire du groupe**    | Oui | Oui                        | Oui | Non                             |
| **Rapports sur la chaîne de responsabilité**            | Non  | Oui                        | Non  | Oui                            |
| **Prise en charge d’autres flux de données**    | Non  | Oui                        | Oui | Non                             |
| **Planification de la migration**              | Non  | Oui                        | Oui | Oui                            |
| **Conservation des ACL**                        | Non  | Oui                        | Oui | Oui                            |
| **Prise en charge DACL**                          | Oui | Oui                        | Oui | Oui                            |
| **Prise en charge SACL**                          | Oui | Oui                        | Oui | Non                             |
| **Conservation du temps d’accès**                | Oui | Oui                        | Oui | Oui                            |
| **Conservation de l’heure de modification**              | Oui | Oui                        | Oui | Oui                            |
| **Conservation de l’heure de création**              | Non  | Oui                        | Oui | Oui                            |
| **Prise en charge d’Azure Data Box**       | Oui | Oui                        | Non  | Non                             |
| **Migration des instantanés**                | Non  | Manuel                     | Oui | Non                             |
| **Prise en charge des liens symboliques**                 | Non  | Oui                        | Non  | Oui                            |
| **Prise en charge des liens physiques**                     | Non  | Migrés en tant que fichiers séparés | Oui | Oui                            |
| **Prise en charge des fichiers ouverts/verrouillés**       | Oui | Oui                        | Oui | Oui                            |
| **Migration incrémentielle**                 | Oui | Oui                        | Oui | Oui                            |
| **Prise en charge du basculement**                    | Non  | Oui                        | Oui | Non (basculement manuel uniquement)               |
| **[Autres fonctionnalités](#other-features)**         | [Lien](#azure-file-sync)| [Lien](#dobimigrate) | [Lien](#data-mobility-and-migration) | [Lien](#intelligent-data-management)                |

## <a name="assessment-and-reporting"></a>Évaluation et création de rapports

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Nom de la solution**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilité des données et migration](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestion intelligente des données](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Capacité**                        | Non      | Oui | Oui | Oui            |
| **Nombre de fichiers/dossiers**            | Non      | Oui | Oui | Oui            |
| **Distribution de l’âge dans le temps**      | Non      | Oui | Oui | Oui            |
| **Temps d’accès**                     | Non      | Oui | Oui | Oui            |
| **Heure de modification**                   | Non      | Oui | Oui | Oui            |
| **Heure de création**                   | Non      | Oui | Oui | Oui (SMB uniquement) |
| **État du rapport par fichier/objet** | Partiel | Oui | Oui | Oui            |

## <a name="licensing"></a>Licence

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Nom de la solution**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilité des données et migration](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Gestion intelligente des données](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **BYOL**             | N/A | Oui | Oui | Oui |
| **Engagement Azure** | Oui   | Oui | Oui | Oui |

## <a name="other-features"></a>Autres fonctionnalités

### <a name="azure-file-sync"></a>Azure File Sync

- Validation de hachage interne

### <a name="dobimigrate"></a>DobiMigrate

- Vérifications préalables à la migration
- Planification de la migration
- Test de basculement
- Détecter et alerter en cas d’activité des utilisateurs côté cible avant le basculement
- Migrations pilotées par une stratégie
- Itérations de copie planifiée
- Options configurables pour la gestion de la sécurité du dossier racine
- Exécutions de la vérification à la demande
- Vérification de la lecture des données sur la source et la destination
- Flux de travail graphique et interactif de gestion des erreurs
- Possibilité de restreindre la propagation de certaines opérations comme les suppressions et les mises à jour
- Possibilité de conserver le temps d’accès sur la source (en plus de la destination)
- Possibilité d’exécuter une restauration vers la source pendant le basculement de la migration
- Possibilité de migrer des attributs de fichier SMB sélectionnés
- Possibilité de nettoyer les descripteurs de sécurité NTFS
- Possibilité de remplacer les autorisations NFSv3 et d’écrire des bits dans un mode nouveau sur la cible
- Possibilité de convertir le brouillon de liste de contrôle d’accès NFSv3 POSIX en liste de contrôle d’accès NFSv4
- SMB 1 (CIFS)

### <a name="data-mobility-and-migration"></a>Mobilité des données et migration

- Validation de hachage

### <a name="intelligent-data-management"></a>Gestion intelligente des données

- Migrations basés sur un projet/répertoire
- Nouvelle tentative automatique en cas d’échec
- Évaluation/création de rapports : types de fichiers, taille de fichier, basés sur un projet
- Évaluation/création de rapports : recherches personnalisées basées sur les métadonnées
- Solution complète de gestion du cycle de vie des données pour l’archivage, la réplication, l’analytique
- Accès à l’analytique basée sur l’heure pour un objet blob, des données S3
- Marquage
- Prise en charge 24h/24, 7j/7, 365j/an
- Validation de hachage

*Cette liste a été vérifiée pour la dernière fois le 31 mars 2021.*

## <a name="see-also"></a>Voir aussi

- [Vue d’ensemble de la migration du stockage](../../../common/storage-migration-overview.md)
- [Choisir une solution Azure pour transférer des données](/azure/storage/common/storage-choose-data-transfer-solution?toc=/azure/storage/blobs/toc.json)
- [Migrer vers des partages de fichiers Azure](/azure/storage/files/storage-files-migration-overview)
- [Migrer vers Data Lake Storage avec WANdisco LiveData Platform for Azure](/azure/storage/blobs/migrate-gen2-wandisco-live-data-platform)
- [Copier ou déplacer des données vers le Stockage Azure avec AzCopy](https://aka.ms/azcopy)
- [Migrer des jeux de données volumineux vers le Stockage Blob Azure avec AzReplicate (exemple d’application)](https://github.com/Azure/AzReplicate/tree/master/)
