---
title: Collecter des données relatives à vos modèles de production
titleSuffix: Azure Machine Learning service
description: Découvrez comment collecter des données de modèle d’entrée Azure Machine Learning dans un stockage d’objets blob Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: df2f0731aff6c90ef83b26041a9d383ce14974c6
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024244"
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
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

## <a name="prerequisites"></a>Conditions préalables

- Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez dès aujourd'hui la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree).

- Un espace de travail du service Azure Machine Learning, un répertoire local contenant vos scripts et le kit SDK Azure Machine Learning pour Python installé. Découvrez comment obtenir ces prérequis à l’aide du document [Guide pratique pour configurer un environnement de développement](how-to-configure-environment.md).

- Un modèle Machine Learning entraîné à déployer sur Azure Kubernetes Service (AKS). Si vous n’en avez pas, consultez le tutoriel [Entraîner un modèle de classification d’images](tutorial-train-models-with-aml.md).

- Cluster Azure Kubernetes Service. Pour plus d’informations sur la création et le déploiement, consultez le document [Où et comment effectuer un déploiement](how-to-deploy-and-where.md).

- [Configurez votre environnement](how-to-configure-environment.md) et installez le [SDK Monitoring](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Activer la collecte des données
La collecte de données peut être activée quel que soit le modèle en cours de déploiement par le biais du service Azure Machine Learning ou d’autres outils. 

Pour l’activer, vous devez :

1. Ouvrir le fichier de scoring. 

1. Ajoutez le [code suivant](https://aka.ms/aml-monitoring-sdk) au début du fichier :

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Déclarez vos variables de collecte de données dans votre fonction `init()` :

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", identifier="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", identifier="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* est un paramètre facultatif ; inutile de le configurer si votre modèle n’en a pas besoin. Un ID de corrélation facilite le mappage avec d’autres données. (Voici quelques exemples : LoanNumber, CustomerId, etc.)
    
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

5. Pour créer une image et déployer le service, consultez le document [Où et comment effectuer un déploiement](how-to-deploy-and-where.md).


Si vous disposez déjà d’un service avec les dépendances installées dans votre **fichier d’environnement** et votre **fichier de scoring**, activez la collecte de données en effectuant les étapes suivantes :

1. Accédez au [portail Azure](https://portal.azure.com).

1. Ouvrez votre espace de travail.

1. Accédez à **Déploiements** -> **Sélectionner un service** -> **Modifier**.

   ![Modifier le service](media/how-to-enable-data-collection/EditService.PNG)

1. Dans **Paramètres avancés**, désélectionnez **Activer la collecte des données de modèle**. 

    [![contrôler la Collection de données](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   Dans cette fenêtre, vous pouvez également choisir d’« Activer les diagnostics Appinsights » pour effectuer le suivi de l’intégrité de votre service.  

1. Sélectionnez **Mettre à jour** pour appliquer la modification.


## <a name="disable-data-collection"></a>Désactiver la collecte de données
Vous pouvez arrêter la collecte de données à tout moment. Pour désactiver la collecte de données, utilisez du code Python ou le portail Azure.

+ Option 1 : désactiver dans le portail Azure : 
  1. Connectez-vous au [portail Azure](https://portal.azure.com).

  1. Ouvrez votre espace de travail.

  1. Accédez à **Déploiements** -> **Sélectionner un service** -> **Modifier**.

     [![Options de modification](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. Dans **Paramètres avancés**, désélectionnez **Activer la collecte des données de modèle**. 

     [![Désactiver la collecte de données](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Sélectionnez **Mettre à jour** pour appliquer la modification.

+ Option 2 : utiliser Python pour désactiver la collecte de données :

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Validez vos données et analysez-les
Vous pouvez choisir l’outil de votre choix pour analyser les données recueillies dans votre objet Blob Azure. 

Pour accéder rapidement aux données à partir de votre objet blob :
1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Ouvrez votre espace de travail.
1. Cliquez sur **Stockage**.

    [![Stockage](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Suivez le chemin des données de sortie dans l’objet blob avec cette syntaxe :

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Analyse des données de modèle via Power BI

1. Téléchargez [PowerBI Desktop](https://www.powerbi.com), puis ouvrez-le.

1. Sélectionnez **Obtenir des données** et cliquez sur [**Stockage Blob Azure**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [![PBI Blob setup](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox) (Configuration blob PBI)


1. Ajoutez le nom de votre compte de stockage et entrez votre clé de stockage. Ces informations se trouvent dans le  chemin **Paramètres** >> Clés d’accès de votre blob. 

1. Sélectionnez le conteneur **modeldata**, puis cliquez sur **Modifier**. 

    [![Navigateur PBI](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. Dans l’éditeur de requête, cliquez sous la colonne « Name » et ajoutez votre Compte de stockage 1. Chemin d’accès du modèle dans le filtre. Remarque : si vous ne voulez examiner que les fichiers d’une année ou d’un mois spécifique, développez simplement le chemin du filtre. Par exemple, regardez simplement les données de mars : /modeldata/subscriptionid>/resourcegroupname>/workspacename>/webservicename>/modelname>/modelversion>/identifier>/year>/3

1. Filtrez les données qui vous concernent en fonction du **Nom**. Si vous avez stocké des **prédictions** et des **entrées**, vous devrez créer une requête pour chacune d’elles.

1. Cliquez sur la flèche double à côté de la colonne **Contenu** pour combiner les fichiers. 

    [![PBI Content](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox) (Contenu PBI)

1. Cliquez sur OK. Les données sont alors préchargées.

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Vous pouvez alors cliquer sur **Fermer et appliquer**.

1.  Si vous avez ajouté des entrées et des prédictions, vos tables sont automatiquement mises en corrélation par **RequestId**.

1. Commencez à créer vos rapports personnalisés à partir des données de votre modèle.


### <a name="analyzing-model-data-using-databricks"></a>Analyse des données de modèle à l’aide de Databricks

1. Créez un [espace de travail Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). 

1. Accédez à votre espace de travail Databricks. 

1. Dans votre espace de travail Databricks, sélectionnez **Télécharger des données**.

    [![Chargement de la base de données](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Créez la nouvelle Table et sélectionnez **Autres sources de données** -> Stockage Blob Azure -> Créer une table dans Notebook.

    [![Table de base de données](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Mettez à jour l’emplacement de vos données. Voici un exemple : 

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![DBsetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Suivez les étapes sur le modèle afin d’afficher et analyser vos données. 

## <a name="example-notebook"></a>Exemple de bloc-notes

Le bloc-notes [how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) illustre les concepts de cet article.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
