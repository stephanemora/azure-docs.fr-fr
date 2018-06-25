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
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 3a7656e54181c8e8e7b6b1bd39f80ce8ed01c807
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294858"
---
# <a name="update-the-sql-resource-provider"></a>Mettre à jour le fournisseur de ressources SQL
Un nouveau fournisseur de ressources SQL peut être publié quand des builds Azure Stack sont mises à jour. Même si l’adaptateur existant continue de fonctionner, nous vous recommandons d’effectuer une mise à jour dès que possible vers la build la plus récente. Les mises à jour doivent être installées dans l’ordre : vous ne pouvez pas ignorer des versions (voir la liste des versions dans [Prérequis pour déployer le fournisseur de ressources](.\azure-stack-sql-resource-provider-deploy.md#prerequisites)).

Pour mettre à jour le fournisseur de ressources, vous utilisez le script *UpdateSQLProvider.ps1*. Le processus est presque le même que pour installer un fournisseur de ressources, comme dans l’article [Déployer le fournisseur de ressources](.\azure-stack-sql-resource-provider-deploy.md). Le script est inclus avec le téléchargement du fournisseur de ressources.

Le script *UpdateSQLProvider.ps1* crée une machine virtuelle avec le dernier code de fournisseur de ressources et migre les paramètres de l’ancienne machine virtuelle vers la nouvelle. Les paramètres qui migrent incluent des informations sur la base de données et le serveur d’hébergement, ainsi que l’enregistrement DNS nécessaire.

Le script exige d’utiliser les mêmes arguments que ceux décrits pour le script DeploySqlProvider.ps1. Fournissez également le certificat ici. 

Nous vous conseillons de télécharger la dernière image Windows Server 2016 Core à partir de la Gestion de la Place de marché. Si vous devez installer une mise à jour, vous pouvez placer un seul package .MSU dans le chemin de dépendance local. Si plusieurs fichiers .MSU sont trouvés, le script échoue.

Voici un exemple du script *UpdateSQLProvider.ps1* que vous pouvez exécuter à partir de l’invite PowerShell. Veillez à modifier les informations de compte et les mots de passe si nécessaire : 

> [!NOTE]
> Le processus de mise à jour s’applique uniquement aux systèmes intégrés.

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
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
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

## <a name="updatesqlproviderps1-parameters"></a>UpdateSQLProvider.ps1 parameters
Vous pouvez spécifier ces paramètres dans la ligne de commande. Si vous ne le faites pas, ou si la validation d’un paramètre échoue, vous êtes invité à fournir les paramètres obligatoires.

| Nom du paramètre | Description | Commentaire ou valeur par défaut |
| --- | --- | --- |
| **CloudAdminCredential** | Informations d’identification de l’administrateur du cloud, nécessaires pour accéder au point de terminaison privilégié. | _Obligatoire_ |
| **AzCredential** | Informations d’identification du compte d’administration de service Azure Stack. Utilisez les mêmes informations d’identification que celles utilisées pour le déploiement d’Azure Stack. | _Obligatoire_ |
| **VMLocalCredential** | Informations d’identification du compte d’administrateur local de la machine virtuelle du fournisseur de ressources SQL. | _Obligatoire_ |
| **PrivilegedEndpoint** | Adresse IP ou nom DNS du point de terminaison privilégié. |  _Obligatoire_ |
| **DependencyFilesLocalPath** | Votre fichier de certificat .pfx doit également être placé dans ce répertoire. | _Facultatif_ (_obligatoire_ pour les nœuds multiples) |
| **DefaultSSLCertificatePassword** | Mot de passe pour le certificat .pfx. | _obligatoire_ |
| **MaxRetryCount** | Nombre de fois où vous souhaitez réessayer chaque opération en cas d’échec.| 2 |
| **RetryDuration** |Délai d’attente entre les tentatives, en secondes. | 120 |
| **Désinstaller** | Supprime le fournisseur de ressources et toutes les ressources associées (voir les remarques ci-dessous). | Non  |
| **DebugMode** | Empêche le nettoyage automatique en cas d’échec. | Non  |


## <a name="next-steps"></a>Étapes suivantes

[Tenir à jour le fournisseur de ressources SQL](azure-stack-sql-resource-provider-maintain.md)
