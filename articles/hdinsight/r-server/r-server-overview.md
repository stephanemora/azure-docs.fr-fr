---
title: Présentation de ML Services sur Azure HDInsight
description: Découvrez comment utiliser ML Services sur HDInsight pour créer des applications pour l’analyse Big Data.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 02/10/2020
ms.openlocfilehash: a77771880da962298f6e80782e5f3e251f5f4641
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122376"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Qu’est-ce que ML Services dans Azure HDInsight

Microsoft Machine Learning Server vous est proposé comme option de déploiement au moment de créer des clusters HDInsight dans Azure. Le type de cluster qui fournit cette option est appelé **ML Services**. Cette fonctionnalité offre aux experts en science des données, aux statisticiens et aux programmeurs R un accès à la demande à des méthodes d’analyse extensibles et distribuées sur HDInsight.

ML Services sur HDInsight fournit les dernières fonctionnalités analytiques R pour des jeux de données de toute taille ou presque, chargés dans un stockage de type Blob Azure ou Data Lake. Sachant que le cluster ML Services repose sur le langage R open source, les applications basées sur R que vous créez peuvent tirer parti des packages R open source qui se dénombrent à plus de 8 000. Les routines de ScaleR, package analytique Big Data de Microsoft, sont également disponibles.

Le nœud de périmètre d’un cluster fournit un lieu d’accueil pratique pour la connexion au cluster et l’exécution de vos scripts R. Un nœud périphérique permet d’exécuter des fonctions distribuées parallélisées de ScaleR sur les différents cœurs du serveur associé. Vous pouvez également les exécuter sur les différents nœuds du cluster à l’aide des contextes de calcul Hadoop Map Reduce ou Apache Spark de ScaleR.

