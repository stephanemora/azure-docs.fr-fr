---
title: Utiliser des notebooks avec Azure Sentinel pour la chasse sécuritaire
description: Cet article décrit comment utiliser des notebooks avec les fonctionnalités de recherche Azure Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 06/24/2021
ms.openlocfilehash: 7b1202648db3aed73ea040fb60610879a6816dd9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532084"
---
# <a name="use-jupyter-notebook-to-hunt-for-security-threats"></a>Utiliser Jupyter Notebook pour rechercher des menaces de sécurité

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Azure Sentinel repose sur le magasin de données. Il associe des capacités d’interrogation hautes performances, un schéma dynamique et s’adapte aux volumes de données importants. Le portail Azure et tous les outils Azure Sentinel utilisent une API commune pour accéder à ce magasin de données. La même API est également disponible pour les outils externes tels que les notebooks [Jupyter](https://jupyter.org/) et Python. Bien que de nombreuses tâches courantes puissent être effectuées dans le portail, Jupyter étend l’étendue de ce que vous pouvez faire avec ces données. Il combine une programmabilité totale à une vaste collection de bibliothèques pour l’apprentissage automatique, la visualisation et l’analyse des données. Ces attributs transforment Jupyter en un outil essentiel pour les scénarios de recherche et d’enquête relatifs à la sécurité.

Par exemple, utilisez les notebooks pour :

- Exécuter des analyses qui ne sont pas intégrées à Azure Sentinel, telles que certaines fonctionnalités de machine learning python
- Créer des visualisations de données qui ne sont pas intégrées à Azure Sentinel, telles que des chronologies personnalisées et des arborescences de processus
- Intégrer des sources de données en dehors d’Azure Sentinel, telles qu’un jeu de données local

Nous avons intégré l’expérience de Jupyter dans le portail Azure , pour que vous puissiez créer et exécuter facilement des notebooks pour analyser vos données. La bibliothèque *Kqlmagic* fournit le ciment qui vous permet d’extraire des requêtes d’Azure Sentinel et de les exécuter directement à l’intérieur d’un notebook. Les requêtes utilisent le [langage de requête Kusto](https://kusto.azurewebsites.net/docs/kusto/query/index.html). Plusieurs notebooks, développés par certains des analystes de sécurité de Microsoft, sont intégrés à Azure Sentinel. Quelques-uns d’entre eux sont conçus pour un scénario spécifique et peuvent être utilisés en tant que tel. D’autres sont des exemples pour illustrer les techniques et les fonctionnalités que vous pouvez copier ou adapter pour les utiliser dans vos propres notebooks. D’autres blocs-notes peuvent également être importés à partir du GitHub de la communauté Azure Sentinel.

L’expérience Jupyter intégrée utilise [Azure Notebooks](https://notebooks.azure.com/) pour stocker, partager et exécuter des notebooks. Vous pouvez également exécuter ces notebooks localement (si vous avez un environnement Python et Jupyter sur votre ordinateur) ou dans d’autres environnements JupyterHub tels qu’Azure Databricks.


## <a name="notebook-components"></a>Composants Notebook

Notebooks a deux composants :
- **L’interface basée sur un navigateur** où vous entrez et exécutez des requêtes et du code, et où les résultats de l’exécution sont affichés.
- Un **noyau* qui est responsable de l’analyse et de l’exécution du code.

Le noyau du bloc-notes Azure Sentinel s’exécute sur une machine virtuelle Azure. Plusieurs options de licence existent pour tirer parti de machines virtuelles plus puissantes si vos blocs-notes incluent des modèles d’apprentissage automatique complexes.

Les notebooks Azure Sentinel utilisent de nombreuses bibliothèques Python populaires telles que *pandas*, *matplotlib*, *bokeh* et bien d’autres. Vous avez le choix entre un grand nombre d’autres packages Python, couvrant des domaines tels que ceux énoncés ci-dessous :

- visualisations et graphiques
- traitement de données et analyse
- statistiques et calculs numériques
- Machine Learning et Deep Learning

Nous avons également publié des outils de sécurité open source Jupyter dans un package nommé [msticpy](https://github.com/Microsoft/msticpy/). Ce package est utilisé dans la plupart des notebooks inclus. Les outils de msticpy sont conçus spécialement pour faciliter la création de notebooks pour la recherche et l’investigation, et nous travaillons activement pour vous proposer de nouvelles fonctionnalités et améliorations. Pour plus d’informations, consultez la [documentation des outils de sécurité MSTIC Jupyter et Python](https://msticpy.readthedocs.io/).

Le [référentiel GitHub de la communauté Azure Sentinel](https://github.com/Azure/Azure-Sentinel) est l’emplacement de tous les futurs notebooks Azure Sentinel créés par Microsoft ou fournis par la communauté.


## <a name="manage-access-to-azure-sentinel-notebooks"></a>Gérer l’accès aux notebooks Azure Sentinel

Pour utiliser les notebooks, vous devez d’abord disposer des autorisations appropriées, en fonction de votre rôle d’utilisateur.

Comme les notebooks Azure Sentinel s’exécutent sur la plateforme [Azure Machine Learning](../machine-learning/overview-what-is-azure-machine-learning.md) (Azure ML), vous devez disposer d’un accès approprié à l’espace de travail Azure Sentinel et à un [espace de travail Azure ML](../machine-learning/concept-workspace.md).

|Autorisation  |Description  |
|---------|---------|
|**Autorisations Azure Sentinel**     |   À l’instar des autres ressources Azure Sentinel, pour accéder aux notebooks sur les notebooks Azure Sentinel, un lecteur Azure Sentinel, un répondeur Azure Sentinel ou un rôle Contributeur Azure Sentinel est requis. <br><br>Pour plus d’informations, consultez [Autorisations dans Azure Sentinel](roles.md).|
|**Autorisations d’Azure Machine Learning**     | Un espace de travail Azure Machine Learning est une ressource Azure. Comme toute autre ressource Azure, la création d'un espace de travail Azure Machine Learning s'accompagne de rôles par défaut. Vous pouvez ajouter des utilisateurs à l’espace de travail et leur attribuer un de ces rôles intégrés. Pour plus d’informations, voir [Rôles par défaut Azure Machine Learning](../machine-learning/how-to-assign-roles.md) et [Rôles intégrés Azure](../role-based-access-control/built-in-roles.md). <br><br>   **Important** : L'accès en fonction du rôle peut être limité à plusieurs niveaux dans Azure. Par exemple, un utilisateur disposant d’un accès propriétaire à un espace de travail peut ne pas disposer d'un accès propriétaire à un groupe de ressources contenu dans cet espace de travail. Pour plus d’informations, consultez [Fonctionnement du contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../role-based-access-control/overview.md). <br><br>Si vous êtes propriétaire d’un espace de travail Azure ML, vous pouvez ajouter et supprimer des rôles pour cet espace de travail et assigner des rôles aux utilisateurs. Pour plus d'informations, consultez les pages suivantes :<br>    - [Portail Azure](../role-based-access-control/role-assignments-portal.md)<br>    - [PowerShell](../role-based-access-control/role-assignments-powershell.md)<br>    - [Interface de ligne de commande Azure](../role-based-access-control/role-assignments-cli.md)<br>   - [REST API](../role-based-access-control/role-assignments-rest.md)<br>    - [Modèles Azure Resource Manager](../role-based-access-control/role-assignments-template.md)<br> - [Interface CLI Azure Machine Learning](../machine-learning/how-to-assign-roles.md#manage-workspace-access)<br><br>Si les rôles intégrés ne suffisent pas, vous pouvez également créer des rôles personnalisés. Les rôles personnalisés peuvent disposer d'autorisations en lecture, écriture, suppression et calcul dans cet espace de travail. Vous pouvez rendre le rôle disponible au niveau d’un espace de travail spécifique, d’un groupe de ressources spécifique ou d’un abonnement spécifique. Pour plus d'informations, consultez [Créer un rôle personnalisé](../machine-learning/how-to-assign-roles.md#create-custom-role). |
|     |         |


## <a name="create-an-azure-ml-workspace-from-azure-sentinel"></a>Créer un espace de travail azure ML à partir d’Azure Sentinel

Cette procédure décrit comment créer un espace de travail Azure ML à partir d’Azure Sentinel pour vos notebooks Azure Sentinel.

**Pour créer votre espace de travail** :

1. À partir du portail Azure, accédez à **Azure Sentinel** > **Gestion des menaces** > **Notebooks**, puis sélectionnez **Créer un espace de travail AML**.

1. Entrez les détails suivants et sélectionnez **Suivant**.

    |Champ|Description|
    |--|--|
    |**Abonnement**|Sélectionnez l’abonnement Azure que vous souhaitez utiliser.|
    |**Groupe de ressources**|Utilisez un groupe de ressources existant dans votre abonnement, ou entrez un nom pour créer un groupe de ressources. Un groupe de ressources contient les ressources associées d’une solution Azure.|
    |**Nom de l’espace de travail**|Entrez un nom unique qui identifie votre espace de travail. Dans le groupe de ressources, les noms doivent être uniques. Utilisez un nom dont il est facile de se rappeler et que vous pouvez facilement différencier des autres espaces de travail.|
    |**Région**|Sélectionnez l’emplacement le plus proche de vos utilisateurs et des ressources de données pour créer votre espace de travail.|
    |**Compte de stockage**| Un compte de stockage est utilisé comme magasin de données par défaut pour l’espace de travail. Vous pouvez créer une ressource de stockage Azure ou en sélectionner une existante dans votre abonnement.|
    |**KeyVault**| Un coffre de clés est utilisé pour stocker les secrets et autres informations sensibles nécessaires à l’espace de travail. Vous pouvez créer une ressource Azure Key Vault ou en sélectionner une existante dans votre abonnement.|
    |**Application Insights**| L’espace de travail utilise Azure Application Insights pour stocker des informations de surveillance sur vos modèles déployés. Vous pouvez créer une ressource Azure Application Insights ou en sélectionner une existante dans votre abonnement.|
    |**Registre de conteneurs**| Un registre de conteneurs est utilisé pour enregistrer les images du docker utilisées dans les formations et les déploiements. Pour réduire les coûts, une ressource Azure Container Registry est créée uniquement après la création de votre première image. Vous pouvez également choisir de créer la ressource maintenant ou d’en sélectionner une existante dans votre abonnement, ou de sélectionner **Aucun** si vous ne souhaitez pas utiliser un registre de conteneurs.|
    | | |

1. Sous l’onglet **Mise en réseau**, indiquez si vous souhaitez connecter votre espace de travail à l’aide d’un point de terminaison public ou un point de terminaison privé que vous configurez. Si votre espace de travail Azure Sentinel possède un point de terminaison public, nous vous recommandons d’utiliser un point de terminaison public pour votre espace de travail Azure ML afin d’éviter des problèmes potentiels dans la communication réseau. Quand vous avez terminé, sélectionnez **Vérifier + créer**.

1. Dans l’onglet **Vérifier + créer**, passez en revue les informations pour vérifier qu’elles sont correctes, puis sélectionnez **Créer** pour démarrer le déploiement de votre espace de travail. Par exemple :

    :::image type="content" source="media/notebooks/machine-learning-create-last-step.png" alt-text="Vérifier + créer votre espace de travail Machine Learning à partir d’Azure Sentinel.":::

    La création de votre espace de travail dans le cloud peut prendre plusieurs minutes. Pendant ce temps, la page **Vue d’ensemble** de l’espace de travail affiche l’état actuel du déploiement et se met à jour une fois le déploiement terminé.

1. Une fois votre déploiement terminé, vous pouvez revenir aux **notebooks** Azure Sentinel et lancer des notebooks à partir de votre nouvel espace de travail Azure ML.

    Si vous disposez de plusieurs notebooks, veillez à sélectionner un espace de travail AML par défaut à utiliser lors du lancement de vos notebooks. Par exemple :

    :::image type="content" source="media/notebooks/default-machine-learning.png" alt-text="Sélectionnez un espace de travail AML par défaut pour vos notebooks.":::


## <a name="launch-a-notebook-in-your-azure-ml-workspace"></a>Lancer un notebook à l’aide de votre espace de travail Azure ML

Une fois que vous avez créé un espace de travail AML, commencez à lancer vos notebooks dans votre espace de travail Azure ML à partir d’Azure Sentinel.

**Pour lancer votre notebook** :

1. À partir du portail Azure, accédez à **Azure Sentinel** > **Gestion des menaces** > **Notebooks**, où vous pouvez voir les notebooks fournis par Azure Sentinel.

    > [!TIP]
    > En haut de la page **Notebooks**, sélectionnez **Guides & commentaires** pour afficher plus de ressources et plus d’aide sur un voler sur la droite.

1. Sélectionnez un notebook pour afficher sa description, les types de données requis et les sources de données.

    Lorsque vous avez trouvé le notebook que vous souhaitez utiliser, sélectionnez **Enregistrer le notebook** pour le cloner dans votre propre espace de travail.

    Changez le nom selon les besoins. Si le notebook existe déjà dans votre espace de travail, vous avez la possibilité de le remplacer pour en créer un nouveau.

    :::image type="content" source="media/notebooks/save-notebook.png" alt-text="Enregistrez un bloc-notes pour le cloner dans votre propre espace de travail.":::

1. Une fois le notebook enregistré, le bouton **Enregistrer le notebook** est remplacé par le bouton **Lancer le notebook**. Sélectionnez **Lancer le notebook** pour l’ouvrir dans votre espace de travail AML.

    Par exemple :

    :::image type="content" source="media/notebooks/sentinel-notebooks-on-machine-learning.png" alt-text="Lancez votre notebook dans votre espace de travail AML.":::

1. En haut de la page, sélectionnez une instance de **calcul** à utiliser pour votre serveur de notebook.

    Si vous n’avez pas d’instance de calcul, [créez-en une](../machine-learning/how-to-create-manage-compute-instance.md?tabs=#use-the-script-in-the-studio). Si votre instance de calcul est arrêtée, assurez-vous de la démarrer. Pour plus d’informations, consultez [Exécuter un notebook dans Azure Machine Learning Studio](../machine-learning/how-to-run-jupyter-notebooks.md).

    Vous seul pouvez voir et utiliser les instances de calcul que vous créez. Vos fichiers utilisateur sont stockés séparément de la machine virtuelle, et partagés entre toutes les instances de calcul dans l’espace de travail.

    > [!TIP]
    > Si vous créez une nouvelle instance de calcul afin de tester vos notebooks, créez votre instance de calcul avec la catégorie **Usage général**.

1. Une fois votre serveur de notebook créé et démarré, vous pouvez commencer à exécuter les cellules de votre notebook. Dans chaque cellule, sélectionnez l’icône **Exécuter** pour exécuter le code de votre notebook.

    Pour plus d’informations, consultez [Raccourcis du mode de commande](../machine-learning/how-to-run-jupyter-notebooks.md).

1. Si vous devez redémarrer le noyau de notebook et réexécuter les cellules de notebook depuis le début, sélectionnez **Opérations de noyau** > **Redémarrer le noyau**. Par exemple :

    :::image type="content" source="media/notebooks/sentinel-notebooks-restart-kernel.png" alt-text="Redémarrez un noyau de notebook.":::

## <a name="troubleshooting"></a>Résolution des problèmes

Si vous rencontrez des problèmes avec vos notebooks, reportez-vous à la [résolution des problèmes de notebook Azure Machine Learning](../machine-learning/how-to-run-jupyter-notebooks.md#troubleshooting).


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur l’utilisation des notebooks dans la recherche et l’investigation des menaces, explorez certains modèles de notebook (par exemple, [**Analyse des informations d’identification dans Azure Log Analytics**](https://www.youtube.com/watch?v=OWjXee8o04M) et **Investigation guidée-traiter les alertes**. 

    Vous trouverez des modèles de notebook dans l’onglet Azure Sentinel > **Notebooks** > **Modèles**.

- Vous trouverez plus d’informations dans le [dépôt GitHub Azure Sentinel](https://github.com/Azure/Azure-Sentinel-Notebooks) :

    - Le répertoire [**Sample-Notebooks**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks) comprend des exemples de notebook enregistrés avec des données que vous pouvez utiliser pour afficher la sortie prévue.

    - Le répertoire [**Procédures**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos) comprend des notebooks qui décrivent des concepts tels que la définition de votre version de Python par défaut, la création de signets Azure Sentinel à partir d’un notebook, et bien plus encore.

    Pour plus d’informations, reportez-vous au guide [Premiers pas avec Azure ML Notebooks et Azure Sentinel](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/A%20Getting%20Started%20Guide%20For%20Azure%20Sentinel%20ML%20Notebooks.ipynb).

> [!NOTE]
> Les notebooks partagés dans le [référentiel GitHub Azure Sentinel](https://github.com/Azure/Azure-Sentinel-Notebooks) sont conçus comme des outils, illustrations et exemples de code utiles que vous pouvez utiliser lors du développement de vos propres notebooks.
>
> Nous apprécions les commentaires, qu’il s’agisse de suggestions, de demandes de fonctionnalités, de contribution aux notebooks, de signalements de bogues ou d’améliorations et d’ajouts aux notebooks existants. Rendez-vous sur le [GitHub de la communauté Azure Sentinel](https://github.com/Azure/Azure-Sentinel) pour créer un problème ou dupliquer et charger une contribution.
>

Pour plus d'informations, consultez les pages suivantes :

- [Webinaire : notions de base du notebook Azure Sentinel](https://www.youtube.com/watch?v=rewdNeX6H94)
- [Didacticiel : Notebooks Azure Sentinel - Bien démarrer](https://www.youtube.com/results?search_query=azazure+sentinel+notebooks)
- [Hunt for threats with in Azure Sentinel Preview](hunting.md) (Rechercher des menaces dans Azure Sentinel en préversion)
- [Keep track of data during hunting](bookmarks.md) (Suivre des données pendant la recherche)
- [Jupyter, Msticpy et Azure Sentinel](https://msticpy.readthedocs.io/en/latest/getting_started/JupyterAndAzureSentinel.html)
- [Didacticiel : modifier et exécuter des notebooks Jupyter sans quitter Azure ML Studio](https://www.youtube.com/watch?v=AAj-Fz0uCNk)
