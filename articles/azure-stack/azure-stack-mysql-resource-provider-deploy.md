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
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 061d9431890622dafafa8c180027b8df735c100e
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296011"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Utiliser des bases de données MySQL sur Microsoft Azure Stack
Utilisez le fournisseur de ressources MySQL Server d’Azure Stack pour exposer des bases de données MySQL en tant que services d’Azure Stack. Le service du fournisseur de ressources MySQL s’exécute sur la machine virtuelle associée, qui est une machine virtuelle Windows Server standard.

## <a name="deploy-the-resource-provider"></a>Déployer le fournisseur de ressources 
1. Si ce n’est déjà fait, inscrivez votre Kit de développement et téléchargez l’image Windows Server 2016 Datacenter Core à partir de Gestion dans la Place de marché. Vous devez utiliser une image Windows Server 2016 Core. Vous pouvez également utiliser un script pour créer une [image de Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Veillez à sélectionner l’option principale.) 

2. Connectez-vous à un hôte qui peut accéder à la machine virtuelle de point de terminaison privilégié :
    - Sur les installations du Kit SDK Azure, connectez-vous à l’hôte physique.  
    - Sur les systèmes intégrés, l’hôte doit être un système qui peut accéder au point de terminaison privilégié. 
    
    >[!NOTE] 
    > Le système sur lequel le script est en cours d’exécution *doit* être un système Windows 10 ou Windows Server 2016 avec la dernière version du runtime .NET installé. Sinon, l’installation échoue. L’hôte du Kit de développement logiciel Azure Stack répond à ce critère. 
    
