---
title: Exécuter des packages SSIS dans SSDT
description: Découvrez comment exécuter des packages SSIS dans Azure à partir de SSDT.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/31/2019
ms.openlocfilehash: 5f21623af9b89bbb020063dfb72f7b60e65a6ebe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927719"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Exécuter des packages SSIS dans Azure à partir de SSDT
Cet article décrit la fonctionnalité des projets Azure SQL Server Integration Services (SSIS) sur SQL Server Data Tools (SSDT), qui vous permet d’exécuter des packages sur Azure-SSIS Integration Runtime (IR) dans Azure Data Factory (ADF).  Vous pouvez utiliser cette fonctionnalité pour tester vos packages SSIS existants avant d'effectuer une opération lift-and-shift ou de les migrer vers Azure, ou de développer de nouveaux packages SSIS à exécuter dans Azure.

Avec cette fonctionnalité, vous pouvez créer un runtime d'intégration Azure-SSIS IR ou attacher un runtime d'intégration Azure-SSIS IR existant aux projets SSIS, puis y exécuter vos packages.  Nous prenons en charge l’exécution de packages à déployer dans le catalogue SSIS (SSISDB), dans le modèle de déploiement de projet, et de ceux à déployer dans les systèmes de fichiers/les partages de fichiers/Azure Files, dans le modèle de déploiement de package. 

## <a name="prerequisites"></a>Prérequis
Pour utiliser cette fonctionnalité, téléchargez et installez la dernière version de SSDT avec l’extension de projets SSIS pour Visual Studio [ici](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) ou en tant que programme d'installation autonome [ici](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer).

## <a name="azure-enable-ssis-projects"></a>Projets SSIS pour Azure
Sur SSDT, vous pouvez créer de nouveaux projets SSIS pour Azure à l’aide du modèle **Projet Integration Services (pour Azure**).

