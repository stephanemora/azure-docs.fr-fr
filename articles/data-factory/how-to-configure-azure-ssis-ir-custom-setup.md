---
title: Personnalisation l’installation du runtime d’intégration Azure-SSIS
description: Cet article décrit comment utiliser l’interface d’installation personnalisée du runtime d’intégration Azure-SSIS pour installer des composants supplémentaires ou modifier des paramètres
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
ms.date: 12/23/2019
ms.openlocfilehash: ccf7ba2fd27dabdb090be87c5438ad68471996da
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/26/2019
ms.locfileid: "75497063"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Personnalisation l’installation du runtime d’intégration Azure-SSIS

L’interface d’installation personnalisée pour le runtime d'intégration Azure-SSIS fournit une interface permettant d’ajouter vos propres étapes de configuration lors de l’approvisionnement ou de la nouvelle configuration de votre IR Azure-SSIS. 

L’installation personnalisée permet de modifier la configuration ou l’environnement d’exploitation par défaut (par exemple, pour lancer des services Windows supplémentaires ou conserver des informations d'identification d'accès pour les partages de fichiers) ou d’installer des composants supplémentaires (par exemple, des assemblys, des pilotes ou des extensions) sur chacun des nœuds d’un IR Azure-SSIS.

Vous pouvez effectuer deux types d’installations personnalisées sur votre Azure-SSIS IR : des installations personnalisées rapides sans scripts et des installations personnalisées standard avec scripts.

Avec des installations personnalisées rapides, vous pouvez exécuter certaines configurations système/commandes Windows courantes ou installer des composants supplémentaires populaires/recommandés sans utiliser le moindre script.  

Avec des installations personnalisées standard, vous devez préparer un script et ses fichiers associés, puis charger le tout dans un conteneur d’objets blob de votre compte de stockage Azure. Vous fournissez ensuite un URI (Uniform Resource IdentifierI) à signature d’accès partagé (SAP) pour votre conteneur lorsque vous approvisionnez ou reconfigurez votre Azure-SSIS IR. Chaque nœud de votre Azure-SSIS IR télécharge ensuite le script et ses fichiers associés à partir de votre conteneur et exécute votre installation personnalisée avec des privilèges élevés. Lorsque votre installation personnalisée est terminée, chaque nœud charge la sortie standard de l’exécution et d’autres journaux d’activité dans votre conteneur.

