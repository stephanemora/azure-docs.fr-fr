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
ms.openlocfilehash: 5fab656734d0984cf44a9fe1f29fd73530bd9aa8
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259853"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Activer la sauvegarde d’Azure Stack avec PowerShell

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Activez le service Infrastructure Backup avec Windows PowerShell pour effectuer des sauvegardes périodiques de ce qui suit :
 - Service d’identité interne et certificat racine
 - Plans, offres et abonnements de l’utilisateur
 - Secrets KeyVault
 - Stratégies et rôles utilisateur RBAC

Vous pouvez accéder aux cmdlets PowerShell pour activer la sauvegarde, démarrer la sauvegarde et obtenir des informations sur la sauvegarde via le point de terminaison de gestion d’opérateur.

## <a name="prepare-powershell-environment"></a>Préparer l’environnement PowerShell

Pour obtenir des instructions sur la configuration de l’environnement PowerShell, consultez [Installer PowerShell pour Azure Stack](azure-stack-powershell-install.md).

## <a name="generate-a-new-encryption-key"></a>Générer une nouvelle clé de chiffrement

Installer et configurez PowerShell pour Azure Stack et les outils Azure Stack.
 - Consultez la page [Devenir opérationnel avec PowerShell dans Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart).
 - Consultez [Télécharger les outils Azure Stack à partir de GitHub](azure-stack-powershell-download.md)

Ouvrez Windows PowerShell avec une invite et exécutez les commandes suivantes :
   
   ```powershell
    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
   ```
   
Dans la même session PowerShell, exécutez les commandes suivantes :

   ```powershell
   $encryptionkey = New-EncryptionKeyBase64
   ```

> [!Warning]  
> Vous devez utiliser les outils AzureStack pour générer la clé.

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Indiquer le partage de sauvegarde, les informations d’identification et la clé de chiffrement pour activer la sauvegarde

Dans la même session PowerShell, modifiez le script PowerShell suivant en ajoutant les variables de votre environnement. Exécutez le script mis à jour pour indiquer le partage de sauvegarde, les informations d’identification et la clé de chiffrement pour le service de sauvegarde d’infrastructure.

| Variable        | Description   |
|---              |---                                        |
| $username       | Saisissez le **Nom d’utilisateur** à l’aide du domaine et du nom d’utilisateur de l’emplacement du lecteur partagé avec un accès suffisant pour lire et écrire des fichiers. Par exemple : `Contoso\backupshareuser`. |
| $password       | Saisissez le **mot de passe** de l’utilisateur. |
| $sharepath      | Saisissez le chemin d’accès à l’**emplacement de stockage de sauvegarde**. Vous devez utiliser une chaîne UNC (Universal Naming Convention) pour le chemin d’un partage de fichiers hébergé sur un appareil distinct. Une chaîne UNC spécifie l’emplacement de ressources telles que des appareils ou des fichiers partagés. Pour garantir la disponibilité des données de sauvegarde, l’appareil doit se trouver dans un emplacement distinct. |

   ```powershell
    $username = "domain\backupoadmin"
    $password = "password"
    $credential = New-Object System.Management.Automation.PSCredential($username, ($password| ConvertTo-SecureString -asPlainText -Force))  
    $location = Get-AzsLocation
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"
    
    Set-AzSBackupShare -Location $location.Name -Path $sharepath -UserName $credential.UserName -Password $credential.GetNetworkCredential().password -EncryptionKey $encryptionkey
   ```
   
##  <a name="confirm-backup-settings"></a>Confirmer les paramètres de sauvegarde

Dans la même session PowerShell, exécutez les commandes suivantes :

   ```powershell
   Get-AzsBackupLocation | Select-Object -ExpandProperty externalStoreDefault | Select-Object -Property Path, UserName, Password | ConvertTo-Json
   ```

Le résultat doit ressembler à la sortie JSON suivante :

   ```json
      {
    "ExternalStoreDefault":  {
        "Path":  "\\\\serverIP\\AzSBackupStore\\contoso.com\\seattle",
        "UserName":  "domain\backupoadmin",
        "Password":  null
       }
   } 
   ```

## <a name="next-steps"></a>Étapes suivantes

 - Pour savoir comment exécuter une sauvegarde, voir [Sauvegarde d’Azure Stack](azure-stack-backup-back-up-azure-stack.md ).  
 - Pour savoir comment vérifier que votre sauvegarde a été exécutée, voir [Confirmer que la sauvegarde est terminée dans le portail d’administration](azure-stack-backup-back-up-azure-stack.md ).
