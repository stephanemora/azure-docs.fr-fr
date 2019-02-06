---
title: FAQ sur Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: 'Azure Machine Learning Studio : FAQ relatives à la facturation, aux fonctionnalités et aux limitations d’un service cloud pour la modélisation prédictive rationalisée.'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: 8c381ca02dcfb194b2807735415ef894d72f74fa
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462277"
---
# <a name="azure-machine-learning-studio-faq-capabilities-and-limitations"></a>FAQ sur Azure Machine Learning Studio
Voici quelques-unes des questions fréquemment posées et des réponses correspondantes sur Azure Machine Learning, un service cloud pour le développement de modèles prédictifs et la mise en œuvre de solutions via des services web. 

## <a name="general-questions"></a>Questions générales
**Qu'est-ce que Machine Learning Studio ?**

Machine Learning Studio est un environnement de canevas utilisant le glisser-déplacer, accessible par l’intermédiaire d’un navigateur web. Il héberge de nombreux modules dans une interface de composition visuelle et vous aide à concevoir un workflow de science des données de bout en bout qui prend la forme d’une expérience.

Pour plus d’informations sur Machine Learning Studio, consultez [Qu'est-ce que Machine Learning Studio ?](what-is-ml-studio.md)

**Qu'est-ce que le service API Machine Learning ?**

Le service API Machine Learning vous permet de déployer des modèles prédictifs, tels que ceux intégrés à Machine Learning Studio, sous forme de services web évolutifs et tolérants aux pannes. Les services web créés par le service API Machine Learning sont des API REST qui fournissent une interface pour les communications entre les applications externes et vos modèles analytiques prédictifs.

Pour plus d’informations, consultez [Utilisation d’un service Web Microsoft Azure Machine Learning](consume-web-services.md).

**Où sont répertoriés mes services web Classic ? Où sont répertoriés mes nouveaux services web (basés sur Azure Resource Manager) ?**

Les services web créés à l’aide du modèle de déploiement Classic et ceux créés à l’aide du nouveau modèle de déploiement Azure Resource Manager sont répertoriés dans le portail [Services web Microsoft Azure Machine Learning](https://services.azureml.net/).

Les services web Classic figurent également dans [Machine Learning Studio](http://studio.azureml.net) dans l’onglet **Services web**.

## <a name="azure-machine-learning-questions"></a>Questions sur Azure Machine Learning
**Que sont les services web Azure Machine Learning ?**

Les services web Machine Learning fournissent une interface entre une application et un modèle de notation du workflow Machine Learning. Une application externe peut utiliser Azure Machine Learning pour communiquer en temps réel avec un modèle de notation du workflow Machine Learning. Un appel à un service web Machine Learning renvoie les résultats d’une prédiction à une application externe. Pour générer cet appel, vous transmettez une clé API créée au moment du déploiement du service web. Un service web Machine Learning s’appuie sur l’architecture REST, souvent choisie pour les projets de programmation web.

Azure Machine Learning propose deux types de services web :

* Service de requête-réponse (RRS, Request-Response Service) : Service hautement évolutif, à faible latence, qui fournit une interface pour les modèles sans état créés et déployés à partir de Machine Learning Studio.
* Service d'exécution de lots (BES, Batch Execution Service) : Service asynchrone qui effectue la notation d’un lot pour les enregistrements de données.

Il existe plusieurs moyens d’utiliser l’API REST et d’accéder au service web. Par exemple, vous pouvez écrire une application en C#, R ou Python en utilisant l’exemple de code généré lors du déploiement du service web.

L’exemple de code est disponible sur :
- La page d’utilisation du service web dans le portail des services web Azure Machine Learning
- La page d’aide de l’API dans le tableau de bord des services web de Machine Learning Studio

Vous pouvez également utiliser l’exemple de classeur Microsoft Excel créé pour vous et disponible dans le tableau de bord des services web de Machine Learning Studio.

**Quelles sont les principales mises à jour apportées à Azure Machine Learning ?**

Pour accéder aux mises à jour les plus récentes, consultez [Nouveautés d’Azure Machine Learning](../../active-directory/fundamentals/whats-new.md).

## <a name="import-and-export-data-for-machine-learning"></a>Importation et exportation de données pour Machine Learning
**Quelles sources de données sont prises en charge par Machine Learning ?**

Il existe trois façons de télécharger des données dans une expérience Machine Learning Studio :

- Chargement d’un fichier local en tant que jeu de données
- Utilisation d’un module pour importer des données à partir des services de données cloud
- Importation d’un jeu de données enregistré à partir d’une autre expérience

Pour en savoir plus sur les formats de fichiers pris en charge, voir [Importation de vos données d’apprentissage Azure Machine Learning Studio depuis différentes sources de données](import-data.md).

### <a id="ModuleLimit"></a>Quelle est la taille maximale du jeu de données de mes modules ?
Les modules de Machine Learning Studio prennent en charge les jeux de données d’une taille maximale de 10 Go de données numériques denses pour les scénarios d’utilisation courants. Si un module accepte plusieurs entrées, le total de toutes les tailles d’entrée est de 10 Go. Vous pouvez également échantillonner des jeux de données plus importants par le biais de requêtes Hive ou Azure SQL Database ou encore via un prétraitement Learning by Counts avant l’ingestion.  

Les types de données suivants peuvent être développés en jeux de données plus importants au moment de la normalisation des fonctionnalités et ils sont limités à moins de 10 Go :

* Partiellement alloué
* Par catégorie
* Chaînes
* Données binaires

Les modules suivants sont limités à des jeux de données inférieurs à 10 Go :

* modules de recommandation
* Module SMOTE (Synthetic Minority Oversampling Technique)
* Modules de scripts : R, Python, SQL
* Modules dont la taille des données de sortie peut être supérieure à la taille des données d’entrée, comme Join ou Feature Hashing
* Validation croisée, réglage des hyperparamètres de modèle, régression ordinale et plusieurs classes de un contre tous, lorsque le nombre d’itérations est très élevé

### <a id="UploadLimit"></a>Quelles sont les limites de téléchargement de données ?
Pour les tailles de jeux de données supérieures à quelques gigaoctets, chargez les données dans le stockage Azure ou Azure SQL Database. Vous pouvez aussi utiliser Azure HDInsight au lieu d’effectuer un chargement directement à partir d’un fichier local.

**Puis-je lire les données à partir d’Amazon S3 ?**

Si vous disposez d’une petite quantité de données et que vous souhaitez l’exposer par le biais d’une URL HTTP, vous pouvez utiliser le module [Importer des données][import-data]. Si vous utilisez de plus grandes quantités de données, commencez par les transférer vers le stockage Azure, puis utilisez le module [Importer des données][import-data] pour les importer dans votre expérience.
<!--

<SEE CLOUD DS PROCESS>
-->

**Existe-t-il une capacité d’entrée d’image intégrée ?**

Découvrez la capacité d’entrée d’image dans la référence [Importer des images][image-reader].

## <a name="modules"></a>Modules
**L’algorithme, la source des données, le format des données ou l’opération de transformation des données que je recherche ne sont pas disponibles dans Azure Machine Learning Studio. Quelles sont mes options ?**

Vous pouvez consulter le [forum des commentaires des utilisateurs](https://go.microsoft.com/fwlink/?LinkId=404231) (en anglais) pour visualiser les demandes de fonctionnalités dont nous effectuons le suivi. Si la fonctionnalité que vous recherchez a déjà fait l’objet d’une requête, ajoutez votre vote à cette requête. Si la fonctionnalité que vous recherchez n’existe pas, créez une requête. Vous pouvez également afficher le statut de votre requête dans ce forum. Nous suivons cette liste de près et mettons à jour régulièrement le statut de disponibilité des fonctionnalités. Par ailleurs, le support intégré pour R et Python permet de créer des transformations personnalisées en fonction des besoins.

**Puis-je importer mon code existant dans Machine Learning Studio ?**

Oui. Vous pouvez importer votre code R ou Python existant dans Machine Learning Studio, l’exécuter au sein de la même expérience avec Azure Machine Learning, puis déployer la solution en tant que service web via Azure Machine Learning. Pour plus d’informations, voir [Prolongez votre expérience avec R](extend-your-experiment-with-r.md) et [Exécution des scripts d’apprentissage automatique Python dans Azure Machine Learning Studio](execute-python-scripts.md).

**Est-il possible d’utiliser [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language), par exemple, pour définir un modèle ?**

Non. Le modèle PMML (Predictive Model Markup Language) n’est pas pris en charge. Vous pouvez utiliser le code R et Python personnalisé pour définir un module.

**Combien de modules puis-je exécuter en parallèle dans mon expérience ?**  

Vous pouvez exécuter jusqu’à quatre modules en parallèle dans une expérience.

## <a name="data-processing"></a>Traitement des données
**Existe-t-il une possibilité de visualiser les données (autre que les visualisations R) de manière interactive au sein de l’expérience ?**

Cliquez sur la sortie d’un module pour visualiser les données et obtenir des statistiques.

**Lorsque vous générez l’aperçu des résultats ou des données dans un navigateur, le nombre de lignes et de colonnes est limité. Pourquoi ?**

Étant donné les grandes quantités de données susceptibles d’être envoyées au navigateur, la taille des données est limitée pour éviter de ralentir Machine Learning Studio. Pour visualiser l’ensemble des données et des résultats, il est préférable de télécharger les données, puis d’utiliser Excel ou un autre outil.

## <a name="algorithms"></a>Algorithmes
**Quels sont les algorithmes ML existants pris en charge dans Machine Learning Studio ?**

Machine Learning Studio fournit des algorithmes de pointe tels que les arbres de décision optimisés évolutifs, les systèmes de recommandation bayésiens, les réseaux neuronaux profonds et les jungles de décision développés chez Microsoft Research. Des modules d’apprentissage automatique open source évolutifs tels que Vowpal Wabbit sont également inclus. Machine Learning Studio prend en charge les algorithmes d’apprentissage automatique pour la classification, la régression et le clustering multiclasses et binaires. Consultez la liste complète des [Modules de Machine Learning][machine-learning-modules].

**Suggérez-vous automatiquement le bon algorithme Machine Learning à utiliser pour mes données ?**

Non. Toutefois, Machine Learning Studio propose plusieurs façons de comparer les résultats de chaque algorithme afin de déterminer le mieux approprié à votre cas.

**Y a-t-il des instructions sur le choix d’un algorithme plutôt qu’un autre pour les algorithmes fournis ?**

Voir [Comment choisir les algorithmes dans Microsoft Azure Machine Learning](algorithm-choice.md).

**Les algorithmes fournis sont-ils écrits en Python ou en R ?**

Non. Pour la plupart, ces algorithmes sont écrits dans des langages compilés afin d’obtenir de meilleures performances.

**Des détails relatifs aux algorithmes sont-ils disponibles ?**

La documentation propose des informations sur les algorithmes, et les paramètres fournis pour le paramétrage sont décrits afin d’optimiser votre utilisation de l’algorithme choisi.  

**Existe-t-il une prise en charge pour l’apprentissage en ligne ?**

Non. Actuellement, seul le recyclage par programme est pris en charge.

**Est-il possible de visualiser les couches d’un modèle de réseau neuronal à l’aide du module intégré ?**

 Non.

**Puis-je créer mes propres modules en C# ou un autre langage ?**

Actuellement, vous pouvez uniquement utiliser R pour créer des modules personnalisés.

## <a name="r-module"></a>Module R
**Quels packages R sont disponibles dans Machine Learning Studio ?**

Machine Learning Studio prend actuellement en charge plus de 400 packages CRAN R. Voici la [liste actuelle](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) de tous les packages inclus. Pour découvrir comment récupérer vous-même cette liste, voir [Prolongez votre expérience avec R](extend-your-experiment-with-r.md). Si le package souhaité ne figure pas dans cette liste, indiquez son nom sur le [forum des commentaires des utilisateurs](https://go.microsoft.com/fwlink/?LinkId=404231).

**Est-il possible de créer un module R personnalisé ?**

Oui. Consultez [Créer des modules R personnalisés dans Microsoft Azure Machine Learning](custom-r-modules.md) pour plus d’informations.

**Existe-t-il un environnement REPL pour R ?**

Non. Il n’existe aucun environnement REPL (Read-Eval-Print-Loop) pour R dans Studio.

## <a name="python-module"></a>Module Python
**Est-il possible de créer un module Python personnalisé ?**

Pas pour l’instant, mais vous pouvez utiliser un ou plusieurs modules [Exécuter le script Python][python] pour obtenir le même résultat.

**Existe-t-il un environnement REPL pour Python ?**

Vous pouvez utiliser les Notebooks Jupyter dans Machine Learning Studio. Pour plus d’informations, voir [Introducing Jupyter Notebooks in Azure Machine Learning Studio](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx)(Présentation des notebooks Jupyter dans Azure Machine Learning Studio).

## <a name="web-service"></a>Service Web

**Comment recycler des modèles Azure Machine Learning par programme ?**

Utilisez les API de recyclage. Pour plus d'informations, consultez la page [Reformation des modèles Machine Learning par programme](retrain-models-programmatically.md). Un exemple de code est également disponible sur la page [Microsoft Azure Machine Learning Retraining Demo](https://azuremlretrain.codeplex.com/)(Démonstration de reformation Microsoft Azure Machine Learning).

**Puis-je déployer le modèle localement ou dans une application sans connexion Internet ?**

 Non.

**Existe-t-il une latence de référence attendue pour tous les services Web ?**

Consultez les [Limites d’abonnement Azure](../../azure-subscription-service-limits.md).

**Quand ai-je avantage à exécuter mon modèle prédictif en tant que service d’exécution de lots plutôt qu’un service Web requête-réponse ?**

Le service de requête-réponse (RRS, Request-Response Service) est un service web à faible latence et à grande échelle qui permet de fournir une interface aux modèles sans état créés et déployés à partir de l’environnement Expérimentation. BES (Batch Execution Service) est un service de notation asynchrone des batchs d’enregistrements de données. La saisie pour BES ressemble à la saisie de données utilisée par RRS. La principale différence réside dans le fait que BES lit un bloc d’enregistrements à partir de différentes sources, par exemple le stockage Blob Azure, le stockage Table Azure, Azure SQL Database, HDInsight (requêtes Hive) et les sources HTTP. Pour plus d’informations, consultez [Utilisation d’un service Web Microsoft Azure Machine Learning](consume-web-services.md).

**Comment puis-je mettre à jour le modèle pour le service Web déployé ?**

Si vous voulez mettre à jour un modèle prédictif pour un service déjà déployé, il vous suffit de modifier et de réexécuter l’expérience utilisée pour créer et enregistrer le modèle formé. Une fois qu’une nouvelle version du modèle formé est disponible, Machine Learning Studio vous demande si vous voulez mettre à jour votre service web. Pour plus d’informations sur la mise à jour d’un service web déployé, voir [Déploiement d’un service web Machine Learning](publish-a-machine-learning-web-service.md).

Vous pouvez également utiliser les API de recyclage.
Pour plus d'informations, consultez la page [Reformation des modèles Machine Learning par programme](retrain-models-programmatically.md). Un exemple de code est également disponible sur la page [Microsoft Azure Machine Learning Retraining Demo](https://azuremlretrain.codeplex.com/)(Démonstration de reformation Microsoft Azure Machine Learning).

**Comment puis-je surveiller mon service web déployé en production ?**

Une fois que vous déployez un modèle prédictif, vous pouvez le surveiller à partir du portail des services web Azure Machine Learning. Chaque service déployé dispose de son propre tableau de bord, à partir duquel vous pouvez consulter les informations de surveillance correspondant à ce service. Pour plus d’informations sur la gestion de vos services web déployés, voir [Gestion d’un service web à l’aide du portail des services web Azure Machine Learning](manage-new-webservice.md) et [Gestion d’un espace de travail Azure Machine Learning](manage-workspace.md).

**Existe-t-il un endroit où je peux voir le résultat de mon service RRS/BES ?**

Pour les enregistrements de ressources, c'est généralement dans la réponse du service web que vous voyez le résultat. Vous pouvez également l’écrire dans le stockage Blob Azure. Pour les environnements d'initialisation, la sortie est écrite dans un objet blob par défaut. Vous pouvez également écrire la sortie dans une base de données ou une table à l’aide du module [Exporter les données][export-data].

**Puis-je créer des services web uniquement à partir de modèles générés dans Machine Learning Studio ?**

Non. Vous pouvez également créer des services web directement à partir de Jupyter Notebooks et RStudio.

**Où puis-je trouver plus d'informations sur les codes d'erreur ?**

Pour obtenir la liste des codes d’erreur et leur description, voir [Machine Learning Module Error Codes](https://msdn.microsoft.com/library/azure/dn905910.aspx) (Codes d’erreur des modules Machine Learning).

**Quelle est l’évolutivité du service Web ?**

Actuellement, le point de terminaison par défaut est doté de 20 demandes RR simultanées par point de terminaison. Vous pouvez l’étendre à 200 demandes simultanées par point de terminaison, et vous pouvez étendre chaque service web à 10 000 points de terminaison par service web, comme indiqué dans l’article [Mise à l’échelle du service web](scaling-webservice.md). Pour BES, chaque point de terminaison permet de traiter 40 demandes simultanées. Au-delà, les demandes supplémentaires sont mises en file d’attente. Ces requêtes en file d’attente sont exécutées automatiquement au fur et à mesure que la file diminue.

**Les travaux R sont-ils répartis entre les nœuds ?**

 Non.  

**Quelle quantité de données puis-je utiliser pour l'apprentissage ?**

Les modules de Machine Learning Studio prennent en charge les jeux de données d’une taille maximale de 10 Go de données numériques denses pour les scénarios d’utilisation courants. Si un module prend plusieurs entrées, la taille totale de toutes les entrées est de 10 Go. Vous pouvez également échantillonner des jeux de données plus importants par le biais de requêtes Hive ou Azure SQL Database, ou encore par l’intermédiaire d’un prétraitement avec des modules [Learning with Counts][counts] avant l’ingestion.  

Les types de données suivants peuvent être développés en jeux de données plus importants au moment de la normalisation des fonctionnalités et ils sont limités à moins de 10 Go :

* Partiellement alloué
* Par catégorie
* Chaînes
* Données binaires

Les modules suivants sont limités à des jeux de données inférieurs à 10 Go :

* modules de recommandation
* Module SMOTE (Synthetic Minority Oversampling Technique)
* Modules de scripts : R, Python, SQL
* Modules dont la taille des données de sortie peut être supérieure à la taille des données d’entrée, comme Join ou Feature Hashing
* Validation croisée, réglage des hyperparamètres de modèle, régression ordinale et plusieurs classes de un contre tous, lorsque le nombre d’itérations est très élevé

Pour les jeux de données supérieurs à quelques gigaoctets, mieux vaut charger les données dans le stockage Azure ou Azure SQL Database, ou encore utiliser HDInsight plutôt que d’effectuer un chargement directement à partir d’un fichier local.

**Existe-t-il des limitations de taille de vecteurs ?**

Les lignes et les colonnes sont soumises à la limitation Max Int de .NET : 2,147,483,647.

**Puis-je ajuster la taille de la machine virtuelle qui exécute le service web ?**

 Non.  

## <a name="security-and-availability"></a>Sécurité et disponibilité
**Qui a accès au point de terminaison http pour le service web par défaut ? Comment puis-je limiter l’accès au point de terminaison ?**

Après le déploiement d’un service web, un point de terminaison par défaut est créé pour ce service. Le point de terminaison par défaut peut être appelé à l’aide de sa clé API. Il est possible d’ajouter des points de terminaison supplémentaires avec leurs propres clés à partir du portail des services web ou par le biais d’un programme, à l’aide des API Gestion des services web. Des clés d’accès sont nécessaires pour effectuer des appels au service web. Pour plus d’informations, consultez [Utilisation d’un service Web Microsoft Azure Machine Learning](consume-web-services.md).

**Que se passe-t-il si mon compte de stockage Azure est introuvable ?**

Machine Learning Studio s’appuie sur un compte de stockage Azure fourni par l’utilisateur pour enregistrer les données intermédiaires au moment de l’exécution du workflow. Ce compte de stockage est fourni à Machine Learning Studio au moment de la création d’un espace de travail. Une fois créé (si le compte de stockage est supprimé et reste introuvable), l’espace de travail cesse de fonctionner et toutes ses expériences échouent.

Si vous avez supprimé par inadvertance le compte de stockage, vous devez recréer ce compte de stockage avec exactement le même nom, dans la même région que celui que vous avez supprimé. Après cela, effectuez une nouvelle synchronisation de la clé d’accès.

**Que se passe-t-il si ma clé d’accès au compte de stockage n’est pas synchronisée ?**

Machine Learning Studio s’appuie sur un compte de stockage Azure fourni par l’utilisateur pour enregistrer les données intermédiaires au moment de l’exécution du workflow. Ce compte de stockage est fourni à Machine Learning Studio au moment de la création d’un espace de travail et les clés d’accès sont associées à cet espace de travail. Si les clés d’accès sont modifiées après la création de l’espace de travail, celui-ci ne pourra plus accéder au compte de stockage. Il cessera de fonctionner, et toutes les expériences dans cet espace de travail échoueront.

Si vous avez modifié les clés d’accès du compte de stockage, resynchronisez-les dans l’espace de travail à l’aide du portail Azure.  


## <a name="billing-questions"></a>Questions sur la facturation

Pour des informations sur la facturation et la tarification, consultez [Tarification Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).


<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx
