---
title: Personnaliser l’installation d’un Azure-SSIS Integration Runtime
description: Cet article décrit comment utiliser l’interface d’installation personnalisée d’un Azure-SSIS Integration Runtime pour installer des composants supplémentaires ou modifier des paramètres
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 07/09/2020
ms.openlocfilehash: 8b29708dbcc6bbb0d127b4277fd4b2d962da6e6a
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87986387"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Personnaliser l’installation d’un Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

L’installation personnalisée d’un runtime d’intégration (IR) Azure-SQL Server Integration Services (SSIS) dans Azure Data Factory (ADF) fournit une interface permettant d’ajouter vos propres étapes lors de l’approvisionnement ou de la reconfiguration de votre IR Azure-SSIS. 

L’installation personnalisée vous permet de modifier la configuration ou l’environnement d’exploitation par défaut pour, par exemple, démarrer des services Windows supplémentaires ou conserver les informations d’identification d’accès pour les partages de fichiers, ou utiliser un chiffrement fort/protocole réseau plus sécurisé (TLS 1.2). Vous pouvez aussi installer des composants tiers ou personnalisés supplémentaires, tels que des assemblys, des pilotes ou des extensions, sur chaque nœud de votre IR Azure-SSIS. Pour plus d’informations sur les composants intégrés/préinstallés, consultez [Composants intégrés/préinstallés sur Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/built-in-preinstalled-components-ssis-integration-runtime).

Vous pouvez effectuer des installations personnalisées sur votre Azure-SSIS IR de l’une des deux manières suivantes : 
* **Installation personnalisée standard avec un script** : Préparez un script et ses fichiers associés, puis chargez le tout dans un conteneur de blobs de votre compte de stockage Azure. Vous fournissez ensuite un URI (Uniform Resource Identifier) à signature d’accès partagé (SAP) pour votre conteneur lorsque vous installez ou reconfigurez votre Azure-SSIS IR. Chaque nœud de votre Azure-SSIS IR télécharge ensuite le script et ses fichiers associés à partir de votre conteneur et exécute votre installation personnalisée avec des autorisations élevées. Lorsque votre installation personnalisée est terminée, chaque nœud charge la sortie standard de l’exécution et d’autres journaux d’activité dans votre conteneur.
* **Installation personnalisée rapide sans script** : Exécutez certaines configurations système et commandes Windows courantes ou installez des composants supplémentaires populaires ou recommandés sans utiliser le moindre script.

