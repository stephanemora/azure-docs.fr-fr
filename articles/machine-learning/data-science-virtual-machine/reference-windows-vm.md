---
title: 'Référence : DSVM Windows'
description: Détails sur les outils inclus dans Windows Data Science VM
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: be4397de477891f2a698c9f7dcb131da79479529
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200014"
---
# <a name="reference-windows-data-science-virtual-machine"></a>Référence : Windows Data Science Virtual Machine

Consultez ci-dessous la liste des outils disponibles dans Windows Data Science Virtual Machine. 

## <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server - Édition Développeur

Vous pouvez utiliser Microsoft Enterprise Library pour votre analytique, car Machine Learning Server - Édition Développeur est installé sur la machine virtuelle. Anciennement Microsoft R Server, Machine Learning Server est une plateforme d’analytique que vous pouvez déployer à grande échelle. Elle est scalable et prise en charge de façon commerciale.

Machine Learning Server prend en charge différentes statistiques Big Data, la modélisation prédictive et les tâches de machine learning. Il prend en charge la gamme complète d’analyses : exploration, analyse, visualisation et modélisation. En utilisant et en étendant les langages R et Python open source, Machine Learning Server est compatible avec les fonctions et scripts R et Python. Il est également compatible avec les packages Conda, pip et CRAN pour analyser les données au niveau de l’entreprise.

Machine Learning Server traite également les limitations en mémoire du langage R open source en ajoutant le traitement des données en parallèle et mémorisé en bloc. Cela signifie que vous pouvez exécuter une analytique sur des données plus volumineuses que ce que peut contenir la mémoire principale. 

