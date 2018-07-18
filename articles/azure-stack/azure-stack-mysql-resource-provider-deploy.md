---
title: Utilisation de bases de données MySQL sur Azure Stack | Microsoft Docs
description: Découvrez comment déployer des bases de données MySQL en tant que services sur Azure Stack et les étapes rapides à suivre pour déployer l’adaptateur de fournisseur de ressources MySQL Server.
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
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: e4c3eb1d7dfd4894576d5fbed52cf4de5fed9e44
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938110"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack"></a>Déployer un fournisseur de ressources MySQL sur Azure Stack

Utilisez le fournisseur de ressources MySQL Server pour exposer des bases de données MySQL en tant que service d’Azure Stack. Le fournisseur de ressources MySQL s’exécute en tant que service sur une machine virtuelle Windows Server 2016 Server Core.

## <a name="prerequisites"></a>Prérequis

Plusieurs prérequis doivent être remplis avant de déployer le fournisseur de ressources MySQL d’Azure Stack. Afin de répondre à ces exigences, complétez les étapes de cet article sur un ordinateur ayant accès à la machine virtuelle du point de terminaison privilégié.

* Si vous ne l’avez pas encore fait, vous devez [inscrire Azure Stack](.\azure-stack-registration.md) auprès d’Azure pour pouvoir ensuite télécharger des éléments de la Place de marché Azure.
* Ajoutez la machine virtuelle Windows Server standard sur la Place de marché Azure Stack en téléchargeant l’image de **Windows Server 2016 Datacenter Server Core**. Vous pouvez également utiliser un script pour créer une [image de Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). Assurez-vous d’avoir sélectionner l’option principale lorsque vous exécutez le script.

  >[!NOTE]
  >Si vous devez installer une mise à jour, vous pouvez placer un seul package .MSU dans le chemin de dépendance local. Si plusieurs fichiers .MSU sont trouvés, l’installation du fournisseur de ressources MySQL échoue.

* Téléchargez le binaire du fournisseur de ressources MySQL, puis exécutez le fichier auto-extracteur pour extraire le contenu dans un répertoire temporaire.

  >[!NOTE]
  >Pour déployer le fournisseur MySQL sur un système qui n’a pas accès à Internet, copiez le fichier [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi) sur un partage local. Ensuite, indiquez ce nom de partage lorsque vous y êtes invité. Vous devez installer les modules PowerShell Azure et Azure Stack.

