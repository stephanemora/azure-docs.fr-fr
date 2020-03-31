---
title: Exécuter des packages SSIS (SQL Server Integration Services) avec l’utilitaire dtexec compatible Azure
description: Découvrez comment exécuter des packages SQL Server Integration Services (SSIS) avec l’utilitaire dtexec compatible Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/21/2019
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.openlocfilehash: a5540eea91937319a6ac947b50698ccaa8b25847
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931698"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>Exécuter des packages SQL Server Integration Services avec l’utilitaire dtexec compatible Azure
Cet article décrit l’utilitaire d’invite de commandes dtexec (AzureDTExec) compatible Azure. Il permet d’exécuter des packages SQL Server Integration Services (SSIS) sur Azure-SSIS Integration Runtime (IR) dans Azure Data Factory.

L’utilitaire dtexec traditionnel est fourni avec SQL Server. Pour plus d'informations, consultez [utilitaire dtexec](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Il est souvent appelé par des orchestrateurs ou des planificateurs tiers, comme ActiveBatch et Control-M, pour exécuter localement des packages SSIS. 

L’utilitaire AzureDTExec moderne est fourni avec un outil SQL Server Management Studio (SSMS). Il peut également être appelé par des orchestrateurs ou des planificateurs tiers pour exécuter des packages SSIS dans Azure. Cela facilite les opérations de soulèvement et de déplacement ou la migration de vos packages SSIS vers le cloud. Après la migration, si vous souhaitez continuer à utiliser des orchestrateurs ou des planificateurs tiers dans vos opérations quotidiennes, ils peuvent désormais appeler AzureDTExec au lieu de dtexec.

AzureDTExec exécute vos packages en tant qu’activités d’exécution de package SSIS dans des pipelines Data Factory. Pour plus d’informations, consultez [Exécuter des packages SSIS en tant qu’activités Azure Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). 

AzureDTExec peut être configuré par le biais de SSMS pour utiliser une application Azure Active Directory (Azure AD) qui génère des pipelines dans votre instance de fabrique de données. Il peut également être configuré pour accéder aux systèmes de fichiers, aux partages de fichiers ou à Azure Files, où vous stockez vos packages. En fonction des valeurs que vous attribuez à ses options d’appel, AzureDTExec génère et exécute un pipeline Data Factory unique comportant une activité Exécuter le package SSIS. L’appel d’AzureDTExec avec les mêmes valeurs pour ses options permet de réexécuter le pipeline existant.

