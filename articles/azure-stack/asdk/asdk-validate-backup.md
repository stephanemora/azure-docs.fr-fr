---
title: Valider une sauvegarde Azure Stack à l’aide du Kit de développement Azure Stack (ASDK) | Microsoft Docs
description: Comment valider une sauvegarde de systèmes intégrés Azure Stack à l’aide du Kit de développement Azure Stack (ASDK).
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 09/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 12d5c2448bc6a5241f6aa4e283acdf9c5a0f602a
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265649"
---
# <a name="use-the-asdk-to-validate-an-azure-stack-backup"></a>Utiliser l’ASDK pour valider une sauvegarde Azure Stack
Après déploiement d’Azure Stack et approvisionnement de ressources utilisateur telles que des offres, des plans, des quotas et des abonnements, vous devez [activer la sauvegarde d’infrastructure Azure Stack](../azure-stack-backup-enable-backup-console.md). La planification et l’exécution de sauvegardes d’infrastructure régulières garantit que les données de gestion d’infrastructure ne seront pas perdues en cas de défaillance catastrophique du matériel ou du service.

> [!TIP]
> Avant d’entamer cette procédure, nous vous recommandons d’[exécuter une sauvegarde à la demande](../azure-stack-backup-back-up-azure-stack.md) afin de disposer d’une copie des données d’infrastructure les plus récentes. Une fois la sauvegarde terminée avec succès, veillez à capturer l’ID de sauvegarde. Cet ID sera nécessaire lors de la récupération à partir du cloud. 

Les sauvegardes d’infrastructure Azure Stack contiennent des données importantes sur votre cloud, qui peuvent être restaurées lors du redéploiement d’Azure Stack. L’ASDK permet de valider ces sauvegardes sans que cela ait d’incidence sur votre cloud de production. 

La validation de sauvegardes sur l’ASDK est prise en charge pour les scénarios suivants :

|Scénario|Objectif|
|-----|-----|
|Valider des sauvegardes d’infrastructure à partir d’une solution intégrée.|Effectuer une validation de courte durée du fait que les données dans la sauvegarde sont valides.|
|Découvrir le flux de travail de récupération de bout en bout.|Utiliser l’ASDK pour valider l’ensemble de l’expérience de sauvegarde et de restauration.|
|     |     |

Le scénario suivant **n’est pas** pris en charge lors de la validation de sauvegardes sur l’ASDK :

|Scénario|Objectif|
|-----|-----|
|Build de l’ASDK pour la sauvegarde et la restauration de build.|Restaurer des données de sauvegarde d’une version antérieure vers une version plus récente de l’ASDK.|
|     |     |


## <a name="cloud-recovery-deployment"></a>Déploiement de récupération cloud
Vous pouvez valide les sauvegardes d’infrastructure de votre déploiement de systèmes intégrés en effectuant un déploiement de récupération cloud de l’ASDK. Dans ce type de déploiement, des données de service spécifiques sont restaurées à partir de la sauvegarde, une fois l’ASDK installé sur l’ordinateur hôte.



### <a name="cloud-recovery-prerequisites"></a>Conditions préalables de récupération cloud
Avant de commencer un déploiement de récupération cloud de l’ASDK, vérifiez que vous disposez des informations suivantes :

|Configuration requise|Description|
|-----|-----|
|Chemin d'accès du partage de la sauvegarde.|Chemin d'accès du partage de fichiers UNC de la dernière sauvegarde Azure Stack qui sera utilisée pour récupérer les informations d’infrastructure d’Azure Stack. Ce partage local sera créé pendant le processus de déploiement de la récupération cloud.|
|Clé de chiffrement de la sauvegarde.|Clé de chiffrement utilisée pour planifier la sauvegarde d’infrastructure à exécuter à l’aide du portail d’administration Azure Stack.|
|ID de la sauvegarde à restaurer.|ID de sauvegarde, sous la forme alphanumérique « xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx », identifiant la sauvegarde à restaurer lors de la récupération cloud.|
|Adresse IP du serveur de temps.|Une adresse IP de serveur de temps valide, telle que 132.163.97.2, est requise pour le déploiement d’Azure Stack.|
|Mot de passe du certificat externe.|Mot de passe du certificat externe utilisé par Azure Stack. La sauvegarde de l’autorité de certification contient des certificats externes qui doivent être restaurés avec ce mot de passe.|
|     |     | 

