---
title: Activer la sauvegarde d’Azure Stack avec PowerShell | Microsoft Docs
description: Activez le service de sauvegarde d’infrastructure avec Windows PowerShell pour permettre la restauration d’Azure Stack en cas de panne.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 8fe7f0ddd630cfca0242af6cc1d728bdef163352
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2018
ms.locfileid: "41946501"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Activer la sauvegarde d’Azure Stack avec PowerShell

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Activez le service Infrastructure Backup avec Windows PowerShell pour effectuer des sauvegardes périodiques de ce qui suit :
 - Service d’identité interne et certificat racine
 - Plans, offres et abonnements de l’utilisateur
 - Secrets KeyVault
 - Stratégies et rôles utilisateur RBAC

Vous pouvez accéder aux cmdlets PowerShell pour activer la sauvegarde, démarrer la sauvegarde et obtenir des informations sur la sauvegardes via le point de terminaison de gestion d’opérateur.

## <a name="prepare-powershell-environment"></a>Préparer l’environnement PowerShell

Pour obtenir des instructions sur la configuration de l’environnement PowerShell, consultez [Installer PowerShell pour Azure Stack](azure-stack-powershell-install.md). Pour vous connecter à Azure Stack, consultez [Configurer l’environnement de l’opérateur et se connecter à Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Indiquer le partage de sauvegarde, les informations d’identification et la clé de chiffrement pour activer la sauvegarde

Dans la même session PowerShell, modifiez le script PowerShell suivant en ajoutant les variables de votre environnement. Exécutez le script mis à jour pour indiquer le partage de sauvegarde, les informations d’identification et la clé de chiffrement pour le service de sauvegarde d’infrastructure.

| Variable        | Description   |
|---              |---                                        |
| $username       | Saisissez le **Nom d’utilisateur** à l’aide du domaine et du nom d’utilisateur de l’emplacement du lecteur partagé avec un accès suffisant pour lire et écrire des fichiers. Par exemple : `Contoso\backupshareuser`. |
| $password       | Saisissez le **mot de passe** de l’utilisateur. |
| $sharepath      | Saisissez le chemin d’accès à l’**emplacement de stockage de sauvegarde**. Vous devez utiliser une chaîne UNC (Universal Naming Convention) pour le chemin d’un partage de fichiers hébergé sur un appareil distinct. Une chaîne UNC spécifie l’emplacement de ressources telles que des appareils ou des fichiers partagés. Pour garantir la disponibilité des données de sauvegarde, l’appareil doit se trouver dans un emplacement distinct. |
| $frequencyInHours | La fréquence en heures détermine la fréquence à laquelle les sauvegardes sont créées. La valeur par défaut est 12. Scheduler prend en charge un maximum de 12 heures et un minimum de 4 heures.|
| $retentionPeriodInDays | La période de rétention en jours détermine le nombre de jours pendant lesquels les sauvegardes sont conservées sur l’emplacement externe. La valeur par défaut est 7. Scheduler prend en charge un maximum de 14 jours et un minimum de 2 jours. Les sauvegardes antérieures à la période de rétention sont automatiquement supprimées de l’emplacement externe.|
|     |     |

   ```powershell
    # Example username:
    $username = "domain\backupadmin"
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"
   
    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString
    
    # The encryption key is generated using the New-AzsEncryptionKeyBase64 cmdlet provided in Azure Stack PowerShell.
    # Make sure to store your encryption key in a secure location after it is generated.
    $Encryptionkey = New-AzsEncryptionKeyBase64
    $key = ConvertTo-SecureString -String ($Encryptionkey) -AsPlainText -Force

    Set-AzsBackupShare -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $key
   ```
   
##  <a name="confirm-backup-settings"></a>Confirmer les paramètres de sauvegarde

Dans la même session PowerShell, exécutez les commandes suivantes :

   ```powershell
    Get-AzsBackupLocation | Select-Object -Property Path, UserName
   ```

Le résultat doit ressembler à l’exemple de sortie suivant :

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
   ```

## <a name="update-backup-settings"></a>Mettre à jour les paramètres de sauvegarde
Dans la même session PowerShell, vous pouvez mettre à jour les valeurs par défaut pour la période de rétention et la fréquence des sauvegardes. 

   ```powershell
    #Set the backup frequency and retention period values.
    $frequencyInHours = 10
    $retentionPeriodInDays = 5

    Set-AzsBackupShare -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays
    Get-AzsBackupLocation | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

Le résultat doit ressembler à l’exemple de sortie suivant :

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

## <a name="next-steps"></a>Étapes suivantes

 - Pour savoir comment exécuter une sauvegarde, voir [Sauvegarde d’Azure Stack](azure-stack-backup-back-up-azure-stack.md ).  
 - Pour savoir comment vérifier que votre sauvegarde a été exécutée, voir [Confirmer que la sauvegarde est terminée dans le portail d’administration](azure-stack-backup-back-up-azure-stack.md ).