## <a name="prerequisites"></a>Prérequis
Pour utiliser AzureDTExec, téléchargez et installez la dernière version de SSMS, à savoir la version 18.3 ou une version ultérieure. Téléchargez-la à partir de [ce site web](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="configure-the-azuredtexec-utility"></a>Configurez l’utilitaire AzureDTExec
L’installation de SSMS sur votre machine locale installe également AzureDTExec. Pour configurer ses paramètres, démarrez SSMS avec l’option **Exécuter en tant qu’administrateur**. Sélectionnez ensuite **Outils** > **Migrer vers Azure** > **Configurer les DTExec compatibles Azure**.

![Configurer le menu de l’utilitaire dtexec compatible Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Cette action ouvre une fenêtre **AzureDTExecConfig** qui doit être ouverte avec des privilèges d’administrateur pour que le fichier *AzureDTExec.settings* soit accessible en écriture. Si vous n’avez pas exécuté SSMS en tant qu’administrateur, une fenêtre de contrôle de compte d’utilisateur (UAC) s’ouvre. Entrez votre mot de passe d’administrateur pour élever vos privilèges.

![Configurer les paramètres de l’utilitaire dtexec compatible Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

Dans la fenêtre **AzureDTExecConfig**, entrez vos paramètres de configuration comme suit :

- **ApplicationId** : Entrez l’identificateur unique de l’application Azure AD que vous créez avec les autorisations appropriées pour générer des pipelines dans votre fabrique de données. Pour plus d’informations, consultez [Créer une application Azure AD et un principal de service Azure via le portail Azure](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).
- **AuthenticationKey** : entrez la clé d’authentification de votre application Azure AD.
- **TenantId** : entrez l’identificateur unique du locataire Azure AD sous lequel votre application Azure AD est créée.
- **SubscriptionId** : entrez l’identificateur unique de l’abonnement Azure AD sous lequel votre fabrique de données Azure AD a été créée.
- **Groupe de ressources** : entrez le nom du groupe de ressources Azure dans lequel votre fabrique de données a été créée.
- **DataFactory** : entrez le nom de la fabrique de données dans laquelle des pipelines uniques avec l’activité Exécuter le package SSIS sont générés en fonction des valeurs des options fournies lorsque vous appelez AzureDTExec.
- **IRName** : entrez le nom d’Azure-SSIS IR dans votre fabrique de données, sur laquelle les packages spécifiés s’exécuteront dans leur chemin de convention d’affectation de noms (UNC) lorsque appellerez AzureDTExec.
- **PackageAccessDomain** : entrez les informations d’identification de domaine pour accéder à vos packages dans leur chemin UNC spécifié lorsque vous appelez AzureDTExec.
- **PackageAccessUserName** : entrez les informations d’identification de nom d'utilisateur pour accéder à vos packages dans leur chemin UNC spécifié lorsque vous appelez AzureDTExec.
- **PackageAccessPassword** : entrez les informations d’identification de mot de passe pour accéder à vos packages dans leur chemin UNC spécifié lorsque vous appelez AzureDTExec.
- **LogPath** : entrez le chemin UNC du dossier de journaux dans lequel les fichiers journaux de vos exécutions de packages sur Azure-SSIS IR sont écrits.
- **LogLevel** : entrez l’étendue sélectionnée de la journalisation à partir des options prédéfinies **null**, **Basic**, **Verbose** ou **Performance** pour vos exécutions de packages sur Azure-SSIS IR.
- **LogAccessDomain** : entrez les informations d’identification de domaine pour accéder à votre dossier de journaux dans son chemin UNC lorsque vous écrivez des fichiers journaux, ce qui est requis quand **LogPath** est spécifié et que **LogLevel** n’est pas **null**.
- **LogAccessUserName** : entrez les informations d’identification de nom d’utilisateur pour accéder à votre dossier de journaux dans son chemin UNC lorsque vous écrivez des fichiers journaux, ce qui est requis quand **LogPath** est spécifié et que **LogLevel** n’est pas **null**.
- **LogAccessPassword** : entrez les informations d’identification de mot de passe pour accéder à votre dossier de journaux dans son chemin UNC lorsque vous écrivez des fichiers journaux, ce qui est requis quand **LogPath** est spécifié et que **LogLevel** n’est pas **null**.
- **PipelineNameHashStrLen** : entrez la longueur des chaînes de hachage à générer à partir des valeurs d’options que vous fournissez lorsque vous appelez AzureDTExec. Les chaînes servent à former des noms uniques pour les pipelines Data Factory qui exécutent vos packages sur Azure-SSIS IR. Généralement, une longueur de 32 caractères est suffisante.

Pour stocker vos packages et vos fichiers journaux dans des systèmes de fichiers ou des partages de fichiers locaux, vous devez joindre votre runtime d’intégration Azure-SSIS à un réseau virtuel connecté à votre réseau local, afin qu’il puisse récupérer vos packages et d’écrire vos fichiers journaux. Pour plus d’informations, consultez [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Pour éviter de montrer des valeurs sensibles écrites dans le fichier *AzureDTExec.settings* en texte brut, nous les encodons dans des chaînes de codage Base64. Quand vous appelez AzureDTExec, toutes les chaînes codées en Base64 sont décodées dans leurs valeurs d’origine. Vous pouvez sécuriser davantage le fichier *AzureDTExec.settings* en limitant les comptes qui peuvent y accéder.

## <a name="invoke-the-azuredtexec-utility"></a>Appeler l’utilitaire AzureDTExec
Vous pouvez appeler AzureDTExec depuis l’invite de ligne de commande et fournir les valeurs appropriées pour les options concernant votre scénario de cas d’usage.

L’utilitaire est installé sur `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn`. Vous pouvez ajouter son chemin d’accès à la variable d’environnement « PATH »pour qu’elle puisse être appelée depuis n’importe quel endroit.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

L’appel d’AzureDTExec offre des options similaires à celles de l’appel de dtexec. Pour plus d'informations, consultez [Utilitaire dtexec](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Voici les options prises en charge :

- **/F[ile]**  : charge un package qui est stocké dans le système de fichiers, le partage de fichiers ou Azure Files. Comme valeur pour cette option, vous pouvez spécifier le chemin UNC de votre fichier de package dans le système de fichiers, de partage de fichiers ou Azure Files avec son extension .dtsx. Si le chemin UNC spécifié contient des espaces, mettez tout le chemin entre guillemets.
- **/Conf[igFile]**  : spécifie un fichier de configuration à partir duquel extraire des valeurs. À l’aide de cette option, vous pouvez définir une configuration d’exécution pour votre package qui diffère de celle spécifiée au moment de la conception. Vous pouvez stocker différents paramètres dans un fichier de configuration XML, puis les charger avant l’exécution de votre package. Pour plus d’informations, consultez [Configurations de package SSIS](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017). Pour spécifier la valeur de cette option, utilisez le chemin UNC de votre fichier de configuration dans le système de fichiers, le partage de fichiers ou Azure Files avec son extension dtsConfig. Si le chemin UNC spécifié contient des espaces, mettez tout le chemin entre guillemets.
- **/Conn[ection]**  : spécifie les chaînes de connexion des gestionnaires de connexions existants dans votre package. À l’aide de cette option, vous pouvez définir des chaînes de connexion d’exécution pour les gestionnaires de connexions existants dans votre package qui diffèrent de celles spécifiées au moment de la conception. Spécifiez la valeur de cette option comme suit : `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`.
- **/Set** : remplace la configuration d’un paramètre, d’une variable, d’une propriété, d’un conteneur, d’un module fournisseur d’informations, d’un énumérateur Foreach ou d’une connexion dans votre package. Cette option peut être spécifiée plusieurs fois. Spécifiez la valeur de cette option comme suit : `property_path;value`. Par exemple, `\package.variables[counter].Value;1` remplace la valeur de la variable `counter` par 1. Vous pouvez utiliser l’Assistant **Configuration de package** pour rechercher, copier et coller la valeur de `property_path` pour les éléments de votre package dont vous souhaitez remplacer la valeur. Pour plus d’informations, consultez [Configurations de package SSIS](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014).
- **/De[crypt]**  : définit le mot de passe de déchiffrement de votre package configuré avec le niveau de protection **EncryptAllWithPassword**/**EncryptSensitiveWithPassword**.

> [!NOTE]
> L’appel d’AzureDTExec avec de nouvelles valeurs pour ses options génère un nouveau pipeline, à l’exception de l’option **/De[cript]** .

## <a name="next-steps"></a>Étapes suivantes

Une fois que des pipelines uniques avec l’activité Exécuter le package SSIS sont générés et exécutés après l’appel d’AzureDTExec, vous pouvez les surveiller sur le portail Data Factory. Pour plus d’informations, consultez [Exécuter des packages SSIS en tant qu’activités Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

> [!WARNING]
> Le pipeline généré est censé être utilisé uniquement par AzureDTExec. Comme ses propriétés ou ses paramètres peuvent changer dans le futur, il ne faut ni les modifier ni les réutiliser à d’autres fins. Les modifications peuvent arrêter AzureDTExec. Dans ce cas, supprimez le pipeline. AzureDTExec génère un nouveau pipeline la prochaine fois qu’il est appelé.