![Nouveau projet SSIS pour Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Vous pouvez également activer pour Azure vos projets SSIS existants en cliquant avec le bouton droit sur le nœud du projet dans le panneau Explorateur de solutions de SSDT pour afficher un menu, puis en sélectionnant l'élément de menu **Pour Azure**.

![Projet SSIS existant pour Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

L'activation pour Azure de vos projets SSIS existants implique que vous définissiez leur version de serveur cible en tant que version récente prise en charge par le runtime d'exécution Azure-SSIS IR, ce qui correspond actuellement à **SQL Server 2017**, et si ce n'est pas encore le cas, une fenêtre contextuelle vous y invite.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>Connecter des projets pour Azure à SSIS dans Azure Data Factory
En connectant vos projets pour Azure à SSIS dans ADF, vous pouvez télécharger vos packages dans Azure Files et les exécuter sur le runtime d'exécution Azure-SSIS IR.  Pour ce faire, procédez comme suit :

1. Cliquez avec le bouton droit sur le projet ou le nœud **Ressources Azure liées** dans le panneau Explorateur de solutions de SSDT pour afficher un menu et sélectionnez l'élément de menu **Connecte à SSIS dans Azure Data Factory** pour lancer l'**Assistant de connexion SSIS dans ADF**.

   ![Connecter à SSIS dans ADF](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. Dans la page **Introduction à SSIS dans ADF**, lisez l'introduction et cliquez sur le bouton **Suivant** pour continuer.

   ![Introduction à SSIS dans ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. Dans la page **Sélectionner SSIS IR dans ADF**, sélectionnez votre instance ADF existante et le runtime d'exécution Azure-SSIS IR pour exécuter des packages ou en créer, le cas échéant.
   - Pour sélectionner votre runtime d'exécution Azure-SSIS IR existant, sélectionnez d’abord l’abonnement Azure et l'instance ADF qui conviennent.
   - Si vous sélectionnez une instance ADF existante sans Azure-SSIS IR, cliquez sur le bouton **Créer SSIS IR** pour créer un runtime d'exécution Azure-SSIS IR sur le portail/l'application ADF.
   - Si vous sélectionnez un abonnement Azure existant sans ADF, cliquez sur le bouton **Créer SSIS IR** pour lancer l'**Assistant de création Integration Runtime**, où vous pouvez entrer l’emplacement et le préfixe pour nous permettre de créer automatiquement un groupe de ressources Azure, Data Factory et SSIS IR pour vous, avec un nom au format suivant : **YourPrefix-RG/DF/IR-YourCreationTime**.
   
   ![Sélectionner SSIS IR dans ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. Dans la page **Sélectionner Stockage Azure**, sélectionnez votre compte Stockage Azure existant pour télécharger des packages dans Azure Files ou en créer un, le cas échéant.
   - Pour sélectionner votre compte Stockage Azure existant, sélectionnez d’abord l’abonnement Azure qui convient.
   - Si vous sélectionnez le même abonnement Azure que votre runtime d'exécution Azure-SSIS IR sans compte Stockage Azure, cliquez sur le bouton **Créer Stockage Azure** pour nous permettre d'en créer un pour vous au même emplacement que votre runtime d'exécution Azure-SSIS IR, avec pour nom un préfixe de votre nom Azure-SSIS IR et sa date de création.
   - Si vous sélectionnez un autre abonnement Azure sans compte Stockage Azure, cliquez sur le bouton **Créer Stockage Azure** pour en créer un sur le portail Azure.
   
   ![Sélectionnez Stockage Azure](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. Cliquez sur le bouton **Connecter** pour terminer votre connexion.  Nous afficherons votre runtime d'exécution Azure-SSIS IR et votre compte Stockage Azure sous le nœud **Ressources Azure liées** du panneau Explorateur de solutions de SSDT.  Nous actualiserons également l’état de votre runtime d'exécution Azure-SSIS IR et vous pourrez le gérer en cliquant avec le bouton droit sur son nœud pour afficher un menu, puis en sélectionnant l'élément de menu **Démarrer\Arrêter\Gérer** afin d'accéder au portail ou à l'application ADF pour le faire.

## <a name="execute-ssis-packages-in-azure"></a>Exécuter des packages SSIS dans Azure
### <a name="starting-package-executions"></a>Vérifier les exécutions des packages
Après avoir connecté vos projets à SSIS dans ADF, vous pouvez exécuter des packages sur Azure-SSIS IR.  Pour ce faire, vous disposez de deux options :
-  Cliquez sur le bouton **Démarrer** dans la barre d’outils SSDT pour faire défiler un menu et sélectionnez l'élément de menu **Exécuter dans Azure** 

   ![Exécuter dans Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Cliquez avec le bouton droit sur le nœud du package dans le panneau Explorateur de solutions de SSDT pour afficher un menu et sélectionnez l'élément de menu **Exécuter le package dans Azure**.

   ![Exécuter le package dans Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> L'exécution de vos packages dans Azure implique que vous exécutiez Azure-SSIS IR et dès lors, si votre runtime d'exécution Azure-SSIS IR est arrêté, vous serez invité à le démarrer.  En dehors des réglages personnalisés, ce processus devrait se terminer dans les 5 minutes. Toutefois, il peut prendre de 20 à 30 minutes environ pour le runtime d’intégration Azure-SSIS se joignant à un réseau virtuel.  Après avoir exécuté vos packages dans Azure, vous pouvez arrêter votre runtime d'exécution Azure-SSIS IR pour gérer son coût d’exécution en cliquant avec le bouton droit sur son nœud dans le panneau Explorateur de solutions de SSDT afin d'afficher un menu, puis en sélectionnant l'élément de menu **Démarrer\Arrêter\Gérer** qui vous renvoie vers le portail ou l'application ADF pour le faire.

### <a name="checking-package-execution-logs"></a>Vérifier les journaux d’exécution des packages
Lorsque vous démarrez l'exécution de vos packages, nous mettons en forme et affichons son journal dans la fenêtre Progression de SSDT.  Pour un package à exécution longue, nous mettons régulièrement à jour son journal.  Vous pouvez arrêter l’exécution de votre package en cliquant sur le bouton **Arrêter** dans la barre d’outils SSDT, après quoi elle sera immédiatement annulée.  Vous pouvez également rechercher temporairement les données brutes du journal dans son chemin d’accès UNC (Universal Naming Convention) : `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`, mais nous le nettoyons après un jour.

### <a name="switching-package-protection-level"></a>Changement du niveau de protection des packages
L’exécution de packages SSIS dans Azure ne prend pas en charge les niveaux de protection **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey**.  Par conséquent, si vos packages sont configurés avec ceux-ci, nous les basculons temporairement vers **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**, avec des mots de passe générés de manière aléatoire lors du chargement de vos packages dans Azure Files à des fins d'exécution sur votre runtime d'exécution Azure-SSIS IR.

> [!NOTE]
> Si vos packages contiennent des tâches d’exécution de packages faisant référence à d'autres packages configurés avec les niveaux de protection **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey**, vous devez reconfigurer manuellement ces packages pour utiliser **EncryptSensitiveWithPassword**/**EncryptAllWithPassword** avant de les exécuter.

Si vos packages sont déjà configurés avec les niveaux de protection **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**, nous ne les modifions pas, mais nous utilisons toujours des mots de passe générés de manière aléatoire pour charger vos packages dans Azure Files à des fins d'exécution dans votre runtime d'exécution Azure-SSIS IR.

### <a name="using-package-configuration-file"></a>Utilisation du fichier de configuration de package
Si vous utilisez des fichiers de configuration de package dans le modèle de déploiement de package pour modifier les valeurs des variables au moment de l’exécution, nous chargeons automatiquement ces fichiers avec vos packages dans Azure Files à des fins d'exécution dans votre runtime d'exécution Azure-SSIS IR.

### <a name="using-execute-package-task"></a>Utilisation de la tâche d’exécution de package
Si vos packages contiennent des tâches d’exécution de package faisant référence à d’autres packages stockés sur des systèmes de fichiers locaux, vous devez effectuer les configurations supplémentaires suivantes :

1. Chargez les autres packages dans Azure Files sous le même compte Stockage Azure connecté à vos projets et récupérez leur nouveau chemin UNC, par exemple `\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. Remplacez le chemin d’accès du fichier de ces autres packages dans le gestionnaire de connexion au fichier des tâches d'exécution de package par leur nouveau chemin d’accès UNC
   - Si la machine qui exécute SSDT n'est pas en mesure d'accéder au nouveau chemin d’accès UNC, vous pouvez modifier le chemin d’accès du fichier dans le panneau Propriétés du gestionnaire de connexion au fichier
   - Vous pouvez également utiliser une variable pour le chemin d’accès du fichier afin d'attribuer la valeur qui convient au moment de l’exécution

Si vos packages contiennent des tâches d’exécution de package faisant référence à d’autres packages dans le même projet, aucune configuration supplémentaire n’est requise.

## <a name="next-steps"></a>Étapes suivantes
Lorsque l'exécution de vos packages dans Azure à partir de SSDT vous convient, vous pouvez les déployer et les exécuter en tant qu’activités Exécuter un package SSIS dans les pipelines ADF. Pour plus d'informations, consultez [Exécuter des packages SSIS en tant qu'activités Exécuter un package SSIS dans les pipelines ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