* Le fournisseur de ressources possède une build Azure Stack minimale correspondante. Assurez-vous de télécharger le binaire correct pour la version d’Azure Stack que vous utilisez.

    | Version d’Azure Stack | Version du fournisseur de ressources MySQL|
    | --- | --- |
    | Version 1804 (1.0.180513.1)|[Version 1.1.24.0 du fournisseur de ressources MySQL](https://aka.ms/azurestackmysqlrp1804) |
    | Version 1802 (1.0.180302.1) | [MySQL RP version 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) |
    | Version 1712 (1.0.180102.3 ou 1.0.180106.1 (systèmes intégrés)) | [MySQL RP version 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) |

### <a name="certificates"></a>Certificats

Pour le Kit de développement logiciel Azure, un certificat auto-signé est créé dans le cadre du processus d’installation. Pour un système intégré Azure Stack, vous devez fournir un certificat approprié. Si vous devez fournir votre propre certificat, placez un fichier .pfx dans le chemin **DependencyFilesLocalPath**, qui répond aux critères suivants :

* Soit un certificat générique pour \*.dbadapter.\<region\>.\<external fqdn\> ou un certificat de site unique avec un nom commun de mysqladapter.dbadapter.\<region\>.\<external fqdn\>.
* Ce certificat doit être fiable. La chaîne d’approbation doit exister sans exiger de certificats intermédiaires.
* Il n’existe qu’un seul fichier de certificat unique dans le chemin local DependencyFilesLocalPath.
* Le nom de fichier ne doit pas contenir de caractères spéciaux ou d’espaces.

## <a name="deploy-the-resource-provider"></a>Déployer le fournisseur de ressources

Une fois tous les composants requis installés, exécutez le script **DeployMySqlProvider.ps1** pour déployer le fournisseur de ressources MYSQL. Le script DeployMySqlProvider.ps1 est extrait du binaire du fournisseur de ressources MySQL que vous avez téléchargé pour votre version d’Azure Stack.

> [!IMPORTANT]
> Le système sur lequel le script est en cours d’exécution doit être un système Windows 10 ou Windows Server 2016 avec la dernière version du runtime .NET installé.

Pour déployer le fournisseur de ressources MySQL, ouvrez une nouvelle fenêtre de console PowerShell avec élévation de privilèges et basculez vers le répertoire où vous avez extrait les fichiers binaires du fournisseur de ressources MySQL. Nous vous recommandons d’utiliser une nouvelle fenêtre PowerShell pour éviter les problèmes potentiels engendrés par les modules PowerShell qui sont déjà chargés.

Exécutez le script **DeployMySqlProvider.ps1**, qui complète les tâches suivantes :

* Chargement des certificats et des autres artefacts dans un compte de stockage sur Azure Stack.
* Publication des packages de la galerie afin que vous puissiez déployer des bases de données MySQL par le biais de la galerie.
* Publication d’un package de galerie pour déployer des serveurs d’hébergement.
* Déploiement d’une machine virtuelle à l’aide de l’image Windows Server 2016 Core que vous avez téléchargée, puis installation du fournisseur de ressources MySQL.
* Inscription d’un enregistrement DNS local mappé à la machine virtuelle de votre fournisseur de ressources.
* Inscription de votre fournisseur de ressources auprès de l’Azure Resource Manager local pour les comptes d’utilisateurs et d’opérateurs.
* Si nécessaire, installez une seule mise à jour Windows Server durant l’installation du fournisseur de ressources.

> [!NOTE]
> Lorsque le déploiement du fournisseur de ressources MySQL démarre, le groupe de ressources **system.local.sqladapter** est créé. Il peut falloir jusqu’à 75 minutes pour terminer les quatre déploiements dans ce groupe de ressources.

### <a name="deploymysqlproviderps1-parameters"></a>Paramètres de DeployMySqlProvider.ps1

Vous pouvez spécifier ces paramètres à partir de la ligne de commande. Si vous ne le faites pas, ou si la validation d’un paramètre échoue, vous êtes invité à fournir les paramètres obligatoires.

| Nom du paramètre | Description | Commentaire ou valeur par défaut |
| --- | --- | --- |
| **CloudAdminCredential** | Informations d’identification de l’administrateur du cloud, nécessaires pour accéder au point de terminaison privilégié. | _Obligatoire_ |
| **AzCredential** | Informations d’identification du compte d’administration de service Azure Stack. Utilisez les mêmes informations d’identification que celles utilisées pour le déploiement d’Azure Stack. | _Obligatoire_ |
| **VMLocalCredential** | Les informations d’identification du compte d’administrateur local de la machine virtuelle du fournisseur de ressources MySQL. | _Obligatoire_ |
| **PrivilegedEndpoint** | Adresse IP ou nom DNS du point de terminaison privilégié. |  _Obligatoire_ |
| **DependencyFilesLocalPath** | Chemin vers un partage local contenant [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi). Si vous fournissez un de ces chemins, le fichier de certificat doit également être placé dans ce répertoire. | _Facultatif_ pour un seul nœud, _obligatoire_ pour plusieurs nœuds. |
| **DefaultSSLCertificatePassword** | Mot de passe pour le certificat .pfx. | _Obligatoire_ |
| **MaxRetryCount** | Nombre de fois où vous souhaitez réessayer chaque opération en cas d’échec.| 2 |
| **RetryDuration** | Délai d’attente entre les tentatives, en secondes. | 120 |
| **Désinstaller** | Supprime le fournisseur de ressources et toutes les ressources associées (voir les remarques ci-dessous). | Non  |
| **DebugMode** | Empêche le nettoyage automatique en cas d’échec. | Non  |
| **AcceptLicense** | Ignore l’invite à accepter la licence GPL.  <http://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

> [!NOTE]
> Une heure entière peut être nécessaire avant que les références n’apparaissent dans le portail. Vous ne pouvez pas créer de base de données tant que la référence (SKU) n’a pas été déployée et exécutée.

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>Déployer le fournisseur de ressources MySQL à l’aide d’un script personnalisé

Pour éliminer toute configuration manuelle lors du déploiement du fournisseur de ressources, vous pouvez personnaliser le script suivant. Modifiez les informations de compte et les mots de passe par défaut en fonction des besoins de votre déploiement Azure Stack.

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

# Set the credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Run the installation script from the folder where you extracted the installation files.
# Find the ERCS01 IP address first, and make sure the certificate file is in the specified directory.
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

Une fois le script d’installation du fournisseur de ressources terminé, actualisez votre navigateur pour vous assurer que vous pouvez voir les dernières mises à jour.

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Vérifier le déploiement à l’aide du portail Azure Stack

1. Connectez-vous au portail d’administration en tant qu’administrateur de service.
2. Sélectionnez **Groupes de ressources**.
3. Sélectionnez le groupe de ressources **system.\<location\>.mysqladapter**.
4. Dans la page Résumé de Vue d’ensemble du groupe de ressources, le message sous **Déploiements** doit être **3 réussis**.
5. Vous pouvez obtenir des informations plus détaillées sur le déploiement du fournisseur de ressources sous **PARAMÈTRES**. Sélectionnez **Déploiements** pour obtenir des informations telles que ÉTAT, TIMESTAMP et DURÉE pour chaque déploiement.

## <a name="next-steps"></a>Étapes suivantes

[Ajouter des serveurs d’hébergement](azure-stack-mysql-resource-provider-hosting-servers.md)
