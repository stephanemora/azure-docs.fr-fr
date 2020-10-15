---
title: Contrôle de sécurité Azure - Récupération des données
description: Contrôle de sécurité Azure – Récupération de données
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 4f3e8540902809f951a441aa2fe8d00026c44d82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81408598"
---
# <a name="security-control-data-recovery"></a>Contrôle de sécurité : Récupération des données

Assurez-vous que toutes les données, configurations et secrets du système sont automatiquement sauvegardés de façon régulière.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1 : garantir des sauvegardes automatisées régulières

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 9,1 | 10.1 | Customer |

Activez la Sauvegarde Azure et configurez la source de sauvegarde (machines virtuelles Azure, SQL Server ou partages de fichiers), ainsi que la fréquence souhaitée et la période de rétention.

- [Guide pratique pour activer la Sauvegarde Azure](https://docs.microsoft.com/azure/backup/)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 : effectuer des sauvegardes complètes du système et sauvegarder les clés gérées par le client

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 9.2 | 10.2 | Customer |

Activez la Sauvegarde Azure et la ou les machines virtuelles cibles, ainsi que la fréquence souhaitée et les périodes de rétention. Clés de sauvegarde gérées par le client dans Azure Key Vault.

- [Guide pratique pour activer la Sauvegarde Azure](https://docs.microsoft.com/azure/backup/)

- [Guide pratique pour sauvegarder des clés de coffre de clés dans Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3 : valider toutes les sauvegardes, y compris les clés gérées par le client

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 9.3 | 10.3 | Customer |

Assurez-vous que la restauration des données du contenu s’effectue régulièrement dans Sauvegarde Azure. Testez la restauration des clés gérées par le client sauvegardées.

- [Guide pratique pour récupérer des fichiers à partir d’une sauvegarde de machines virtuelles Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm)

- [Guide pratique pour restaurer des clés de coffre de clés dans Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 : garantir la protection des sauvegardes et des clés gérées par le client

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 9,4 | 10,4 | Customer |

Pour la sauvegarde sur site, le chiffrement au repos est assuré à l’aide de la phrase secrète que vous fournissez lorsque vous sauvegardez sur Azure. Pour les machines virtuelles Azure, les données sont chiffrées au repos à l’aide de Storage Service Encryption (SSE). Utilisez le contrôle d’accès en fonction du rôle pour protéger les sauvegardes et les clés gérées par le client.  

Activez la suppression réversible et la protection contre la purge dans Key Vault pour protéger les clés contre une suppression accidentelle ou malveillante.  Si le Stockage Azure est utilisé pour stocker les sauvegardes, la suppression réversible vous permet d’enregistrer et de récupérer vos données en cas de suppression d’objets blob ou d’instantanés d’objets blob. 

- [Présentation d’Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

- [Guide pratique pour activer la suppression réversible et la protection contre la purge dans Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Suppression réversible pour les objets blob de Stockage Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)


## <a name="next-steps"></a>Étapes suivantes

- Consultez le contrôle de sécurité suivant :  [Réponse aux incidents](security-control-incident-response.md)