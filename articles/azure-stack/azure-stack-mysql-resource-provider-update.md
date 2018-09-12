---
title: Mise à jour du fournisseur de ressources mySQL Azure Stack | Microsoft Docs
description: Découvrez comment vous pouvez mettre à jour le fournisseur de ressources mySQL Azure Stack.
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
ms.date: 09/04/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 86e72787347cddd399fbdde4cd943b86ba48375f
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697830"
---
# <a name="update-the-mysql-resource-provider"></a>Mettre à jour le fournisseur de ressources MySQL 

*S’applique à : systèmes intégrés Azure Stack.*

Un nouvel adaptateur de fournisseur de ressources SQL peut être publié quand des builds Azure Stack sont mises à jour. Même si l’adaptateur existant continue de fonctionner, nous vous recommandons d’effectuer une mise à jour dès que possible vers la build la plus récente. 

>[!IMPORTANT]
>Vous devez installer les mises à jour dans l’ordre de leur publication. Vous ne pouvez pas ignorer les versions. Reportez-vous à la liste des versions dans [Déployer les conditions préalables du fournisseur de ressources](.\azure-stack-mysql-resource-provider-deploy.md#prerequisites).

## <a name="update-the-mysql-resource-provider-adapter-integrated-systems-only"></a>Mettre à jour l’adaptateur de fournisseur de ressources MySQL (uniquement pour les systèmes intégrés)

Un nouvel adaptateur de fournisseur de ressources SQL peut être publié quand des builds Azure Stack sont mises à jour. Même si l’adaptateur existant continue de fonctionner, nous vous recommandons d’effectuer une mise à jour dès que possible vers la build la plus récente.  
 
Pour mettre à jour le fournisseur de ressources, vous utilisez le script **UpdateMySQLProvider.ps1**. Le processus est semblable au processus utilisé pour installer un fournisseur de ressources, comme décrit dans la section [Déployer le fournisseur de ressources](#deploy-the-resource-provider) de cet article. Le script est inclus avec le téléchargement du fournisseur de ressources. 

Le script **UpdateMySQLProvider.ps1** crée une machine virtuelle avec le dernier code de fournisseur de ressources et migre les paramètres de l’ancienne machine virtuelle vers la nouvelle. Les paramètres qui migrent incluent des informations sur la base de données et le serveur d’hébergement, ainsi que l’enregistrement DNS nécessaire. 

>[!NOTE]
>Nous vous conseillons de télécharger la dernière image Windows Server 2016 Core à partir de la Gestion de la Place de marché. Si vous devez installer une mise à jour, vous pouvez placer un **seul** package MSU dans le chemin de dépendance local. Le script échoue s’il existe plusieurs fichiers MSU à cet emplacement.

Le script exige d’utiliser les mêmes arguments que ceux décrits pour le script DeployMySqlProvider.ps1. Fournissez également le certificat ici.  

Voici un exemple du script *UpdateMySQLProvider.ps1* que vous pouvez exécuter à partir de l’invite PowerShell. Veillez à modifier les informations de compte et les mots de passe si nécessaire :  

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
$tempDir = 'C:\TEMP\MYSQLRP' 

# The service admin account (can be Azure Active Directory or Active Directory Federation Services). 
$serviceAdmin = "admin@mydomain.onmicrosoft.com" 
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 
 
# Set credentials for the new resource provider VM. 
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass) 
 
# And the cloudadmin credential required for privileged endpoint access. 
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass) 

# Change the following as appropriate. 
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
 
# Change directory to the folder where you extracted the installation files. 
# Then adjust the endpoints. 
$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds ` 
-VMLocalCredential $vmLocalAdminCreds ` 
-CloudAdminCredential $cloudAdminCreds ` 
-PrivilegedEndpoint $privilegedEndpoint ` 
-DefaultSSLCertificatePassword $PfxPass ` 
-DependencyFilesLocalPath $tempDir\cert ` 
-AcceptLicense 
``` 
 
### <a name="updatemysqlproviderps1-parameters"></a>Paramètres de UpdateMySQLProvider.ps1 
Vous pouvez spécifier ces paramètres dans la ligne de commande. Si vous ne le faites pas, ou si la validation d’un paramètre échoue, vous êtes invité à fournir les paramètres obligatoires. 

| Nom du paramètre | Description | Commentaire ou valeur par défaut | 
| --- | --- | --- | 
| **CloudAdminCredential** | Informations d’identification de l’administrateur du cloud, nécessaires pour accéder au point de terminaison privilégié. | _Obligatoire_ | 
| **AzCredential** | Informations d’identification du compte d’administration de service Azure Stack. Utilisez les mêmes informations d’identification que celles utilisées pour le déploiement d’Azure Stack. | _Obligatoire_ | 
| **VMLocalCredential** |Informations d’identification du compte d’administrateur local de la machine virtuelle du fournisseur de ressources SQL. | _Obligatoire_ | 
| **PrivilegedEndpoint** | Adresse IP ou nom DNS du point de terminaison privilégié. |  _Obligatoire_ | 
| **AzureEnvironment** | L’environnement Azure du compte administrateur de service que vous avez utilisé pour déployer Azure Stack. Obligatoire uniquement s’il ne s’agit pas d’AD FS. Les noms d’environnement pris en charge sont **AzureCloud**, **AzureUSGovernment** ou, si vous utilisez Azure Active Directory en Chine, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Votre fichier de certificat .pfx doit également être placé dans ce répertoire. | _Facultatif_ (_obligatoire_ pour les nœuds multiples) | 
| **DefaultSSLCertificatePassword** | Mot de passe pour le certificat .pfx. | _Obligatoire_ | 
| **MaxRetryCount** | Nombre de fois où vous souhaitez réessayer chaque opération en cas d’échec.| 2 | 
| **RetryDuration** | Délai d’attente entre les tentatives, en secondes. | 120 | 
| **Désinstaller** | Supprimez le fournisseur de ressources et toutes les ressources associées (voir les remarques ci-dessous). | Non  | 
| **DebugMode** | Empêche le nettoyage automatique en cas d’échec. | Non  | 
| **AcceptLicense** | Ignore l’invite à accepter la licence GPL.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 
 

## <a name="next-steps"></a>Étapes suivantes
[Tenir à jour le fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-maintain.md)
