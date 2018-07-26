---
title: Utilisation de bases de données SQL sur Azure Stack | Microsoft Docs
description: Découvrez comment déployer des bases de données SQL en tant que service sur Azure Stack et les étapes rapides à suivre pour déployer l’adaptateur de fournisseur de ressources SQL Server.
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
ms.date: 07/13/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: f53b1e08da1cb2d0dc02381bf47c27e8f84cb1d0
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044830"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack"></a>Déployer le fournisseur de ressources SQL Server sur Azure Stack
Utilisez le fournisseur de ressources SQL Server d’Azure Stack pour exposer des bases de données SQL en tant que service d’Azure Stack. Le fournisseur de ressources SQL s’exécute en tant que service sur une machine virtuelle (VM) Windows Server 2016 Server Core.

## <a name="prerequisites"></a>Prérequis

Plusieurs prérequis doivent être remplis avant de déployer le fournisseur de ressources SQL d’Azure Stack. Afin de répondre à ces exigences, complétez les étapes suivantes sur un ordinateur ayant accès à la machine virtuelle du point de terminaison privilégié :

- Si vous ne l’avez pas encore fait, vous devez [inscrire Azure Stack](azure-stack-registration.md) auprès d’Azure pour pouvoir ensuite télécharger des éléments de la Place de marché Azure.
- Vous devez installer les modules PowerShell Azure Stack et Azure sur le système où vous allez exécuter cette installation. Le système doit être une image Windows 10 ou Windows Server 2016 dotée de la dernière version du runtime .NET. Consultez [Installer PowerShell pour Azure Stack](.\azure-stack-powershell-install.md).
- Ajoutez la machine virtuelle Windows Server standard sur la Place de marché Azure Stack en téléchargeant l’image de **Windows Server 2016 Datacenter Server Core**. 
- Téléchargez le binaire du fournisseur de ressources SQL, puis exécutez le fichier auto-extracteur pour extraire le contenu dans un répertoire temporaire. Le fournisseur de ressources possède une build Azure Stack minimale correspondante. Assurez-vous de télécharger le binaire correct pour la version d’Azure Stack que vous utilisez :

    |Version d’Azure Stack|Version SQL RP|
    |-----|-----|
    |Version 1804 (1.0.180513.1)|[SQL RP version 1.1.24.0](https://aka.ms/azurestacksqlrp1804)
    |Version 1802 (1.0.180302.1)|[SQL RP version 1.1.18.0](https://aka.ms/azurestacksqlrp1802)|
    |     |     |

- Vérifiez que les conditions préalables d’intégration du centre de données sont remplies :

    |Configuration requise|Référence|
    |-----|-----|
    |La redirection DNS conditionnelle est correctement définie.|[Intégration au centre de données Azure Stack - DNS](azure-stack-integrate-dns.md)|
    |Les ports d’entrée pour les fournisseurs de ressources sont ouverts.|[Intégration au centre de données Azure Stack : publier des points de terminaison](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |Les objets du certificat PKI et SAN sont correctement définis.|[Prérequis PKI obligatoires pour le déploiement Azure Stack](azure-stack-pki-certs.md#mandatory-certificates)<br>[Prérequis de certificat PaaS pour le déploiement Azure Stack](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

### <a name="certificates"></a>Certificats

_Pour les installations de systèmes intégrés uniquement_. Vous devez fournir le certificat PKI SQL Paas décrit dans la section sur les certificats PaaS facultatif de [Exigences relatives à l’infrastructure de clé publique pour le déploiement Azure Stack](.\azure-stack-pki-certs.md#optional-paas-certificates). Copiez le fichier .pfx à l’emplacement spécifié par le paramètre **DependencyFilesLocalPath**. Ne fournissez pas un certificat pour les systèmes ASDK.

## <a name="deploy-the-sql-resource-provider"></a>Déployer le fournisseur de ressources SQL

Une fois tous les composants requis installés, exécutez le script **DeploySqlProvider.ps1** pour déployer le fournisseur de ressources SQL. Le script DeploySqlProvider.ps1 est extrait du binaire du fournisseur de ressources SQL que vous avez téléchargé pour votre version d’Azure Stack.

Pour déployer le fournisseur de ressources SQL, ouvrez une **nouvelle** fenêtre PowerShell (pas PowerShell ISE) avec élévation de privilèges et basculez vers le répertoire où vous avez extrait les fichiers binaires du fournisseur de ressources SQL. Nous vous recommandons d’utiliser une nouvelle fenêtre PowerShell pour éviter les problèmes potentiels engendrés par les modules PowerShell qui sont déjà chargés.

Exécutez le script DeploySqlProvider.ps1, qui complète les tâches suivantes :

- Chargement des certificats et des autres artefacts dans un compte de stockage sur Azure Stack.
- Publication des packages de la galerie afin que vous puissiez déployer des bases de données SQL à l’aide de la galerie.
- Publication d’un package de galerie pour déployer des serveurs d’hébergement.
- Déployez une machine virtuelle à l’aide de l’image Windows Server 2016 Core que vous avez téléchargée, puis installez le fournisseur de ressources SQL.
- Inscription d’un enregistrement DNS local mappé à la machine virtuelle de votre fournisseur de ressources.
- Inscription de votre fournisseur de ressources auprès de l’Azure Resource Manager local pour le compte d’opérateur.

> [!NOTE]
> Lorsque le déploiement du fournisseur de ressources SQL démarre, le groupe de ressources **system.local.sqladapter** est créé. Il peut falloir jusqu’à 75 minutes pour terminer les déploiements requis dans ce groupe de ressources.

### <a name="deploysqlproviderps1-parameters"></a>Paramètres de DeploySqlProvider.ps1

Vous pouvez spécifier les paramètres suivants à partir de la ligne de commande. Si vous ne le faites pas, ou si la validation d’un paramètre échoue, vous êtes invité à fournir les paramètres requis.

| Nom du paramètre | Description | Commentaire ou valeur par défaut |
| --- | --- | --- |
| **CloudAdminCredential** | Informations d’identification de l’administrateur du cloud, nécessaires pour accéder au point de terminaison privilégié. | _Obligatoire_ |
| **AzCredential** | Informations d’identification du compte d’administration de service Azure Stack. Utilisez les mêmes informations d’identification que celles utilisées pour le déploiement d’Azure Stack. | _Obligatoire_ |
| **VMLocalCredential** | Informations d’identification du compte d’administrateur local de la machine virtuelle du fournisseur de ressources SQL. | _Obligatoire_ |
| **PrivilegedEndpoint** | Adresse IP ou nom DNS du point de terminaison privilégié. |  _Obligatoire_ |
| **DependencyFilesLocalPath** | Pour les systèmes intégrés uniquement, votre fichier de certificat .pfx doit être placé dans ce répertoire. Vous pouvez éventuellement copier un package MSU Windows Update ici. | _Facultatif_ (_obligatoire_ pour les systèmes intégrés) |
| **DefaultSSLCertificatePassword** | Mot de passe pour le certificat .pfx. | _Obligatoire_ |
| **MaxRetryCount** | Nombre de fois où vous souhaitez réessayer chaque opération en cas d’échec.| 2 |
| **RetryDuration** | Délai d’attente entre les tentatives, en secondes. | 120 |
| **Désinstaller** | Supprime le fournisseur de ressources et toutes les ressources associées (voir les remarques ci-dessous). | Non  |
| **DebugMode** | Empêche le nettoyage automatique en cas d’échec. | Non  |

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Déployer le fournisseur de ressources SQL à l’aide d’un script personnalisé

Pour éliminer toute configuration manuelle lors du déploiement du fournisseur de ressources, vous pouvez personnaliser le script suivant. Modifiez les informations de compte et les mots de passe par défaut en fonction des besoins de votre déploiement Azure Stack.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module  -Name AzureStack -RequiredVersion 1.3.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change to the directory folder where you extracted the installation files. Do not provide a certificate on ASDK!
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

Une fois le script d’installation du fournisseur de ressources terminé, actualisez votre navigateur pour vous assurer que vous pouvez voir les dernières mises à jour.

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Vérifier le déploiement à l’aide du portail Azure Stack

Vous pouvez utiliser les étapes suivantes pour vérifier que le fournisseur de ressources SQL est correctement déployé.

1. Connectez-vous au portail d’administration en tant qu’administrateur de service.
2. Sélectionnez **Groupes de ressources**.
3. Sélectionnez le groupe de ressources **system.\<location\>.sqladapter**.
4. La page de la vue d’ensemble du groupe de ressources ne doit pas indiquer que des déploiements ont échoué.

      ![Vérifiez le déploiement du fournisseur de ressources SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)

## <a name="next-steps"></a>Étapes suivantes

[Ajouter des serveurs d’hébergement](azure-stack-sql-resource-provider-hosting-servers.md)