Les modèles ou prévisions résultant des analyses peuvent être téléchargés pour une utilisation locale. Ils peuvent aussi être opérationnalisés ailleurs dans Azure, en particulier par le biais du [service web](../../machine-learning/studio/deploy-a-machine-learning-web-service.md) [Azure Machine Learning Studio (classique)](https://studio.azureml.net).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Démarrer avec ML Services sur HDInsight

Pour créer un cluster ML Services dans Azure HDInsight, sélectionnez le type de cluster **ML Services** au moment de créer un cluster HDInsight via le portail Azure. Le type de cluster ML Services inclut ML Server sur les nœuds de données du cluster et sur un nœud de périphérie, qui sert de zone d’accueil pour l’analytique ML Services. Consultez [Créer des clusters Apache Hadoop à l’aide du portail Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) pour une procédure pas à pas permettant de créer le cluster.

## <a name="why-choose-ml-services-in-hdinsight"></a>Pourquoi choisir ML Services dans HDInsight ?

ML Services dans HDInsight offre les avantages suivants :

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Innovation Microsoft et open source en matière d’IA

  ML Services intègre un ensemble complet d’algorithmes distribués et hautement évolutifs, tels que [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) et [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package), qui peuvent fonctionner sur des données de taille plus importante que la mémoire physique et qui s’exécutent sur un large éventail de plateformes de manière distribuée. En savoir plus sur la collection Microsoft de [packages R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) et de [packages Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) inclus avec le produit.
  
  ML Services jette un pont entre ces innovations Microsoft et les contributions de la communauté open source (kits d’outils R, Python et IA), le tout dans une plateforme unique de niveau entreprise. Quel que soit le package R ou Python de Machine Learning open source, il peut fonctionner aux côtés de n’importe quelle innovation propriétaire de Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Opérationnalisation et administration simples, sécurisées et à grande échelle

  Les entreprises qui reposent sur des environnements et des modèles classiques consentent trop de temps et d’efforts à l’opérationnalisation. Cela se traduit par une augmentation des coûts et un allongement des délais, ce qui inclut le temps nécessaire à la traduction des modèles, les itérations pour maintenir leur validité et les tenir à jour, les approbations réglementaires, la gestion des autorisations via l’opérationnalisation.

  ML Services offre une [opérationnalisation](https://docs.microsoft.com/machine-learning-server/what-is-operationalization) de niveau entreprise dans la mesure où, dès qu’un modèle de Machine Learning est terminé, quelques clics suffisent pour générer des API de services web. Ces [services web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) sont hébergés sur une grille de serveurs dans le cloud et peuvent être intégrés à des applications métier. La possibilité d’effectuer le déploiement sur une grille élastique vous permet d’évoluer en toute transparence en fonction des besoins de votre entreprise, pour la notation par lot et en temps réel. Pour obtenir des instructions, consultez [Rendre opérationnel ML Services sur HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

> [!NOTE]  
> Le type de cluster ML Services sur HDInsight est pris en charge uniquement sur HDInsight 3.6. La mise hors service de HDInsight 3.6 est prévue pour le 31 décembre 2020.

## <a name="key-features-of-ml-services-on-hdinsight"></a>Fonctionnalités clés de ML Services sur HDInsight

ML Services sur HDInsight inclut les fonctionnalités suivantes.

| Catégorie de fonctionnalité | Description |
|------------------|-------------|
| Pour R | [Packages R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) pour les solutions écrites en R, avec une distribution open source de R et une infrastructure d’exécution pour l’exécution de script. |
| Pour Python | [Modules Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) pour les solutions écrites en Python, avec une distribution open source de Python et une infrastructure d’exécution pour l’exécution de script.
| [Modèles préentraînés](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Pour une analyse visuelle et une analyse des sentiments sur le texte, prêts pour noter les données que vous fournissez. |
| [Déploiement et consommation](r-server-operationalize.md) | Opérationnalisez votre serveur et déployez des solutions en tant que service web. |
| [Exécution à distance](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Démarrez des sessions à distance sur un cluster ML Services de votre réseau à partir de votre station de travail cliente. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Options de stockage de données pour ML Services sur HDInsight

Le stockage par défaut du système de fichiers HDFS des clusters HDInsight peut être associé à un compte de stockage Azure ou à un compte Azure Data Lake Storage. Cette association garantit que toutes les données chargées dans l’espace de stockage en cluster au cours de l’analyse sont rendues persistantes et qu’elles sont disponibles même après la suppression du cluster. Il existe différents outils permettant de gérer le transfert de données vers l’option de stockage sélectionnée, notamment la fonctionnalité de chargement sur le portail du compte de stockage et l’utilitaire [AzCopy](../../storage/common/storage-use-azcopy.md).

Vous pouvez autoriser l’accès à des objets Blob et des magasins Data Lake supplémentaires pendant le processus de provisionnement du cluster, quelle que soit l’option de stockage principal utilisée.  Consultez l’article [Options de stockage Azure pour ML Services sur HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) pour en savoir plus sur l’utilisation de plusieurs comptes de stockage.

Vous pouvez également utiliser [Azure Files](../../storage/files/storage-how-to-use-files-linux.md) comme option de stockage pour une utilisation sur le nœud de périphérie. Le service Azure Files vous permet de monter un partage de fichiers qui a été créé dans un compte Azure Storage sur le système de fichiers Linux. Pour plus d’informations sur ces options de stockage de données pour un cluster ML Services sur HDInsight, consultez [Options de stockage Azure pour ML Services sur HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Accéder à un nœud de périphérie ML Services

Vous pouvez vous connecter à Microsoft ML Server sur le nœud de périphérie à l’aide d’un navigateur. R Server est installé par défaut lors de la création du cluster.  Vous pouvez aussi vous connecter au nœud de périphérie du cluster à partir de la ligne de commande en utilisant SSH/PuTTY pour accéder à la console R.

## <a name="develop-and-run-r-scripts"></a>Développer et exécuter des scripts R

Les scripts R que vous créez et exécutez peuvent utiliser n’importe quels packages R open source parmi plus de 8000, en plus des routines distribuées et parallélisées de la bibliothèque ScaleR. En général, un script exécuté avec ML Services sur le nœud de périphérie s’exécute au sein de l’interpréteur R de ce nœud, à l’exception des étapes qui doivent appeler une fonction ScaleR avec un contexte de calcul défini sur Hadoop MapReduce (RxHadoopMR) ou Spark (RxSpark). Dans ce cas, la fonction s’exécute en mode distribué sur les différents nœuds de données (tâches) du cluster qui sont associés aux données référencées. Pour plus d’informations sur les différentes options de contexte de calcul, consultez [Options de contexte de calcul pour ML Services sur HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Faire fonctionner un modèle

Une fois la modélisation de vos données terminée, vous pouvez rendre opérationnel le modèle de façon à effectuer des prédictions pour de nouvelles données, que ce soit à partir d’Azure ou localement. Ce processus est appelé notation. La notation est possible dans HDInsight, dans Azure Machine Learning ou en local.

### <a name="score-in-hdinsight"></a>Noter dans HDInsight

Pour noter dans HDInsight, écrivez une fonction R qui appelle votre modèle pour effectuer des prévisions pour un nouveau fichier de données que vous avez chargé sur votre compte de stockage. Ensuite, enregistrez les prévisions dans le compte de stockage. Vous pouvez exécuter cette routine à la demande sur le nœud de périphérie de votre cluster ou à l’aide d’une tâche planifiée.

### <a name="score-in-azure-machine-learning-aml"></a>Noter dans Azure Machine Learning (AML)

Pour noter à l’aide d’Azure Machine Learning, utilisez le package R Azure Machine Learning open source nommé [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) pour publier votre modèle en tant que service web Azure. Pour plus de commodité, ce package est déjà installé sur le nœud de périphérie. Ensuite, utilisez les fonctionnalités d’Azure Machine Learning pour créer une interface utilisateur pour le service web, puis appelez le service web en fonction des besoins de notation.

Si vous choisissez cette option, vous devez convertir tous les objets de modèle ScaleR en objets de modèle open source équivalents pour les utiliser avec le service web. Utilisez les fonctions de forçage de ScaleR, notamment `as.randomForest()` dans le cas des modèles basés sur un ensemble, pour cette conversion.

### <a name="score-on-premises"></a>Noter localement

Pour noter localement après la création de votre modèle, vous pouvez sérialiser le modèle dans R, le télécharger, le désérialiser, puis l’utiliser pour noter de nouvelles données. Vous pouvez noter les nouvelles données à l’aide de l’approche décrite précédemment dans [Noter dans HDInsight](#score-in-hdinsight)ou en utilisant le [service web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Maintenance du cluster

### <a name="install-and-maintain-r-packages"></a>Installation et maintenance des packages R

La plupart des packages R utilisés seront requis sur le nœud périphérique, car la plupart des étapes des scripts R y sont exécutées. Pour installer des packages R supplémentaires sur le nœud de périphérie, vous pouvez utiliser la méthode `install.packages()` de R.

Si vous utilisez simplement des routines de la bibliothèque ScaleR au sein du cluster, il n’est généralement pas nécessaire d’installer des packages R supplémentaires sur les nœuds de données. Toutefois, il se peut que vous ayez besoin de packages supplémentaires pour prendre en charge l’utilisation de **rxExec** ou l’exécution de **RxDataStep** sur les nœuds de données.

Dans ce cas, les packages supplémentaires peuvent être installés à l’aide d’une action de script après la création du cluster. Pour plus d’informations, consultez [Gérer un cluster ML Services dans HDInsight](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Modifier les paramètres de mémoire d’Apache Hadoop MapReduce

Vous pouvez modifier un cluster afin de changer la quantité de mémoire accessibles à ML Services lors de l’exécution d’un travail MapReduce. Pour modifier un cluster, utilisez l’interface utilisateur Apache Ambari qui est disponible via le panneau du portail Azure pour votre cluster. Pour obtenir des instructions concernant l’accès à l’interface utilisateur d’Ambari pour votre cluster, consultez la section [Gérer des clusters HDInsight à l’aide de l’interface utilisateur web d’Ambari](../hdinsight-hadoop-manage-ambari.md).

Vous pouvez aussi modifier la quantité de mémoire accessible à ML Services en utilisant des commutateurs Hadoop dans l’appel à **RxHadoopMR** comme suit :

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Mettre à l’échelle votre cluster

Vous pouvez faire monter ou descendre en puissance un cluster ML Services sur HDInsight existant via le portail. Grâce à la mise à l’échelle, vous pouvez obtenir la capacité supplémentaire éventuellement nécessaire pour les tâches de traitement plus importantes, ou rétablir l’échelle d’un cluster quand il est inactif. Pour obtenir des instructions concernant la mise à l’échelle d’un cluster, consultez la section [Gérer des clusters HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Maintenance du système

La maintenance, pour appliquer des correctifs de système d’exploitation ainsi que d’autres mises à jour, est effectuée sur les machines virtuelles Linux sous-jacentes dans un cluster HDInsight pendant les heures creuses. En règle générale, la maintenance s’effectue à 3 h 30 (en fonction de l’heure locale de la machine virtuelle) tous les lundis et jeudis. Les mises à jour sont effectuées de façon à ne pas avoir d’incidence sur plus d’un quart du cluster à la fois.

Étant donné que les nœuds principaux sont redondants et que certains nœuds de données ne sont pas affectés, toutes les tâches en cours d’exécution pendant ce temps peuvent être ralenties. Elles doivent cependant être exécutées jusqu’à la fin. L’ensemble des logiciels personnalisés ou des données locales que vous détenez sont conservés durant ces événements de maintenance, sauf si une défaillance irrémédiable se produit, nécessitant une reconstruction du cluster.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>Options IDE pour ML Services sur HDInsight

Le nœud de périmètre Linux d’un cluster HDInsight est la zone d’accueil pour l’analyse basée sur R. Les versions récentes de HDInsight permettent une installation par défaut de RStudio Server sur le nœud de périphérie en tant qu’IDE basé sur navigateur. L'utilisation de RStudio Server comme IDE pour le développement et l’exécution de scripts R peuvent être considérablement plus productifs que la simple utilisation de la console R.

Vous pouvez également installer un IDE de bureau et à l’utiliser pour accéder au cluster au moyen d’un contexte de calcul MapReduce ou Spark distant. Parmi les options disponibles figurent les [Outils R pour Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS) de Microsoft, RStudio et [StatET](http://www.walware.de/goto/statet) sur Eclipse de WalWare.

Par ailleurs, vous pouvez accéder à la console R sur le nœud de périphérie en tapant **R** à l’invite de commandes Linux après vous être connecté via SSH ou PuTTY. Si vous utilisez l’interface de la console, il est pratique d’exécuter un éditeur de texte pour le développement de scripts R dans une autre fenêtre, et de couper-coller des sections de votre script dans la console R au fur et à mesure.

## <a name="pricing"></a>Tarifs

Les prix associés à un cluster ML Services HDInsight sont structurés de la même manière que ceux applicables aux autres types de cluster HDInsight. Ils sont basés sur le dimensionnement des machines virtuelles sous-jacentes pour le nom, les données et les nœuds de périmètre, avec l’ajout d’une extension pendant les heures normales. Pour plus d’informations, voir [Tarification de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation des clusters ML Services sur HDInsight, consultez les rubriques suivantes :

* [Exécuter un script R sur un cluster ML Services dans HDInsight Azure à l’aide de RStudio Server](machine-learning-services-quickstart-job-rstudio.md)
* [Options de contexte de calcul pour un cluster ML Services sur HDInsight](r-server-compute-contexts.md)
* [Options de stockage pour un cluster ML Services sur HDInsight](r-server-storage.md)
