---
title: Suivre, superviser et analyser des exécutions
titleSuffix: Azure Machine Learning
description: Apprenez à démarrer, à surveiller et à suivre vos exécutions d’expériences Machine Learning avec le Kit de développement logiciel (SDK) Azure Machine Learning Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: swinner95
ms.author: shwinne
ms.reviewer: sgilley
ms.date: 04/19/2021
ms.topic: how-to
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: f61e1a604c9296f85bbfd508f39dcbc74e256caf
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110098719"
---
# <a name="start-monitor-and-track-run-history"></a>Démarrer, surveiller et suivre l’historique des exécutions

Le [Kit de développement logiciel (SDK) Azure Machine Learning pour Python](/python/api/overview/azure/ml/intro), [l’interface de ligne de commande de Machine Learning](reference-azure-machine-learning-cli.md) et [Azure Machine Learning studio](https://ml.azure.com) fournissent diverses méthodes permettant de surveiller, d’organiser et de suivre vos exécutions d’entraînement et d’expérimentation. L’historique des exécutions ML est un élément important d’un processus de développement ML explicable et reproductible.

Cet article explique comment effectuer les tâches suivantes :

* Analyse des performances d’exécution.
* Création d’un affichage personnalisé. 
* Ajout d’une description de l’exécution. 
* Identification et recherche d’exécutions.
* Recherche sur l’historique des exécutions. 
* Annulation ou mise en échec d’exécutions.
* Création d’exécutions enfants.
* Analyse de l’état d’exécution par notification par e-mail.
 

> [!TIP]
> Si vous recherchez des informations sur la supervision du service Azure Machine Learning et des services Azure associés, consultez [Guide pratique pour superviser Azure Machine Learning](monitor-azure-machine-learning.md).
> Si vous recherchez des informations sur les modèles de supervision déployés en tant que services web ou modules IoT Edge, consultez [Collecter les données des modèles](how-to-enable-data-collection.md) et [Superviser avec Application Insights](how-to-enable-app-insights.md).

## <a name="prerequisites"></a>Prérequis

Vous devez disposer des éléments suivants :

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.

* Un [espace de travail Azure Machine Learning](how-to-manage-workspace.md).

* Le Kit de développement logiciel (SDK) Azure Machine Learning pour Python (version 1.0.21 ou ultérieure). Pour installer ou mettre à jour la dernière version du SDK, consultez [Installer ou mettre à jour le SDK](/python/api/overview/azure/ml/install).

    Pour installer votre version du Kit de développement logiciel (SDK) Azure Machine Learning, utilisez le code suivant :

    ```python
    print(azureml.core.VERSION)
    ```

* [Azure CLI](/cli/azure/?preserve-view=true&view=azure-cli-latest) et l’[extension CLI pour Azure Machine Learning](reference-azure-machine-learning-cli.md).


## <a name="monitor-run-performance"></a>Analyse des performances d’exécution

* Démarrer une exécution et son processus de journalisation

    # <a name="python"></a>[Python](#tab/python)
    
    1. Configurez votre expérience en important les classes Set up your experiment by importing the [Workspace](/python/api/azureml-core/azureml.core.workspace.workspace), [Experiment](/python/api/azureml-core/azureml.core.experiment.experiment), [Run](/python/api/azureml-core/azureml.core.run%28class%29), et [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) depuis le package [azureml.core](/python/api/azureml-core/azureml.core).
    
        ```python
        import azureml.core
        from azureml.core import Workspace, Experiment, Run
        from azureml.core import ScriptRunConfig
        
        ws = Workspace.from_config()
        exp = Experiment(workspace=ws, name="explore-runs")
        ```
    
    1. Démarrez une exécution et son processus de journalisation à l’aide de la méthode [`start_logging()`](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-).
    
        ```python
        notebook_run = exp.start_logging()
        notebook_run.log(name="message", value="Hello from run!")
        ```
        
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    Pour démarrer une exécution de votre expérience, procédez comme suit :
    
    1. À partir d’un interpréteur de commandes ou d’une invite, utilisez Azure CLI pour vous identifier auprès de votre abonnement Azure :
    
        ```azurecli-interactive
        az login
        ```
        
        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 
    
    1. Attachez une configuration d’espace de travail au dossier qui contient votre script de d’entraînement. Remplacez `myworkspace` par votre espace de travail Azure Machine Learning. Remplacez `myresourcegroup` par le groupe de ressources Azure qui contient votre espace de travail :
    
        ```azurecli-interactive
        az ml folder attach -w myworkspace -g myresourcegroup
        ```
    
        Cette commande crée un sous-répertoire `.azureml` qui contient des exemples de fichiers d’environnement runconfig et conda. Il contient également un fichier `config.json` utilisé pour communiquer avec votre espace de travail Azure Machine Learning.
    
        Pour plus d’informations, consultez [az ml folder attach](/cli/azure/ml/folder?preserve-view=true&view=azure-cli-latest#az_ml_folder_attach).
    
    2. Pour démarrer l’exécution, utilisez la commande suivante : Lorsque vous utilisez cette commande, spécifiez le nom du fichier runconfig (le texte avant \*.runconfig si vous regardez le système de fichiers) avec le paramètre -c.
    
        ```azurecli-interactive
        az ml run submit-script -c sklearn -e testexperiment train.py
        ```
    
        > [!TIP]
        > La commande `az ml folder attach` crée un sous-répertoire `.azureml` qui contient deux exemples de fichiers runconfig.
        >
        > Si vous disposez d’un script Python qui crée un objet de configuration de série de tests par programmation, vous pouvez utiliser [RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) pour l’enregistrer dans un fichier runconfig.
        >
        > Pour plus d’exemples de fichiers runconfig, consultez [https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/).
    
        Pour plus d’informations, consultez [az ml run submit-script](/cli/azure/ml/run?preserve-view=true&view=azure-cli-latest#az_ml_run_submit-script).

    # <a name="studio"></a>[Studio](#tab/azure-studio)

    Pour obtenir un exemple d’entraînement d’un modèle dans le concepteur Azure Machine Learning, consultez [Tutoriel : Prédire le prix de voitures avec le concepteur](tutorial-designer-automobile-price-train-score.md).

    ---

* Surveiller l’état d’une exécution

    # <a name="python"></a>[Python](#tab/python)
    
    * Obtenir l’état d’une exécution avec la méthode [`get_status()`](/python/api/azureml-core/azureml.core.run%28class%29#get-status--).
    
        ```python
        print(notebook_run.get_status())
        ```
    
    * Pour obtenir l’ID d’exécution, la durée d’exécution et des détails supplémentaires sur l’exécution, utilisez la méthode [`get_details()`](/python/api/azureml-core/azureml.core.workspace.workspace#get-details--).
    
        ```python
        print(notebook_run.get_details())
        ```
    
    * Lorsque votre exécution se termine correctement, utilisez la méthode [`complete()`](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) pour la marquer comme terminé.
    
        ```python
        notebook_run.complete()
        print(notebook_run.get_status())
        ```
    
    * Si vous utilisez le modèle de conception `with...as` de Python, l’exécution se marque automatiquement comme étant terminée quand l’exécution est en dehors de l’étendue. Vous n’avez pas besoin de marquer manuellement l’exécution comme étant terminée.
        
        ```python
        with exp.start_logging() as notebook_run:
            notebook_run.log(name="message", value="Hello from run!")
            print(notebook_run.get_status())
        
        print(notebook_run.get_status())
        ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    * Pour afficher une liste des exécutions de votre expérience, utilisez la commande suivante. Remplacez `experiment` par le nom de votre expérience :
    
        ```azurecli-interactive
        az ml run list --experiment-name experiment
        ```
    
        Cette commande renvoie un document JSON qui répertorie des informations sur les exécutions de cette expérience.
    
        Pour plus d’informations, consultez [az ml experiment list](/cli/azure/ml/experiment?preserve-view=true&view=azure-cli-latest#az_ml_experiment_list).
    
    * Pour afficher des informations sur une exécution spécifique, utilisez la commande suivante. Remplacez `runid` par l’ID d’exécution.
    
        ```azurecli-interactive
        az ml run show -r runid
        ```
    
        Cette commande renvoie un document JSON qui répertorie des informations sur l’exécution.
    
        Pour plus d’informations, consultez [az ml run show](/cli/azure/ml/run?preserve-view=true&view=azure-cli-latest#az_ml_run_show).
    
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    ---
## <a name="custom-view"></a>Vue personnalisée 
    
Pour afficher vos exécutions dans Studio : 
    
1. Accédez à l’onglet **Expériences**.
    
1. Sélectionnez soit **Toutes les expériences** pour afficher toutes les exécutions d’une expérience, soit **Toutes les exécutions** pour afficher toutes les exécutions soumises dans l’espace de travail.
    
Dans la page **Toutes les exécutions**, vous pouvez filtrer la liste des exécutions en fonction des étiquettes, des expériences, de la cible de calcul et d’autres valeurs. Cela vous permet de mieux organiser et définir l’étendue de votre travail.  
    
1. Personnalisez la page en sélectionnant les exécutions que vous souhaitez comparer, en ajoutant des graphiques ou en appliquant des filtres. Ces modifications peuvent être enregistrées sous la forme d’un **affichage personnalisé** afin que vous puissiez facilement revenir à votre travail. Les utilisateurs disposant d’autorisations d’espace de travail peuvent modifier ou consulter l’affichage personnalisé. Partagez également la vue personnalisée avec les membres de l’équipe pour une meilleure collaboration en sélectionnant **Partager l’affichage**.   

1. Pour afficher les journaux d’exécution, sélectionnez une exécution spécifique. Vous trouverez les journaux de diagnostic et d’erreurs de votre exécution sous l’onglet **Sorties + journaux**.    

:::image type="content" source="media/how-to-track-monitor-analyze-runs/custom-views.gif" alt-text="Capture d’écran : créer un affichage personnalisé":::
    
## <a name="run-description"></a>Description de l’exécution 

Une description de l’exécution peut être ajoutée à une exécution pour fournir davantage de contexte et d’informations relatifs à l’exécution. Vous pouvez également rechercher ces descriptions à partir de la liste des exécutions et ajouter la description de l’exécution en tant que colonne dans la liste des exécutions. 

Accédez à la page **Détails de l’exécution** de votre exécution, puis sélectionnez l’icône de modification ou en forme de crayon pour ajouter, modifier ou supprimer des descriptions pour votre exécution. Pour conserver les modifications apportées à la liste des exécutions, enregistrez-les dans votre vue personnalisée existante ou dans une nouvelle vue personnalisée. Le format Markdown est pris en charge pour les descriptions d’exécution, ce qui permet d’intégrer des images et de créer des liens profonds, comme indiqué ci-dessous.

:::image type="content" source="media/how-to-track-monitor-analyze-runs/run-description.gif" alt-text="Capture d’écran : créer une description de l’exécution"::: 

## <a name="tag-and-find-runs"></a>Identifier et rechercher des exécutions

Dans Azure Machine Learning, vous pouvez utiliser des balises et propriétés pour vous aider dans l’organisation et l’interrogation d’exécutions en vue de rechercher des informations importantes.

* Ajouter des propriétés et des balises

    # <a name="python"></a>[Python](#tab/python)
    
    Pour ajouter des métadonnées de recherche à vos exécutions, utilisez la méthode [`add_properties()`](/python/api/azureml-core/azureml.core.run%28class%29#add-properties-properties-). Par exemple, le code suivant ajoute la propriété `"author"` à l’exécution :
    
    ```Python
    local_run.add_properties({"author":"azureml-user"})
    print(local_run.get_properties())
    ```
    
    Les propriétés sont immuables et créent enregistrement permanent à des fins d’audit. L’exemple de code suivant génère une erreur, car nous avons déjà ajouté `"azureml-user"` comme valeur de propriété `"author"` dans le code précédent :
    
    ```Python
    try:
        local_run.add_properties({"author":"different-user"})
    except Exception as e:
        print(e)
    ```
    
    Contrairement aux propriétés, les étiquettes sont modifiables. Pour ajouter des informations consultables et ayant du sens pour les utilisateurs de votre expérience, utilisez la méthode [`tag()`](/python/api/azureml-core/azureml.core.run%28class%29#tag-key--value-none-).
    
    ```Python
    local_run.tag("quality", "great run")
    print(local_run.get_tags())
    
    local_run.tag("quality", "fantastic run")
    print(local_run.get_tags())
    ```
    
    Vous pouvez également ajouter des balises de chaîne simples. Quand ces étiquettes apparaissent dans le dictionnaire des étiquettes, leur valeur est `None`.
    
    ```Python
    local_run.tag("worth another look")
    print(local_run.get_tags())
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    > [!NOTE]
    > À l’aide de l’interface CLI, vous pouvez uniquement ajouter ou mettre à jour des balises.
    
    Utilisez la commande suivante pour ajouter ou mettre à jour une balise :
    
    ```azurecli-interactive
    az ml run update -r runid --add-tag quality='fantastic run'
    ```
    
    Pour plus d’informations, consultez [az ml run update](/cli/azure/ml/run?preserve-view=true&view=azure-cli-latest#az_ml_run_update).
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Vous pouvez ajouter, modifier ou supprimer des étiquettes d’exécution à partir de Studio. Accédez à la page **Détails de l’exécution** de votre exécution, puis sélectionnez l’icône de modification ou en forme de crayon pour ajouter, modifier ou supprimer des étiquettes pour votre exécution. Vous pouvez également effectuer une recherche ou appliquer un filtre sur ces étiquettes à partir de la page de liste des exécutions.
    
    :::image type="content" source="media/how-to-track-monitor-analyze-runs/run-tags.gif" alt-text="Capture d’écran : Ajouter, modifier ou supprimer des étiquettes d’exécution":::
    
    ---

* Interroger des propriétés et des balises

    Vous pouvez interroger des exécutions au sein d’une expérience pour renvoyer une liste d’exécutions qui correspondent aux propriétés et balises spécifiques.

    # <a name="python"></a>[Python](#tab/python)
    
    ```Python
    list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
    list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    L’interface de ligne de commande Azure prend en charge les requêtes [JMESPath](http://jmespath.org) qui peuvent être utilisées pour filtrer les exécutions en fonction des balises et des propriétés. Pour utiliser une requête JMESPath avec l’interface de ligne de commande Azure, spécifiez-la avec le paramètre `--query`. Les exemples suivants montrent des requêtes utilisant des balises et propriétés :
    
    ```azurecli-interactive
    # list runs where the author property = 'azureml-user'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user']
    # list runs where the tag contains a key that starts with 'worth another look'
    az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
    # list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
    ```
    
    Pour plus d’informations sur l’interrogation des résultats de l’interface de ligne de commande Azure, consultez [Interroger le résultat des commandes de l’interface de ligne de commande Azure](/cli/azure/query-azure-cli?preserve-view=true&view=azure-cli-latest).
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Pour rechercher des exécutions spécifiques, accédez à la liste **Toutes les exécutions**. À partir de là, vous avez le choix entre deux options :
    
    1. Utilisez le bouton **Ajouter un filtre** et sélectionnez Filtrer sur les étiquettes pour filtrer les exécutions en fonction de l’étiquette qui a été attribuée aux exécutions. <br><br>
    OR
    
    1. Utilisez la barre de recherche pour trouver rapidement des exécutions en faisant une recherche sur les métadonnées d’exécution comme l’état de l’exécution, les descriptions, les noms d’expériences et le nom de l’expéditeur. 
    
## <a name="cancel-or-fail-runs"></a>Annulation ou mise en échec d’exécutions

Si vous constatez une erreur ou si votre exécution prend trop de temps, vous pouvez l’annuler.

# <a name="python"></a>[Python](#tab/python)

Pour annuler une exécution à l’aide du Kit de développement logiciel (SDK), utilisez la méthode [`cancel()`](/python/api/azureml-core/azureml.core.run%28class%29#cancel--) :

```python
src = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_run = exp.submit(src)
print(local_run.get_status())

local_run.cancel()
print(local_run.get_status())
```

Si votre exécution se termine, mais contient une erreur (par exemple, utilisation d’un script d’entraînement incorrect), vous pouvez utiliser la méthode [`fail()`](/python/api/azureml-core/azureml.core.run%28class%29#fail-error-details-none--error-code-none---set-status-true-) la marquer comme ayant échoué.

```python
local_run = exp.submit(src)
local_run.fail()
print(local_run.get_status())
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour annuler une exécution à l’aide de l’interface de ligne de commande, utilisez la commande suivante : Remplacez `runid` par l’ID d’exécution.

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Pour plus d’informations, consultez [az ml run cancel](/cli/azure/ml/run?preserve-view=true&view=azure-cli-latest#az_ml_run_cancel).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Pour annuler une exécution dans le studio, procédez comme suit :

1. Accédez au pipeline en cours d’exécution dans la section **Expériences** ou **Pipelines**. 

1. Sélectionnez le numéro d’exécution du pipeline que vous souhaitez annuler.

1. Dans la barre d’outils, sélectionnez **Annuler**

---

## <a name="create-child-runs"></a>Créer des exécutions enfants

Créez des exécutions enfants pour regrouper les exécutions associées, comme pour différentes itérations de réglage des hyperparamètres.

> [!NOTE]
> Les exécutions enfants peuvent uniquement être créées à l’aide du Kit de développement logiciel (SDK).

Cet exemple de code utilise le script `hello_with_children.py` de création d’un lot de cinq exécutions enfants issues d’une exécution envoyée à l’aide de la méthode [`child_run()`](/python/api/azureml-core/azureml.core.run%28class%29#child-run-name-none--run-id-none--outputs-none-) :

```python
!more hello_with_children.py
src = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_run = exp.submit(src)
local_run.wait_for_completion(show_output=True)
print(local_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> Dès qu’elles sont hors de portée, les exécutions enfants sont automatiquement marquées comme étant terminées.

Pour créer efficacement de nombreuses exécutions enfants, utilisez la méthode [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-). Chaque création se traduisant par un appel réseau, la création d’un lot d’exécutions est plus efficace que la création d’exécutions une à une.

### <a name="submit-child-runs"></a>Envoyer des exécutions enfants

Les exécutions enfants peuvent également être envoyées à partir d’une exécution parente. Cela vous permet de créer des hiérarchies d’exécutions parentes et enfants. Vous ne pouvez pas créer une exécution enfant sans parent : même si l’exécution parente ne fait rien d’autre que de lancer des exécutions enfants, il est toujours nécessaire de créer la hiérarchie. Les états de toutes les exécutions sont indépendants : un parent peut afficher l’état de réussite `"Completed"` même si une ou plusieurs exécutions enfants ont été annulées ou ont échoué.  

Vous pouvez souhaiter que vos exécutions enfants utilisent une configuration d’exécution différente de celle de l’exécution parente. Par exemple, vous pouvez utiliser une configuration basée sur l’UC et moins puissante pour le parent, tout en utilisant des configurations basées sur le GPU pour les enfants. Un autre souhait courant consiste à passer à chaque enfant des données et des arguments différents. Pour personnaliser une exécution enfant, créez un objet `ScriptRunConfig` pour l’exécution enfant. 

> [!IMPORTANT]
> Pour soumettre une exécution enfant à partir d’une exécution parente sur un ordinateur distant, vous devez d’abord vous connecter à l’espace de travail dans le code de l’exécution parente. Par défaut, l’objet de contexte d’exécution d’une exécution distante ne dispose pas d’informations d’identification permettant de soumettre des exécutions enfants. Utilisez un principal de service ou les informations d’identification d’une identité managée pour vous connecter. Pour plus d’informations sur l’authentification, consultez [Configurer l’authentification](how-to-setup-authentication.md).

Le code ci-dessous :

- Récupère une ressource de calcul nommée `"gpu-cluster"` dans l’espace de travail `ws`
- Itère sur différentes valeurs d’argument à passer aux objets `ScriptRunConfig` des enfants
- Crée et envoie une nouvelle exécution enfant à l’aide de la ressource de calcul personnalisée et de l’argument
- Bloque jusqu’à ce que toutes les exécutions enfants soient terminées

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig

compute_target = ws.compute_targets["gpu-cluster"]

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], compute_target=compute_target)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

Pour créer efficacement de nombreuses exécutions enfants avec des configurations, des arguments et des entrées identiques, utilisez la méthode [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-). Chaque création se traduisant par un appel réseau, la création d’un lot d’exécutions est plus efficace que la création d’exécutions une à une.

Dans une exécution enfant, vous pouvez afficher l’ID d’exécution parente :

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Interroger les exécutions enfants

Pour interroger les exécutions enfants d’un parent spécifique, utilisez la méthode [`get_children()`](/python/api/azureml-core/azureml.core.run%28class%29#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-). L’argument ``recursive = True`` vous permet d’interroger une arborescence imbriquée d’enfants et de petits-enfants.

```python
print(parent_run.get_children())
```

### <a name="log-to-parent-or-root-run"></a>Connexion à l’exécution parente ou racine

Vous pouvez utiliser le champ `Run.parent` pour accéder à l’exécution qui a lancé l’exécution enfant actuelle. `Run.parent` est souvent utilisé pour regrouper les résultats des journaux en un seul endroit. Les exécutions enfants s’exécutent de façon asynchrone et il n’y a aucune garantie de classement ou de synchronisation au-delà de la capacité du parent à attendre la fin de l’exécution de ses enfants.

```python
# in child (or even grandchild) run

def root_run(self : Run) -> Run :
    if self.parent is None : 
        return self
    return root_run(self.parent)

current_child_run = Run.get_context()
root_run(current_child_run).log("MyMetric", f"Data from child run {current_child_run.id}")

```

## <a name="monitor-the-run-status-by-email-notification"></a>Analyse de l’état d’exécution par notification par e-mail

1. Dans le [portail Azure](https://ms.portal.azure.com/), dans la barre de navigation de gauche, sélectionnez l’onglet **Analyse**. 

1. Sélectionnez **Paramètres de diagnostic**, puis sélectionnez **+Ajouter un paramètre de diagnostic**.

    ![Capture d’écran des paramètres de diagnostic pour la notification par e-mail](./media/how-to-track-monitor-analyze-runs/diagnostic-setting.png)

1. Dans le paramètre de diagnostic : 
    1. Sous **Détails de la catégorie**, sélectionnez **AmlRunStatusChangedEvent**. 
    1. Dans **Détails de la destination**, sélectionnez **Envoyer à l’espace de travail Log Analytics** et spécifiez l’**abonnement** et l’**espace de travail Log Analytics**. 

    > [!NOTE]
    > L’**espace de travail Azure Log Analytics** est un type de ressource Azure différent de celui de l’**espace de travail Azure Machine Learning service**. S’il n’existe aucune option dans cette liste, vous pouvez [créer un espace de travail Log Analytics](../azure-monitor/logs/quick-create-workspace.md). 
    
    ![Emplacement où est enregistrée la notification par e-mail](./media/how-to-track-monitor-analyze-runs/log-location.png)

1. Dans l’onglet **Journaux**, ajoutez une **nouvelle règle d’alerte**. 

    ![Nouvelle règle d’alerte](./media/how-to-track-monitor-analyze-runs/new-alert-rule.png)

1. Découvrez [comment créer et gérer les alertes de journal à l’aide d’Azure Monitor](../azure-monitor/alerts/alerts-log.md).

## <a name="example-notebooks"></a>Exemples de notebooks

Les notebooks suivants illustrent les concepts de cet article :

* Pour en savoir plus sur les API de journalisation, consultez [Notebook de l’API de journalisation](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb).

* Pour plus d’informations sur la gestion des exécutions avec le Kit de développement logiciel (SDK) Azure Machine Learning, consultez le [notebook sur la gestion des exécutions](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb).

## <a name="next-steps"></a>Étapes suivantes

* Pour savoir comment enregistrer les mesures de vos expériences, consultez [Enregistrer des métriques pendant les exécutions d’entraînement](how-to-log-view-metrics.md).
* Pour découvrir comment superviser les ressources et les journaux dans Azure Machine Learning, consultez [Supervision d’Azure Machine Learning](monitor-azure-machine-learning.md).
