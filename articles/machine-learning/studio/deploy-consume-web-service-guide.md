---
title: Déploiement et consommation
titleSuffix: ML Studio (classic) - Azure
description: Vous pouvez utiliser Azure Machine Learning Studio (classique) pour déployer des workflows et des modèles de Machine Learning en tant que services web. Ces services web peuvent ensuite servir à appeler les modèles de machine learning à partir d’applications via Internet pour effectuer des prévisions en temps réel ou par lots.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: ff6ae0de0bbd8c47b81fa5066a97eb0b3e0cf6bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204390"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Services web Azure Machine Learning Studio (classique) : Déploiement et consommation

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Vous pouvez utiliser Azure Machine Learning Studio (classique) pour déployer des workflows et des modèles de Machine Learning en tant que services web. Ces services web peuvent ensuite servir à appeler les modèles de machine learning à partir d’applications via Internet pour effectuer des prévisions en temps réel ou par lots. Les services web, RESTful, peuvent être appelés avec divers langages et plateformes de programmation, notamment Java, .NET et des applications comme Excel.

Les sections suivantes fournissent des liens vers des procédures pas à pas, du code et de la documentation pour vous aider à démarrer.

## <a name="deploy-a-web-service"></a>Déployer un service web

### <a name="with-azure-machine-learning-studio-classic"></a>Avec Azure Machine Learning Studio (classique)

Le portail Studio(classique) et le portail des services web Microsoft Azure Machine Learning vous permettent de déployer et de gérer un service web sans écrire de code.

Les liens suivants fournissent des informations générales sur le processus de déploiement d’un nouveau service web :

* Pour une vue d’ensemble du déploiement d’un nouveau service web basé sur Azure Resource Manager, consultez [Déployer un nouveau service web](deploy-a-machine-learning-web-service.md).
* Pour une présentation du déploiement d’un service web, consultez [Déployer un service web Azure Machine Learning](deploy-a-machine-learning-web-service.md).
* Pour une présentation complète de la création et du déploiement d'un service web, consultez [Tutoriel 1 : Prédire le risque de crédit](tutorial-part1-credit-risk.md).
* Pour des exemples spécifiques de déploiement d’un service web, consultez :

  * [Tutoriel 3 : Déployer un modèle de risque de crédit](tutorial-part3-credit-risk-deploy.md)
  * [Comment déployer un service web dans plusieurs régions](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Avec les API du fournisseur de ressources des services web (API Azure Resource Manager)

Le fournisseur de ressources Azure Machine Learning Studio (classique) pour les services web permet le déploiement et la gestion des services web au moyen d’appels de l’API REST. Pour plus d'informations, consultez la référence [Service web Machine Learning (REST)](/rest/api/machinelearning/index).

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>Avec des applets de commande PowerShell

Le fournisseur de ressources Azure Machine Learning Studio (classique) pour les services web permet le déploiement et la gestion des services web au moyen de cmdlets PowerShell.

Pour utiliser les cmdlets, vous devez d’abord vous connecter à votre compte Azure à partir de l’environnement PowerShell à l’aide de la cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Si vous ne savez pas comment appeler les commandes PowerShell basées sur Resource Manager, consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md).

Pour exporter votre expérience prédictive, utilisez cet [exemple de code](https://github.com/ritwik20/AzureML-WebServices). Après avoir créé le fichier .exe à partir du code, vous pouvez taper :

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

L’exécution de l’application crée un modèle JSON de service web. Pour utiliser le modèle afin de déployer un service web, vous devez ajouter les informations suivantes :

* Nom et clé du compte de stockage

    Vous pouvez récupérer le nom et la clé du compte de stockage à partir du [portail Azure](https://portal.azure.com/).
* ID de plan d’engagement

    Vous pouvez récupérer l’ID du plan sur le portail des [services web Azure Machine Learning](https://services.azureml.net) en vous connectant et en cliquant sur le nom d’un plan.

Ajoutez-les au modèle JSON en tant qu’enfants du nœud *Propriétés* au même niveau que le nœud *MachineLearningWorkspace*.

Voici un exemple :

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Consultez les articles et les exemples de code suivants pour plus de détails :

* Référence [Cmdlets Azure Machine Learning Studio (classique)](https://docs.microsoft.com/powershell/module/az.machinelearning) sur MSDN
* Exemple de [procédure pas à pas](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) sur GitHub

## <a name="consume-the-web-services"></a>Utiliser des services web

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>À partir de l’interface utilisateur des services web Azure Machine Learning (test)

Vous pouvez tester votre service web sur le portail de services web Azure Machine Learning. Cela comprend le test du service de requête-réponse (RRS) et des interfaces de service d’exécution de lots (BES).

* [Déployer comme un nouveau service web](deploy-a-machine-learning-web-service.md)
* [Déploiement d’un service web Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* [Tutoriel 3 : Déployer un modèle de risque de crédit](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>À partir d’Excel

Vous pouvez télécharger un modèle Excel qui consomme le service web :

* [Utilisation d’un service web Microsoft Azure Machine Learning à partir de Microsoft Excel.](consuming-from-excel.md)
* [Complément Excel de services web Azure Machine Learning](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>À partir d’un client basé sur REST

Les services web Azure Machine Learning sont des API RESTful. Vous pouvez consommer ces API à partir de différentes plateformes, notamment .NET, Python, R, Java, etc. La page **Consommer** de votre service web sur le [portail des services web Microsoft Azure Machine Learning](https://services.azureml.net) contient des exemples de code susceptibles de vous aider à démarrer. Pour plus d’informations, consultez [Utilisation d’un service Web Microsoft Azure Machine Learning](consume-web-services.md).
