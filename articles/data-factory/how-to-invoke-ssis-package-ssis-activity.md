---
title: Exécuter un package SSIS avec l’activité Exécuter le Package SSIS
description: Cet article décrit comment exécuter un package SQL Server Integration Services (SSIS) dans un pipeline Azure Data Factory à l’aide de l’activité Exécuter le Package SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 11/14/2019
ms.openlocfilehash: f400463f6102d46d9da48bbb10466ad4ca04a69b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413233"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Exécuter un package SSIS avec l’activité Exécuter le Package SSIS dans Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Cet article décrit comment exécuter un package SQL Server Integration Services (SSIS) dans un pipeline Azure Data Factory à l’aide de l’activité Exécuter le Package SSIS. 

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Créez un runtime d’intégration Azure-SSIS si vous n’en avez pas encore en suivant les instructions pas à pas fournies dans le [Tutoriel : Approvisionnement de votre IR Azure-SSIS](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Exécuter un package dans le portail Azure
Dans cette section, vous utilisez l’interface utilisateur ou l’application de Data Factory pour créer un pipeline Data Factory avec une activité Exécuter le Package SSIS qui exécute votre package SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Créer un pipeline avec une activité Exécuter le Package SSIS
Lors de cette étape, vous utilisez l’interface utilisateur ou l’application Data Factory pour créer un pipeline. Vous ajoutez une activité Exécuter le Package SSIS au pipeline et la configurez pour exécuter votre package SSIS. 

1. Sur votre vue d’ensemble Data Factory ou sur la page d’accueil du portail Azure, sélectionnez la vignette **Créer et surveiller** pour démarrer l’application ou l’interface utilisateur Data Factory dans un onglet séparé. 

   ![Page d’accueil Data Factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Dans la page **Prise en main**, cliquez sur **Créer un pipeline**. 

   ![Page de prise en main](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. Dans la boîte à outils **Activités**, étendez **Général**. Ensuitez, faites glisser une activité **Execute SSIS Package** jusqu’à l’aire du concepteur de pipeline. 

   ![Faire glisser une activité Execute SSIS Package jusqu’à l’aire du concepteur](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

1. Sous l’onglet **Général** d’Exécuter le Package SSIS, fournissez un nom et une description pour l’activité. Définissez les valeurs de **Délai d’attente** et de **Nouvelle tentative** facultatives.

   ![Définissez les propriétés sous l’onglet Général](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

1. Dans l’onglet **Paramètres** pour l’activité Exécuter le package SSIS, sélectionnez un Azure-SSIS IR sur lequel exécuter votre package. Si votre package utilise l’authentification Windows pour accéder aux magasins de données (par exemple, les serveurs SQL Server ou les partages de fichiers locaux ou Azure Files), sélectionnez case à cocher **Authentification Windows**. Entrez les valeurs pour vos informations d’identification pour l’exécution du package dans les zones **Domaine**, **Nom d'utilisateur** et **Mot de passe**. 

    Vous pouvez également utiliser les secrets stockés dans votre Azure Key Vault en tant que valeurs. Pour ce faire, sélectionnez la case à cocher **COFFRE DE CLÉS AZURE** en regard des informations d’identification correspondantes. Sélectionnez ou modifiez votre service de coffre de clés lié existant ou créez-en un. Sélectionnez ensuite la version ou le nom du secret pour la valeur de vos informations d’identification.

    Lorsque vous créez ou modifiez votre service de coffre de clés lié, vous pouvez sélectionner ou modifier votre coffre de clés existant ou en créer un nouveau. Veillez à accorder à Data Factory l’accès géré à votre coffre de clés si vous ne l’avez pas déjà fait. Vous pouvez également entrer vos secrets directement au format suivant : `<Key vault linked service name>/<secret name>/<secret version>`. Si votre package a besoin d’un runtime 32 bits pour fonctionner, sélectionnez la case **Runtime 32 bits**.

   Pour **Emplacement du package**, sélectionnez **SSISDB**, **File System (Package)** (Système de fichiers (Package)), **File System (Project)** (Système de fichiers (Projet)), ou **Embedded package** (Package incorporé). Si vous sélectionnez **SSISDB** comme emplacement pour votre package, qui est automatiquement sélectionné si Azure-SSIS IR a été provisionné avec le catalogue SSIS (SSISDB) hébergé par un serveur Azure SQL Database/Managed Instance, spécifiez votre package à exécuter qui a été déployé dans SSISDB. 

    Si votre Azure-SSIS IR est en cours d’exécution et que la case **Entrées manuelles** n’est pas cochée, vous pouvez parcourir et sélectionner vos dossiers, projets, packages ou environnements existants dans SSISDB. Sélectionnez **Actualiser** pour récupérer vos nouveaux dossiers, projets, packages ou environnements ajoutés dans SSISDB afin qu’ils soient sélectionnables lors de la navigation. Pour parcourir ou sélectionner les environnements pour vos exécutions de package, vous devez configurer vos projets au préalable afin d’ajouter ces environnements en tant que références à partir des mêmes dossiers sous SSISDB. Pour plus d’informations, consultez [Créer et mapper des environnements SSIS](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014).

   Concernant **Niveau de journalisation**, sélectionnez un périmètre prédéfini de journalisation pour l’exécution de votre package. Sélectionnez la case **Personnalisé** si vous préférez entrer un nom de journalisation personnalisé. 

   ![Définir les propriétés sous l’onglet Paramètres - Automatique](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Si votre Azure-SSIS IR n’est pas en cours d’exécution ou que la case **Entrées manuelles** est sélectionnée, entrez les chemins de votre package et de votre environnement directement à partir de SSISDB aux formats suivants : `<folder name>/<project name>/<package name>.dtsx` et `<folder name>/<environment name>`.

   ![Définir les propriétés sous l’onglet Paramètres - Manuel](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Si vous sélectionnez **File System (Package)** (Système de fichiers (Package)) comme emplacement de votre package, qui est sélectionné automatiquement si votre Azure-SSIS IR a été provisionné sans SSISDB, spécifiez votre package à exécuter en fournissant un chemin d’accès UNC à votre fichier de package (`.dtsx`) dans la zone **Chemin d’accès du package**. Par exemple, si vous stockez votre package dans Azure Files, le chemin d’accès du package est `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`. 
   
   Si vous configurez votre package dans un fichier distinct, vous devez également fournir un chemin d’accès UNC à votre fichier de configuration (`.dtsConfig`) dans la zone **Chemin d’accès de configuration**. Par exemple, si vous stockez votre configuration dans Azure Files, son chemin d’accès de configuration est `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`.

   ![Définir les propriétés sous l’onglet Paramètres - Manuel](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Si vous sélectionnez **File System (Project)** (Système de fichiers (Projet)) comme emplacement de votre package, spécifiez votre package à exécuter en fournissant un chemin d’accès UNC à votre fichier projet (`.ispac`) dans la zone **Chemin d’accès du projet** et un fichier de package (`.dtsx`) de votre projet dans la zone **Nom du package**. Par exemple, si vous stockez votre projet dans Azure Files, le chemin d’accès du projet est `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`.

   ![Définir les propriétés sous l’onglet Paramètres - Manuel](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Ensuite, spécifiez les informations d’identification pour accéder à votre projet, votre package ou vos fichiers de configuration. Si vous avez précédemment entré les valeurs de vos informations d’identification d’exécution de package (voir plus tôt), vous pouvez les réutiliser en sélectionnant la case **Same as package execution credentials** (Identiques aux informations d’identification pour l’exécution du package). Dans le cas contraire, entrez les valeurs pour vos informations d’identification pour l’accès au package dans les zones **Domaine**, **Nom d'utilisateur** et **Mot de passe**. Par exemple, si vous stockez votre projet, package ou configuration dans Azure Files, le domaine est `Azure` ; le nom d’utilisateur est `<storage account name>` ; et le mot de passe est `<storage account key>`. 

   Vous pouvez également utiliser les secrets stockés dans votre coffre de clés en tant que valeurs (voir ce qui précède). Ces informations d’identification seront utilisées pour accéder à votre package et aux packages enfants dans la tâche Exécuter le package, tous depuis leur propre chemin d’accès/le même projet, ainsi qu’aux configurations, y compris celles spécifiées dans vos packages. 

   Si vous sélectionnez **Embedded package** (Package incorporé) comme emplacement de votre package, glissez-déposez votre package à exécuter ou **chargez-le** depuis un dossier vers la boîte fournie. Votre package sera automatiquement compressé et incorporé à la charge utile de l'activité. Une fois votre package incorporé, vous pouvez le **télécharger** ultérieurement afin de le modifier. Vous pouvez également **paramétrer** votre package incorporé en l'assignant à un paramètre pipeline qui peut être utilisé dans plusieurs activités, optimisant ainsi la taille de votre charge utile de pipeline. Si votre package incorporé n'est pas entièrement chiffré et que nous détectons la commande Tâche d'exécution de package, la case **Tâche d'exécution de package**  sera automatiquement cochée et les packages enfants pertinents avec leurs références système seront automatiquement ajoutés pour que vous puissiez également les incorporer. Si nous ne pouvons pas détecter l'utilisation de la commande Tâche d'exécution de package, vous devrez cocher la case **Tâche d'exécution de package** et ajouter un par un les packages enfants pertinents avec leurs références de système de fichiers afin de les incorporer. Si les packages enfants utilisent des références SQL Server, veuillez vous assurer que le serveur SQL est accessible par votre runtime d’intégration Azure SSIS.  L'utilisation de références de projets pour les packages enfants n'est actuellement pas prise en charge.
   
   ![Définir les propriétés sous l’onglet Paramètres - Manuel](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)
   
   Si vous avez utilisé le niveau de protection **EncryptAllWithPassword** ou **EncryptSensitiveWithPassword** lorsque vous avez créé votre package via SQL Server Data Tools, entrez la valeur de votre mot de passe dans la zone **Mot de passe de chiffrement**. Vous pouvez également utiliser un secret stocké dans votre coffre de clés en tant que valeur (voir précédemment). Si vous avez utilisé le niveau de protection **EncryptSensitiveWithUserKey**, entrez de nouveau vos valeurs sensibles dans les fichiers config ou dans les onglets **Paramètres SSIS**, **Gestionnaires de connexions** ou **Substitutions de propriété** (voir plus loin). 

   Si vous avez utilisé le niveau de protection **EncryptAllWithUserKey** , cela n’est pas pris en charge. Vous devez reconfigurer votre package pour utiliser un autre niveau de protection via SQL Server Data Tools ou l’utilitaire de ligne de commande `dtutil`. 
   
   Concernant **Niveau de journalisation**, sélectionnez un périmètre prédéfini de journalisation pour l’exécution de votre package. Sélectionnez la case **Personnalisé** si vous préférez entrer un nom de journalisation personnalisé. Si vous souhaitez journaliser vos exécutions de package au-delà à l’aide de modules fournisseurs d’informations, spécifiez votre dossier de journaux en fournissant son chemin d’accès UNC dans la zone **Chemin d’accès de journalisation**. Par exemple, si vous stockez vos journaux dans Azure Files, votre chemin d’accès de journalisation est `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. Un sous-dossier est créé dans ce chemin d’accès pour chaque exécution de package individuel et nommé d’après l’ID d’exécution de l’activité Exécuter le package SSIS, dans lequel les fichiers journaux sont générés toutes les cinq minutes. 
   
   Enfin, spécifiez les informations d’identification pour accéder à votre dossier de journal. Si vous avez précédemment entré les valeurs de vos informations d’identification d’accès de package (voir plus tôt), vous pouvez les réutiliser en sélectionnant la case **Same as package execution credentials** (Identiques aux informations d’identification pour l’accès au package). Dans le cas contraire, entrez les valeurs pour vos informations d’identification pour l’accès aux journaux dans les zones **Domaine**, **Nom d'utilisateur** et **Mot de passe**. Par exemple, si vous stockez vos journaux dans Azure Files, le domaine est `Azure` ; le nom d’utilisateur est `<storage account name>` ; et le mot de passe est `<storage account key>`. 

    Vous pouvez également utiliser les secrets stockés dans votre coffre de clés en tant que valeurs (voir ce qui précède). Ces informations d’identification sont utilisées pour stocker vos journaux. 
   
   Pour tous les chemins d’accès UNC mentionnés précédemment, le nom de fichier complet doit être inférieur à 260 caractères. Le nom de répertoire ne doit pas dépasser 248 caractères.

1. Sous l’onglet **Paramètres SSIS** de l’activité Exécuter le package SSIS, si votre Azure-SSIS IR s’exécute, **SSISDB** est sélectionné en tant qu’emplacement du package et si la case **Entrées manuelles** de l’onglet **Paramètres** est décochée, les paramètres SSIS existants dans votre projet ou package sélectionné depuis SSISDB sont affichés pour que vous puissiez leur assigner des valeurs. Sinon, vous pouvez affecter manuellement des valeurs une par une. Assurez-vous que les valeurs existent et qu’elles sont correctement entrées pour que l’exécution du package réussisse. 
   
   Si vous avez utilisé le niveau de protection **EncryptSensitiveWithUserKey** lorsque vous avez créé votre package via SSDT et que **File System (Package) (Système de fichiers (Package))** ou **File System (Project) (Système de fichiers (Projet))** est sélectionné en tant qu’emplacement de votre package, vous devez également entrer de nouveau vos paramètres sensibles pour y attribuer des valeurs dans les fichiers config ou dans cet onglet. 
   
   Lorsque vous attribuez des valeurs à vos paramètres, vous pouvez ajouter du contenu dynamique à l’aide d’expressions, de fonctions, de variables système Data Factory et de paramètres ou variables de pipeline Data Factory. Vous pouvez également utiliser les secrets stockés dans votre coffre de clés en tant que valeurs (voir ce qui précède).

   ![Définir les propriétés sous l’onglet Paramètres SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

1. Dans l’onglet **Gestionnaires de connexions** de l’activité Exécuter un package SSIS, si votre Azure-SSIS IR s’exécute, si **SSISDB** est sélectionné en tant qu’emplacement de votre package et si la case **Entrées manuelles** de l’onglet **Paramètres** est décochée, les gestionnaires de connexions existants dans votre projet ou package sélectionné depuis SSISDB sont affichés pour que vous puissiez attribuer des valeurs à leurs propriétés. Sinon, vous pouvez affecter manuellement des valeurs aux propriétés une par une. Assurez-vous que les valeurs existent et qu’elles sont correctement entrées pour que l’exécution du package réussisse. 
   
   Si vous avez utilisé le niveau de protection **EncryptSensitiveWithUserKey** lorsque vous avez créé votre package via SQL Server Data Tools et que **File System (Package)** (Système de fichiers (Package)) ou **File System (Project)** (Système de fichiers (Projet)) est sélectionné en tant qu’emplacement de votre package, vous devez également entrer de nouveau vos propriétés de gestionnaire de connexions sensibles pour y attribuer des valeurs dans les fichiers config ou dans cet onglet. 
   
   Lorsque vous attribuez des valeurs à vos propriétés de gestionnaire de connexions, vous pouvez ajouter du contenu dynamique à l’aide d’expressions, de fonctions, de variables système Data Factory et de paramètres ou variables de pipeline Data Factory. Vous pouvez également utiliser les secrets stockés dans votre coffre de clés en tant que valeurs (voir ce qui précède).

   ![Définir les propriétés sous l’onglet Gestionnaires de connexions](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

1. Sous l’onglet **Substitutions de propriété** de l’activité Exécuter le package SSIS, entrez les chemins d’accès des propriétés existantes dans le package sélectionné un par un pour leur affecter manuellement des valeurs. Assurez-vous que les valeurs existent et qu’elles sont correctement entrées pour que l’exécution du package réussisse. Par exemple, pour remplacer la valeur de votre variable utilisateur, entrez son chemin d’accès au format suivant : `\Package.Variables[User::<variable name>].Value`. 
   
   Si vous avez utilisé le niveau de protection **EncryptSensitiveWithUserKey** lorsque vous avez créé votre package via SSDT et que **File System (Package)(Système de fichiers (Package))** ou **File System (Project)** (Système de fichiers (Projet)) est sélectionné en tant qu’emplacement de votre package, vous devez également entrer de nouveau vos propriétés sensibles pour y attribuer des valeurs dans les fichiers config ou dans cet onglet. 
   
   Lorsque vous attribuez des valeurs à vos propriétés, vous pouvez ajouter du contenu dynamique à l’aide d’expressions, de fonctions, de variables système Data Factory et de paramètres ou variables de pipeline Data Factory.

   ![Définir les propriétés sous l’onglet Substitutions de propriété](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   Les valeurs attribuées dans les fichiers de configuration et dans l’onglet **Paramètres SSIS** peuvent être remplacées à l’aide des onglets **Gestionnaires de connexions** et **Substitutions de propriété**. Vous pouvez également remplacer les valeurs affectées dans l’onglet **Gestionnaires de connexions** dans l’onglet **Substitutions de propriété**.

1. Pour valider la configuration du pipeline, sélectionnez **Valider** dans la barre d’outils. Pour fermer le **Rapport de validation de pipeline**, sélectionnez **>>** .

1. Pour publier le pipeline sur Data Factory, sélectionnez **Publier tout**. 

### <a name="run-the-pipeline"></a>Exécuter le pipeline
À cette étape, vous déclenchez une exécution du pipeline. 

1. Pour déclencher une exécution de pipeline, sélectionnez **Déclencher** dans la barre d’outils, puis **Déclencher maintenant**. 

   ![Déclencher maintenant](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. Dans la fenêtre **Exécution du pipeline**, sélectionnez **Terminer**. 

### <a name="monitor-the-pipeline"></a>Surveiller le pipeline

1. Basculez vers l’onglet **Surveiller** sur la gauche. Vous voyez l’exécution de pipeline et son état, ainsi que d’autres informations (telles que l’heure de **Début d’exécution**). Sélectionnez **Actualiser** pour actualiser l’affichage.

   ![Exécutions de pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Cliquez sur le lien **Afficher les exécutions d’activités** dans la colonne **Actions**. Une seule exécution d’activité est affichée, étant donné que le pipeline n’a qu’une seule activité. Il s’agit de l’activité Exécuter le package SSIS.

   ![Exécutions d’activités](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Exécutez la requête suivante par rapport à la base de données SSISDB dans votre serveur SQL pour vérifier que le package s’est exécuté. 

   ```sql
   select * from catalog.executions
   ```

   ![Vérifier les exécutions de package](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Vous pouvez également obtenir l’ID de l’exécution SSISDB à partir de la sortie de l’exécution de l’activité de pipeline, et utiliser l’ID pour consulter des journaux d’activité d’exécution et des messages d’erreur plus complets dans SQL Server Management Studio.

   ![Obtenez l’ID de l'exécution.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Planifier le pipeline avec un déclencheur

Vous pouvez également créer un déclencheur planifié pour votre pipeline afin que le pipeline s’exécute selon une planification, par exemple horaire ou quotidienne. Pour obtenir un exemple, consultez [Créer une fabrique de données - Interface utilisateur de Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Exécuter un package avec PowerShell
Dans cette section, vous utilisez Azure PowerShell pour créer un pipeline Data Factory avec une activité Execute SSIS Package qui exécute votre package SSIS. 

Installez les modules Azure PowerShell les plus récents en suivant les instructions pas à pas décrites dans [Comment installer et configurer Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Créer une fabrique de données avec Azure-SSIS IR
Vous pouvez utiliser une fabrique de données existante avec Azure-SSIS IR configuré ou créer une nouvelle fabrique de données avec Azure-SSIS IR. Suivez les instructions pas à pas du [Tutoriel : Déployer des packages SSIS sur Azure via Powershell](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Créer un pipeline avec une activité Exécuter le Package SSIS 
Au cours de cette étape, vous créez un pipeline avec une activité Execute SSIS Package. L’activité exécute votre package SSIS. 

1. Créez un fichier JSON nommé *RunSSISPackagePipeline.json* dans le dossier *C:\ADF\RunSSISPackage* avec un contenu similaire à l’exemple suivant.

   > [!IMPORTANT]
   > Remplacez les noms d’objets, les descriptions et les chemins ou que les valeurs de propriété et de paramètre, les mots de passe et autres valeurs de variable avant d’enregistrer le fichier. 
    
   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx",
                       "type": "SSISDB"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

   Pour exécuter des packages stockés dans des systèmes de fichiers, partages de fichiers ou Azure Files, entrez les valeurs des propriétés d’emplacement de votre package et journal comme suit :

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "type": "File",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                           },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Pour exécuter des packages dans des projets stockés dans des systèmes de fichiers, partages de fichiers ou Azure Files, vous pouvez entrer les valeurs de la propriété d’emplacement de votre package comme suit :

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Pour exécuter des packages incorporés, entrez les valeurs de propriété d'emplacement de votre package comme suit :

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

2. Dans Azure PowerShell, basculez vers le dossier *C:\ADF\RunSSISPackage*.

3. Pour créer le pipeline **RunSSISPackagePipeline**, exécutez la cmdlet **Set-AzDataFactoryV2Pipeline**.

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Voici l'exemple de sortie :

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>Exécuter le pipeline
Utilisez la cmdlet **Invoke-AzDataFactoryV2Pipeline** pour exécuter le pipeline. L’applet de commande renvoie l’ID d’exécution du pipeline pour permettre une surveillance ultérieure.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Surveiller le pipeline

Exécutez le script PowerShell suivant afin de vérifier continuellement l’état de l’exécution du pipeline jusqu’à la fin de la copie des données. Copiez ou collez le script suivant dans la fenêtre PowerShell et sélectionnez Entrée. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Vous pouvez également surveiller le pipeline à l’aide du portail Azure. Pour obtenir des instructions pas à pas, consultez [Surveiller le pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Planifier le pipeline avec un déclencheur
À l’étape précédente, vous avez exécuté le pipeline à la demande. Vous pouvez également créer un déclencheur de planification pour exécuter le pipeline d’après une planification, par exemple horaire ou quotidienne.

1. Créez un fichier JSON nommé *MyTrigger.json* dans le dossier *C:\ADF\RunSSISPackage* avec le contenu suivant : 
        
   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
    
1. Dans Azure PowerShell, basculez vers le dossier *C:\ADF\RunSSISPackage*.
1. Exécutez la cmdlet **Set-AzDataFactoryV2Trigger** pour créer le déclencheur. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. Par défaut, le déclencheur est arrêté. Démarrez le déclencheur en exécutant la cmdlet **Start-AzDataFactoryV2Trigger**. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Vérifiez que le déclencheur est démarré en exécutant la cmdlet **Get-AzDataFactoryV2Trigger**. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Exécutez la commande ci-dessous après l’heure suivante. Par exemple, si l’heure actuelle est 15h25 UTC, exécutez la commande à 16h00 UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Exécutez la requête suivante par rapport à la base de données SSISDB dans votre serveur SQL pour vérifier que le package s’est exécuté. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Étapes suivantes
Consultez le billet de blog suivant :
- [Moderniser et étendre vos flux de travail ETL/ELT avec des activités SSIS dans des pipelines Azure Data Factory](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
