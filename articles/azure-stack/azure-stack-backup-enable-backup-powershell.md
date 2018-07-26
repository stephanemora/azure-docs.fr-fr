---
title: Activer la sauvegarde d’Azure Stack avec PowerShell | Microsoft Docs
description: Activez le service de sauvegarde d’infrastructure avec Windows PowerShell pour permettre la restauration d’Azure Stack en cas de panne.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/10/2018
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 76a24e7096cbc2a9bcea8bf68e2b333345dbff68
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242952"
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
| $key            | Tapez la **clé de chiffrement** utilisée pour chiffrer chaque sauvegarde. |
| $password       | Saisissez le **mot de passe** de l’utilisateur. |
| $sharepath      | Saisissez le chemin d’accès à l’**emplacement de stockage de sauvegarde**. Vous devez utiliser une chaîne UNC (Universal Naming Convention) pour le chemin d’un partage de fichiers hébergé sur un appareil distinct. Une chaîne UNC spécifie l’emplacement de ressources telles que des appareils ou des fichiers partagés. Pour garantir la disponibilité des données de sauvegarde, l’appareil doit se trouver dans un emplacement distinct. |

   ```powershell
    $username = "domain\backupadmin"
   
    $Secure = Read-Host -Prompt ("Password for: " + $username) -AsSecureString
    $Encrypted = ConvertFrom-SecureString -SecureString $Secure
    $password = ConvertTo-SecureString -String $Encrypted
    
    $BackupEncryptionKeyBase64 = ""
    $tempEncryptionKeyString = ""
    foreach($i in 1..64) { $tempEncryptionKeyString += -join ((65..90) + (97..122) | Get-Random | % {[char]$_}) }
    $tempEncryptionKeyBytes = [System.Text.Encoding]::UTF8.GetBytes($tempEncryptionKeyString)
    $BackupEncryptionKeyBase64 = [System.Convert]::ToBase64String($tempEncryptionKeyBytes)
    $BackupEncryptionKeyBase64
    
    $Securekey = ConvertTo-SecureString -String $BackupEncryptionKeyBase64 -AsPlainText -Force
    $Encryptedkey = ConvertFrom-SecureString -SecureString $Securekey
    $key = ConvertTo-SecureString -String $Encryptedkey
    
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    Set-AzSBackupShare -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $key
   ```
   
##  <a name="confirm-backup-settings"></a>Confirmer les paramètres de sauvegarde

Dans la même session PowerShell, exécutez les commandes suivantes :

   ```powershell
    Get-AzsBackupLocation | Select-Object -Property Path, UserName, AvailableCapacity
   ```

Le résultat doit ressembler à la sortie suivante :

   ```powershell
    Path                        : \\serverIP\AzSBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
   ```

## <a name="next-steps"></a>Étapes suivantes

 - Pour savoir comment exécuter une sauvegarde, voir [Sauvegarde d’Azure Stack](azure-stack-backup-back-up-azure-stack.md ).  
 - Pour savoir comment vérifier que votre sauvegarde a été exécutée, voir [Confirmer que la sauvegarde est terminée dans le portail d’administration](azure-stack-backup-back-up-azure-stack.md ).