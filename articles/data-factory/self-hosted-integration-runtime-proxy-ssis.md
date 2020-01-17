---
title: Configurer un runtime d’intégration auto-hébergé en tant que proxy pour SSIS
description: Apprenez à configurer un runtime d'intégration auto-hébergé en tant que proxy pour Azure-SSIS Integration Runtime.
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
ms.date: 12/23/2019
ms.openlocfilehash: 8754b2ef9727b5526cc9bcf756085f2f05f6a398
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552098"
---
# <a name="configure-self-hosted-ir-as-a-proxy-for-azure-ssis-ir-in-adf"></a>Configurer un runtime d'intégration auto-hébergé en tant que proxy pour Azure-SSIS IR dans ADF

Cet article explique comment exécuter des packages SQL Server Integration Services (SSIS) sur Azure-SSIS Integration Runtime (IR) dans Azure Data Factory (ADF) avec un runtime d'intégration auto-hébergé configuré en tant que proxy.  Cette fonctionnalité vous permet d'accéder aux données locales sans avoir à [joindre votre instance d'Azure-SSIS IR à un réseau virtuel](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).  Ceci est particulièrement utile lorsque la stratégie de configuration/stratégie restrictive de votre réseau d’entreprise est trop complexe pour y injecter votre instance d’Azure-SSIS IR.

Cette fonctionnalité divisera un package contenant une tâche de flux de données avec source de données locale en deux tâches intermédiaires : la première exécutée sur votre runtime d'intégration auto-hébergé transférera d'abord les données de la source locale vers une zone de transit de votre Stockage Blob Azure, tandis que la seconde exécutée sur votre instance d'Azure-SSIS IR transférera ensuite les données de la zone de transit vers la destination prévue.

Cette fonctionnalité présente également d'autres avantages puisqu'elle vous permet d'approvisionner votre runtime d'intégration auto-hébergé dans les régions qui ne sont pas encore prises en charge par Azure-SSIS IR, d'autoriser l'adresse IP statique publique de votre runtime d'intégration auto-hébergé sur le pare-feu de vos sources de données, etc.

## <a name="prepare-self-hosted-ir"></a>Préparer le runtime d'intégration auto-hébergé

Pour utiliser cette fonctionnalité, vous devrez d'abord créer une fabrique de données ADF et, si ce n'est déjà fait, approvisionner votre instance d'Azure-SSIS IR sous celle-ci en suivant les instructions de l'article [Approvisionner une instance d'Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure).

Vous devrez ensuite approvisionner votre runtime d'intégration auto-hébergé sous la fabrique de données ADF où votre instance d'Azure-SSIS IR est elle-même approvisionnée en suivant les instructions de l'article [Créer un runtime d'intégration auto-hébergé](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).

Enfin, vous devrez télécharger et installer la dernière version du runtime d'intégration auto-hébergé, ainsi que les pilotes et runtime supplémentaires, sur votre ordinateur local/machine virtuelle Azure en procédant comme suit :
- Téléchargez et installez la dernière version de l’IR auto-hébergé en cliquant [ici](https://www.microsoft.com/download/details.aspx?id=39717).
- Si vous utilisez des connecteurs OLEDB dans vos packages, téléchargez et installez les pilotes OLEDB appropriés sur l’ordinateur où l’IR auto-hébergé est installé (si ce n’est pas déjà fait).  Si vous utilisez une version antérieure du pilote OLEDB pour SQL Server (SQLNCLI), vous pouvez télécharger la version 64 bits en cliquant [ici](https://www.microsoft.com/download/details.aspx?id=50402).  Si vous utilisez la dernière version du pilote OLEDB pour SQL Server (MSOLEDBSQL), vous pouvez télécharger la version 64 bits en cliquant [ici](https://www.microsoft.com/download/details.aspx?id=56730).  Si vous utilisez des pilotes OLEDB pour d'autres systèmes de base de données, comme PostgreSQL, MySQL, Oracle, etc., vous pouvez télécharger la version 64 bits sur leurs sites web respectifs.
- Si ce n’est pas déjà fait, téléchargez et installez le runtime Visual C++ (VC) sur l’ordinateur où l’IR auto-hébergé est installé.  Vous pouvez télécharger la version 64 bits en cliquant [ici](https://www.microsoft.com/download/details.aspx?id=40784).

## <a name="prepare-azure-blob-storage-linked-service-for-staging"></a>Préparer le service lié Stockage Blob Azure pour le transit

Si ce n’est pas déjà fait, créez un service lié Stockage Blob Azure sous la fabrique de données ADF où votre instance d’Azure-SSIS IR est approvisionnée en suivant les instructions de l’article [Créer un service lié ADF](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service).  Vérifiez ce qui suit :
- **Stockage Blob Azure** est sélectionné sous **Magasin de données**
- **AutoResolveIntegrationRuntime** est sélectionné sous **Se connecter via le runtime d'intégration**
- **Clé de compte,** /**URI SAS** /**ou Principal du service** est sélectionné sous **Méthode d'authentification**

>[!TIP]
>Lorsque  **Principal du service**  est sélectionné, accordez au Moins le  **rôle de contributeur aux données blob de stockage**. Pour plus d’informations, reportez-vous à  [Connecteur Stockage Blob Azure](connector-azure-blob-storage.md#linked-service-properties).

![Préparer le service lié Stockage Blob Azure pour le transit](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-azure-ssis-ir-with-self-hosted-ir-as-a-proxy"></a>Configurer Azure-SSIS IR avec le runtime d'intégration auto-hébergé comme proxy

Une fois votre runtime d'intégration auto-hébergé et votre service lié Stockage Blob Azure prêts pour le transit, vous pouvez configurer votre instance d'Azure-SSIS IR nouvelle/existante avec le runtime d'intégration auto-hébergé comme proxy sur le portail/l'application ADF.  Si l’instance existante d’Azure-SSIS IR est en cours d’exécution, arrêtez-la. Vous la redémarrerez plus tard.

1. Dans le panneau de configuration du runtime d’intégration, passez les sections **Paramètres généraux** et **Paramètres SQL** en sélectionnant le bouton **Suivant**. 

1. Dans la section **Paramètres avancés** :

   1. Cochez la case **Configurer un runtime d’intégration auto-hébergé en tant que proxy pour votre Azure-SSIS Integration Runtime**. 

   1. Sous **Runtime d’intégration auto-hébergé**, sélectionnez votre IR auto-hébergé existant en tant que proxy pour Azure-SSIS IR.

   1. Sous **Service lié de stockage de préproduction**, sélectionnez votre service lié de stockage Blob Azure existant ou créez-en un pour la préproduction.

   1. Sous **Chemin d’accès intermédiaire**, spécifiez un conteneur d’objets blob dans le compte de stockage Blob Azure sélectionné ou laissez ce champ pour utiliser un conteneur par défaut pour la préproduction.

   1. Sélectionnez **Continuer**.

   ![Paramètres avancés avec un IR auto-hébergé](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Activer les packages SSIS pour la connexion par proxy

À l'aide de la dernière version de SSDT avec extension Projets SSIS pour Visual Studio téléchargeable [ici](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) ou d'un programme d'installation autonome téléchargeable [ici](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer), vous pouvez accéder à une nouvelle propriété **ConnectByProxy** ajoutée dans les gestionnaires de connexions de fichiers plats/OLEDB.  

Lors de la conception de nouveaux packages contenant des tâches de flux de données avec sources de fichiers plats/OLEDB pour accéder aux bases de données/fichiers locaux, vous pouvez activer cette propriété en la définissant sur **True** dans le panneau Propriétés des gestionnaires de connexions appropriés.

![Activer la propriété ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Vous pouvez également activer cette propriété lors de l'exécution de packages existants sans qu'il soit nécessaire de les modifier manuellement un par un.  Nous avons deux options :
- Ouvrir, régénérer et redéployer le projet contenant ces packages avec la dernière version de SSDT pour une exécution sur votre instance d'Azure-SSIS IR : La propriété peut ensuite être activée en choisissant la valeur **True** pour les gestionnaires de connexions appropriés qui apparaissent dans l'onglet **Gestionnaires de connexions** de la fenêtre indépendante Exécuter le package lors de l'exécution des packages à partir de SSMS.

  ![Activer la propriété ConnectByProxy 2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  La propriété peut également être activée en choisissant la valeur **True** pour les gestionnaires de connexions appropriés qui apparaissent dans l'onglet **Gestionnaires de connexions** de [Exécuter l'activité du package SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) lors de l'exécution des packages dans les pipelines ADF.
  
  ![Activer la propriété ConnectByProxy 3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- Redéployer le projet contenant ces packages pour une exécution sur votre instance de SSIS IR : La propriété peut ensuite être activée en fournissant son chemin, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`, et en choisissant la valeur **True** comme substitution de propriété dans l'onglet **Avancé** de la fenêtre indépendante Exécuter le package lors de l'exécution des packages à partir de SSMS.

  ![Activer la propriété ConnectByProxy 4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  La propriété peut également être activée en fournissant son chemin, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`, et en choisissant la valeur **True** comme substitution de propriété dans l'onglet **Substitutions de propriété** de [Exécuter l'activité du package SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) lors de l'exécution des packages dans les pipelines ADF.
  
  ![Activer la propriété ConnectByProxy 5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Déboguer les première et deuxième tâches intermédiaires

Sur votre runtime d'intégration auto-hébergé, vous pouvez accéder aux journaux du runtime dans le dossier `C:\ProgramData\SSISTelemetry` et aux journaux d'exécution des premières tâches intermédiaires dans le dossier `C:\ProgramData\SSISTelemetry\ExecutionLog`.  Les journaux d'exécution des deuxièmes tâches intermédiaires se trouvent dans votre SSISDB ou dans les chemins d'accès de journalisation spécifiés, selon que vous stockez vos packages dans SSISDB ou dans le système de fichiers/des partages de fichiers/Azure Files, respectivement.  Les ID uniques des premières tâches intermédiaires peuvent également se trouver dans les journaux d'exécution des deuxièmes tâches intermédiaires, par exemple 

![ID unique de la première tâche intermédiaire](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="using-windows-authentication-in-staging-tasks"></a>Utilisation de l’authentification Windows dans les tâches de mise en lots

Si vos tâches de mise en lots sur l’IR auto-hébergé nécessitent l’authentification Windows, vous devez [configurer vos packages SSIS pour qu’ils utilisent la même authentification Windows](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). Vos tâches de mise en lots seront appelées avec le compte de service de l’IR auto-hébergé (`NT SERVICE\DIAHostService` par défaut) et vos magasins de données seront accessibles avec le compte d’authentification Windows. Les deux comptes nécessitent que certaines stratégies de sécurité leur soient attribuées. Par conséquent, sur l’ordinateur de l’IR auto-hébergé, ouvrez `Local Security Policy` -> `Local Policies` -> `User Rights Assignment` et procédez comme suit.
- Attribuez les stratégies **Ajuster les quotas de mémoire pour un processus** et **Remplacer un jeton de niveau processus** au compte de service de l’IR auto-hébergé. Ceci doit être effectué automatiquement lors de l’installation de votre IR auto-hébergé avec le compte de service par défaut. Si vous utilisez un autre compte de service, attribuez-lui les mêmes stratégies.
- Attribuez la stratégie **Se connecter en tant que service** au compte d’authentification Windows.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Facturation des première et deuxième tâches intermédiaires

Les premières tâches intermédiaires exécutées sur votre runtime d'intégration auto-hébergé seront facturées séparément. De même, les activités de déplacement de données exécutées sur le runtime d'intégration auto-hébergé seront facturées comme décrit dans l'article [Tarifs des pipelines de données ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/).

Les deuxièmes tâches intermédiaires exécutées sur votre instance d'Azure-SSIS IR ne seront pas facturées séparément, mais votre instance d'Azure-SSIS IR en cours d'exécution sera facturée comme décrit dans l'article [Tarifs d'Azure-SSIS IR](https://azure.microsoft.com/pricing/details/data-factory/ssis/).

## <a name="current-limitations"></a>Limites actuelles

- Seules les tâches de flux de données avec les gestionnaires de connexions de fichiers plats/OLEDB/ODBC et les sources de fichiers plats/OLEDB/ODBC sont actuellement prises en charge. 
- Seuls les services liés Stockage Blob Azure configurés avec une authentification de type **Clé de compte**/**URI SAS**/**Principal de service** sont actuellement pris en charge.

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré votre runtime d'intégration auto-hébergé en tant que proxy pour votre instance d'Azure-SSIS IR, vous pouvez déployer et exécuter vos packages pour accéder aux données locales sous forme d'activités Exécuter un package SSIS dans les pipelines ADF. Pour plus d'informations, consultez [Exécuter des packages SSIS en tant qu'activités Exécuter un package SSIS dans les pipelines ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).