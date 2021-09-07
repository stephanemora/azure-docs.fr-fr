---
title: 'ML Studio (classique) : Déploiement et consommation – Azure'
description: Vous pouvez utiliser Machine Learning Studio (classique) pour déployer des workflows et des modèles de Machine Learning en tant que services web. Ces services web peuvent ensuite servir à appeler les modèles de machine learning à partir d’applications via Internet pour effectuer des prévisions en temps réel ou par lots.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: f6d2875261028814a925169d84a3de6307433dcb
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688031"
---
# <a name="machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Services web Machine Learning Studio (classique) : Déploiement et consommation

**S’APPLIQUE À :**  ![S’applique à ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (classique)   ![Ne s’applique pas à ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

[!INCLUDE [ML Studio (classic) retirement](../../../includes/machine-learning-studio-classic-deprecation.md)]

Vous pouvez utiliser Machine Learning Studio (classique) pour déployer des workflows et des modèles de Machine Learning en tant que services web. Ces services web peuvent ensuite servir à appeler les modèles de machine learning à partir d’applications via Internet pour effectuer des prévisions en temps réel ou par lots. Les services web, RESTful, peuvent être appelés avec divers langages et plateformes de programmation, notamment Java, .NET et des applications comme Excel.

Les sections suivantes fournissent des liens vers des procédures pas à pas, du code et de la documentation pour vous aider à démarrer.

## <a name="deploy-a-web-service"></a>Déployer un service web

### <a name="with-machine-learning-studio-classic"></a>Avec Machine Learning Studio (classique)

Le portail Studio (classique) et le portail des services web Machine Learning vous permettent de déployer et de gérer un service web sans écrire de code.

Les liens suivants fournissent des informations générales sur le processus de déploiement d’un nouveau service web :

* Pour une vue d’ensemble du déploiement d’un nouveau service web basé sur Azure Resource Manager, consultez [Déployer un nouveau service web](deploy-a-machine-learning-web-service.md).
* Pour une présentation du déploiement d’un service web, consultez [Déployer un service web Machine Learning](deploy-a-machine-learning-web-service.md).
* Pour une présentation complète de la création et du déploiement d'un service web, consultez [Tutoriel 1 : Prédire le risque de crédit](tutorial-part1-credit-risk.md).
* Pour des exemples spécifiques de déploiement d’un service web, consultez :

  * [Tutoriel 3 : Déployer un modèle de risque de crédit](tutorial-part3-credit-risk-deploy.md)
  * [Comment déployer un service web dans plusieurs régions](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Avec les API du fournisseur de ressources des services web (API Azure Resource Manager)

Le fournisseur de ressources Machine Learning Studio (classique) pour les services web permet le déploiement et la gestion des services web au moyen d’appels de l’API REST. Pour plus d'informations, consultez la référence [Service web Machine Learning (REST)](/rest/api/machinelearning/index).

<!-- [Machine Learning Web Service (REST)](/rest/api/machinelearning/webservices) reference. -->

### <a name="with-powershell-cmdlets"></a>Avec des applets de commande PowerShell

Le fournisseur de ressources Machine Learning Studio (classique) pour les services web permet le déploiement et la gestion des services web au moyen d’applets de commande PowerShell.

Pour utiliser les cmdlets, vous devez d’abord vous connecter à votre compte Azure à partir de l’environnement PowerShell à l’aide de la cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Si vous ne savez pas comment appeler les commandes PowerShell basées sur Resource Manager, consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md).

Pour exporter votre expérience prédictive, utilisez cet [exemple de code](https://github.com/ritwik20/AzureML-WebServices). Après avoir créé le fichier .exe à partir du code, vous pouvez taper :

```azurepowershell
C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>
```

L’exécution de l’application crée un modèle JSON de service web. Pour utiliser le modèle afin de déployer un service web, vous devez ajouter les informations suivantes :

* Nom et clé du compte de stockage

    Vous pouvez récupérer le nom et la clé du compte de stockage à partir du [portail Azure](https://portal.azure.com/).
* ID de plan d’engagement

    Vous pouvez récupérer l’ID du plan sur le portail des [services web Machine Learning](https://services.azureml.net) en vous connectant et en cliquant sur le nom d’un plan.

Ajoutez-les au modèle JSON en tant qu’enfants du nœud *Propriétés* au même niveau que le nœud *MachineLearningWorkspace*.

Voici un exemple :

```json
"StorageAccount": {
        "name": "YourStorageAccountName",
        "key": "YourStorageAccountKey"
},
"CommitmentPlan": {
    "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
}
```

Consultez les articles et les exemples de code suivants pour plus de détails :

* Informations de référence sur les [applets de commande Machine Learning Studio (classique)](/powershell/module/az.machinelearning) sur MSDN

## <a name="consume-the-web-services"></a>Utiliser des services web

### <a name="from-the-machine-learning-web-services-ui-testing"></a>À partir de l’interface utilisateur des services web Machine Learning (test)

Vous pouvez tester votre service web sur le portail de services web Machine Learning. Cela comprend le test du service de requête-réponse (RRS) et des interfaces de service d’exécution de lots (BES).

* [Déployer comme un nouveau service web](deploy-a-machine-learning-web-service.md)
* [Déploiement d’un service web Machine Learning](deploy-a-machine-learning-web-service.md)
* [Tutoriel 3 : Déployer un modèle de risque de crédit](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>À partir d’Excel

Vous pouvez télécharger un modèle Excel qui consomme le service web :

* [Utilisation d’un service web Machine Learning à partir de Microsoft Excel](consuming-from-excel.md)
* [Complément Excel de services web Machine Learning](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>À partir d’un client basé sur REST

Les services web Machine Learning sont des API RESTful. Vous pouvez consommer ces API à partir de différentes plateformes, notamment .NET, Python, R, Java, etc. La page **Consommer** de votre service web sur le [portail des services web Machine Learning](https://services.azureml.net) contient des exemples de code susceptibles de vous aider à démarrer. Pour plus d’informations, consultez [Guide pratique pour utiliser un service web Machine Learning](consume-web-services.md).