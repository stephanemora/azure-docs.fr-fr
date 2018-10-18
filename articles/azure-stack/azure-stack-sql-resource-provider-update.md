---
title: Mise à jour du fournisseur de ressources SQL Azure Stack | Microsoft Docs
description: Découvrez comment vous pouvez mettre à jour le fournisseur de ressources SQL Azure Stack.
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
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 01815de68552e30ac5158c68d09cbb6157d03034
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49362971"
---
# <a name="update-the-sql-resource-provider"></a>Mettre à jour le fournisseur de ressources SQL

*S’applique à : systèmes intégrés Azure Stack.*

Un nouveau fournisseur de ressources SQL peut être publié lorsque le build Azure Stack est mis à jour. Même si l’adaptateur existant continue de fonctionner, nous vous recommandons d’effectuer une mise à jour dès que possible vers le build le plus récent.

>[!IMPORTANT]
>Vous devez installer les mises à jour dans l’ordre de leur publication. Vous ne pouvez pas ignorer les versions. Reportez-vous à la liste des versions dans [Déployer les conditions préalables du fournisseur de ressources](.\azure-stack-sql-resource-provider-deploy.md#prerequisites).

## <a name="overview"></a>Vue d’ensemble

Pour mettre à jour le fournisseur de ressources, utilisez le script *UpdateSQLProvider.ps1*. Le script est inclus avec le téléchargement du nouveau fournisseur de ressources SQL. Le processus de mise à jour est similaire au processus utilisé pour [déployer le fournisseur de ressources](.\azure-stack-sql-resource-provider-deploy.md). Le script de mise à jour utilise les mêmes arguments que le script DeploySqlProvider.ps1. Vous devrez fournir les informations de certificat.

### <a name="update-script-processes"></a>Processus du script de mise à jour

Le script *UpdateSQLProvider.ps1* crée une machine virtuelle (VM) avec le tout dernier code de fournisseur de ressources.

> [!NOTE]
> Nous vous conseillons de télécharger la dernière image Windows Server 2016 Core à partir de la Gestion de la Place de marché. Si vous devez installer une mise à jour, vous pouvez placer un **seul** package MSU dans le chemin de dépendance local. Le script échoue s’il existe plusieurs fichiers MSU à cet emplacement.

Une fois qu’il a créé une machine virtuelle, le script *UpdateSQLProvider.ps1* migre les paramètres suivants depuis la machine virtuelle de l’ancien fournisseur :

* les informations de base de données
* les informations sur le serveur d’hébergement
* l’enregistrement DNS requis

### <a name="update-script-powershell-example"></a>Exemple de script de mise à jour PowerShell

Vous pouvez modifier et exécuter le script suivant depuis une session PowerShell ISE avec élévation de privilèges. 

Veillez à modifier les informations de compte et les mots de passe requis par votre environnement, si nécessaire.

> [!NOTE]
> Ce processus de mise à jour s’applique uniquement aux systèmes Azure Stack intégrés.

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service administrator account (this can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -AzureEnvironment $AzureEnvironment `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `

 ```

## <a name="updatesqlproviderps1-parameters"></a>UpdateSQLProvider.ps1 parameters

Vous pouvez spécifier les paramètres suivants à partir de la ligne de commande lorsque vous exécutez le script. Si vous ne le faites pas, ou si la validation d’un paramètre échoue, vous êtes invité à fournir les paramètres requis.

| Nom du paramètre | Description | Commentaire ou valeur par défaut |
| --- | --- | --- |
| **CloudAdminCredential** | Informations d’identification de l’administrateur du cloud, nécessaires pour accéder au point de terminaison privilégié. | _Obligatoire_ |
| **AzCredential** | Informations d’identification du compte d’administration de service Azure Stack. Utilisez les mêmes informations d’identification que celles utilisées pour le déploiement d’Azure Stack. | _Obligatoire_ |
| **VMLocalCredential** | Informations d’identification du compte d’administrateur local de la machine virtuelle du fournisseur de ressources SQL. | _Obligatoire_ |
| **PrivilegedEndpoint** | Adresse IP ou nom DNS du point de terminaison privilégié. |  _Obligatoire_ |
| **AzureEnvironment** | L’environnement Azure du compte administrateur de service que vous avez utilisé pour déployer Azure Stack. Nécessaire uniquement pour les déploiements Azure AD. Les noms d’environnement pris en charge sont **AzureCloud**, **AzureUSGovernment**, ou, si vous utilisez un compte Azure AD en Chine, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Vous devez également placer le fichier de certificat .pfx dans ce répertoire. | _Facultatif pour un seul nœud, mais obligatoire pour plusieurs nœuds._ |
| **DefaultSSLCertificatePassword** | Mot de passe pour le certificat .pfx. | _Obligatoire_ |
| **MaxRetryCount** | Nombre de fois où vous souhaitez réessayer chaque opération en cas d’échec.| 2 |
| **RetryDuration** |Délai d’attente entre les tentatives, en secondes. | 120 |
| **Désinstaller** | Supprime le fournisseur de ressources et toutes les ressources associées. | Non  |
| **DebugMode** | Empêche le nettoyage automatique en cas d’échec. | Non  |

## <a name="next-steps"></a>Étapes suivantes

[Tenir à jour le fournisseur de ressources SQL](azure-stack-sql-resource-provider-maintain.md)