Vous pouvez installer des composants gratuits/sans licence et payants/sous licence avec des installations personnalisées rapides/standard. Si vous êtes un éditeur de logiciels, voir notre documentation [Comment développer des composants payants, ou sous licence, pour Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Les nœuds de série v2 d’Azure-SSIS IR ne convenant pas pour une installation personnalisée, veuillez utiliser les nœuds de série v3 à la place.  Si vous utilisez déjà les nœuds de série v2, optez dès que possible pour l’utilisation des nœuds de série v3.

## <a name="current-limitations"></a>Limites actuelles

Les limitations suivantes s’appliquent uniquement aux installations personnalisées standard :

- Si vous souhaitez utiliser `gacutil.exe` dans votre script pour installer des assemblys dans Global Assembly Cache (GAC), vous devez fournir `gacutil.exe` dans le cadre de votre installation personnalisée, ou utiliser l’exemplaire fourni dans notre conteneur en préversion publique ci-dessous.

- Si vous souhaitez faire référence à un sous-dossier dans votre script, `msiexec.exe` ne prend pas en charge la notation `.\` pour référencer le dossier racine. Utilisez une commande telle que `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` au lieu de `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

- Les partages administratifs, c.-à-d. des partages réseau masqués créés automatiquement par Windows, ne sont actuellement pas pris en charge sur l’Azure-SSIS IR.

- Le pilote ODBC IBM iSeries Access n’est pas pris en charge sur Azure-SSIS IR. Il se peut que des erreurs d’installation s’affichent lors de votre installation personnalisée. Pour obtenir de l’aide, contactez le support IBM.

## <a name="prerequisites"></a>Conditions préalables requises

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pour personnaliser votre IR Azure-SSIS, vous avez besoin de ce qui suit :

- [Abonnement Azure](https://azure.microsoft.com/)

- [Approvisionnement de votre Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Un compte Stockage Azure](https://azure.microsoft.com/services/storage/). Non requis pour les installations personnalisées rapides. Pour des installations personnalisées standard, vous chargez et stockez votre script d’installation personnalisée et ses fichiers associés dans un conteneur d’objets blob. Le processus d’installation personnalisée télécharge également ses journaux d’activité d’exécution sur le même conteneur d’objets blob.

## <a name="instructions"></a>Instructions

1. Si vous souhaitez approvisionner ou reconfigurer votre Azure-SSIS IR avec PowerShell, téléchargez et installez [Azure PowerShell](/powershell/azure/install-az-ps). Pour les configurations personnalisées rapides, passez à l’étape 4.

1. Préparer votre script d’installation personnalisée et ses fichiers associés (par exemple, les fichiers .bat, .cmd, .exe, .dll, .msi ou .ps1).

   1. Vous devez avoir un fichier de script nommé `main.cmd`, qui est le point d’entrée de votre installation personnalisée.

   1. Vous devez vous assurer que le script peut être exécuté en mode silencieux, il est recommandé de tester d’abord le script sur l’ordinateur local.

   1. Si vous souhaitez que les fichiers journaux supplémentaires générés par d’autres outils (par exemple, `msiexec.exe`) soient chargés dans votre conteneur, spécifiez la variable d’environnement prédéfinie `CUSTOM_SETUP_SCRIPT_LOG_DIR` comme dossier de journal d’activité dans vos scripts (par exemple, `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

1. Téléchargez, installez et lancez [l’Explorateur Stockage Azure](https://storageexplorer.com/).

   1. Sous **(Local et attaché)** , sélectionnez avec le bouton droit **Comptes de stockage**, puis sélectionnez **Se connecter au stockage Azure**.

      ![Se connecter au Stockage Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   1. Sélectionnez **Utiliser le nom et la clé d’un compte de stockage**, puis sélectionnez **Suivant**.

      ![Utiliser le nom et la clé d’un compte de stockage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   1. Entrez le nom et la clé de votre compte de stockage Azure, sélectionnez **Suivant**, puis **Se connecter**.

      ![Fournissez le nom du compte et la clé du compte de stockage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   1. Dans votre compte de stockage Azure connecté, cliquez avec le bouton droit sur **Conteneurs d’objets blob**, sélectionnez **Créer un conteneur d’objets Blob** et nommez le nouveau conteneur.

      ![Création d’un conteneur d’objets blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   1. Sélectionnez le nouveau conteneur et chargez votre script d’installation personnalisée et ses fichiers associés. Veillez à charger `main.cmd` au niveau supérieur de votre conteneur, pas dans un dossier. Vérifiez également que votre conteneur contient uniquement les fichiers d’installation personnalisée nécessaires afin que leur téléchargement ultérieur sur votre runtime d’intégration Azure-SSIS ne prenne pas beaucoup de temps. Le délai maximal pour le programme d’installation personnalisé est actuellement fixé à 45 minutes avant sont expiration, ce qui inclut le temps nécessaire au téléchargement de tous les fichiers de votre conteneur et leur installation Azure-SSIS IR. Si une période plus longue est nécessaire, veuillez envoyer un ticket de support.

      ![Chargez des fichiers vers le conteneur d’objets blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   1. Cliquez avec le bouton droit sur le conteneur, puis sélectionnez **Obtenir une signature d’accès partagé**.

      ![Obtenez la signature d’accès partagé pour le conteneur](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   1. Créez l’URI de SAP pour votre conteneur avec un délai d’expiration suffisamment long et des autorisations de lecture + d’écriture + de liste. Vous aurez besoin de l’URI de SAP pour télécharger et exécuter votre script d’installation personnalisée et les fichiers associés à chaque fois qu’un nœud de votre IR Azure-SSIS sera réinitialisé/redémarré. Vous avez besoin d’une permission d’écriture pour charger des journaux d’activité d’exécution de l’installation.

      > [!IMPORTANT]
      > Vérifiez que l’URI SAP n’arrive pas à expiration et que les ressources d’installation personnalisée restent disponibles pendant la totalité du cycle de vie de votre Azure-SSIS IR, de la création à la suppression, en particulier si vous l’arrêtez et le redémarrez régulièrement au cours de cette période.

      ![Générez la signature d’accès partagé pour le conteneur](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   1. Copiez et enregistrez l’URI de SAP de votre conteneur.

      ![Copiez et enregistrez la signature d’accès partagé.](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Lorsque vous approvisionnez ou reconfigurez votre Azure-SSIS IR avec l’interface utilisateur de Data Factory, vous pouvez ajouter/supprimer des configurations personnalisées en activant la case à cocher **Personnaliser votre Azure-SSIS Integration Runtime avec des installations de composants/configurations système supplémentaires** dans la section **Paramètres avancés** du panneau de configuration du runtime d’intégration. 

   Si vous souhaitez ajouter des installations personnalisées standard, entrez l’URI SAP de votre conteneur dans le champ **URI SAP du conteneur d’installation personnalisée**. 
   
   Si vous souhaitez ajouter des installations personnalisées rapides, sélectionnez **Nouveau** pour ouvrir le panneau **Ajouter une installation personnalisée rapide**, puis sélectionnez les types de votre choix dans le menu déroulant **Type d’installation personnalisée rapide** :

   1. Si vous sélectionnez le type **Exécuter la commande cmdkey**, vous pouvez conserver les informations d’identification d’accès pour vos partages de fichiers/Azure Files sur Azure-SSIS IR en entrant vos nom d’ordinateur/de domaine ciblé, nom de compte/nom d’utilisateur, et clé de compte/mot de passe respectivement dans les champs **/Add**, **/User** et **/Pass**. Cela est similaire à l’exécution de la commande Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) sur votre ordinateur local.
   
   1. Si vous sélectionnez le type **Ajouter une variable d’environnement**, vous pouvez ajouter des variables d’environnement Windows à utiliser dans vos packages, qui s’exécutent sur Azure-SSIS IR, en entrant le nom et la valeur de votre variable d’environnement respectivement dans les champs **Nom de la variable**  et **Valeur de la variable**. Cela est similaire à l’exécution de la commande Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) sur votre ordinateur local.

   1. Si vous sélectionnez le type **Installer le composant sous licence**, vous pouvez sélectionner tous les composants intégrés de nos partenaires ISV dans le menu déroulant **Nom du composant** :

      1. Si vous sélectionnez le composant **Fabrique de tâches de SentryOne**, vous pouvez installer la suite de composants [Fabrique de tâches](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) de SentryOne sur votre Azure-SSIS IR en entrant la clé de licence du produit que vous leur avez achetée dans le champ **Clé de licence**. La version intégrée actuelle est **2019.4.3**.

      1. Si vous sélectionnez le composant **HEDDA.IO d’oh22**, vous pouvez installer le composant de qualité/nettoyage des données [HEDDA. IO](https://hedda.io/ssis-component/) d’oh22 sur votre Azure-SSIS IR après avoir acheté leur service. La version intégrée actuelle est **1.0.13**.

      1. Si vous sélectionnez le composant **SQLPhonetics.NET d’oh22**, vous pouvez installer le composant de qualité/nettoyage des données [SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) d’oh22 sur votre Azure-SSIS IR en entrant la clé de licence du produit que vous leur avez achetée dans le champ **Clé de licence**. La version intégrée actuelle est **1.0.43**.
   
   Vos installations personnalisées rapides ajoutées s’affichent dans la section **Paramètres avancés**. Pour les supprimer, vous pouvez activer leurs cases à cocher, puis sélectionner **Supprimer**.

   ![Paramètres avancés avec des installations personnalisées](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

   Lorsque vous approvisionnez ou reconfigurez votre Azure-SSIS IR avec PowerShell, vous pouvez ajouter/supprimer des installations personnalisées en exécutant la cmdlet `Set-AzDataFactoryV2IntegrationRuntime` avant de démarrer votre Azure-SSIS IR.
   
   Pour les installations personnalisées standard, vous pouvez fournir l’URI SAP de votre conteneur comme valeur pour le paramètre `SetupScriptContainerSasUri`. Par exemple :

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                         -Name $MyAzureSsisIrName `
                                         -ResourceGroupName $MyResourceGroupName `
                                         -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

   Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                           -Name $MyAzureSsisIrName `
                                           -ResourceGroupName $MyResourceGroupName
   ```
   
   Une fois votre installation personnalisée terminée et votre Azure-SSIS IR démarré, vous pouvez trouver la sortie standard de `main.cmd` et d’autres journaux d’exécution dans le dossier `main.cmd.log` de votre conteneur de stockage.

1. Pour voir d’autres exemples d’installations personnalisées standard, connectez-vous à notre conteneur en préversion publique avec l’Explorateur Stockage Azure.

   1. Sous **(Locale et attaché)** , cliquez avec le bouton droit sur **Comptes de stockage**, sélectionnez **Se connecter au stockage Azure**, **Utiliser une chaîne de connexion ou un URI de signature d’accès partagé**, puis **Suivant**.

      ![Se connecter au stockage Azure avec la signature d’accès partagé](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   1. Sélectionnez **Utiliser un URI de SAP** et entrez l’URI de SAP suivant pour le conteneur de préversion publique. Sélectionnez **Suivant**, puis **Connecter**.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![Fournissez la signature d’accès partagé pour le conteneur](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   1. Sélectionnez le conteneur de version préliminaire publique connecté et double-cliquez sur le dossier `CustomSetupScript`. Ce dossier contient les éléments suivants :

      1. Un dossier `Sample`, qui contient une installation personnalisée pour une tâche de base sur chaque nœud de votre IR Azure-SSIS. La tâche est juste mise en veille pendant quelques secondes. Le dossier comporte aussi un dossier `gacutil` dont le contenu entier (`gacutil.exe`, `gacutil.exe.config` et `1033\gacutlrc.dll`) peut être copié tel quel dans votre conteneur.

      1. Un dossier `UserScenarios` contenant plusieurs exemples d’installations personnalisées pour des scénarios d’utilisateurs réels.

      ![Contenu du conteneur de version préliminaire publique](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   1. Double-cliquez sur le dossier `UserScenarios` pour rechercher les éléments suivants :

      1. Un dossier `.NET FRAMEWORK 3.5`, qui contient une installation personnalisée permettant d’installer une version précédente de .NET Framework, potentiellement nécessaire pour les composants personnalisés sur chaque nœud de votre runtime d’intégration Azure-SSIS.

      1. Un dossier `BCP` qui contient une installation personnalisée pour les utilitaires de ligne de commande SQL Server (`MsSqlCmdLnUtils.msi`), y compris le programme de copie en bloc (`bcp`), sur chaque nœud de votre IR Azure-SSIS.

      1. Un dossier `EXCEL` contenant un script d’installation personnalisée (`main.cmd`) pour installer des assemblys/bibliothèques C# que vous pouvez utiliser dans des tâches de scripts pour lire et écrire de façon dynamique des fichiers Excel sur chaque nœud de votre Azure-SSIS IR. Commencez par télécharger `ExcelDataReader.dll` à partir d’[ici](https://www.nuget.org/packages/ExcelDataReader/) et `DocumentFormat.OpenXml.dll` à partir d’[ici](https://www.nuget.org/packages/DocumentFormat.OpenXml/), puis chargez-les avec `main.cmd` dans votre conteneur. Autrement, si vous souhaitez simplement utiliser le gestionnaire/la source/la destination de connexion Excel standard, le composant redistribuable d’accès requis est déjà préinstallé sur votre Azure-SSIS IR et vous n’avez donc pas besoin d’une installation personnalisée.
      
      1. Un dossier `MYSQL ODBC` contenant un script d’installation personnalisée (`main.cmd`) pour installer les pilotes ODBC MySQL sur chaque nœud de votre Azure-SSIS IR. Cette installation vous permet d’utiliser le gestionnaire/la source/la destination de connexion ODBC pour vous connecter au serveur MySQL. Commencez par télécharger les dernières versions 64 bits et 32 bits des programmes d’installation de pilotes ODBC MySQL (par exemple, `mysql-connector-odbc-8.0.13-winx64.msi` et `mysql-connector-odbc-8.0.13-win32.msi`) de [MySQL](https://dev.mysql.com/downloads/connector/odbc/), puis chargez-les avec `main.cmd` dans votre conteneur.

      1. Un dossier `ORACLE ENTERPRISE`, qui contient un script d’installation personnalisée (`main.cmd`) et le fichier de configuration de l’installation sans assistance (`client.rsp`) pour installer les connecteurs Oracle et le pilote OCI sur chaque nœud de votre instance Azure-SSIS IR, Enterprise Edition. Cette installation vous permet d’utiliser le gestionnaire/la source/la destination de connexion Oracle pour vous connecter au serveur Oracle. Commencez par télécharger Microsoft Connectors v5.0 pour Oracle (`AttunitySSISOraAdaptersSetup.msi` et `AttunitySSISOraAdaptersSetup64.msi`) à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=55179), et le dernier client Oracle (par exemple, `winx64_12102_client.zip`) depuis [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), puis chargez-les tous ensemble avec `main.cmd` et `client.rsp` dans votre conteneur. Si vous utilisez TNS pour vous connecter à Oracle, vous devez également télécharger `tnsnames.ora`, le modifier, puis le charger dans votre conteneur de manière à ce qu’il puisse être copié dans le dossier d’installation Oracle pendant l’installation.

      1. Un dossier `ORACLE STANDARD ADO.NET`, qui contient un script d’installation personnalisée (`main.cmd`) pour installer le pilote Oracle ODP.NET sur chaque nœud de votre IR Azure-SSIS. Cette installation vous permet d’utiliser le gestionnaire/la source/la destination de connexion ADO.NET pour vous connecter au serveur Oracle. Vous devez commencer par télécharger le dernier pilote ODP.NET Oracle (par exemple `ODP.NET_Managed_ODAC122cR1.zip`) d’[Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html), puis le charger avec `main.cmd` dans votre conteneur.
       
      1. Un dossier `ORACLE STANDARD ODBC`, qui contient un script d’installation personnalisée (`main.cmd`) pour installer le pilote Oracle ODBC et configurer le nom de source de données sur chaque nœud de votre IR Azure-SSIS. Cette configuration vous permet d’utiliser le gestionnaire de connexions/la source/la destination ODBC ou le gestionnaire de connexions/la source Power Query avec le type de source de données ODBC pour vous connecter au serveur Oracle. Téléchargez les derniers packages Oracle Instant Client (Basic ou Basic Lite) et ODBC, par exemple, les packages 64 bits à partir d’[ici](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (package Basic : `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`, package Basic Lite : `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`, package ODBC : `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`) ou les packages 32 bits à partir d’[ici](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (package Basic : `instantclient-basic-nt-18.3.0.0.0dbru.zip`, package Basic Lite : `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`, package ODBC : `instantclient-odbc-nt-18.3.0.0.0dbru.zip`), puis chargez-les avec `main.cmd` dans votre conteneur.

      1. Un dossier `ORACLE STANDARD OLEDB` contenant un script d’installation personnalisée (`main.cmd`) pour installer le pilote OLEDB Oracle sur chaque nœud de votre Azure-SSIS IR. Cette installation vous permet d’utiliser le gestionnaire/la source/la destination de connexion OLEDB pour vous connecter au serveur Oracle. Vous devez commencer par télécharger le dernier pilote OLEDB Oracle (par exemple `ODAC122010Xcopy_x64.zip`) d’[Oracle](https://www.oracle.com/partners/campaign/index-090165.html), puis le charger avec `main.cmd` dans votre conteneur.

      1. Un dossier `POSTGRESQL ODBC` contenant un script d’installation personnalisée (`main.cmd`) pour installer les pilotes ODBC PostgreSQL sur chaque nœud de votre Azure-SSIS IR. Cette installation vous permet d’utiliser le gestionnaire/la source/la destination de connexion ODBC pour vous connecter au serveur PostgreSQL. Commencez par télécharger les dernières versions 64 bits et 32 bits des programmes d’installation de pilotes ODBC PostgreSQL (par exemple, `psqlodbc_x64.msi` et `psqlodbc_x86.msi`) de [PostgreSQL](https://www.postgresql.org/ftp/odbc/versions/msi/), puis chargez-les avec `main.cmd` dans votre conteneur.

      1. Un dossier `SAP BW` contenant un script d’installation personnalisée (`main.cmd`) pour installer l’assembly de connecteurs SAP .NET (`librfc32.dll`) sur chaque nœud de votre IR Azure-SSIS, Édition Entreprise. Cette installation vous permet d’utiliser le gestionnaire/la source/la destination de connexion SAP BW pour vous connecter au serveur SAP BW. Commencez par charger la version 64 bits ou 32 bits de `librfc32.dll` du dossier d’installation SAP avec `main.cmd` dans votre conteneur. Le script copie ensuite l’assembly SAP dans le dossier `%windir%\SysWow64` ou `%windir%\System32` pendant l’installation.

      1. Un dossier `STORAGE`, qui contient une installation personnalisée pour Azure PowerShell sur chaque nœud de votre IR Azure-SSIS. Ce programme d’installation vous permet de déployer et d’exécuter des packages SSIS qui exécutent des scripts PowerShell [ pour manipuler votre compte Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Copiez `main.cmd`, un échantillon `AzurePowerShell.msi` (ou installez la dernière version) et `storage.ps1` dans votre conteneur. Utilisez PowerShell.dtsx comme modèle pour vos packages. Le modèle de package combine une [tâche de téléchargement Azure Blob](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), qui télécharge `storage.ps1` sous forme de script PowerShell modifiable, et une [tâche d’exécution du processus](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) qui exécute le script sur chaque nœud.

      1. Un dossier `TERADATA`, qui contient un script d’installation personnalisée (`main.cmd`), son fichier associé (`install.cmd`) et les packages du programme d’installation (`.msi`). Ces fichiers installent les connecteurs Teradata, l’API TPT et le pilote ODBC sur chaque nœud de votre Azure-SSIS IR, Édition Entreprise. Cette installation vous permet d’utiliser le gestionnaire/la source/la destination de connexion Teradata pour vous connecter au serveur Teradata. Commencez par télécharger le fichier Teradata Outils et utilitaires 15.x zip (par exemple, `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) de [Teradata](http://partnerintelligence.teradata.com), puis chargez-le avec les fichiers `.cmd` et `.msi` ci-dessus dans votre conteneur.

      1. Un dossier `ZULU OPENJDK` contenant un script d’installation personnalisée (`main.cmd`) et un fichier PowerShell (`install_openjdk.ps1`) pour installer Zulu OpenJDK sur chaque nœud de votre Azure-SSIS IR. Cette installation vous permet d’utiliser des connecteurs de fichiers Azure Data Lake Store/flexibles pour traiter des fichiers ORC/Parquet. Pour plus d’informations, voir [ici](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java). Vous devez commencer par télécharger le dernier client Oracle (par exemple `zulu8.33.0.1-jdk8.0.192-win_x64.zip`) d’[Oracle](https://www.azul.com/downloads/zulu/zulu-windows/), puis le charger avec `main.cmd` et `install_openjdk.ps1` dans votre conteneur.

      ![Dossiers dans le dossier de scénarios utilisateur](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   1. Pour tester ces exemples d’installation personnalisée, copiez et collez le contenu du dossier sélectionné dans votre conteneur.
   
      Lorsque vous approvisionnez ou reconfigurez votre Azure-SSIS IR avec l’interface utilisateur de Data Factory, activez la case à cocher **Personnaliser votre Azure-SSIS Integration Runtime avec des installations de composants/configurations système supplémentaires** dans la section **Paramètres avancés**, puis entrez l’URI SAP de votre conteneur dans le champ **URI SAP du conteneur d’installation personnalisée**.
   
      Lorsque vous approvisionnez ou reconfigurez votre Azure-SSIS IR avec PowerShell, exécutez la cmdlet `Set-AzDataFactoryV2IntegrationRuntime` avec l’URI SAP de votre conteneur comme valeur pour le paramètre `SetupScriptContainerSasUri`.

## <a name="next-steps"></a>Étapes suivantes

-   [Enterprise Edition du runtime d’intégration Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Comment développer des composants personnalisés payants, ou sous licence, pour le runtime d’intégration Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)
