---
title: Installation personnalisée du runtime d’intégration Azure SSIS | Microsoft Docs
description: Cet article décrit comment utiliser l’interface d’installation personnalisée du runtime d’intégration Azure-SSIS
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: douglasl
ms.openlocfilehash: ff47060ddfee458279c9fed0fd3fcafcf35229d2
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33885436"
---
# <a name="custom-setup-for-the-azure-ssis-integration-runtime"></a>Installation personnalisée du runtime d’intégration Azure-SSIS

L’interface d’installation personnalisée du runtime d’intégration Azure-SSIS vous permet de modifier la configuration ou l’environnement d’exploitation par défaut (par exemple, pour démarrer des services supplémentaires Windows) ou d’installer des composants supplémentaires (par exemple, des assemblys, des pilotes ou des extensions) sur chaque nœud de votre IR Azure-SSIS. En règle générale, elle fournit une interface permettant d’ajouter vos propres étapes de configuration lors de l’approvisionnement ou de la nouvelle configuration de votre IR Azure-SSIS.

Vous configurez votre installation personnalisée en préparant un script et ses fichiers associés et en les téléchargeant dans un conteneur d’objets blob de votre compte de stockage Azure. Vous fournissez un Uniform Resource Identifier (URI) à signature d’accès partagé (SAP) pour votre conteneur lorsque vous approvisionnez ou reconfigurez votre IR Azure-SSIS. Chaque nœud de votre IR Azure-SSIS télécharge ensuite le script et ses fichiers associés à partir de votre conteneur et exécute votre installation personnalisée avec des privilèges élevés. Lorsque l’installation personnalisée est terminée, chaque nœud télécharge la sortie standard de l’exécution et d’autres journaux dans votre conteneur.

Vous pouvez installer des composants libres, ou sans licence, et des composants payants, ou sous licence. Si vous êtes un éditeur de logiciels, consultez [Comment développer des composants payants, ou sous licence, pour l’IR Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md).


## <a name="current-limitations"></a>Limitations actuelles

-   Si vous souhaitez utiliser `gacutil.exe` pour installer des assemblys dans Global Assembly Cache (GAC), vous devez le fournir dans le cadre de votre installation personnalisée, ou utiliser l’exemplaire fourni dans le conteneur Préversion publique.

-   Si vous devez accompagner votre IR Azure-SSIS d’une installation personnalisée pour former un réseau virtuel, seul le réseau virtuel Azure Resource Manager est pris en charge. Le réseau virtuel classique n’est pas pris en charge.

-   Actuellement, le partage administratif n’est pas pris en charge sur l’IR Azure-SSIS.

-   Si vous souhaitez mapper un partage de fichiers à un lecteur dans votre configuration personnalisée, la commande `net use` n’est actuellement pas prise en charge. Par conséquent, vous ne pouvez pas utiliser une commande telle que `net use d: \\fileshareserver\sharename`. Au lieu de cela, utilisez la commande `cmdkey`, par exemple `cmdkey /add:fileshareserver /user:yyy /pass:zzz`, pour accéder à `\\fileshareserver\folder` directement dans vos packages.

## <a name="prerequisites"></a>Prérequis


Pour personnaliser votre IR Azure-SSIS, vous avez besoin de ce qui suit :

