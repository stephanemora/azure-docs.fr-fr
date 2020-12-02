---
title: Utiliser Azure Data Lake Tools pour Visual Studio Code
description: Découvrez comment utiliser Azure Data Lake Tools pour Visual Studio Code pour créer, tester et exécuter des scripts U-SQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: e7da5454581e0e414dc832d4fcec50277f3b7f40
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019053"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Utiliser Azure Data Lake Tools pour Visual Studio Code

Dans cet article, vous allez apprendre à utiliser Azure Data Lake Tools pour Visual Studio Code (VS Code) pour créer, tester et exécuter des scripts U-SQL. Ces informations sont également décrites dans la vidéo suivante :

[![Lecteur vidéo : Azure Data Lake Tools pour VS Code](media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png)](https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode")

## <a name="prerequisites"></a>Prérequis

Azure Data Lake Tools pour VS Code prend en charge Windows, Linux et MacOS.  L’exécution locale et le débogage local de U-SQL fonctionnent uniquement pour les utilisateurs de Windows.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

Pour MacOS et Linux :

- [Kit SDK .NET Core 2.0](https://www.microsoft.com/net/download/core)
- [Mono 5.2.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Installer les outils Azure Data Lake

Après avoir installé les prérequis, vous pouvez installer Azure Data Lake Tools pour Visual Studio Code.

### <a name="to-install-azure-data-lake-tools"></a>Pour installer Azure Data Lake Tools

1. Ouvrez Visual Studio Code.
2. Sélectionnez **Extensions** dans le volet gauche. Entrez **Azure Data Lake Tools** dans la zone de recherche.
3. Sélectionnez **Installer** en regard de **Azure Data Lake Tools**.

   ![Sélections à effectuer pour installer Data Lake Tools](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Après quelques secondes, le bouton **Installer** est remplacé par le bouton **Recharger**.
4. Sélectionnez **Recharger** pour activer l’extension **Azure Data Lake Tools**.
5. Sélectionnez **Recharger la fenêtre** pour confirmer. **Azure Data Lake Tools** s’affiche dans le volet **Extensions**.

## <a name="activate-azure-data-lake-tools"></a>Activer Azure Data Lake Tools

Créez un fichier .usql ou ouvrez un fichier .usql existant pour activer l’extension.

## <a name="work-with-u-sql"></a>Utilisation de U-SQL

Vous devez ouvrir un fichier ou un dossier U-SQL pour utiliser U-SQL.

### <a name="to-open-the-sample-script"></a>Pour ouvrir l’exemple de script

Ouvrez la palette de commandes (Ctrl+Maj+P) et entrez **ADL: Open Sample Script**. Une autre instance de cet exemple s’ouvre. Vous pouvez également modifier, configurer et envoyer un script sur cette instance.

### <a name="to-open-a-folder-for-your-u-sql-project"></a>Pour ouvrir un dossier pour votre projet U-SQL

1. À partir de Visual Studio Code, sélectionnez le menu **Fichier**, puis **Ouvrir un dossier**.
2. Spécifiez un dossier, puis sélectionnez **Sélectionner le dossier**.
3. Sélectionnez le menu **Fichier**, puis **Nouveau**. Un fichier Untitled-1 est ajouté au projet.
4. Entrez le code suivant dans le fichier Untitled-1 :

   ```usql
   @departments  =
       SELECT * FROM
           (VALUES
               (31,    "Sales"),
               (33,    "Engineering"),
               (34,    "Clerical"),
               (35,    "Marketing")
           ) AS
                 D( DepID, DepName );
   ```

   OUTPUT @departments     TO "/Output/departments.csv" USING Outputters.Csv();

    Le script crée un fichier departments.csv avec des données dans le dossier /output.

5. Enregistrez le fichier sous **myUSQL.usql** dans le dossier ouvert.

### <a name="to-compile-a-u-sql-script"></a>Pour compiler un script U-SQL

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes.
2. Entrez **ADL: Compile Script**. Les résultats de la compilation s’affichent dans la fenêtre **Output**. Vous pouvez également cliquer avec le bouton droit sur un fichier de script, puis sélectionner **ADL: Compile Script** pour compiler un travail U-SQL. Le résultat de la compilation s’affiche dans le volet **Output**.

### <a name="to-submit-a-u-sql-script"></a>Pour envoyer un script U-SQL

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes.
2. Entrez **ADL: Submit Job**. Vous pouvez également cliquer avec le bouton droit sur un fichier de script, puis sélectionner **ADL: Submit Job**.

Une fois que vous avez envoyé un travail U-SQL, les journaux d’activité d’envoi apparaissent dans la fenêtre **Output** dans VS Code. La vue des travaux apparaît dans le volet droit. Si l’envoi a abouti, l’URL du travail est également affichée. Vous pouvez ouvrir l’URL du travail dans un navigateur web pour suivre l’état du travail en temps réel.

L’onglet **SUMMARY** (RÉSUMÉ) de la vue des travaux affiche les détails du travail. Les principales fonctions disponibles sont le renvoi d'un script, la duplication d'un script et l’ouverture dans le portail. L’onglet **DATA** (DONNÉES) de la vue des travaux vous permet de vous référer aux fichiers d’entrée, aux fichiers de sortie et aux ressources. Vous pouvez télécharger les fichiers sur l’ordinateur local.

![Onglet Summary (Résumé) de la vue des travaux](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Onglet Data (Données) de la vue des travaux](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

### <a name="to-set-the-default-context"></a>Pour définir le contexte par défaut

Si vous n’avez pas configuré les fichiers individuellement, vous pouvez définir le contexte par défaut pour l'appliquer à tous les fichiers de script.

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes.
2. Entrez **ADL: Set Default Context**. Ou cliquez avec le bouton droit sur l’éditeur de script, puis sélectionnez **ADL: Set Default Context**.
3. Choisissez le compte, la base de données et le schéma souhaités. La configuration est enregistrée dans le fichier xxx_settings.json.

   ![Compte, base de données et schéma définis comme contexte par défaut](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

### <a name="to-set-script-parameters"></a>Pour définir les paramètres de script

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes.
2. Entrez **ADL: Set Script Parameters**.
3. Le fichier xxx_settings.json s’ouvre avec les propriétés suivantes :

   - **account** : compte Azure Data Lake Analytics situé sous votre abonnement Azure et nécessaire à la compilation et à l’exécution des travaux U-SQL. Vous devez configurer le compte d’ordinateur avant de compiler et d’exécuter les travaux U-SQL.
   - **database**: base de données sous votre compte. La valeur par défaut est **master**.
   - **schema**: schéma sous votre base de données. La valeur par défaut est **dbo**.
   - **optionalSettings** :
        - **priority**: la plage de priorité est comprise entre 1 et 1000. 1 correspond à la priorité la plus élevée. La valeur par défaut est **1000**.
        - **degreeOfParallelism**: la plage de parallélisme est comprise entre 1 et 150. La valeur par défaut est le parallélisme maximal autorisé dans votre compte Azure Data Lake Analytics.

   ![Contenu du fichier JSON](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)

> [!NOTE]
> Une fois la configuration enregistrée et si vous n’avez pas défini de contexte par défaut, les informations relatives au compte, à la base de données et au schéma s’affichent sur la barre d’état, en bas à gauche du fichier .usql correspondant.

### <a name="to-set-git-ignore"></a>Pour définir Git Ignore

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes.
2. Entrez **ADL: Set Git Ignore**.

   - Si votre dossier de travail VSCode ne contient pas de fichier **.gitIgnore**, un fichier nommé **.gitIgnore** y est créé. Quatre éléments (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache** et **obj**) sont ajoutés au fichier par défaut. Si nécessaire, vous pouvez effectuer d'autres mises à jour.
   - Si votre dossier de travail VSCode contient déjà un fichier **.gitIgnore**, l’outil ajoute quatre éléments (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache** et **obj**) dans votre fichier **.gitIgnore**, à condition qu'ils n'y figurent pas déjà.

   ![Éléments présents dans le fichier .gitIgnore](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Travailler avec les fichiers code-behind : C Sharp, Python et R

Azure Data Lake Tools prend en charge différents codes personnalisés. Pour obtenir des instructions, reportez-vous à [Développer en U-SQL avec Python, R et C Sharp pour Azure Data Lake Analytics dans VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Travailler avec les assemblies

Pour plus d’informations sur le développement d’assemblys, consultez [Développement d’assemblys U-SQL pour les tâches d’Azure Data Lake Analytics]().

Vous pouvez utiliser Data Lake Tools pour inscrire des assemblys de code personnalisé dans le catalogue Data Lake Analytics.

### <a name="to-register-an-assembly"></a>Pour inscrire un assembly

Vous pouvez inscrire l’assembly via la commande **ADL: Register Assembly** ou **ADL: Register Assembly (Advanced)** .

### <a name="to-register-through-the-adl-register-assembly-command"></a>Pour effectuer une inscription via ADL : Commande Register Assembly

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes.
2. Entrez **ADL: Register Assembly**.
3. Spécifiez le chemin d’accès de l’assembly local.
4. Sélectionnez un compte Data Lake Analytics.
5. Sélectionnez une base de données.

Le portail s’ouvre dans un navigateur et affiche le processus d’inscription de l’assembly.  

Pour déclencher la commande **ADL: Register Assembly**, vous pouvez également cliquer avec le bouton droit sur le fichier .dll dans l’Explorateur de fichiers.

### <a name="to-register-through-the-adl-register-assembly-advanced-command"></a>Pour effectuer une inscription via ADL : Commande Register Assembly (Advanced)

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes.
2. Entrez **ADL: Register Assembly (Advanced)** .
3. Spécifiez le chemin d’accès de l’assembly local.
4. Le fichier JSON s’affiche. Examinez et modifiez, si nécessaire, les paramètres des ressources et les dépendances de l’assembly. Les instructions s’affichent dans la fenêtre **Output**. Pour procéder à l’inscription de l’assembly, enregistrez (Ctrl+S) le fichier JSON.

   ![Fichier JSON avec paramètres de ressources et dépendances de l’assembly](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)

>[!NOTE]
>
>- S'il existe des dépendances de l’assembly dans la bibliothèque de liens dynamiques (DLL), Azure Data Lake Tools les détecte automatiquement. Une fois détectées, les dépendances apparaissent dans le fichier JSON.
>- Vous pouvez charger vos ressources DLL (.txt, .png et .csv, par exemple) dans le cadre de l’inscription de l’assembly.

Pour déclencher la commande **ADL: Register Assembly (Advanced)** , vous pouvez également cliquer avec le bouton droit sur le fichier .dll dans l’Explorateur de fichiers.

Le code U-SQL suivant montre comment appeler un assembly. Dans l’exemple, le nom de l’assembly est *test*.

```usql
REFERENCE ASSEMBLY [test];
@a =
    EXTRACT
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string
    FROM @"Sample/SearchLog.txt"
    USING Extractors.Tsv();
@d =
    SELECT DISTINCT Region
    FROM @a;
@d1 =
    PROCESS @d
    PRODUCE
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();
OUTPUT @d1
    TO @"Sample/SearchLogtest.txt"
    USING Outputters.Tsv();
```

## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Utiliser l'exécution locale et le débogage local de U-SQL pour les utilisateurs de Windows

L’exécution locale de U-SQL teste vos données locales et valide votre script localement, avant que votre code soit publié dans Data Lake Analytics. Vous pouvez utiliser la fonctionnalité de débogage local pour accomplir les tâches suivantes avant que votre code soit soumis à Data Lake Analytics :

- Déboguer votre code-behind C#
- Examiner le code
- Valider votre script localement

La fonctionnalité d’exécution locale et de débogage local fonctionne uniquement dans des environnements Windows, et elle n’est pas prise en charge sur les systèmes d’exploitation MacOS et Linux.

Pour obtenir des instructions sur l’exécution locale et le débogage local, consultez [Exécution locale et débogage local U-SQL avec Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="connect-to-azure"></a>Connexion à Azure

Avant de pouvoir compiler et exécuter des scripts U-SQL dans Data Lake Analytics, vous devez vous connecter à votre compte Azure.

<a id="sign-in-by-command"></a>

### <a name="to-connect-to-azure-by-using-a-command"></a>Pour se connecter à Azure à l'aide d'une commande

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes.

2. Entrez **ADL: Connexion**. Les informations de connexion s’affichent en bas à droite.

   ![Entrer la commande de connexion](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

   ![Notification relative à la connexion et à l'authentification](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3. Sélectionnez **Copier et ouvrir** pour ouvrir la [page web de connexion](https://aka.ms/devicelogin). Collez le code dans la zone, puis sélectionnez **Continuer**.

    ![Page web de connexion](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  

4. Suivez les instructions pour vous connecter à partir de la page web. Une fois connecté, le nom de votre compte Azure s’affiche sur la barre d’état dans le coin inférieur gauche de la fenêtre VS Code.

> [!NOTE]
>
> - Si vous ne vous déconnectez pas, Data Lake Tools vous connectera automatiquement lors de votre prochaine visite.
> - Si votre compte a deux facteurs activés, nous vous recommandons d’utiliser l’authentification par téléphone plutôt qu’un code PIN.

Pour vous déconnecter, entrez la commande **ADL: Logout**.

### <a name="to-connect-to-azure-from-the-explorer"></a>Pour se connecter à Azure à partir de l’Explorateur

Développez **AZURE DATALAKE**, sélectionnez **Se connecter à Azure**, puis suivez les étapes 3 et 4 de la section [Pour vous connecter à Azure à l’aide d’une commande](#sign-in-by-command).

![Sélection de « Connexion à Azure » dans l’Explorateur](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Vous ne pouvez pas vous déconnecter depuis l’Explorateur. Pour vous déconnecter, reportez-vous à la section [Pour vous connecter à Azure à l'aide d'une commande](#sign-in-by-command).

## <a name="create-an-extraction-script"></a>Créer un script d’extraction

Vous pouvez créer un script d’extraction pour des fichiers .csv, .tsv et .txt à l’aide de la commande **ADL: Create EXTRACT Script** ou à partir de l’Explorateur Azure Data Lake.

### <a name="to-create-an-extraction-script-by-using-a-command"></a>Pour créer un script d’extraction à l’aide d’une commande

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes, puis entrez **ADL: Create EXTRACT Script**.
2. Spécifiez le chemin d’accès complet d’un fichier de stockage Azure, puis sélectionnez la touche Entrée.
3. Sélectionnez un compte.
4. Pour un fichier .txt, sélectionnez un séparateur afin d'extraire le fichier.

![Processus de création d’un script d’extraction](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Le script d’extraction est généré d’après vos entrées. Pour empêcher un script de détecter les colonnes, choisissez l’une des deux options. Sans intervention de votre part, un seul script sera généré.

![Résultat de la création d’un script d’extraction](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

### <a name="to-create-an-extraction-script-from-the-explorer"></a>Pour créer un script d’extraction à partir de l’Explorateur

Pour créer le script d’extraction, vous pouvez également utiliser le menu contextuel (raccourci) sur le fichier .csv, . tsv ou .txt dans Azure Data Lake Store ou dans le stockage d’objets blob Azure.

![Commande « Create EXTRACT Script » du menu contextuel](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Intégration à Azure Data Lake Analytics à l'aide d'une commande

Vous pouvez accéder aux ressources Azure Data Lake Analytics pour consulter la liste des comptes, accéder aux métadonnées et afficher les travaux d’analyse.

### <a name="to-list-the-azure-data-lake-analytics-accounts-under-your-azure-subscription"></a>Pour lister les comptes Azure Data Lake Analytics de votre abonnement Azure

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes.
2. Entrez **ADL: List Accounts**. Les comptes s’affichent dans le panneau **Sortie**.

### <a name="to-access-azure-data-lake-analytics-metadata"></a>Pour accéder aux métadonnées Azure Data Lake Analytics

1. Sélectionnez Ctrl+Maj+P et entrez **ADL: List Tables**.
2. Sélectionnez l’un des comptes Data Lake Analytics.
3. Sélectionnez l’une des bases de données Data Lake Analytics.
4. Sélectionnez l’un des schémas. La liste des tables s’affiche.

### <a name="to-view-azure-data-lake-analytics-jobs"></a>Pour afficher les travaux Azure Data Lake Analytics

1. Ouvrez la palette de commandes (Ctrl+Maj+P) et sélectionnez **ADL: Show Jobs**.
2. Sélectionnez un compte local ou Data Lake Analytics.
3. Attendez que la liste des travaux correspondant au compte apparaisse.
4. Sélectionnez un travail dans la liste des travaux. Data Lake Tools ouvre la vue des travaux dans le volet droit et affiche des informations dans la sortie VS Code.

    ![Liste des travaux](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Intégration à Azure Data Lake Store à l'aide d'une commande

Vous pouvez utiliser les commandes Azure Data Lake Store pour :

- [Parcourir les ressources Azure Data Lake Store](#list-the-storage-path)
- [Afficher un aperçu du fichier Azure Data Lake Store](#preview-the-storage-file)
- Charger directement le fichier sur Azure Data Lake Store dans VS Code
- Télécharger directement le fichier à partir d’Azure Data Lake Store dans VS Code

### <a name="list-the-storage-path"></a>Répertorier le chemin de stockage

### <a name="to-list-the-storage-path-through-the-command-palette"></a>Pour lister le chemin de stockage par le biais de la palette de commandes

1. Cliquez avec le bouton droit sur l’éditeur de script, et sélectionnez **ADL: List Path**.
2. Choisissez le dossier dans la liste, ou sélectionnez **Enter a path** (Entrer un chemin) ou **Browse from root path** (Parcourir à partir de la racine). (Nous utilisons **Enter a path** comme exemple).
3. Sélectionnez votre compte Data Lake Analytics.
4. Entrez ou accédez au chemin du dossier de stockage (par exemple, /output/).  

La palette de commandes répertorie les informations relatives au chemin d'après vos entrées.

![Résultats de chemin de stockage](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Pour répertorier facilement le chemin relatif, vous pouvez également utiliser le menu contextuel.

### <a name="to-list-the-storage-path-through-the-shortcut-menu"></a>Pour lister le chemin de stockage à l'aide du menu contextuel

Cliquez avec le bouton droit sur la chaîne du chemin et sélectionnez **List Path** (Répertorier le chemin).

![Option « List Path » du menu contextuel](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)

### <a name="preview-the-storage-file"></a>Afficher un aperçu du fichier de stockage

1. Cliquez avec le bouton droit sur l’éditeur de script, et sélectionnez **ADL: Preview File**.
2. Sélectionnez votre compte Data Lake Analytics.
3. Entrez le chemin d'un fichier de stockage Azure (par exemple, /output/SearchLog.txt).

Le fichier s’ouvre dans VS Code.

![Afficher un aperçu du fichier de stockage - Étapes et résultat](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Vous pouvez également afficher un aperçu du fichier en utilisant le menu contextuel sur le chemin complet ou relatif du fichier dans l’éditeur de script.

### <a name="upload-a-file-or-folder"></a>Charger un fichier ou un dossier

1. Cliquez avec le bouton droit sur l’éditeur de script et sélectionnez **Upload File** (Charger un fichier) ou **Upload Folder** (Charger un dossier).
2. Choisissez un ou plusieurs fichiers si vous avez sélectionné **Upload File**, ou le dossier tout entier si vous avez sélectionné **Upload Folder**. Puis sélectionnez **Upload** (Charger).
3. Choisissez le dossier de stockage dans la liste, ou sélectionnez **Enter a path** (Entrer un chemin) ou **Browse from root path** (Parcourir à partir de la racine). (Nous utilisons **Enter a path** comme exemple).
4. Sélectionnez votre compte Data Lake Analytics.
5. Entrez ou accédez au chemin du dossier de stockage (par exemple, /output/).
6. Sélectionnez **Choose Current Folder** (Choisir le dossier actuel) pour spécifier la destination du chargement.

![Charger un fichier ou un dossier - Étapes et résultat](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)

Vous pouvez également charger des fichiers dans le stockage en utilisant le menu contextuel sur le chemin complet ou relatif du fichier dans l’éditeur de script.

Vous pouvez [surveiller l’état du chargement](#check-storage-tasks-status).

### <a name="download-a-file"></a>Téléchargement d’un fichier

Vous pouvez télécharger un fichier avec la commande **ADL: Download File** ou **ADL: Download File (Advanced)** .

### <a name="to-download-a-file-through-the-adl-download-file-advanced-command"></a>Pour télécharger un fichier via ADL : Commande Download File (Advanced)

1. Cliquez avec le bouton droit sur l’éditeur de script, puis sélectionnez **Download File (Advanced)** (Télécharger un fichier (Avancé)).
2. VS Code affiche un fichier JSON. Vous pouvez entrer des chemins de fichiers et télécharger plusieurs fichiers en même temps. Les instructions s’affichent dans la fenêtre **Output**. Pour télécharger un ou plusieurs fichiers, enregistrez (Ctrl+S) le fichier JSON.

    ![Fichier JSON avec chemins d’accès pour le téléchargement de fichiers](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

La fenêtre **Output** affiche l’état de téléchargement du fichier.

![Fenêtre Output avec état de téléchargement](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)

Vous pouvez [surveiller l’état du téléchargement](#check-storage-tasks-status).

### <a name="to-download-a-file-through-the-adl-download-file-command"></a>Pour télécharger un fichier via ADL : Commande Download File

1. Cliquez avec le bouton droit sur l’éditeur de script, sélectionnez **Download File** (Télécharger un fichier), puis sélectionnez le dossier de destination dans la boîte de dialogue **Select Folder** (Sélectionner un dossier).

1. Choisissez le dossier dans la liste, ou sélectionnez **Enter a path** (Entrer un chemin) ou **Browse from root path** (Parcourir à partir de la racine). (Nous utilisons **Enter a path** comme exemple).

1. Sélectionnez votre compte Data Lake Analytics.

1. Entrez ou accédez au chemin du dossier de stockage (par exemple, /output/), puis choisissez un fichier à télécharger.

![Télécharger un fichier - Étapes et résultat](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png)

Vous pouvez également télécharger des fichiers de stockage en utilisant le menu contextuel sur le chemin complet ou relatif du fichier dans l’éditeur de script.

Vous pouvez [surveiller l’état du téléchargement](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Vérifier l’état des tâches de stockage

L’état du chargement et du téléchargement s’affiche sur la barre d’état. Sélectionnez la barre d’état. L’état apparaît alors dans l'onglet **OUTPUT** (SORTIE).

![Barre d’état et détails de la sortie](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Intégration à Azure Data Lake Analytics depuis l’Explorateur

Une fois connecté, tous les abonnements associés à votre compte Azure apparaissent dans le volet gauche, sous **AZURE DATALAKE**.

![Explorateur de Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Navigation au sein des métadonnées Data Lake Analytics

Développez votre abonnement Azure. Sous le nœud **U-SQL Databases** (Bases de données U-SQL), vous pouvez parcourir votre base de données U-SQL et afficher des dossiers tels que **Schemas** (Schémas), **Credentials** (Informations d’identification), **Assemblies** (Assemblys), **Tables** et **Index**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Gestion des entités de métadonnées Data Lake Analytics

Développez **U-SQL Databases** (Bases de données U-SQL). Vous pouvez créer une base de données, un schéma, une table, un type de table, un index ou des statistiques en double-cliquant sur le nœud correspondant, puis en sélectionnant **Script to Create** (Script à créer) dans le menu contextuel. Sur la page du script ouvert, modifiez le script conformément à vos besoins. Puis soumettez le travail en cliquant dessus avec le bouton droit et en sélectionnant **ADL: Submit Job**.

Au terme de la création de l’élément, cliquez sur le nœud avec le bouton droit, puis sélectionnez **Refresh** (Actualiser) pour afficher l’élément. Vous pouvez également supprimer l’élément en cliquant dessus avec le bouton droit, puis en sélectionnant **Delete** (Supprimer).

![Commande « Script to Create » (Script à créer) du menu contextuel de l’Explorateur de Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Page de script relative au nouvel élément](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Inscription de l’assembly Data Lake Analytics

Vous pouvez inscrire un assembly dans la base de données correspondante en cliquant avec le bouton droit sur le nœud **Assemblies** (Assemblys) et en sélectionnant **Register assembly** (Inscrire l'assembly).

![Commande « Register assembly » (Inscrire l'assembly) du menu contextuel pour le nœud Assemblies (Assemblys)](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Intégration à Azure Data Lake Store depuis l’Explorateur

Accédez à **Data Lake Store** :

- Vous pouvez cliquer avec le bouton droit sur le nœud de dossier, puis utiliser les commandes suivantes du menu contextuel : **Refresh** (Actualiser), **Delete** (Supprimer), **Upload** (Charger), **Upload Folder** (Charger un dossier), **Copy Relative Path** (Copier le chemin relatif) et **Copy Full Path** (Copier le chemin complet).

   ![Commandes du menu contextuel pour un nœud de dossier dans l’Explorateur de Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Vous pouvez cliquer avec le bouton droit sur le nœud de fichier, puis utiliser les commandes suivantes du menu contextuel : **Preview** (Aperçu), **Download** (Télécharger), **Delete** (Supprimer), **Create EXTRACT Script** (Créer un script d'extraction) (uniquement disponible pour les fichiers CSV, TSV et TXT), **Copy Relative Path** (Copier le chemin relatif) et **Copy Full Path** (Copier le chemin complet).

   ![Commandes du menu contextuel pour un nœud de fichier dans l’Explorateur de Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Intégration au stockage d'objets blob Azure depuis l’Explorateur

Accédez au stockage d’objets Blob :

- Vous pouvez cliquer avec le bouton droit sur le nœud de conteneur d'objets blob, puis utilisez les commandes suivantes du menu contextuel : **Refresh** (Actualiser), **Delete Blob Container** (Supprimer le conteneur d'objets blob) et **Upload Blob** (Charger l'objet blob).

   ![Commandes du menu contextuel pour un nœud de conteneur d’objets blob sous le stockage d’objets Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Vous pouvez cliquer avec le bouton droit sur le nœud de dossier, puis utiliser les commandes suivantes du menu contextuel : **Refresh** (Actualiser) et **Upload Blob** (Charger l'objet blob).

   ![Commandes du menu contextuel pour un nœud de dossier sous le stockage d’objets Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Vous pouvez cliquer avec le bouton droit sur le nœud de fichier, puis utiliser les commandes suivantes du menu contextuel : **Preview/Edit** (Aperçu/Modifier), **Download** (Télécharger), **Delete** (Supprimer), **Create EXTRACT Script** (Créer un script d'extraction) (uniquement disponible pour les fichiers CSV, TSV et TXT), **Copy Relative Path** (Copier le chemin relatif) et **Copy Full Path** (Copier le chemin complet).

    ![Commandes du menu contextuel pour un nœud de fichier sous le stockage d’objets Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Ouvrir l’Explorateur de Data Lake dans le portail

1. Sélectionnez Ctrl+Maj+P pour ouvrir la palette de commandes.
2. Entrez **Open Web Azure Storage Explorer**. Ou, dans l’éditeur de script, cliquez avec le bouton droit sur un chemin relatif ou sur le chemin complet et sélectionnez **Open Web Azure Storage Explorer**.
3. Sélectionnez un compte Data Lake Analytics.

Data Lake Tools ouvre le chemin de stockage Azure dans le portail Azure. Vous pouvez rechercher le chemin et afficher un aperçu du fichier à partir du web.

## <a name="additional-features"></a>Fonctionnalités supplémentaires

Data Lake Tools pour VS Code prend en charge les fonctionnalités suivantes :

- **IntelliSense autocomplete** (Saisie semi-automatique IntelliSense) : des suggestions s’affichent dans des fenêtres indépendantes autour des éléments tels que les mots clés, les méthodes et les variables. Les différentes icônes représentent les différents types d’objets :

  - Type de données Scala
  - Type de données complexe
  - Types définis par l’utilisateur (UDT) intégrés
  - Classes et collection .NET
  - Expressions C#
  - UDF, UDO et UDAAG C# intégrés
  - Fonctions U-SQL
  - Fonctions de fenêtrage U-SQL

    ![Types d’objets IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)

- Data Lake Tools **IntelliSense autocomplete on Data Lake Analytics metadata** (Saisie semi-automatique IntelliSense sur les métadonnées Data Lake Analytics) : Data Lake Tools télécharge les informations de métadonnées Data Lake Analytics localement.télécharge en local les informations sur les métadonnées Data Lake Analytics. La fonctionnalité IntelliSense renseigne automatiquement les objets à partir des métadonnées Data Lake Analytics. Ces objets incluent la base de données, le schéma, la table, la vue, la fonction table, les procédures et les assemblys C#.

  ![Métadonnées IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

- **IntelliSense error marker** (Marqueur d’erreurs IntelliSense) : Data Lake Tools souligne les erreurs d’édition pour U-SQL et C#.
- **Syntax highlights** (Coloration syntaxique) : Data Lake Tools utilise des couleurs pour différencier les éléments tels que les variables, les mots clés, les types de données et les fonctions.

    ![Syntaxe avec couleurs](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Nous vous recommandons de procéder à une mise à niveau vers Azure Data Lake Tools pour Visual Studio version 2.3.3000.4 ou ultérieure. Notez que les versions précédentes ne peuvent plus être téléchargées et que Microsoft les déconseille.  

## <a name="next-steps"></a>Étapes suivantes

- [Développer en U-SQL avec Python, R et C Sharp pour Azure Data Lake Analytics dans VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Exécution locale et débogage local U-SQL avec Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Tutoriel : Prise en main d’Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Tutoriel : Développer des scripts U-SQL à l’aide de Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)