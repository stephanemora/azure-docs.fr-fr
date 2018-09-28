---
title: Activer la collecte de données pour des modèles en production - Azure Machine Learning
description: Découvrez comment collecter des données de modèle d’entrée Azure Machine Learning dans un stockage d’objets blob Azure.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 09/24/2018
ms.openlocfilehash: 2c5b4607fda6bc3c6b990096feb55da7a3886b8e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46959865"
---
# <a name="collect-data-for-models-in-production"></a>Collecter des données pour des modèles en production

Dans cet article, vous allez découvrir comment collecter des données de modèle d’entrée à partir des services Azure Machine Learning que vous avez déployés dans Azure Kubernetes Cluster (AKS) dans un stockage d’objets blob Azure. 

Une fois la collecte activée, ces données vous aideront à :
* Superviser les dérives de données à mesure que les données de production entrent dans votre modèle.

* Prendre de meilleures décisions concernant le réentraînement ou l’optimisation de votre modèle.

* Réentraîner votre modèle avec les données collectées.

## <a name="what-is-collected-and-where-does-it-go"></a>Quelles sont les données collectées et où sont-elles placées ?

Les données suivantes peuvent être collectées :
* Données d’**entrée** de modèle à partir de services web déployés dans Azure Kubernetes Cluster (AKS) (les données vocales, d’images et vidéo ne sont **pas** collectées). 
  
* Prédictions de modèle utilisant des données d’entrée de production.

> [!Note]
> La pré-agrégation ou les précalculs sur ces données ne font pas partie du service à l’heure actuelle.   

La sortie est enregistrée dans un objet blob Azure. Les données étant ajoutées à un objet blob Azure, vous pouvez ensuite choisir votre outil préféré pour exécuter l’analyse. 

Le chemin des données de sortie dans l’objet blob respecte cette syntaxe :

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/92c76a2f-0e1c-4216-b65e-abf7a3f34c1e/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- Un espace de travail Azure Machine Learning, un répertoire local contenant vos scripts, et le SDK Azure Machine Learning pour Python. Découvrez comment obtenir ces prérequis à l’aide du document [Guide pratique pour configurer un environnement de développement](how-to-configure-environment.md).

- Un modèle Machine Learning entraîné à déployer sur Azure Kubernetes Service (AKS). Si vous n’en avez pas, consultez le tutoriel [Entraîner un modèle de classification d’images](tutorial-train-models-with-aml.md).

- Un [cluster AKS](how-to-deploy-to-aks.md).

- Les dépendances et le module suivants doivent être installés [dans votre environnement](how-to-configure-environment.md) :
  + Sur Linux :
    ```shell
    sudo apt-get install libxml++2.6-2v5
    pip install azureml-monitoring
    ```

  + Sur Windows :
    ```shell
    pip install azureml-monitoring
    ```

## <a name="enable-data-collection"></a>Activer la collecte des données
La collecte de données peut être activée quel que soit le modèle en cours de déploiement par le biais du service Azure Machine Learning ou d’autres outils. 

Pour l’activer, vous devez :

1. Ouvrir le fichier de scoring. 

1. Ajoutez le code suivant au début du fichier :

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Déclarez vos variables de collecte de données dans votre fonction `init()` :

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", identifier="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", identifier="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* est un paramètre facultatif ; inutile de le configurer si votre modèle n’en a pas besoin. Un ID de corrélation facilite le mappage avec d’autres données. (Par exemple : LoanNumber, CustomerId, et ainsi de suite.)
    
    *Identifier* est utilisé ultérieurement pour générer la structure de dossiers dans votre objet blob. Il peut servir à diviser les données « brutes » et « traitées ».

3.  Ajoutez les lignes de code suivantes à la fonction `run(input_df)` :

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. La collecte de données n’est **pas** automatiquement définie sur **true** quand vous déployez un service dans AKS. Par conséquent, vous devez mettre à jour votre fichier de configuration, par exemple comme suit : 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    AppInsights pour la supervision de service peut également être activé en modifiant cette configuration :
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. [Créez une nouvelle image et déployez votre service.](how-to-deploy-to-aks.md) 


Si vous disposez déjà d’un service avec les dépendances installées dans votre **fichier d’environnement** et votre **fichier de scoring**, activez la collecte de données en effectuant les étapes suivantes :

1. Accédez au [portail Azure](https://portal.azure.com).

1. Ouvrez votre espace de travail.

1. Accédez à **Déploiements** -> **Sélectionner un service** -> **Modifier**.

   ![Modifier le service](media/how-to-enable-data-collection/EditService.png)

1. Dans **Paramètres avancés**, désélectionnez **Activer la collecte des données de modèle**. 

   ![Désactiver la collecte de données](media/how-to-enable-data-collection/CheckDataCollection.png)

   Dans cette fenêtre, vous pouvez également choisir d’« Activer les diagnostics Appinsights » pour effectuer le suivi de l’intégrité de votre service.  

1. Sélectionnez **Mettre à jour** pour appliquer la modification.


## <a name="disable-data-collection"></a>Désactiver la collecte de données
Vous pouvez arrêter la collecte de données à tout moment. Pour désactiver la collecte de données, utilisez du code Python ou le portail Azure.

+ Option 1 : désactiver dans le portail Azure : 
  1. Connectez-vous au [portail Azure](https://portal.azure.com).

  1. Ouvrez votre espace de travail.

  1. Accédez à **Déploiements** -> **Sélectionner un service** -> **Modifier**.

     ![Modifier le service](media/how-to-enable-data-collection/EditService.png)

  1. Dans **Paramètres avancés**, désélectionnez **Activer la collecte des données de modèle**. 

     ![Désactiver la collecte de données](media/how-to-enable-data-collection/UncheckDataCollection.png) 

  1. Sélectionnez **Mettre à jour** pour appliquer la modification.

* Option 2 : utiliser Python pour désactiver la collecte de données :

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="example-notebook"></a>Exemple de bloc-notes

Le bloc-notes `00.Getting Started/12.enable-data-collection-for-models-in-aks.ipynb` illustre les concepts de cet article.  

Consultez ce bloc-notes :
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]