3. Téléchargez l’adaptateur du fournisseur de ressources MySQL binaire. Exécutez ensuite l’auto-extracteur pour extraire le contenu dans un répertoire temporaire. 
    >[!NOTE]  
    > Le fournisseur de ressources possède une build Azure Stack minimale correspondante. Vérifiez que vous téléchargez le binaire correct pour la version d’Azure Stack en cours d’exécution.

    | Version d’Azure Stack | Version du fournisseur de ressources MySQL| 
    | --- | --- | 
    | Version 1804 (1.0.180513.1)|[Version 1.1.24.0 du fournisseur de ressources MySQL](https://aka.ms/azurestackmysqlrp1804) | 
    | Version 1802 (1.0.180302.1) | [MySQL RP version 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) | 
    | Version 1712 (1.0.180102.3 ou 1.0.180106.1 (systèmes intégrés)) | [MySQL RP version 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) | 

4.  Pour le Kit de développement logiciel Azure, un certificat auto-signé est créé dans le cadre de ce processus. Pour les systèmes intégrés, vous devez fournir un certificat approprié. Si vous devez fournir votre propre certificat, placez un fichier .pfx dans le chemin **DependencyFilesLocalPath**, qui répond aux critères suivants : 
    - Soit un certificat générique pour \*.dbadapter.\<region\>.\<external fqdn\> ou un certificat de site unique avec un nom commun de mysqladapter.dbadapter.\<region\>.\<external fqdn\>. 
    - Ce certificat doit être fiable. Autrement dit, la chaîne d’approbation doit exister sans exiger de certificats intermédiaires. 
    - Il n’existe qu’un seul fichier de certificat unique dans le chemin local DependencyFilesLocalPath. 
    - Le nom de fichier ne doit pas contenir de caractères spéciaux ou d’espaces. 

5. Ouvrez une **nouvelle** console PowerShell avec élévation de privilèges (administratif). Accédez ensuite au répertoire où vous avez extrait les fichiers. Utilisez une nouvelle fenêtre pour éviter les problèmes qui pourraient se produire à cause des modules PowerShell incorrects déjà chargés sur le système. 

6. Exécutez le script **DeployMySqlProvider.ps1**. Le script effectue les étapes suivantes : 
    - Téléchargement du fichier binaire de connecteur MySQL (peut être fourni en mode hors connexion). 
    - Chargement des certificats et des autres artefacts dans un compte de stockage sur Azure Stack. 
    - Publication des packages de la galerie afin que vous puissiez déployer des bases de données SQL par le biais de la galerie. 
    - Publication d’un package de galerie pour déployer des serveurs d’hébergement. 
    - Déploie une machine virtuelle à l’aide d’une image de marketplace Azure Stack Windows Server 2016 et installe le fournisseur de ressources. 
    - Inscription d’un enregistrement DNS local mappé à la machine virtuelle de votre fournisseur de ressources. 
    - Inscription de votre fournisseur de ressources auprès de l’Azure Resource Manager local (locataire et administrateur). 

    Vous pouvez spécifier les paramètres requis sur la ligne de commande ou exécuter le script sans aucun paramètre, puis les entrer lorsque vous y êtes invité. 

    Voici un exemple que vous pouvez exécuter à partir de l’invite de PowerShell. Veillez à modifier les informations de compte et les mots de passe si nécessaire : 

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
    # Find the ERCS01 IP address first, and make sure the certificate 
    # file is in the specified directory. 
    $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds ` 
        -VMLocalCredential $vmLocalAdminCreds ` 
        -CloudAdminCredential $cloudAdminCreds ` 
        -PrivilegedEndpoint $privilegedEndpoint ` 
        -DefaultSSLCertificatePassword $PfxPass ` 
        -DependencyFilesLocalPath $tempDir\cert ` 
        -AcceptLicense 
    ``` 

### <a name="deploymysqlproviderps1-parameters"></a>Paramètres de DeployMySqlProvider.ps1 
Vous pouvez spécifier ces paramètres dans la ligne de commande. Si vous ne le faites pas, ou si la validation d’un paramètre échoue, vous êtes invité à fournir les paramètres obligatoires. 

| Nom du paramètre | Description | Commentaire ou valeur par défaut | 
| --- | --- | --- | 
| **CloudAdminCredential** | Informations d’identification de l’administrateur du cloud, nécessaires pour accéder au point de terminaison privilégié. | _Obligatoire_ | 
| **AzCredential** | Informations d’identification du compte d’administration de service Azure Stack. Utilisez les mêmes informations d’identification que celles utilisées pour le déploiement d’Azure Stack. | _Obligatoire_ | 
| **VMLocalCredential** | Les informations d’identification du compte d’administrateur local de la machine virtuelle du fournisseur de ressources MySQL. | _Obligatoire_ | 
| **PrivilegedEndpoint** | Adresse IP ou nom DNS du point de terminaison privilégié. |  _Obligatoire_ | 
| **DependencyFilesLocalPath** | Chemin vers un partage local contenant [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi). Si vous fournissez un de ces chemins, le fichier de certificat doit également être placé dans ce répertoire. | _Facultatif_ (_obligatoire_ pour les nœuds multiples) | 
| **DefaultSSLCertificatePassword** | Mot de passe pour le certificat .pfx. | _Obligatoire_ | 
| **MaxRetryCount** | Nombre de fois où vous souhaitez réessayer chaque opération en cas d’échec.| 2 | 
| **RetryDuration** | Délai d’attente entre les tentatives, en secondes. | 120 | 
| **Désinstaller** | Supprime le fournisseur de ressources et toutes les ressources associées (voir les remarques ci-dessous). | Non  | 
| **DebugMode** | Empêche le nettoyage automatique en cas d’échec. | Non  | 
| **AcceptLicense** | Ignore l’invite à accepter la licence GPL.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

> [!NOTE] 
> Une heure entière peut être nécessaire avant que les références n’apparaissent dans le portail. Vous ne pouvez pas créer une base de données tant que la référence n’a pas été créée. 

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Vérifier le déploiement à l’aide du portail Azure Stack 
> [!NOTE] 
>  Une fois que l’exécution du script d’installation est terminée, vous devez actualiser le portail pour afficher le panneau d’administration. 

1. Connectez-vous au portail d’administration en tant qu’administrateur de service. 
2. Vérifiez que le déploiement a réussi. Accédez à **Groupes de ressources**, puis sélectionnez le groupe de ressources **system.\<location\>.mysqladapter**. Vérifiez que les quatre déploiements ont réussi :

      ![Vérifier le déploiement du fournisseur de ressources MySQL](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png) 


## <a name="next-steps"></a>Étapes suivantes
[Ajouter des serveurs d’hébergement](azure-stack-mysql-resource-provider-hosting-servers.md)
