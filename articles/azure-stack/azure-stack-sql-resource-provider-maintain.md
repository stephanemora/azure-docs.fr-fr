---
title: Gestion du fournisseur de ressources SQL sur Azure Stack | Microsoft Docs
description: Découvrez comment vous pouvez gérer le service de fournisseur de ressources SQL sur Azure Stack.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: e7ddbe1235b3957a1e0cb7693ee728bfdbf9db6b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295657"
---
# <a name="maintenance-operations"></a>Opérations de maintenance 
Le fournisseur de ressources SQL est une machine virtuelle verrouillée. La mise à jour de la sécurité de la machine virtuelle du fournisseur de ressources peut être effectuée par le biais du point de terminaison JEA (Just Enough Administration) PowerShell _DBAdapterMaintenance_. Pour faciliter ces opérations, un script est fourni avec le package d’installation du fournisseur de ressources.

## <a name="patching-and-updating"></a>Mise à jour corrective et mise à jour
Le fournisseur de ressources SQL n’est pas traité au sein d’Azure Stack, car il s’agit d’un composant additionnel. Microsoft fournira au besoin des mises à jour du fournisseur de ressources SQL. Le fournisseur de ressources SQL est instancié sur la machine virtuelle d’un _utilisateur_ sous l’abonnement du fournisseur par défaut. Par conséquent, il est nécessaire de fournir des correctifs de Windows, des signatures d’antivirus, etc. Les packages de mise à jour Windows fournis dans le cadre du cycle de correction et de mise à jour peuvent servir à appliquer les mises à jour à la machine virtuelle Windows. Lorsqu’une mise à jour d’adaptateur est publiée, un script est fourni pour appliquer la mise à jour. Ce script crée une nouvelle machine virtuelle de fournisseur de ressources et migre tous les états dont vous disposez déjà.

 ## <a name="backuprestoredisaster-recovery"></a>Sauvegarde/restauration/récupération d’urgence
 Le fournisseur de ressources SQL n’est pas sauvegardé au cours du processus Continuité d’activité-récupération d’urgence d’Azure Stack, car il s’agit d’un composant additionnel. Des scripts sont fournis afin de faciliter :
- La sauvegarde des informations d’état nécessaires (stockées dans un compte de stockage Azure Stack)
- La restauration du fournisseur de ressources si une récupération de l’ensemble de la pile est nécessaire.
Les serveurs de base de données doivent être récupérés en premier (si nécessaire), avant le fournisseur de ressources.

## <a name="updating-sql-credentials"></a>Mise à jour des informations d’identification SQL
Vous êtes responsable de la création et de la gestion des comptes d’administrateur système sur vos serveurs SQL. Le fournisseur de ressources a besoin d’un compte disposant de ces privilèges pour gérer les bases de données pour le compte d’utilisateurs. Il n’a pas besoin d’accéder aux données de ces bases de données. Pour mettre à jour les mots de passe d’administrateur système sur vos serveurs SQL, vous pouvez utiliser la fonctionnalité de mise à jour de l’interface d’administration du fournisseur de ressources afin de modifier le mot de passe stocké utilisé par ce dernier. Ces mots de passe sont stockés dans un coffre de clés sur votre instance Azure Stack.

Pour modifier les paramètres, cliquez sur **Parcourir** &gt; **RESSOURCES ADMINISTRATIVES** &gt; **Serveurs d’hébergement SQL** &gt; **Connexions SQL** et sélectionnez un nom de connexion. La modification doit d’abord être effectuée sur l’instance SQL (et sur tous les réplicas, si nécessaire). Dans le volet **Paramètres**, cliquez sur **Mot de passe**.

