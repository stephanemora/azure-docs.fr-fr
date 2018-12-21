---
title: Démarrage rapide via le Portail Azure
titleSuffix: Azure Machine Learning service
description: Prise en main d’Azure Machine Learning service. Utilisez le Portail Azure pour créer un espace de travail, qui est le socle dans le cloud que vous utilisez pour expérimenter, effectuer l’apprentissage et déployer des modèles Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: c215a3947069e572f73692ea54010c77d78ae4fd
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273559"
---
# <a name="quickstart-use-the-azure-portal-to-get-started-with-azure-machine-learning"></a>Démarrage rapide : Utiliser le Portail Azure pour prendre en main Azure Machine Learning

Dans ce démarrage rapide, vous utilisez le Portail Azure pour créer un espace de travail Azure Machine Learning. Dans le cloud, cet espace de travail est le socle que vous utilisez pour expérimenter, effectuer l’apprentissage et déployer des modèles Machine Learning avec Machine Learning. Ce démarrage rapide utilise des ressources cloud et ne requiert aucune installation. Pour configurer votre propre serveur Jupyter Notebook à la place, consultez [Démarrage rapide : Utilisation de Python pour démarrer avec Azure Machine Learning](quickstart-create-workspace-with-python.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2F9Ad]

Dans ce guide de démarrage rapide, vous allez :

* Créer un espace de travail dans votre abonnement Azure.
* Essayer cet espace de travail avec Python dans un bloc-notes Azure et journaliser les valeurs sur plusieurs itérations.
* Afficher les valeurs journalisées dans votre espace de travail.

Les ressources Azure suivantes sont automatiquement ajoutées à votre espace de travail lorsqu’elles sont disponibles au niveau régional :

  - [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
  - [Stockage Azure](https://azure.microsoft.com/services/storage/)
  - [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
  - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

Les ressources que vous créez peuvent être utilisées comme prérequis dans d’autres didacticiels et guides pratiques du service Machine Learning. Comme avec d’autres services Azure, il existe des limites concernant certaines ressources associées à Machine Learning, telles que la taille de cluster de calcul. Apprenez-en davantage sur [les limites par défaut et comment augmenter votre quota](how-to-manage-quotas.md).

Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning service](http://aka.ms/AMLFree) dès aujourd’hui.


## <a name="create-a-workspace"></a>Créer un espace de travail 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Sur la page de l’espace de travail, sélectionnez `Explore your Azure Machine Learning service workspace`.

 ![Explorer l’espace de travail](./media/quickstart-get-started/explore_aml.png)


## <a name="use-the-workspace"></a>Utiliser l'espace de travail

Voyons maintenant comme un espace de travail vous permet de gérer vos scripts d’apprentissage automatique. Dans cette section, vous allez :

* Ouvrir un bloc-notes dans Azure Notebooks.
* Exécuter le code qui crée certaines valeurs journalisées.
* Afficher les valeurs journalisées dans votre espace de travail.

Cet exemple montre comment l’espace de travail peut vous aider à suivre les informations générées dans un script. 

### <a name="open-a-notebook"></a>Ouvrir un bloc-notes 

Azure Notebooks offre une plateforme cloud gratuite pour les blocs-notes Jupyter, préconfigurés avec tout ce dont vous avez besoin pour exécuter Machine Learning.  

Sélectionnez `Open Azure Notebooks` pour tester votre première expérience.

 ![Ouvrir Azure Notebooks](./media/quickstart-get-started/explore_ws.png)

Votre organisation peut exiger le [consentement de l’administrateur](https://notebooks.azure.com/help/signing-up/work-or-school-account/admin-consent) avant que vous puissiez vous connecter.

Une fois que vous êtes connecté, un nouvel onglet s’ouvre et une invite `Clone Library` s’affiche. Sélectionnez `Clone`.


### <a name="run-the-notebook"></a>Exécuter le bloc-notes

En plus des deux blocs-notes, vous voyez un fichier `config.json`. Ce fichier config contient des informations sur l’espace de travail que vous venez de créer.  

Sélectionnez `01.run-experiment.ipynb` pour ouvrir le bloc-notes.

Pour exécuter les cellules une par une, utilisez `Shift`+`Enter`. Ou sélectionnez `Cells` > `Run All` pour exécuter tout le bloc-notes. Lorsque vous voyez un astérisque [*] en regard d’une cellule, cela signifie qu’elle est en cours d’exécution. À la fin de l’exécution du code de cette cellule, un numéro s’affiche. 

Une fois que vous avez exécuté toutes les cellules dans le notebook, vous pouvez afficher les valeurs enregistrées dans votre espace de travail.

## <a name="view-logged-values"></a>Afficher les valeurs journalisées

Une fois toutes les cellules du bloc-notes exécutées, revenez à la page du portail.  

Sélectionnez `View Experiments`.

![Afficher les expériences](./media/quickstart-get-started/view_exp.png)

Fermez la fenêtre contextuelle `Reports`.

Sélectionnez `my-first-experiment`.

Consultez des informations sur l’exécution que vous venez de réaliser. Faites défiler la page vers le bas pour trouver la table des exécutions. Sélectionnez le lien du numéro de l’exécution.

 ![Lien Historique des exécutions](./media/quickstart-get-started/report.png)

Vous voyez les tracés qui ont été créés automatiquement à partir des valeurs journalisées. Chaque fois que vous consignez plusieurs valeurs avec le même paramètre de nom, un tracé est généré automatiquement pour vous.

   ![Afficher l’historique](./media/quickstart-get-started/plots.png)

Étant donné que le code pour se rapprocher de pi utilise des valeurs aléatoires, vos tracés affichent des valeurs différentes.  

## <a name="clean-up-resources"></a>Supprimer des ressources 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Vous pouvez également conserver le groupe de ressources mais supprimer un espace de travail unique. Affichez les propriétés de l’espace de travail, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé les ressources nécessaires pour expérimenter des modèles et les déployer. Vous avez également exécuté du code dans un bloc-notes. Enfin, vous avez exploré l’historique des exécutions de ce code dans votre espace de travail dans le cloud.

Pour une expérience approfondie du flux de travail, suivez les didacticiels Machine Learning relatifs à l’apprentissage et au déploiement d’un modèle.  

> [!div class="nextstepaction"]
> [Tutoriel : Entraîner un modèle de classification d’images](tutorial-train-models-with-aml.md)