Visual Studio Community est inclus sur la machine virtuelle. Il est doté des outils R pour Visual Studio et des extensions PTVS (Python Tools pour Visual Studio) qui fournissent un IDE complet pour travailler avec R ou Python. Nous fournissons également d’autres IDE comme [RStudio](https://www.rstudio.com) et l’[édition PyCharm Community](https://www.jetbrains.com/pycharm/) sur la machine virtuelle.

## <a name="python"></a>Python

Pour un développement basé sur Python, les versions 2.7 et 3.6 des distributions Anaconda Python sont installées. Ces distributions contiennent le langage Python de base avec environ 300 packages de mathématiques, d’ingénierie et d’analytique données figurant parmi les plus populaires. Vous pouvez utiliser PTVS, qui est installé dans Visual Studio Community 2017. Ou vous pouvez utiliser un des IDE fournis avec Anaconda, comme IDLE ou Spyder. Recherchez et ouvrez l’un de ces packages (touche Windows + S).

> [!NOTE]
> Pour pointer Python Tools pour Visual Studio sur Anaconda Python 2.7, vous devez créer des environnements personnalisés pour chaque version. Pour définir les chemins de ces environnements dans Visual Studio 2017 Community, accédez à **Outils** > **Outils Python** > **Environnements Python**. Sélectionnez ensuite **+ Personnalisé**.

Anaconda Python 3.6 est installé sous C:\Anaconda. Anaconda Python 2.7 est installé sous C:\Anaconda\envs\python2. Pour obtenir des instructions détaillées, consultez la [documentation de PTVS](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

## <a name="the-jupyter-notebook"></a>Jupyter Notebook

La distribution Anaconda est également fournie avec Jupyter Notebook, environnement conçu pour le partage de code et d’analyses. Le serveur Jupyter Notebook est préconfiguré avec les noyaux Python 2.7, Python 3.x, PySpark, Julia et R. Pour démarrer le serveur Jupyter et ouvrir le navigateur afin d’accéder au serveur Notebook, utilisez l’icône du Bureau **Jupyter Notebook**.

Nous plaçons dans un package plusieurs exemples de notebooks en langage Python et R. Après avoir accédé à Jupyter, les notebooks vous montrent comment utiliser les technologies suivantes :

* Machine Learning Server
* SQL Server Machine Learning Services, pour l’analytique dans la base de données
* Python
* Microsoft Cognitive Toolkit
* TensorFlow
* Autres technologies Azure

Après vous être authentifié auprès de Jupyter Notebook avec le mot de passe que vous avez créé, vous pouvez voir le lien vers les exemples dans la page d’accueil du notebook.

## <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

La DSVM comprend Visual Studio Community. Vous pouvez utiliser cette version gratuite du très connu IDE de Microsoft à des fins d’évaluation et pour des petites équipes. Consultez les [termes du contrat de licence du logiciel Microsoft](https://www.visualstudio.com/support/legal/mt171547).

Ouvrez Visual Studio à l’aide de l’icône du Bureau ou du menu **Démarrer**. Recherchez des programmes (en tapant touche Windows + S), suivi de **Visual Studio**. À partir d’ici, vous pouvez créer des projets dans des langages comme C#, Python, R et Node.js. Les plug-ins permettent d’utiliser facilement les services Azure suivants :

* Azure Data Catalog
* Azure HDInsight pour Hadoop et Spark
* Azure Data Lake

Un plug-in appelé Azure Machine Learning for Visual Studio Code s’intègre également à Azure Machine Learning et vous permet de créer rapidement des applications IA.

> [!NOTE]
> Il est possible que vous receviez un message indiquant que votre période d’évaluation a expiré. Entrez les informations d’identification de votre compte Microsoft. Vous pouvez aussi créer un compte gratuit pour accéder à Visual Studio Community.

## <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer Edition

La DSVM est fournie avec une version de développement de SQL Server 2017 avec Machine Learning Services. Cette édition de SQL Server est fournie avec R ou Python et peut exécuter une analytique dans la base de données. 

Machine Learning Services est doté d’une plateforme pour développer et déployer des applications intelligentes. Pour créer des modèles et générer des prédictions pour vos données SQL Server, vous pouvez utiliser ces langages et les nombreux packages issus de la communauté. Vous pouvez conserver les analyses à proximité des données, car Machine Learning Services, dans la base de données, intègre à la fois les langages R et Python à SQL Server. Cette intégration permet d’éliminer les coûts et les risques de sécurité liés au déplacement des données.

> [!NOTE]
> L’édition SQL Server Développeur est uniquement destinée à être utilisée à des fins de test et de développement. Vous devez posséder une licence pour l’exécuter en production.

Vous pouvez accéder à SQL Server en ouvrant Microsoft SQL Server Management Studio. Le nom de votre machine virtuelle correspond au **Nom du serveur**. Utilisez l’authentification Windows pour vous connecter à Windows en tant qu’administrateur. Dans SQL Server Management Studio, vous pouvez créer d’autres utilisateurs, créer des bases de données, importer des données et exécuter des requêtes SQL.

Pour activer l’analytique dans la base de données à l’aide de SQL Server Machine Learning Services, exécutez la commande suivante en tant qu’action unique dans SQL Server Management Studio après vous être connecté en tant qu’administrateur du serveur :

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

Remplacez `%COMPUTERNAME%` par le nom de votre machine virtuelle.

## <a name="azure"></a>Azure

Plusieurs outils Azure sont installés sur la machine virtuelle :

* Un raccourci sur le bureau redirige vers la documentation du Kit de développement logiciel (SDK) Azure.
* Utilisez AzCopy pour déplacer des données dans et en dehors de votre compte de stockage Azure. Pour afficher l’utilisation, entrez **Azcopy** dans une invite de commandes.
* Utilisez l’Explorateur Stockage Azure pour parcourir les objets que vous stockez dans votre compte de stockage Azure. Il permet aussi de copier des données vers et à partir de Stockage Azure. Pour accéder à cet outil, entrez **Explorateur Stockage** dans le champ **Recherche**. Ou recherchez-le dans le menu **Démarrer** de Windows.
* AdlCopy copie des données vers Azure Data Lake. Pour afficher l’utilisation, entrez **adlcopy** dans une invite de commandes.
* L’outil dtui copie des données vers et à partir d’Azure Cosmos DB, une base de données NoSQL dans le cloud. Entrez **dtui** dans une invite de commandes.
* Le runtime d’intégration copie des données entre des sources de données locales et le cloud. Elle est utilisée dans les outils tels que Azure Data Factory.
* Utilisez Azure PowerShell pour gérer vos ressources Azure dans le langage de script PowerShell. Il est également installé sur votre machine virtuelle.

## <a name="power-bi"></a>Power BI

Power BI Desktop est installé sur la DSVM pour vous aider à créer des tableaux de bord et des visualisations. Utilisez cet outil pour extraire des données de différentes sources, créer vos tableaux de bord et vos rapports, puis les publier sur le cloud. Pour plus d’informations, consultez le [site de Power BI](https://powerbi.microsoft.com). Power BI Desktop est accessible à partir du menu **Démarrer**.

> [!NOTE]
> Vous devez disposer d’un compte Microsoft Office 365 pour accéder à Power BI.

## <a name="azure-machine-learning-sdk-for-python"></a>Kit SDK Azure Machine Learning pour Python

Les scientifiques des données et les développeurs d’IA utilisent le SDK Azure Machine Learning pour Python afin de générer et d’exécuter des workflows de machine learning avec [Azure Machine Learning service](../service/overview-what-is-azure-ml.md). Vous pouvez interagir avec le service dans Jupyter Notebooks ou un autre IDE Python à l’aide de frameworks open source tels que TensorFlow et scikit-learn.

Le SDK Python est préinstallé sur la machine virtuelle Data Science Virtual Machine. Pour commencer à utiliser le SDK Python, consultez [Utiliser Python pour démarrer avec Azure Machine Learning](../service/quickstart-create-workspace-with-python.md).

## <a name="more-microsoft-development-tools"></a>Outils de développement Microsoft supplémentaires

Vous pouvez utiliser le programme [Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) pour rechercher et télécharger d’autres outils de développement Microsoft. Il existe également un raccourci vers l’outil sur le bureau Microsoft Data Science Virtual Machine.  

## <a name="important-directories-on-the-virtual-machine"></a>Répertoires importants sur la machine virtuelle

| Item | Répertoire |
| --- | --- |
| Configurations du serveur Jupyter Notebook | C:\ProgramData\jupyter |
| Répertoire des exemples de Jupyter Notebook | C:\dsvm\notebooks et c:\users\\<nom_utilisateur\>\notebooks |
| Autres exemples | C:\dsvm\samples |
| Anaconda, valeur par défaut : Python 3.6 | C:\Anaconda |
| Environnement Anaconda Python 2.7 | C:\Anaconda\envs\python2 |
| Microsoft Machine Learning Server (autonome) pour Python | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Instance R par défaut, Machine Learning Server (autonome) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| Répertoire de l’instance dans la base de données SQL Server Machine Learning Services | C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Outils divers | C:\dsvm\tools |

> [!NOTE]
> Dans l’édition Windows Server 2012 de la DSVM et l’édition Windows Server 2016 antérieure à mars 2018, l’environnement Anaconda par défaut est Python 2.7. L’environnement secondaire est Python 3.5, à l’emplacement C:\Anaconda\envs\py35.

## <a name="next-steps"></a>Étapes suivantes

Vous avez d’autres questions ? N’hésitez pas à créer un [ticket de support](https://azure.microsoft.com/support/create-ticket/).