-   [Abonnement Azure](https://azure.microsoft.com/)

-   [Serveur Azure SQL Database ou Managed Instance](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Approvisionner votre IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Un compte Stockage Azure](https://azure.microsoft.com/services/storage/). Pour une installation personnalisée, vous téléchargez et stockez votre script d’installation personnalisée et ses fichiers associés dans un conteneur d’objets blob. Le processus d’installation personnalisée télécharge également ses journaux d’exécution sur le même conteneur d’objets blob.

## <a name="instructions"></a>Instructions

2.  Téléchargez et installez [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) (version 5.4 ou ultérieure).

3.  Préparer votre script d’installation personnalisée et ses fichiers associés (par exemple, les fichiers .bat, .cmd, .exe, .dll, .msi ou .ps1).

    1.  Vous devez avoir un fichier de script nommé `main.cmd`, qui est le point d’entrée de votre installation personnalisée.

    2.  Si vous souhaitez que les fichiers journaux supplémentaires générés par d’autres outils (par exemple, `msiexec.exe`) soient chargés dans votre conteneur, spécifiez la variable d’environnement prédéfinie `CUSTOM_SETUP_SCRIPT_LOG_DIR` comme dossier de journal dans vos scripts (par exemple, `msiexec /i xxx.msi /quiet
        /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

4.  Téléchargez, installez et lancez [l’Explorateur Stockage Azure](http://storageexplorer.com/).

    1.  Sous **(Local et attaché)**, sélectionnez avec le bouton droit **Comptes de stockage**, puis sélectionnez **Se connecter au stockage Azure**.

       ![Se connecter au Stockage Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

    2.  Sélectionnez **Utiliser le nom et la clé d’un compte de stockage**, puis sélectionnez **Suivant**.

       ![Utiliser le nom et la clé d’un compte de stockage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

    3.  Entrez le nom et la clé de votre compte de stockage Azure, sélectionnez **Suivant**, puis **Se connecter**.

       ![Fournissez le nom du compte et la clé du compte de stockage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

    4.  Dans votre compte de stockage Azure connecté, cliquez avec le bouton droit sur **Conteneurs d’objets blob**, sélectionnez **Créer un conteneur d’objets Blob** et nommez le nouveau conteneur.

       ![Création d’un conteneur d’objets blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

    5.  Sélectionnez le nouveau conteneur et chargez votre script d’installation personnalisée et ses fichiers associés. Assurez-vous que vous chargez `main.cmd` au niveau supérieur du conteneur, pas dans un dossier. 

       ![Chargez des fichiers vers le conteneur d’objets blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

    6.  Cliquez avec le bouton droit sur le conteneur, puis sélectionnez **Obtenir une signature d’accès partagé**.

       ![Obtenez la signature d’accès partagé pour le conteneur](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

    7.  Créez l’URI de SAP pour votre conteneur avec un délai d’expiration suffisamment long et des autorisations de lecture + d’écriture + de liste. Vous devez utiliser l’URI de SAP pour télécharger et exécuter votre script d’installation personnalisée et ses fichiers associés à chaque fois qu’un nœud de votre IR Azure-SSIS est réinitialisé. Vous avez besoin d’une permission d’écriture pour charger des journaux d’exécution de l’installation.

       ![Générez la signature d’accès partagé pour le conteneur](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

    8.  Copiez et enregistrez l’URI de SAP de votre conteneur.

       ![Copiez et enregistrez la signature d’accès partagé.](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

    9.  Lorsque vous approvisionnez ou reconfigurez votre IR Azure-SSIS avec PowerShell, avant de commencer votre IR Azure-SSIS, exécutez l’applet de commande `Set-AzureRmDataFactoryV2IntegrationRuntime` avec l’URI de SAP de votre conteneur comme valeur pour le nouveau paramètre `SetupScriptContainerSasUri`. Par exemple : 

       ```powershell
       Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName `
                                                  -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

       Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                    -Name $MyAzureSsisIrName `
                                                    -ResourceGroupName $MyResourceGroupName
       ```

    10.  Une fois que votre installation personnalisée est terminée et que votre IR Azure-SSIS démarre, vous pouvez trouver la sortie standard de `main.cmd` et d’autres journaux d’exécution dans le dossier `main.cmd.log` de votre conteneur de stockage.

2.  Pour voir d’autres exemples d’installation personnalisée, connectez-vous au conteneur de préversion publique avec l’Explorateur de stockage Azure.

    a.  Sous **(Locale et attaché)**, cliquez avec le bouton droit sur **Comptes de stockage**, sélectionnez **Se connecter au stockage Azure**, **Utiliser une chaîne de connexion ou un URI de signature d’accès partagé**, puis **Suivant**.

       ![Se connecter au stockage Azure avec la signature d’accès partagé](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

    b.  Sélectionnez **Utiliser un URI de SAP** et entrez l’URI de SAP suivant pour le conteneur de préversion publique. Sélectionnez **Suivant**, puis **Se connecter**.

       `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

       ![Fournissez la signature d’accès partagé pour le conteneur](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

    c. Sélectionnez le conteneur de version préliminaire publique connecté et double-cliquez sur le dossier `CustomSetupScript`. Ce dossier contient les éléments suivants :

       1. Un dossier `Sample`, qui contient une installation personnalisée pour une tâche de base sur chaque nœud de votre IR Azure-SSIS. La tâche est juste mise en veille pendant quelques secondes. Le dossier contient également un dossier `gacutil` dans lequel se trouve `gacutil.exe`.

       2. Un dossier `UserScenarios`, qui contient plusieurs installations personnalisées pour des scénarios utilisateur réels.

    ![Contenu du conteneur de version préliminaire publique](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

    d. Double-cliquez sur le dossier `UserScenarios`. Ce dossier contient les éléments suivants :

       1. Un dossier `.NET FRAMEWORK 3.5`, qui contient une installation personnalisée permettant d’installer une version précédente de .NET Framework, potentiellement nécessaire pour les composants personnalisés sur chaque nœud de votre runtime d’intégration Azure-SSIS.

       2. Un dossier `BCP` qui contient une installation personnalisée pour les utilitaires de ligne de commande SQL Server (`MsSqlCmdLnUtils.msi`), y compris le programme de copie en bloc (`bcp`), sur chaque nœud de votre IR Azure-SSIS.

       3. Un dossier `EXCEL`, qui contient une installation personnalisée pour des assemblys open source (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll` et `ExcelDataReader.dll`) sur chaque nœud de votre IR Azure-SSIS.

       4. Un dossier `MSDTC`, qui contient une installation personnalisée permettant de modifier les configurations du réseau et de la sécurité pour l’instance Microsoft Distributed Transaction Coordinator (MSDTC) sur chaque nœud de votre runtime d’intégration Azure-SSIS.

       5. Un dossier `ORACLE ENTERPRISE`, qui contient un script d’installation personnalisée (`main.cmd`) et le fichier de configuration de l’installation sans assistance (`client.rsp`) pour installer le pilote Oracle OCI sur chaque nœud de votre IR Azure-SSIS, Enterprise Edition. Cette installation vous permet d’utiliser le gestionnaire de connexions, la source et la destination Oracle. Vous devez d’abord télécharger le dernier client Oracle, par exemple `winx64_12102_client.zip`, depuis [Oracle](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), puis le charger dans votre conteneur avec `main.cmd` et `client.rsp`. Si vous utilisez TNS pour vous connecter à Oracle, vous devez également télécharger `tnsnames.ora`, le modifier, puis le charger dans votre conteneur de manière à ce qu’il puisse être copié dans le dossier d’installation Oracle pendant l’installation.

       6. Un dossier `ORACLE STANDARD`, qui contient un script d’installation personnalisée (`main.cmd`) pour installer le pilote Oracle ODP.NET sur chaque nœud de votre IR Azure-SSIS. Cette installation vous permet d’utiliser le gestionnaire de connexions, la source et la destination ADO.NET. Vous devez d’abord télécharger le dernier pilote ODP.NET Oracle, par exemple `ODP.NET_Managed_ODAC122cR1.zip`, depuis [Oracle](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html), puis le charger dans votre conteneur avec `main.cmd`.

       7. Un dossier `SAP BW`, qui contient un script d’installation personnalisée (`main.cmd`) pour installer l’assembly de connecteurs SAP .NET (`librfc32.dll`) sur chaque nœud de votre IR Azure-SSIS, Enterprise Edition. Cette installation vous permet d’utiliser le gestionnaire de connexions, la source et la destination SAP BW. Commencez par charger la version 64 bits ou 32 bits de `librfc32.dll` du dossier d’installation SAP vers votre conteneur avec `main.cmd`. Le script copie ensuite l’assembly SAP dans le dossier `%windir%\SysWow64` ou `%windir%\System32` pendant l’installation.

       8. Un dossier `STORAGE`, qui contient une installation personnalisée pour Azure PowerShell sur chaque nœud de votre IR Azure-SSIS. Ce programme d’installation vous permet de déployer et d’exécuter des packages SSIS qui exécutent des scripts PowerShell [ pour manipuler votre compte Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Copiez `main.cmd`, un échantillon `AzurePowerShell.msi` (ou installez la dernière version) et `storage.ps1` dans votre conteneur. Utilisez PowerShell.dtsx comme modèle pour vos packages. Le modèle de package combine une [tâche de téléchargement Azure Blob](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), qui télécharge `storage.ps1` sous forme de script PowerShell modifiable, et une [tâche d’exécution du processus](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) qui exécute le script sur chaque nœud.

       9. Un dossier `TERADATA`, qui contient un script d’installation personnalisée (`main.cmd)`), son fichier associé (`install.cmd`) et les packages du programme d’installation (`.msi`). Ces fichiers installent des connecteurs Teradata, l’API TPT et le pilote ODBC sur chaque nœud de votre IR Azure-SSIS, Enterprise Edition. Cette installation vous permet d’utiliser le gestionnaire de connexions, la source et la destination Teradata. Commencez par télécharger le fichier Teradata Outils et utilitaires (TTU) 15.x zip (par exemple, `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) depuis [Teradata](http://partnerintelligence.teradata.com), puis chargez-le dans votre conteneur avec les fichiers `.cmd` et `.msi` ci-dessus.

    ![Dossiers dans le dossier de scénarios utilisateur](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

    e. Pour tester ces exemples d’installation personnalisée, copiez et collez le contenu à partir du dossier sélectionné dans votre conteneur. Lorsque vous approvisionnez ou reconfigurez votre IR Azure-SSIS avec PowerShell, exécutez l’applet de commande `Set-AzureRmDataFactoryV2IntegrationRuntime` avec l’URI de SAP de votre conteneur comme valeur pour le nouveau paramètre `SetupScriptContainerSasUri`.

## <a name="next-steps"></a>Étapes suivantes

-   [Enterprise Edition du runtime d’intégration Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Comment développer des composants personnalisés payants, ou sous licence, pour le runtime d’intégration Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)