Vous pouvez installer des composants gratuits et sans licence ainsi que des composants payants et sous licence avec des installations personnalisées rapides et standard. Si vous êtes un éditeur de logiciels indépendant (ISV), consultez [Développer des composants payants ou sous licence pour Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Afin de tirer parti des améliorations futures, nous vous recommandons d'utiliser la série de nœuds v3 ou version ultérieure pour votre runtime d'intégration Azure-SSIS IR, en l'association à une configuration personnalisée.

## <a name="current-limitations"></a>Limites actuelles

Les limitations suivantes s’appliquent uniquement aux installations personnalisées standard :

- Si vous souhaitez utiliser *gacutil.exe* dans votre script pour installer des assemblys dans Global Assembly Cache (GAC), vous devez fournir *gacutil.exe* dans le cadre de votre installation personnalisée. Ou vous pouvez utiliser l’exemplaire fourni dans notre conteneur en *Préversion publique*, plus loin dans la section « Instructions ».

- Si vous souhaitez faire référence à un sous-dossier dans votre script, *msiexec.exe* ne prend pas en charge la notation `.\` pour faire référence au dossier racine. Utilisez une commande telle que `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` au lieu de `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

- Les partages administratifs, ou des partages réseau masqués qui sont créés automatiquement par Windows, ne sont actuellement pas pris en charge sur l’Azure-SSIS IR.

- Le pilote ODBC IBM iSeries Access n’est pas pris en charge sur Azure-SSIS IR. Il se peut que des erreurs d’installation s’affichent lors de votre installation personnalisée. Si c’est le cas, contactez le support IBM pour obtenir de l’aide.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pour personnaliser votre Azure-SSIS IR, vous avez besoin des éléments suivants :

- [Un abonnement Azure](https://azure.microsoft.com/)

- [Approvisionner votre IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Un compte Azure Storage](https://azure.microsoft.com/services/storage/). Non requis pour les installations personnalisées rapides. Pour des installations personnalisées standard, vous chargez et stockez votre script d’installation personnalisée et ses fichiers associés dans un conteneur d’objets blob. Le processus d’installation personnalisée télécharge également ses journaux d’activité d’exécution sur le même conteneur d’objets blob.

## <a name="instructions"></a>Instructions

Vous pouvez approvisionner ou reconfigurer votre IR Azure-SSIS avec des installations personnalisées sur l’interface utilisateur ADF. Si vous souhaitez faire de même avec PowerShell, téléchargez et installez [Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="standard-custom-setup"></a>Installation personnalisée standard

Pour approvisionner ou reconfigurer votre IR Azure-SSIS à l’aide d’installations personnalisées standard, procédez comme suit.

1. Préparer votre script d’installation personnalisée et ses fichiers associés (par exemple, les fichiers .bat, .cmd, .exe, .dll, .msi ou .ps1).

   * Vous devez avoir un fichier de script nommé *main.cmd*, qui est le point d’entrée de votre installation personnalisée.  
   * Pour vous assurer que le script peut être exécuté sans assistance, nous vous recommandons de le tester d’abord sur votre ordinateur local.  
   * Si vous souhaitez que des journaux supplémentaires générés par d’autres outils (par exemple, *msiexec.exe*) soient chargés dans votre conteneur, spécifiez la variable d’environnement prédéfinie `CUSTOM_SETUP_SCRIPT_LOG_DIR` comme dossier de journal d’activité dans vos scripts (par exemple, *msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log*).

1. Téléchargez, installez et ouvrez [Explorateur Stockage Azure](https://storageexplorer.com/).

   a. Sous **(Local et attaché)** , cliquez avec le bouton droit sur **Comptes de stockage**, puis sélectionnez **Se connecter au stockage Azure**.

      ![Se connecter à Stockage Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Sélectionnez **Utiliser le nom et la clé d’un compte de stockage**, puis **Suivant**.

      ![Utiliser le nom et la clé d’un compte de stockage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Entrez le nom et la clé de votre compte de stockage Azure, sélectionnez **Suivant**, puis **Se connecter**.

      ![Fournir le nom et la clé du compte de stockage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. Dans votre compte de stockage Azure connecté, cliquez avec le bouton droit sur **Conteneurs d’objets blob**, sélectionnez **Créer un conteneur d’objets Blob** et nommez le nouveau conteneur.

      ![Création d’un conteneur d’objets blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Sélectionnez le nouveau conteneur et chargez votre script d’installation personnalisée et ses fichiers associés. Veillez à charger *main.cmd* au niveau supérieur de votre conteneur, et non dans un dossier. Vérifiez également que votre conteneur contient uniquement les fichiers d’installation personnalisée nécessaires afin que leur téléchargement ultérieur sur votre Azure-SSIS Integration Runtime ne prenne pas beaucoup de temps. La durée maximale d’une installation personnalisée est actuellement fixée à 45 minutes avant qu’elle n’expire. Cela comprend le temps nécessaire pour télécharger tous les fichiers de votre conteneur et les installer sur l’Azure-SSIS IR. Si l’installation nécessite plus de temps, créez un ticket de support.

      ![Chargez des fichiers vers le conteneur d’objets blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Cliquez avec le bouton droit sur le conteneur, puis sélectionnez **Obtenir une signature d’accès partagé**.

      ![Obtenez la signature d’accès partagé pour le conteneur](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. Créez l’URI de SAP pour votre conteneur avec un délai d’expiration suffisamment long et une autorisation de lecture, d’écriture ou de liste. Vous aurez besoin de l’URI de SAP pour télécharger et exécuter votre script d’installation personnalisée et les fichiers associés à chaque fois qu’un nœud de votre Azure-SSIS IR sera réinitialisé ou redémarré. Vous avez besoin d’une permission d’écriture pour charger des journaux d’activité d’exécution de l’installation.

      > [!IMPORTANT]
      > Vérifiez que l’URI de SAP n’arrive pas à expiration et que les ressources d’installation personnalisée restent disponibles pendant la totalité du cycle de vie de votre Azure-SSIS IR, de la création à la suppression, en particulier si vous l’arrêtez et le redémarrez régulièrement au cours de cette période.

      ![Générez la signature d’accès partagé pour le conteneur](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Copiez et enregistrez l’URI de SAP de votre conteneur.

      ![Copiez et enregistrez la signature d’accès partagé.](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Lorsque vous approvisionnez ou reconfigurez votre IR Azure-SSIS sur l’interface utilisateur ADF, activez la case à cocher **Personnaliser votre IR Azure-SSIS avec des installations de composants/configurations système supplémentaires** sur la page **Paramètres avancés** du volet **Configuration du runtime d’intégration**, puis entrez l’URI de SAP de votre conteneur dans la zone **URI de SAP du conteneur d’installation personnalisée**.

   ![Paramètres avancés avec des installations personnalisées](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

### <a name="express-custom-setup"></a>Installation personnalisée rapide

Pour approvisionner ou reconfigurer votre IR Azure-SSIS à l’aide d’installations personnalisées rapides, procédez comme suit.

1. Lorsque vous approvisionnez ou reconfigurez votre IR Azure-SSIS sur l’interface utilisateur ADF, activez la case à cocher **Personnaliser votre IR Azure-SSIS avec des installations de composants/configurations système supplémentaires** sur la page **Paramètres avancés** du volet **Configuration du runtime d’intégration**. 

1. Sélectionnez **Nouveau** pour ouvrir le volet **Ajouter une installation personnalisée rapide**, puis sélectionnez un type de votre choix dans la liste déroulante **Type d’installation personnalisée rapide** :

   * Si vous sélectionnez le type **Exécuter la commande cmdkey**, vous pouvez conserver les informations d’identification d’accès pour vos partages de fichiers ou vos partages Azure Files sur Azure-SSIS IR en entrant votre nom d’ordinateur ou de domaine ciblé, votre nom de compte ou nom d’utilisateur et votre clé de compte ou mot de passe dans les zones **/Add**, **/User** et **/Pass**. Cela est similaire à l’exécution de la commande Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) sur votre ordinateur local.
   
   * Si vous sélectionnez le type **Ajouter une variable d’environnement**, vous pouvez ajouter des variables d’environnement Windows à utiliser dans vos packages qui s’exécutent sur Azure-SSIS IR en entrant le nom et la valeur de votre variable d’environnement dans les zones **Nom de la variable**  et **Valeur de la variable**. Cela est similaire à l’exécution de la commande Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) sur votre ordinateur local.

   * Si vous sélectionnez le type **Installer un composant sous licence**, vous pouvez sélectionner un composant intégré de nos partenaires ISV dans la liste déroulante **Nom du composant** :

     * Si vous sélectionnez le composant **Task Factory de SentryOne**, vous pouvez installer la suite de composants [Task Factory](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) de SentryOne sur votre Azure-SSIS IR en entrant la clé de licence du produit que vous leur avez achetée dans la zone **Clé de licence**. La version intégrée actuelle est **2020.1.3**.

     * Si vous sélectionnez le composant **HEDDA.IO d’oh22**, vous pouvez installer le composant de qualité/nettoyage des données [HEDDA. IO](https://hedda.io/ssis-component/) d’oh22 sur votre Azure-SSIS IR après avoir acheté leur service. La version intégrée actuelle est **1.0.14**.

     * Si vous sélectionnez le composant **SQLPhonetics.NET d’oh22**, vous pouvez installer le composant de mise en correspondance/qualité des données [SQLPhonetics.NET](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis) d’oh22 sur votre Azure-SSIS IR en entrant la clé de licence du produit que vous leur avez achetée dans la zone **Clé de licence**. La version intégrée actuelle est **1.0.45**.

     * Si vous sélectionnez le composant **SSIS Integration Toolkit de KingswaySoft**, vous pouvez installer la suite de connecteurs de [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) pour les applications CRM/ERP/marketing/collaboration, telles que Microsoft Dynamics, SharePoint, Project Server, Oracle ou Salesforce Marketing Cloud, à partir de KingswaySoft sur votre Azure-SSIS IR en entrant la clé de licence du produit que vous avez achetée dans la zone **Clé de licence** La version intégrée actuelle est **2019.2**.

     * Si vous sélectionnez le composant **SSIS Productivity Pack de KingswaySoft**, vous pouvez installer la suite de composants [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) à partir de KingswaySoft sur votre Azure-SSIS IR en entrant la clé de licence du produit que vous leur avez achetée dans la zone **Clé de licence**. La version intégrée actuelle est **10.0**.

     * Si vous sélectionnez le composant **Xtract IS de Theobald Software**, vous pouvez installer la suite de connecteurs [Xtract IS](https://theobald-software.com/en/xtract-is/) pour SAP System (ERP, S/4HANA, BW) de Theobald Software sur votre Azure-SSIS IR en glissant-déposant/chargeant le fichier de licence produit que vous leur avez acheté dans la zone **Fichier de licence**. La version intégrée actuelle est **6.1.1.3**.

Vos nouvelles installations personnalisées rapides s’affichent sur la page **Paramètres avancés**. Pour les supprimer, activez leurs cases à cocher, puis sélectionnez **Supprimer**.

### <a name="azure-powershell"></a>Azure PowerShell

Pour approvisionner ou reconfigurer votre IR Azure-SSIS avec des installations personnalisées en utilisant Azure PowerShell, procédez comme suit.

1. Si votre IR Azure-SSIS est déjà démarré ou en cours d’exécution, arrêtez-le d’abord.

1. Vous pouvez ensuite ajouter ou supprimer des installations personnalisées en exécutant la cmdlet `Set-AzDataFactoryV2IntegrationRuntime` avant de démarrer votre IR Azure-SSIS.

   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       if($ExpressCustomSetup -eq "Theobald.XtractIS")
       {
           $jsonData = Get-Content -Raw -Path YourLicenseFile.json
           $jsonData = $jsonData -replace '\s',''
           $jsonData = $jsonData.replace('"','\"')
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```

1. Une fois votre installation personnalisée standard terminée et votre IR Azure-SSIS démarré, vous pouvez trouver la sortie standard de *main.cmd* et d’autres journaux d’exécution dans le dossier *main.cmd.log* de votre conteneur.

### <a name="standard-custom-setup-samples"></a>Exemples d’installation personnalisée standard

Pour afficher et réutiliser certains exemples d’installation personnalisée standard, procédez comme suit.

1. Connectez-vous à notre conteneur Préversion publique à l’aide d’Explorateur Stockage Azure.

   a. Sous **(Locale et attaché)** , cliquez avec le bouton droit sur **Comptes de stockage**, sélectionnez **Se connecter au stockage Azure**, **Utiliser une chaîne de connexion ou un URI de signature d’accès partagé**, puis **Suivant**.

      ![Se connecter au stockage Azure avec la signature d’accès partagé](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Sélectionnez **Utiliser un URI SAP** puis, dans la zone **URI**, entrez l’URI SAP suivant :

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![Fournissez la signature d’accès partagé pour le conteneur](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Sélectionnez **Suivant**, puis **Connecter**.

   d. Dans le volet gauche, sélectionnez le conteneur **publicpreview** connecté, puis double-cliquez sur le dossier *CustomSetupScript*. Ce dossier contient les éléments suivants :

      * Un dossier *Sample*, qui contient une installation personnalisée pour une tâche de base sur chaque nœud de votre Azure-SSIS IR. La tâche est juste mise en veille pendant quelques secondes. Le dossier contient également un dossier *gacutil*, dont le contenu entier (*gacutil.exe*, *gacutil.exe.config* et *1033\gacutlrc.dll*) peut être copié tel quel dans votre conteneur.

      * Un dossier *UserScenarios* contenant plusieurs exemples d’installations personnalisées pour des scénarios d’utilisateurs réels.

        ![Contenu du conteneur de version préliminaire publique](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Double-cliquez sur le dossier *UserScenarios* pour rechercher les éléments suivants :

      * Un dossier *.NET FRAMEWORK 3.5* contenant un script d’installation personnalisée (*main.cmd*) pour installer une version précédente de .NET Framework, potentiellement nécessaire pour des composants personnalisés sur chaque nœud de votre Azure-SSIS IR.

      * Un dossier *BCP* contenant un script d’installation personnalisée (*main.cmd*) pour installer les utilitaires de ligne de commande SQL Server (*MsSqlCmdLnUtils.msi*), incluant le programme de copie en bloc (*bcp*), sur chaque nœud de votre Azure-SSIS IR.

      * Un dossier *EXCEL* contenant un script d’installation personnalisée (*main.cmd*) pour installer des assemblys et des bibliothèques C# que vous pouvez utiliser dans des tâches de scripts pour lire et écrire de façon dynamique des fichiers Excel sur chaque nœud de votre Azure-SSIS IR. 
      
        Tout d’abord, téléchargez [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) et [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/), puis chargez-les tous les deux avec *main.cmd* dans votre conteneur. Autrement, si vous souhaitez utiliser uniquement le Gestionnaire des connexions Excel standard, la source Excel et la destination Excel, le composant redistribuable d’accès requis est déjà préinstallé sur votre Azure-SSIS IR et vous n’avez donc pas besoin d’une installation personnalisée.
      
      * Un dossier *MYSQL ODBC* contenant un script d’installation personnalisée (*main.cmd*) pour installer les pilotes ODBC MySQL sur chaque nœud de votre Azure-SSIS IR. Cette installation vous permet d’utiliser le Gestionnaire des connexions, la source et la destination ODBC pour vous connecter au serveur MySQL. 
     
        Tout d’abord, [téléchargez les dernières versions 64 bits et 32 bits des programmes d’installation de pilotes ODBC MySQL](https://dev.mysql.com/downloads/connector/odbc/) (par exemple, *mysql-connector-odbc-8.0.13-winx64.msi* et *mysql-connector-odbc-8.0.13-win32.msi*), puis chargez-les tous avec *main.cmd* dans votre conteneur.

      * Un dossier *ORACLE ENTERPRISE*, qui contient un script d’installation personnalisée (*main.cmd*) et le fichier config de l’installation sans assistance (*client.rsp*) pour installer les connecteurs Oracle et le pilote OCI sur chaque nœud de votre instance Azure-SSIS IR Édition Entreprise. Cette installation vous permet d’utiliser le Gestionnaire des connexions, la source et la destination Oracle pour vous connecter au serveur Oracle. 
      
        Tout d’abord, téléchargez Microsoft Connectors v5.0 pour Oracle (*AttunitySSISOraAdaptersSetup.msi* et *AttunitySSISOraAdaptersSetup64.msi*) depuis le [Centre de téléchargement Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=55179) et le dernier client Oracle (par exemple, *winx64_12102_client.zip*) à partir du site [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), puis chargez-les tous avec *main.cmd* et *client.rsp* dans votre conteneur. Si vous utilisez TNS pour vous connecter à Oracle, vous devez également télécharger *tnsnames.ora*, le modifier, puis le charger dans votre conteneur de manière à ce qu’il puisse être copié dans le dossier d’installation Oracle pendant l’installation.

      * Un dossier *ORACLE STANDARD ADO.NET*, qui contient un script d’installation personnalisée (*main.cmd*) pour installer le pilote Oracle ODP.NET sur chaque nœud de votre Azure-SSIS IR. Cette installation vous permet d’utiliser le Gestionnaire des connexions, la source et la destination ADO.NET pour vous connecter au serveur Oracle. 
      
        Tout d’abord, [téléchargez le dernier pilote Oracle ODP.NET](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html), par exemple *ODP.NET_Managed_ODAC122cR1.zip*, puis chargez-le dans votre conteneur avec *main.cmd*.
       
      * Un dossier *ORACLE STANDARD ODBC*, qui contient un script d’installation personnalisée (*main.cmd*) pour installer le pilote ODBC Oracle et configurer le nom de source de données sur chaque nœud de votre Azure-SSIS IR. Cette installation vous permet d’utiliser le Gestionnaire des connexions, la source et la destination ODBC ou le Gestionnaire des connexions et la source Power Query avec le type de source de données ODBC pour vous connecter au serveur Oracle. 
      
        Tout d’abord, téléchargez la dernière version d’Oracle Instant Client (package de base ou package de base Lite) et le package ODBC, puis chargez-les tous avec *main.cmd* dans votre conteneur :
        * [Télécharger les packages 64 bits](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (package de base : *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip* ; package de base Lite : *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip* ; package ODBC : *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [Télécharger les packages 32 bits](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (package de base : *instantclient-basic-nt-18.3.0.0.0dbru.zip* ; package de base Lite : *instantclient-basiclite-nt-18.3.0.0.0dbru.zip* ; package ODBC : *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * Un dossier *ORACLE STANDARD OLEDB*, qui contient un script d’installation personnalisée (*main.cmd*) pour installer le pilote Oracle OLEDB sur chaque nœud de votre Azure-SSIS IR. Cette installation vous permet d’utiliser le Gestionnaire des connexions, la source et la destination OLEDB pour vous connecter au serveur Oracle. 
     
        Tout d’abord, [téléchargez le dernier pilote Oracle OLEDB](https://www.oracle.com/partners/campaign/index-090165.html), par exemple *ODAC122010Xcopy_x64.zip*, puis chargez-le dans votre conteneur avec *main.cmd*.

      * Un dossier *POSTGRESQL ODBC* contenant un script d’installation personnalisée (*main.cmd*) pour installer les pilotes ODBC PostgreSQL sur chaque nœud de votre Azure-SSIS IR. Cette installation vous permet d’utiliser le Gestionnaire des connexions, la source et la destination ODBC pour vous connecter au serveur PostgreSQL. 
     
        Tout d’abord, [téléchargez les dernières versions 64 bits et 32 bits des programmes d’installation de pilotes ODBC PostgreSQL](https://www.postgresql.org/ftp/odbc/versions/msi/) (par exemple, *psqlodbc_x64.msi* et *psqlodbc_x86.msi*), puis chargez-les avec *main.cmd* dans votre conteneur.

      * Un dossier *SAP BW* contenant un script d’installation personnalisée (*main.cmd*) pour installer l’assembly de connecteurs SAP .NET (*librfc32.dll*) sur chaque nœud de votre Azure-SSIS IR Édition Entreprise. Cette installation vous permet d’utiliser le Gestionnaire des connexions, la source et la destination SAP Business Warehouse pour vous connecter au serveur SAP BW. 
      
        Commencez par charger la version 64 bits ou 32 bits de *librfc32.dll* du dossier d’installation SAP avec *main.cmd* dans votre conteneur. Le script copie ensuite l’assembly SAP dans le dossier *%windir%\SysWow64* ou *%windir%\System32* lors de l’installation.

      * Un dossier *STORAGE* contenant un script d’installation personnalisée (*main.cmd*) pour installer Azure PowerShell sur chaque nœud de votre Azure-SSIS IR. Cette installation vous permet de déployer et d’exécuter des packages SSIS qui exécutent des [scripts PowerShell pour manipuler votre compte de stockage Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). 
      
        Copiez *main.cmd*, un exemple *AzurePowerShell.msi* (ou utilisez la version la plus récente) et *storage.ps1* dans votre conteneur. Utilisez *PowerShell.dtsx* comme modèle pour vos packages. Le modèle de package combine une [tâche de téléchargement Azure Blob](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), qui télécharge *storage.ps1* sous forme de script PowerShell modifiable, et une [tâche d’exécution du processus](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) qui exécute le script sur chaque nœud.

      * Un dossier*TERADATA*, qui contient un script d’installation personnalisée (*main.cmd*), son fichier associé (*install.cmd*) et les packages du programme d’installation ( *.msi*). Ces fichiers installent les connecteurs Teradata, l’API Teradata Parallel Transporter (TPT) et le pilote ODBC sur chaque nœud de votre Azure-SSIS IR Édition Entreprise. Cette installation vous permet d’utiliser le Gestionnaire des connexions, la source et la destination Teradata pour vous connecter au serveur Teradata. 
      
        Tout d’abord, [téléchargez le fichier zip Teradata Tools and Utilities 15.x](http://partnerintelligence.teradata.com) (par exemple, *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*), puis chargez-le avec les fichiers *.cmd* et *.msi* précédemment mentionnés dans votre conteneur.

      * Un dossier *TLS 1.2* contenant un script d’installation personnalisée (*main. cmd*) pour utiliser un chiffrement fort/protocole réseau plus sécurisé (TLS 1.2) et désactiver des versions SSL/TLS plus anciennes sur chaque nœud de votre Azure-SSIS IR.

      * Un dossier *ZULU OPENJDK* contenant un script d’installation personnalisée (*main.cmd*) et un fichier PowerShell (*install_openjdk.ps1*) pour installer Zulu OpenJDK sur chaque nœud de votre Azure-SSIS IR. Cette installation vous permet d’utiliser des connecteurs Azure Data Lake Store et Flexible File pour traiter des fichiers ORC et Parquet. Pour plus d’informations, consultez [Feature Pack Azure pour Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java). 
      
        Tout d’abord, [téléchargez la dernière version de Zulu OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) (par exemple, *zulu8.33.0.1-jdk8.0.192-win_x64.zip*), puis téléchargez-la avec *main.cmd* et *install_openjdk.ps1* dans votre conteneur.

        ![Dossiers dans le dossier de scénarios utilisateur](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Pour réutiliser ces exemples d’installation personnalisée standard, copiez le contenu du dossier sélectionné et collez-le dans votre conteneur.

1. Lorsque vous approvisionnez ou reconfigurez votre IR Azure-SSIS sur l’interface utilisateur ADF, activez la case à cocher **Personnaliser votre IR Azure-SSIS avec des installations de composants/configurations système supplémentaires** sur la page **Paramètres avancés** du volet **Configuration du runtime d’intégration**, puis entrez l’URI de SAP de votre conteneur dans la zone **URI de SAP du conteneur d’installation personnalisée**.
   
1. Lorsque vous approvisionnez ou reconfigurez votre IR Azure-SSIS à l’aide de PowerShell, arrêtez-le s’il est déjà démarré ou en cours d’exécution, exécutez la cmdlet `Set-AzDataFactoryV2IntegrationRuntime` avec l’URI de SAP de votre conteneur comme valeur du paramètre `SetupScriptContainerSasUri`, puis démarrez votre IR Azure-SSIS.

1. Une fois votre installation personnalisée standard terminée et votre IR Azure-SSIS démarré, vous pouvez trouver la sortie standard de *main.cmd* et d’autres journaux d’exécution dans le dossier *main.cmd.log* de votre conteneur.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer Enterprise Edition pour Azure-SSIS IR](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Développer des composants payants ou sous licence pour Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md)
