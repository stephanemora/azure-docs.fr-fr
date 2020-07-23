---
title: Utiliser des packages Python privés
titleSuffix: Azure Machine Learning
description: Accédez en toute sécurité aux packages Python privés à partir d'environnements Azure Machine Learning.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: laobri
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 580525b2e8e408949ce1d8f2d1b8241c431fc755
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86208960"
---
# <a name="use-private-python-packages-with-azure-machine-learning"></a>Utiliser des packages Python privés avec Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Cet article explique comment utiliser des packages Python privés en toute sécurité au sein d'Azure Machine Learning. Les cas d'usage des packages Python privés sont les suivants :

 * Vous avez développé un package privé que vous ne souhaitez pas partager publiquement.
 * Vous souhaitez utiliser un référentiel organisé de packages stockés dans un pare-feu d'entreprise.

L'approche recommandée varie selon que vous disposez d'un petit nombre de packages destinés à un seul espace de travail Azure Machine Learning ou d'un référentiel complet de packages destinés à tous les espaces de travail d'une organisation.

Les packages privés sont utilisés par le biais de la classe [Environment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment). Au sein d'un environnement, vous déclarez les packages Python à utiliser, y compris les packages privés. Pour en savoir plus sur l'utilisation des environnements dans Azure Machine Learning en général, consultez [Utiliser des environnements](how-to-use-environments.md). 

## <a name="prerequisites"></a>Prérequis

 * Le [kit de développement logiciel (SDK) Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
 * Un [espace de travail Azure Machine Learning](how-to-manage-workspace.md)

### <a name="use-small-number-of-packages-for-development-and-testing"></a>Utiliser un petit nombre de packages à des fins de développement et de test

Pour un petit nombre de packages privés destinés à un seul espace de travail, utilisez la méthode statique [`Environment.add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-). Cette approche vous permet d'ajouter rapidement un package privé à l'espace de travail et peut parfaitement être utilisée à des fins de développement et de test.

Pointez l'argument filepath vers un fichier wheel local et exécutez la commande ```add_private_pip_wheel```. La commande renvoie une URL qui permet de suivre l'emplacement du package dans votre espace de travail. Capturez l'URL de stockage et transmettez-la à la méthode `add_pip_package()`.

```python
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

En interne, le service Azure Machine Learning remplace l'URL par une URL SAS sécurisée afin que votre fichier wheel reste privé et sécurisé.

### <a name="consume-a-repository-of-packages-from-azure-devops-feed"></a>Utiliser un référentiel de packages à partir du flux Azure DevOps

Si vous développez activement des packages Python pour votre application de Machine Learning, vous pouvez les héberger dans un référentiel Azure DevOps sous forme d'artefacts et les publier en tant que flux. Cette approche vous permet d'intégrer le flux de travail DevOps pour créer des packages avec votre espace de travail Azure Machine Learning. Pour apprendre à configurer des flux Python à l'aide d'Azure DevOps, consultez la page [Bien démarrer avec les packages Python dans Azure Artifacts](https://docs.microsoft.com/azure/devops/artifacts/quickstarts/python-packages?view=azure-devops).

Cette approche utilise un jeton d'accès personnel pour l'authentification auprès du référentiel. La même approche est applicable à d'autres référentiels avec authentification basée sur un jeton, comme les référentiels GitHub privés. 

 1. [Créez un PAT (jeton d'accès personnel)](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=preview-page#create-a-pat) pour votre instance d'Azure DevOps. Définissez l'étendue du jeton sur __Empaquetage > Lire__. 

 2. Ajoutez l'URL et le PAT Azure DevOps en tant que propriétés de l'espace de travail, en utilisant la méthode [Workspace.set_connection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#set-connection-name--category--target--authtype--value-).

     ```python
    from azureml.core import Workspace
    
    pat_token = input("Enter secret token")
    ws = Workspace.from_config()
    ws.set_connection(name="connection-1", 
        category = "PythonFeed",
        target = "https://<my-org>.pkgs.visualstudio.com", 
        authType = "PAT", 
        value = pat_token) 
     ```

 3. Créez un environnement Azure Machine Learning et ajoutez des packages Python à partir du flux.
    
    ```python
    from azureml.core import Environment
    from azureml.core.conda_dependencies import CondaDependencies
    
    env = Environment(name="my-env")
    cd = CondaDependencies()
    cd.add_pip_package("<my-package>")
    cd.set_pip_option("--extra-index-url https://<my-org>.pkgs.visualstudio.com/<my-project>/_packaging/<my-feed>/pypi/simple")
    env.python.conda_dependencies=cd
    ```

L'environnement est maintenant prêt à être utilisé à des fins d'apprentissage ou de déploiement de points de terminaison de service web. Lors de la création de l'environnement, le service Azure Machine Learning utilise le PAT pour s'authentifier auprès du flux avec l'URL de base correspondante.

### <a name="consume-a-repository-of-packages-from-private-storage"></a>Utiliser un référentiel de packages à partir d'un stockage privé

Vous pouvez utiliser des packages à partir d'un compte de stockage Azure du pare-feu de votre organisation. Un tel compte de stockage peut contenir un ensemble organisé de packages pour une utilisation en entreprise, ou un miroir interne de packages publiquement accessibles.

Pour configurer un stockage privé de ce type :

 1. [Placez l'espace de travail sur un réseau virtuel](how-to-enable-virtual-network.md).
 2. Créez un compte de stockage et [interdisez l'accès public](https://docs.microsoft.com/azure/storage/common/storage-network-security).
 2. Placez les packages Python que vous souhaitez utiliser dans un conteneur associé au compte de stockage. 
 3. [Autorisez l'accès au compte de stockage à partir du réseau virtuel de l'espace de travail](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network). 

Dans Stockage Blob Azure, vous pouvez ensuite référencer les packages par leur URL complète dans la définition de l'environnement Azure Machine Learning.

## <a name="next-steps"></a>Étapes suivantes

 * Apprenez-en davantage sur la [sécurité d'entreprise dans Azure Machine Learning](concept-enterprise-security.md).