![Mettre à jour le mot de passe de l’administrateur](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="secrets-rotation"></a>Rotation des secrets 
*Ces instructions s’appliquent uniquement aux systèmes intégrés Azure Stack versions 1804 et ultérieures. N’essayez pas d’effectuer la rotation des secrets sur les versions d’Azure Stack antérieures à la version 1804.*

Quand vous utilisez les fournisseurs de ressources SQL et MySQL avec des systèmes intégrés Azure Stack, vous pouvez effectuer une rotation des secrets d’infrastructure (déploiement) suivants :
- Certificat SSL externe [fourni au cours du déploiement](azure-stack-pki-certs.md).
- Mot de passe de compte d’administrateur local des machines virtuelles du fournisseur de ressources fourni au moment du déploiement.
- Mot de passe (dbadapterdiag) utilisateur de diagnostic du fournisseur de ressources.

### <a name="powershell-examples-for-rotating-secrets"></a>Exemples PowerShell pour la rotation des secrets

**Changer tous les secrets en même temps**
```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd  `
    -VMLocalCredential $localCreds
```

**Changer le mot de passe utilisateur de diagnostic uniquement**
```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd 
```

**Changer le mot de passe de compte d’administrateur local des machines virtuelles**
```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**Changer le certificat SSL**
```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd 
```

### <a name="secretrotationsqlproviderps1-parameters"></a>Paramètres SecretRotationSQLProvider.ps1

|Paramètre|Description|
|-----|-----|
|AzCredential|Informations d’identification du compte d’administration de service Azure Stack.|
|CloudAdminCredential|Informations d’identification du compte de domaine d’administrateur cloud d’Azure Stack.|
|PrivilegedEndpoint|Point de terminaison privilégié pour accéder à Get-AzureStackStampInformation.|
|DiagnosticsUserPassword|Mot de passe utilisateur des diagnostics.|
|VMLocalCredential|Compte d’administrateur local de la machine virtuelle MySQLAdapter.|
|DefaultSSLCertificatePassword|Mot de passe du certificat SSL par défaut (*.pfx)|
|DependencyFilesLocalPath|Chemin local des fichiers de dépendances|
|     |     |

### <a name="known-issues"></a>Problèmes connus
**Problème** : Les journaux de la rotation des secrets ne sont pas automatiquement collectés en cas d’échec de l’exécution du script personnalisé de la rotation des secrets.

**Solution de contournement** : Utilisez l’applet de commande Get-AzsDBAdapterLogs pour collecter tous les journaux du fournisseur de ressources, notamment AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log, sous C:\Logs.

## <a name="update-the-virtual-machine-operating-system"></a>Mettre à jour le système d’exploitation de la machine virtuelle
Il existe plusieurs façons de mettre à jour la machine virtuelle Windows Server :
- Installer le package de fournisseur de ressources le plus récent à l’aide d’une image corrigée de Windows Server 2016 Core
- Installer un package Windows Update pendant l’installation ou la mise à jour du fournisseur de ressources

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Mettre à jour les définitions Windows Defender de machine virtuelle
Suivez les étapes ci-après pour mettre à jour les définitions Defender :

1. Télécharger la mise à jour des définitions Windows Defender à partir de [Définitions Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions).

    Dans cette page, sous « Télécharger et installer les définitions manuellement », télécharger le fichier 64 bits « Antivirus Windows Defender pour Windows 10 et Windows 8.1 » 
    
    Lien direct : https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64.

2. Créer une session PowerShell sur le point de terminaison de maintenance de la machine virtuelle de l’adaptateur de fournisseur de ressources SQL
3. Copier le fichier de mise à jour des définitions pour la machine de l’adaptateur de base de données à l’aide de la session du point de terminaison de maintenance
4. Sur la session PowerShell de maintenance, appeler la commande _Update-DBAdapterWindowsDefenderDefinitions_
5. Après l’installation, il est recommandé de supprimer le fichier de mise à jour des définitions utilisé. Vous pouvez le supprimer dans la session de maintenance à l’aide de la commande _Remove-ItemOnUserDrive)_.


Voici un exemple de script pour mettre à jour les définitions Defender (remplacer l’adresse ou le nom de la machine virtuelle par la valeur réelle) :

```powershell
# Set credentials for the RP VM local admin user
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession 
```


## <a name="collect-diagnostic-logs"></a>Collecter des journaux de diagnostic
Le fournisseur de ressources SQL est une machine virtuelle verrouillée. S’il s’avère nécessaire de collecter des journaux à partir de la machine virtuelle, un point de terminaison PowerShell JEA (Just Enough Administration) _DBAdapterDiagnostics_ est fourni à cette fin. Deux commandes sont disponibles via ce point de terminaison :

- **Get-AzsDBAdapterLog**. Prépare un package zip contenant des journaux de diagnostic de fournisseur de ressources et le place sur le lecteur de l’utilisateur de session. La commande peut être appelée sans paramètres. Elle collecte alors les journaux des quatre dernières heures.
- **Remove-AzsDBAdapterLog**. Nettoie les packages de journaux existants sur la machine virtuelle du fournisseur de ressources.

Un compte d’utilisateur appelé **dbadapterdiag** est créé pendant le déploiement ou la mise à jour d’un fournisseur de ressources pour se connecter au point de terminaison de diagnostics et extraire les journaux de fournisseur de ressources. Le mot de passe de ce compte est le même que celui fourni pour le compte d’administrateur local pendant le déploiement/la mise à jour.

Pour utiliser ces commandes, vous devez créer une session PowerShell à distance sur la machine virtuelle du fournisseur de ressources, puis appeler la commande. Vous pouvez éventuellement fournir les paramètres FromDate et ToDate. Si vous ne spécifiez pas l’un de ces paramètres, ou les deux, la valeur FromDate est quatre heures avant l’heure actuelle et la valeur ToDate est l’heure actuelle.

Cet exemple de script illustre l’utilisation de ces commandes :

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample captures logs from the previous one hour
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup logs
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session
$session | Remove-PSSession
```

## <a name="next-steps"></a>Étapes suivantes
[Ajouter des serveurs d’hébergement SQL Server](azure-stack-sql-resource-provider-hosting-servers.md)
