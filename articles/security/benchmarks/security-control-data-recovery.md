---
title: Contrôle de sécurité Azure - Récupération des données
description: Contrôle de sécurité de récupération des données
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: c585ebd903d4070f6247456e06efffbc6ec45270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934505"
---
# <a name="security-control-data-recovery"></a>Contrôle de sécurité : Récupération des données

Assurez-vous que toutes les données, configurations et secrets du système sont automatiquement sauvegardés de façon régulière.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1 : garantir des sauvegardes automatisées régulières

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 9,1 | 10.1 | Customer |

Activez la Sauvegarde Azure et configurez la source de sauvegarde (machines virtuelles Azure, SQL Server ou partages de fichiers), ainsi que la fréquence souhaitée et la période de rétention.

Comment activer Sauvegarde Azure :

https://docs.microsoft.com/azure/backup/

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 : effectuer des sauvegardes complètes du système et sauvegarder les clés gérées par le client

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 9.2 | 10.2 | Customer |

Activez la Sauvegarde Azure et la ou les machines virtuelles cibles, ainsi que la fréquence souhaitée et les périodes de rétention. Clés de sauvegarde gérées par le client dans Azure Key Vault.

Comment activer Sauvegarde Azure :

https://docs.microsoft.com/azure/backup/

Comment sauvegarder des clés de coffre de clés dans Azure :

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3 : valider toutes les sauvegardes, y compris les clés gérées par le client

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 9.3 | 10.3 | Customer |

Assurez-vous que la restauration des données du contenu s’effectue régulièrement dans Sauvegarde Azure. Si nécessaire, testez la restauration vers un réseau local virtuel isolé. Testez la restauration des clés gérées par le client sauvegardées.

Comment récupérer des fichiers à partir d’une sauvegarde de machine virtuelle Azure :

https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm

Comment restaurer des clés de coffre de clés dans Azure :

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 : garantir la protection des sauvegardes et des clés gérées par le client

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 9,4 | 10,4 | Customer |

Pour la sauvegarde sur site, le chiffrement au repos est assuré à l’aide de la phrase secrète que vous fournissez lorsque vous sauvegardez sur Azure. Pour les machines virtuelles Azure, les données sont chiffrées au repos à l’aide de Storage Service Encryption (SSE). Vous pouvez activer la suppression réversible dans Key Vault pour protéger les clés contre une suppression accidentelle ou malveillante.

Comment activer la suppression réversible dans Key Vault :

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

## <a name="next-steps"></a>Étapes suivantes

Reportez-vous au contrôle de sécurité suivant : [Réponse aux incidents](security-control-incident-response.md)
