---
title: Configurer un runtime d’intégration auto-hébergé en tant que proxy pour SSIS
description: Découvrez comment configurer un runtime d’intégration auto-hébergé en tant que proxy pour Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 02/06/2020
ms.openlocfilehash: 5f9e15b83c36c6c19fbe93c5f1df365f6f763c81
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187681"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Configurer un IR auto-hébergé en tant que proxy pour Azure-SSIS IR dans Azure Data Factory

Cet article explique comment exécuter des packages SQL Server Integration Services (SSIS) sur Azure-SSIS Integration Runtime (Azure-SSIS IR) dans Azure Data Factory avec un runtime d’intégration auto-hébergé (IR auto-hébergé) configuré en tant que proxy. 

Avec cette fonctionnalité, vous pouvez accéder aux données localement sans avoir à [joindre votre instance d’Azure-SSIS IR à un réseau virtuel](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Cette fonctionnalité est utile quand votre réseau d’entreprise a une configuration trop complexe ou une stratégie trop restrictive pour pouvoir y injecter votre instance Azure-SSIS IR.

Elle divise les packages qui contiennent une tâche de flux de données avec une source de données locale en deux tâches intermédiaires : 
* La première tâche, qui s’exécute sur votre IR auto-hébergé, déplace d’abord les données de la source de données locale vers une zone intermédiaire dans votre stockage d’objets blob Azure.
* La deuxième tâche, qui s’exécute sur votre instance Azure-SSIS IR, déplace les données de la zone intermédiaire vers la destination de données prévue.

Cette fonctionnalité vous permet aussi, entre autres, de configurer votre IR auto-hébergé dans des régions qui ne sont pas encore prises en charge par Azure-SSIS IR, et d’autoriser l’adresse IP statique publique de votre IR auto-hébergé sur le pare-feu de vos sources de données.

## <a name="prepare-the-self-hosted-ir"></a>Préparer l’IR auto-hébergé

Pour utiliser cette fonctionnalité, vous devez d’abord créer une fabrique de données et y configurer une instance d’Azure-SSIS IR. Si ce n’est déjà fait, suivez les instructions fournies dans [Configurer un runtime d’intégration Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure).

Vous configurez ensuite votre IR auto-hébergé dans la fabrique de données où votre instance Azure-SSIS IR est configurée. Pour ce faire, consultez [Créer un IR auto-hébergé](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).

Pour finir, vous téléchargez et installez la dernière version de l’IR auto-hébergé, ainsi que les pilotes et runtime supplémentaires, sur votre ordinateur local ou machine virtuelle Azure en effectuant les étapes suivantes :
- Téléchargez et installez la dernière version de l’[IR auto-hébergé](https://www.microsoft.com/download/details.aspx?id=39717).
- Si vous utilisez des connecteurs OLEDB (Object Linking and Embedding Database) dans vos packages, téléchargez et installez les pilotes OLEDB appropriés sur l’ordinateur où votre IR auto-hébergé est installé, si ce n’est pas déjà fait.  

  Si vous utilisez la version antérieure du pilote OLEDB pour SQL Server (SQL Server Native Client [SQLNCLI]), [téléchargez la version 64 bits](https://www.microsoft.com/download/details.aspx?id=50402).  

  Si vous utilisez la dernière version du pilote OLEDB pour SQL Server (MSOLEDBSQL), [téléchargez la version 64 bits](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  Si vous utilisez des pilotes OLEDB pour d’autres systèmes de base de données, comme PostgreSQL, MySQL, Oracle, et ainsi de suite, vous pouvez télécharger les versions 64 bits à partir de leurs sites web.
- Si ce n’est pas déjà fait, [téléchargez et installez la version 64 bits du runtime Visual C++ (VC)](https://www.microsoft.com/download/details.aspx?id=40784) sur l’ordinateur où votre IR auto-hébergé est installé.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Préparer le service lié de stockage d’objets blob Azure pour la préproduction

Si ce n’est pas déjà fait, créez un service lié de stockage d’objets blob Azure dans la fabrique de données où votre instance d’Azure-SSIS IR est configurée. Pour ce faire, consultez [Créer un service lié Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service). Veillez à effectuer les opérations suivantes :
- Pour **Magasin de données**, sélectionnez **Stockage Blob Azure**.  
- Pour **Se connecter via le runtime d’intégration**, sélectionnez **AutoResolveIntegrationRuntime**.  
- Pour **Méthode d’authentification**, sélectionnez **Clé de compte**, **URI SAS** ou **Principal du service**.  

    >[!TIP]
    >Si vous sélectionnez  **Principal du service**, accordez au moins le rôle *Contributeur aux données Blob du stockage*. Pour plus d’informations, consultez  [Connecteur Stockage Blob Azure](connector-azure-blob-storage.md#linked-service-properties).

![Préparer le service lié de stockage d’objets blob Azure pour la préproduction](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Configurer une instance Azure-SSIS IR avec votre IR auto-hébergé en tant que proxy

Une fois votre IR auto-hébergé et votre service lié de stockage d’objets blob Azure prêts pour la préproduction, vous pouvez configurer votre instance Azure-SSIS IR nouvelle ou existante avec l’IR auto-hébergé en tant que proxy dans votre application ou portail de fabrique de données. Avant cela, cependant, si votre instance Azure-SSIS IR existante est déjà en cours d’exécution, arrêtez-la, puis redémarrez-la.

1. Dans le volet **Installation d’Integration Runtime**, ignorez les sections **Paramètres généraux** et **Paramètres SQL** en sélectionnant **Suivant**. 

1. Dans la boîte de dialogue **Paramètres avancés**, effectuez les étapes suivantes :

   1. Cochez la case **Configurer un runtime d’intégration auto-hébergé en tant que proxy pour votre Azure-SSIS Integration Runtime**. 

   1. Dans la liste déroulante **Runtime d’intégration auto-hébergé**, sélectionnez votre IR auto-hébergé existant en tant que proxy pour l’instance Azure-SSIS IR.

   1. Dans la liste déroulante **Service lié de stockage de préproduction**, sélectionnez votre service lié de stockage d’objets blob Azure existant ou créez-en un pour la préproduction.

   1. Dans la zone **Chemin d’accès intermédiaire**, spécifiez un conteneur d’objets blob dans le compte de stockage d’objets blob Azure sélectionné, ou laissez ce champ vide afin d’utiliser un conteneur par défaut pour la préproduction.

   1. Sélectionnez **Continuer**.

   ![Paramètres avancés avec un IR auto-hébergé](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

Vous pouvez également configurer votre instance Azure-SSIS IR, qu’elle soit nouvelle ou existante, avec l’IR auto-hébergé en tant que proxy à l’aide de PowerShell.

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Activer les packages SSIS pour la connexion par proxy

En utilisant la version la plus récente de l’extension SSDT with SSIS Projects pour Visual Studio ou un programme d’installation autonome, vous trouverez une nouvelle propriété `ConnectByProxy` qui a été ajoutée dans les gestionnaires de connexions de fichiers plats ou OLEDB.
* [Télécharger l’extension SSDT with SSIS Projects pour Visual Studio](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Télécharger le programme d’installation autonome](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

Lors de la conception de nouveaux packages qui contiennent des tâches de flux de données avec sources de fichiers plats ou OLEDB, qui vous permettent d’accéder à des bases de données ou des fichiers locaux, vous pouvez activer cette propriété en lui affectant la valeur *True* dans le volet **Propriétés** des gestionnaires de connexions appropriés.

![Activer la propriété ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Vous pouvez également activer cette propriété quand vous exécutez des packages existants, sans qu’il soit nécessaire de les modifier manuellement un par un.  Nous avons deux options :
- **Option A** : Ouvrir, regénérer et redéployer le projet contenant ces packages avec la dernière version de SSDT pour une exécution sur votre instance Azure-SSIS IR. Vous pouvez ensuite activer la propriété en lui affectant la valeur *true* pour les gestionnaires de connexions appropriés. Quand ils exécutent des packages à partir de SSMS, ces gestionnaires de connexions apparaissent sous l’onglet **Gestionnaires de connexions** de la fenêtre contextuelle **Exécuter le package**.

  ![Activer la propriété ConnectByProxy 2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Vous pouvez aussi activer la propriété en lui affectant la valeur *True* pour les gestionnaires de connexions appropriés qui apparaissent sous l’onglet **Gestionnaires de connexions** de l’activité [Exécuter le package SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) quand ils exécutent des packages dans des pipelines Data Factory.
  
  ![Activer la propriété ConnectByProxy 3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Option B** : Redéployer le projet contenant ces packages pour une exécution sur votre instance de SSIS IR. Vous pouvez ensuite activer la propriété en spécifiant son chemin de propriété, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`, et en lui affectant la valeur *True* comme substitution de propriété sous l’onglet **Avancé** de la fenêtre contextuelle **Exécuter le package** quand vous exécutez des packages à partir de SSMS.

  ![Activer la propriété ConnectByProxy 4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Vous pouvez aussi activer la propriété en spécifiant son chemin de propriété, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`, et en lui affectant la valeur *True* comme substitution de propriété sous l’onglet **Substitutions de propriété** de l’activité [Exécuter le package SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) quand vous exécutez des packages dans des pipelines Data Factory.
  
  ![Activer la propriété ConnectByProxy 5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Déboguer les première et deuxième tâches intermédiaires

Sur votre IR auto-hébergé, vous trouverez les journaux du runtime dans le dossier *C:\ProgramData\SSISTelemetry* et les journaux d’exécution des premières tâches intermédiaires dans le dossier *C:\ProgramData\SSISTelemetry\ExecutionLog*.  Vous trouverez les journaux d’exécution des deuxièmes tâches intermédiaires dans votre catalogue SSISDB ou aux chemins de journalisation spécifiés, selon que vous stockez vos packages dans SSISDB ou dans le système de fichiers, des partages de fichiers ou Azure Files. Vous trouverez également les ID uniques des premières tâches intermédiaires dans les journaux d’exécution des deuxièmes tâches intermédiaires. 

![ID unique de la première tâche intermédiaire](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>Utiliser l’authentification Windows dans les tâches intermédiaires

Si vos tâches intermédiaires sur votre IR auto-hébergé nécessitent l’authentification Windows, [configurez vos packages SSIS pour qu’ils utilisent la même authentification Windows](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). 

Vos tâches intermédiaires seront appelées avec le compte de service de l’IR auto-hébergé (*NT SERVICE\DIAHostService* par défaut), et vos magasins de données seront accessibles avec le compte d’authentification Windows. Les deux comptes nécessitent que certaines stratégies de sécurité leur soient attribuées. Sur l’ordinateur IR auto-hébergé, accédez à **Stratégie de sécurité locale** > **Stratégies locales** > **Attribution des droits utilisateur**, puis effectuez les étapes suivantes :

1. Attribuez les stratégies *Ajuster les quotas de mémoire pour un processus* et *Remplacer un jeton de niveau processus* au compte de service de l’IR auto-hébergé. Ceci doit se produire automatiquement quand vous installez votre IR auto-hébergé avec le compte de service par défaut. Si vous utilisez un autre compte de service, attribuez-lui les mêmes stratégies.

1. Attribuez la stratégie *Se connecter en tant que service* au compte d’authentification Windows.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Facturation des première et deuxième tâches intermédiaires

Les premières tâches intermédiaires qui s’exécutent sur votre IR auto-hébergé sont facturées séparément, tout comme les activités de déplacement de données qui s’exécutent sur un IR auto-hébergé. Cela est indiqué dans l’article [Tarification Pipeline de données Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/).

Les deuxièmes tâches intermédiaires qui s’exécutent sur votre instance d’Azure-SSIS IR ne sont pas facturées séparément, mais votre instance d’Azure-SSIS IR en cours d’exécution est facturée comme décrit dans l’article [Tarification d’Azure-SSIS IR](https://azure.microsoft.com/pricing/details/data-factory/ssis/).

## <a name="current-limitations"></a>Limites actuelles

- Seules les tâches de flux de données avec les gestionnaires de connexions de fichiers plats, OLEDB ou ODBC (Open Database Connectivity) et les sources de fichiers plats, OLEDB ou ODBC, ou destination OLEDB, sont actuellement prises en charge. 
- Seuls les services liés de stockage d’objets blob Azure configurés avec une authentification de type *Clé de compte*, *URI SAS (Shared Access Signature)* ou *Principal du service* sont actuellement pris en charge.

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré votre IR auto-hébergé en tant que proxy pour votre instance Azure-SSIS IR, vous pouvez déployer et exécuter vos packages pour accéder aux données locales sous forme d’activités Exécuter le package SSIS dans des pipelines ADF. Pour découvrir comment procéder, consultez [Exécuter un package SSIS avec l’activité Exécuter le Package SSIS dans Azure Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
