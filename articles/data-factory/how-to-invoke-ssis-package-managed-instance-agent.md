---
title: Exécuter des packages SSIS avec l’agent Azure SQL Managed Instance
description: Découvrez comment exécuter des packages SSIS avec l’agent Azure SQL Managed Instance.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: b3b7a25149a9d075c81b30307ade2beb71907637
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394059"
---
# <a name="execute-ssis-packages-by-azure-sql-managed-instance-agent"></a>Exécuter des packages SSIS avec l’agent Azure SQL Managed Instance
Cet article décrit comment exécuter un package SQL Server Integration Services (SSIS) à l'aide de l'agent Azure SQL Managed Instance. Cette fonctionnalité fournit des comportements similaires comme lorsque vous planifiez des packages SSIS par SQL Server Agent dans votre environnement local.

Avec cette fonctionnalité, vous pouvez exécuter des packages SSIS stockés dans SSISDB dans Azure SQL Managed Instance ou un système de fichiers comme Azure Files.

## <a name="prerequisites"></a>Prérequis
Pour utiliser cette fonctionnalité, téléchargez et installez la dernière version de SSMS, à savoir la version 18.5 ou une version ultérieure. Téléchargez-la à partir de [ce site web](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

Vous devez approvisionner une instance Azure-SSIS Integration Runtime dans Azure Data Factory, qui utilise Azure SQL Managed Instance en tant que serveur de point de terminaison. Si vous ne l'avez pas déjà provisionné, faites-le en suivant les instructions fournies dans ce [tutoriel](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="run-ssis-packages-in-ssisdb-by-azure-sql-managed-instance-agent"></a>Exécuter des packages SSIS dans SSISDB avec l’agent Azure SQL Managed Instance
Au cours de cette étape, vous utilisez l'agent Azure SQL Managed Instance pour appeler des packages SSIS stockés dans SSISDB dans Azure SQL Managed Instance.
1. Dans la dernière version de SSMS, connectez-vous à Azure SQL Managed Instance.
2. Créez un travail d'agent et une étape de travail.

![Nouveau travail d'agent](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. Dans la page **Nouvelle étape de travail**, sélectionnez le type **Package SQL Server Integration Services**.

![Nouvelle étape de travail SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. Sous l’onglet **Package**, sélectionnez **Catalogue SSIS** comme type de source du package.
5. SSISDB se trouvant dans la même instance Azure SQL Managed Instance, vous n'êtes pas tenu de spécifier l’authentification.
6. Spécifiez un package SSIS à partir de votre SSISDB.

![Type de source du package - Catalogue SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

7. Sous l’onglet **Configurations**, vous pouvez spécifier les valeurs de **paramètre**, remplacer des valeurs dans **Gestionnaires de connexions**, remplacer la **propriété** et sélectionner le **niveau de journalisation**.

![Type de source du package - Configuration du catalogue SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

8. Une fois les configurations ci-dessus terminées, cliquez sur **OK** pour enregistrer la configuration du travail de l’agent.
9. Démarrez le travail de l’agent pour exécuter le package SSIS.


## <a name="run-ssis-packages-in-file-system-by-azure-sql-managed-instance-agent"></a>Exécuter des packages SSIS dans le système de fichiers avec l’agent Azure SQL Managed Instance
Au cours de cette étape, vous utilisez l'agent Azure SQL Managed Instance pour appeler des packages SSIS stockés dans le système de fichiers à exécuter.
1. Dans la dernière version de SSMS, connectez-vous à Azure SQL Managed Instance.
2. Créez un travail d'agent et une étape de travail.

   ![Nouveau travail d'agent](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. Dans la page **Nouvelle étape de travail**, sélectionnez le type **Package SQL Server Integration Services**.

   ![Nouvelle étape de travail SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. Sous l’onglet **Package**, sélectionnez **Système de fichiers** comme type de source du package.

   ![Type de source du package - Système de fichiers](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)

   1. Si votre package est chargé dans le fichier Azure, sélectionnez **Partage de fichiers Azure** comme type de source du fichier.
      - Le chemin d’accès au package est **\\<storage account name>. file.core.windows.net\<nom du partage de fichiers>\<nom du package.dtsx**
      - Entrez le nom du compte de fichier Azure et la clé du compte dans **Informations d’identification d’accès au fichier du package** pour accéder au fichier Azure. Le domaine est défini sur **Azure**.
   2. Si votre package est chargé sur un partage réseau, sélectionnez **Partage réseau** comme type de source du fichier.
      - Le chemin d’accès au package correspond au **chemin d'accès UNC** de votre fichier de package avec son extension dtsx.
      - Entrez le **domaine**, le **nom d’utilisateur** et le **mot de passe** correspondants pour accéder au fichier de package de partage réseau.
   3. Si votre fichier de package est chiffré avec un mot de passe, sélectionnez **Mot de passe de chiffrement** et entrez le mot de passe.

 5. Sous l’onglet **Configurations**, entrez le **chemin d'accès au fichier de configuration** s'il vous faut un fichier de configuration pour exécuter le package SSIS.
 6. Sous l’onglet **Options d'exécution**, sélectionnez **Authentification Windows** ou **Runtime 32 bits** pour exécuter le package SSIS.
 7. Sous l’onglet **Journalisation**, sélectionnez le **chemin de journalisation** et les informations d’identification d’accès à la journalisation correspondantes pour stocker les fichiers journaux. Par défaut, le chemin de journalisation est identique au chemin d’accès du dossier du package, et les informations d’identification d’accès à la journalisation sont identiques à celles du package.
 8. Sous l’onglet **Valeurs définies**, vous pouvez entrer le **chemin d’accès de la propriété** et la **valeur** pour remplacer les propriétés du package.
 Par exemple, pour remplacer la valeur de votre variable utilisateur, entrez son chemin d’accès au format suivant : **\Package.Variables[User::<variable name>].Value**.
 9. Une fois les configurations ci-dessus terminées, cliquez sur **OK** pour enregistrer la configuration du travail de l’agent.
 10. Démarrez le travail de l’agent pour exécuter le package SSIS.


 ## <a name="cancel-ssis-package-execution"></a>Annuler une exécution de package SSIS
 Pour annuler l’exécution d’un package à partir d’un travail de l’agent Azure SQL Managed Instance, suivez les étapes ci-dessous plutôt que d’arrêter directement le travail de l’agent.
 1. Recherchez la valeur **jobId** de votre agent SQL à partir de **msdb.dbo.sysjobs**.
 2. Recherchez la valeur **executionId** SSIS correspondante en fonction de l'ID de travail à l'aide de la requête ci-dessous :
    ```sql
    select * from ssisdb.internal.execution_parameter_values_noncatalog where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
    ```
 3. Sélectionnez **Opérations actives** sous le catalogue SSIS.

    ![Type de source du package - Système de fichiers](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

 4. Arrêtez l’opération correspondante en fonction de la valeur **executionId**.

## <a name="next-steps"></a>Étapes suivantes
 Vous pouvez également planifier des packages SSIS à l’aide d'Azure Data Factory. Pour obtenir des instructions pas à pas, consultez [Déclencheur d'événements Azure Data Factory](how-to-create-event-trigger.md). 