## <a name="prepare-the-host-computer"></a>Préparer l’ordinateur hôte 
Comme pour un déploiement normal de l’ASDK, l’environnement du système hôte de l’ASDK doit être préparé pour l’installation. Une fois préparé, l’ordinateur hôte du kit de développement Azure Stack démarre à partir du disque dur de la machine virtuelle CloudBuilder.vhdx pour commencer le déploiement du kit.

Sur l’ordinateur hôte de l’ASDK, téléchargez un nouveau fichier cloudbuilder.vhdx de la même version que l’Azure Stack sauvegardé, puis suivez les instructions de [préparation de l’ordinateur hôte de l’ASDK](asdk-prepare-host.md).

Lorsque le serveur hôte redémarre à partir du fichier cloudbuilder.vhdx, vous devez créer un partage de fichiers et y copier vos données de sauvegarde. Le partage de fichiers doit être accessible au compte exécutant le programme d’installation, à savoir le compte Administrator dans ces exemples de commandes PowerShell : 

```powershell
$shares = New-Item -Path "c:\" -Name "Shares" -ItemType "directory"
$azsbackupshare = New-Item -Path $shares.FullName -Name "AzSBackups" -ItemType "directory"
New-SmbShare -Path $azsbackupshare.FullName -FullAccess ($env:computername + "\Administrator")  -Name "AzSBackups"
```

Ensuite, copiez vos derniers fichiers de sauvegarde Azure Stack vers le partage nouvellement créé. La structure de dossiers au sein du partage doit être la suivante : `\\<ComputerName>\AzSBackups\MASBackup\<BackupID>\`.

## <a name="deploy-the-asdk-in-cloud-recovery-mode"></a>Déployer l’ASDK en mode de récupération cloud
Le script **InstallAzureStackPOC.ps1** est utilisé pour lancer la récupération cloud. 

> [!IMPORTANT]
> L’installation d’ASDK prend en charge une seule carte réseau de mise en réseau. Si vous avez plusieurs cartes réseau, vérifiez qu’une seule d’entre elles est activée (et que toutes les autres sont désactivées) avant d’exécuter le script de déploiement.

Modifiez les commandes PowerShell suivantes pour votre environnement, puis exécutez-les pour déployer l’ASDK en mode de récupération cloud :

```powershell
cd C:\CloudDeployment\Setup     
$adminPass = Get-Credential Administrator
$key = ConvertTo-SecureString "<Your backup encryption key>" -AsPlainText -Force ` 
$certPass = Read-Host -AsSecureString  

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
-BackupEncryptionKeyBase64 $key -BackupStoreCredential $adminPass -BackupId "<Backup ID to restore>" `
-TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

## <a name="restore-infrastructure-data-from-backup"></a>Restaurer les données de l’infrastructure à partir de la sauvegarde
Après un déploiement de récupération cloud réussi, vous devez effectuer la restauration à l’aide de cmdlet **restauration-AzureStack**. 

Après vous être connecté en tant qu’opérateur Azure Stack, [installez Azure Stack PowerShell](asdk-post-deploy.md#install-azure-stack-powershell), puis, en remplaçant le paramètre `Name` par votre ID de sauvegarde, exécutez la commande suivante :

```powershell
Restore-AzsBackup -Name "<BackupID>"
```
Attendez 60 minutes après l’appel de cette cmdlet pour démarrer la vérification des données de sauvegarde sur l’ASDK récupéré du cloud.

## <a name="next-steps"></a>Étapes suivantes
[Inscrire Azure Stack](asdk-register.md)

