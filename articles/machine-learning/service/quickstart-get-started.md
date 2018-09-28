---
title: 'Démarrage rapide : créer un espace de travail d’apprentissage automatique - Azure'
description: Utilisez le Portail Azure pour créer un espace de travail Azure Machine Learning. Dans le cloud, cet espace de travail est le socle permettant l’expérimentation, l’entraînement et le déploiement de modèles d’apprentissage automatique avec le service Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: rastala
ms.author: roastala
ms.date: 09/24/2018
ms.openlocfilehash: c1610291e06255e2c724268f63d740f7e4debea4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46959986"
---
# <a name="quickstart-get-started-with-azure-machine-learning-service"></a>Démarrage rapide : bien démarrer avec le service Azure Machine Learning

Dans ce guide de démarrage rapide, vous allez utiliser le Portail Azure pour créer un espace de travail Azure Machine Learning. Dans le cloud, cet espace de travail est le socle permettant l’expérimentation, l’entraînement et le déploiement de modèles d’apprentissage automatique avec le service Azure Machine Learning. 

Ce didacticiel présente les procédures suivantes :

* Créer un espace de travail dans votre abonnement Azure
* Essayer cet espace de travail avec Python dans un bloc-notes Azure et journaliser les valeurs sur plusieurs itérations
* Afficher les valeurs journalisées dans votre espace de travail

Pour des raisons pratiques, les ressources Azure suivantes sont automatiquement ajoutées à votre espace de travail, si elles sont disponibles dans votre région : [Container Registry](https://azure.microsoft.com/services/container-registry/), [Stockage](https://azure.microsoft.com/services/storage/), [Application Insights](https://azure.microsoft.com/services/application-insights/) et [Key Vault](https://azure.microsoft.com/services/key-vault/).

Les ressources que vous créez peuvent être utilisées comme prérequis dans d’autres tutoriels et guides pratiques Azure Machine Learning. À l’instar d’autres services Azure, certaines ressources associées au service Azure Machine Learning présentent des limites (comme la taille de cluster  BatchAI). Veuillez lire [cet](how-to-manage-quotas.md) article sur les limites par défaut et comme demander un quota plus élevé.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.


## <a name="create-a-workspace"></a>Créer un espace de travail 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Dans la page de l’espace de travail, cliquez sur `Explore your Azure Machine Learning Workspace`

 ![explorer l’espace de travail](./media/quickstart-get-started/explore_aml.png)


## <a name="use-the-workspace"></a>Utiliser l'espace de travail

Voyons maintenant comme un espace de travail vous permet de gérer vos scripts d’apprentissage automatique. Dans cette section, vous allez :

* Ouvrir un bloc-notes dans Azure Notebooks
* Exécuter le code qui crée certaines valeurs journalisées
* Afficher les valeurs journalisées dans votre espace de travail

Il s’agit d’un exemple montrant comment l’espace de travail peut vous aider à suivre les informations générées dans un script. 

### <a name="open-a-notebook"></a>Ouvrir un bloc-notes 

Azure Notebooks offre une plateforme cloud gratuite pour les blocs-notes Jupyter, préconfigurés avec tout ce dont vous avez besoin pour exécuter le service Azure Machine Learning.  

Cliquez sur le bouton `Open Azure Notebooks` pour réaliser votre première expérience.

 ![Lancer Azure Notebooks](./media/quickstart-get-started/explore_ws.png)

Une fois que vous êtes connecté, un nouvel onglet s’ouvre et une invite `Clone Library` s’affiche.  Cliquez sur `Clone`.


### <a name="run-the-notebook"></a>Exécuter le bloc-notes

En plus des deux bloc-notes, vous verrez un fichier `config.json`.  Ce fichier de configuration contient des informations sur l’espace de travail que vous venez de créer.  

Cliquez sur `01.run-experiment.ipynb` pour ouvrir le bloc-notes.

Vous pouvez exécuter les cellules une par une en utilisant `Shift` + `Enter`.  Ou utilisez le menu `Cells` > `Run All` pour exécuter le bloc-notes entier.

Vous pouvez être invité à vous connecter.  Copiez le code du message, puis cliquez sur le lien et collez le code dans la nouvelle fenêtre.  Veillez à ne pas copier un espace avant ou après le code.

 ![se connecter](./media/quickstart-get-started/login.png)

Dans le bloc-notes, la deuxième cellule lit le contenu de `config.json` afin de vous connecter à votre espace de travail.
```
ws = Workspace.from_config()
```

La troisième cellule de code démarre une expérience avec le nom « my-first-experience ».  Vous utiliserez ce nom pour rechercher des informations sur l’exécution une fois revenu dans votre espace de travail.

```
experiment = Experiment(workspace_object=ws, name = "my-first-experiment")
```

Dans la dernière cellule du bloc-notes, notez les valeurs qui sont écrites dans un fichier journal.

```
# Log final results
run.log("Final estimate: ",pi_estimate)
run.log("Final error: ",math.pi-pi_estimate)
```

Une fois le code exécuté, vous pouvez afficher ces valeurs dans votre espace de travail.

## <a name="view-logged-values"></a>Afficher les valeurs journalisées

Une fois toutes les cellules du bloc-notes terminées, revenez à la page du portail.  

Cliquez sur `View Experiments`.

![afficher les expériences](./media/quickstart-get-started/view_exp.png)

Fermez la fenêtre contextuelle `Reports`.

Cliquez sur `my-first-experiment`.

Consultez des informations sur l’exécution que vous venez de réaliser.  Faites défiler la page pour trouver le tableau des exécutions, puis cliquez sur le lien du numéro d’exécution.

 ![liste Historique des exécutions](./media/quickstart-get-started/report.png)

Vous voyez les tracés qui ont été créées automatiquement à partir des valeurs journalisées :

   ![afficher l'historique](./media/quickstart-get-started/plots.png)

## <a name="clean-up-resources"></a>Supprimer des ressources 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Vous pouvez également conserver le groupe de ressources, mais supprimer un espace de travail individuel en affichant les propriétés de l’espace de travail, puis en sélectionnant le bouton Supprimer.

## <a name="next-steps"></a>Étapes suivantes

Vous venez de créer les ressources nécessaires pour commencer à expérimenter et à déployer des modèles. Vous avez également exécuté du code dans un bloc-notes et exploré l’historique des exécutions de ce code dans votre espace de travail dans le cloud.

Pour une expérience approfondie du flux de travail, suivez les tutoriels Azure Machine Learning relatifs à l’entraînement et au déploiement d’un modèle.  

> [!div class="nextstepaction"]
> [Tutoriel : entraîner un modèle de classification d’images](tutorial-train-models-with-aml.md)
