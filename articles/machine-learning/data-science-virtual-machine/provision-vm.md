---
title: 'Démarrage rapide : Créer une machine virtuelle Windows'
titleSuffix: Azure Data Science Virtual Machine
description: Configurez et créez une machine virtuelle pour la science des données sur Microsoft Azure pour vos besoins d’analyse et d’apprentissage automatique.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: quickstart
ms.date: 02/22/2019
ms.openlocfilehash: ac4b9b4d32d05083ceabd41207243eb483648baa
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278597"
---
# <a name="quickstart-set-up-a-windows-data-science-virtual-machine-on-azure"></a>Démarrage rapide : Configurer une machine virtuelle Science des données Windows sur Azure

La DSVM (Microsoft Windows Data Science Virtual Machine) est une image de machine virtuelle Windows Server 2016 sur Azure. Elle est fournie préinstallée et configurée avec des outils pour l’analytique données et le machine learning.

## <a name="included-data-science-tools"></a>Outils de science des données inclus

Les outils suivants sont inclus dans une DSVM :

* SDK Python pour le service [Azure Machine Learning service](../index.yml)
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) - Édition Développeur.
* Distribution Anaconda Python.
* Jupyter Notebook avec noyaux R, Python et PySpark.
* Microsoft Visual Studio Community.
* Microsoft Power BI Desktop.
* Microsoft SQL Server 2017 - Édition Développeur.
* Une instance autonome Apache Spark pour le développement et test locaux.
* [JuliaPro](https://juliacomputing.com/products/juliapro.html).
* Outils de machine learning et d’analytique données :
  * Frameworks de deep learning : Les frameworks d’intelligence artificielle [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), MXNet et Keras sont inclus dans la machine virtuelle.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) : Un système de machine learning rapide prenant en charge des techniques comme le hachage en ligne, les réductions allreduce, learning2search et l’apprentissage actif et interactif.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/) : Un outil qui offre une implémentation rapide et précise des arborescences optimisées.
  * [Rattle](https://togaware.com/rattle/). Outil d’analytique R qui facilite la prise en main de l’analytique données et du machine learning en langage R. Il inclut une exploration des données basée sur une interface graphique utilisateur et une modélisation utilisant la génération automatique de code R.
  * [Weka](https://www.cs.waikato.ac.nz/ml/weka/) : Un logiciel de machine learning et d’exploration de données visuelle en langage Java.
  * [Apache Drill](https://drill.apache.org/) : Un moteur de requêtes SQL sans schéma pour Apache Hadoop, NoSQL et le stockage cloud. Il prend en charge les interfaces ODBC et JDBC pour l’interrogation de NoSQL et des fichiers à partir d’outils décisionnels standard tels que Power BI, Excel et Tableau.
* Bibliothèques dans les langages R et Python à utiliser dans Azure Machine Learning et d’autres services Azure.
* Git, incluant Git Bash, pour travailler avec les dépôts de code source tels que GitHub et Azure DevOps. Git fournit plusieurs outils en ligne de commande Linux populaires, qui sont accessibles à la fois dans Git Bash et à partir d’une invite de commandes. Awk, sed, perl, grep, find, wget et curl en sont des exemples.
* Outils de développement et éditeurs (RStudio, PyCharm).

### <a name="about-data-science"></a>À propos de la science des données

La science des données consiste à itérer sur une séquence de tâches :

1. Rechercher, charger et prétraiter les données.
1. Générer et tester les modèles.
1. Déployer les modèles à des fins d’utilisation dans des applications intelligentes.

Les scientifiques des données utilisent plusieurs outils pour effectuer ces tâches. La recherche des versions adéquates des logiciels, puis leur téléchargement et leur installation peuvent prendre du temps. La DSVM peut vous faire gagner du temps en fournissant une image prête à l’emploi qui peut être provisionnée sur Azure, avec des outils populaires préalablement installés et configurés.

La DSVM vous permet de démarrer rapidement votre projet d’analytique. Vous pouvez travailler sur des tâches basées sur différents langages, notamment R, Python, SQL et C#. Visual Studio fournit un environnement de développement intégré (IDE) facile à utiliser pour développer et tester votre code. Le SDK Azure est inclus dans la machine virtuelle, ce qui vous permet de créer des applications à l’aide de services disponibles sur la plateforme cloud Microsoft.

Cette image DSVM ne génère pas de frais logiciels. Vous payez uniquement les frais d’utilisation pour Azure. Ces derniers dépendent de la taille de la machine virtuelle que vous provisionnez. Pour plus d’informations, consultez la page [Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows).

### <a name="other-dsvm-versions"></a>Autres versions de la machine virtuelle DSVM

* Une image [Ubuntu](dsvm-ubuntu-intro.md). Elle présente des outils similaires à la DSVM et compte quelques frameworks de deep learning.
* Une image [Linux CentOS](linux-dsvm-intro.md).
* [L’édition Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) de la machine virtuelle Data Science Virtual Machine. Certains outils sont disponibles uniquement sur l’édition Windows Server 2016. Sinon, cet article s’applique également à l’édition Windows Server 2012.

## <a name="prerequisite"></a>Configuration requise

Pour créer une machine virtuelle Microsoft Data Science Virtual Machine, vous devez disposer d’un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.com/free).

## <a name="create-your-dsvm"></a>Créer votre DSVM

Pour créer une instance de DSVM

1. Accédez à la liste des machines virtuelles présentes sur le [portail Azure](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016). Si vous n’êtes pas connecté, vous pouvez être invité à vous connecter à votre compte Azure.
1. Sélectionnez le bouton **Créer** en bas.

   ![Liste des machines virtuelles sur le portail Azure, avec le bouton Créer](./media/provision-vm/configure-data-science-virtual-machine.png)

1. Entrez les informations suivantes pour configurer chacune des étapes présentées dans le volet droit de la capture d’écran :

   1. **Paramètres de base**:
      * **Nom** : Entrez le nom de la DSVM.
      * **Type de disque de machine virtuelle** : Sélectionnez **SSD** ou **HDD**. Pour une instance GPU NC_v1, par exemple une instance basée sur Nvidia Tesla K80, choisissez le type de disque **HDD**.
      * **Nom d’utilisateur** : Entrez l’ID de compte d’administrateur.
      * **Mot de passe** : Entrez le mot de passe du compte administrateur.
      * **Abonnement**: Si vous disposez de plusieurs abonnements, sélectionnez celui qui sera associé à la création et à la facturation de la machine.
      * **Groupe de ressources** : Créez un groupe ou sélectionnez-en un.
      * **Emplacement** : Sélectionnez le centre de données qui convient le mieux. Pour un accès réseau plus rapide, il s’agit du centre de données qui héberge la plupart de vos données ou du centre de données le plus proche de votre emplacement physique.
   1. **Taille**: Sélectionnez le type de serveur qui répond à vos exigences fonctionnelles et à vos contraintes de coût. Sélectionnez **Afficher tout** pour afficher d’autres tailles de machines virtuelles.
   1. **Paramètres**:  
      * **Utiliser la fonctionnalité Disques managés**. Choisissez **Géré** si vous voulez confier à Azure la gestion des disques de la machine virtuelle. Dans le cas contraire, vous devez spécifier un compte de stockage nouveau ou existant.  
      * **Autres paramètres**. Vous pouvez utiliser les valeurs par défaut. Si vous ne souhaitez pas utiliser les valeurs par défaut, survolez le lien d’informations pour obtenir de l’aide sur les différents champs.
   1. **Résumé**: Vérifiez que toutes les informations que vous avez saisies sont correctes. Sélectionnez **Create** (Créer).

> [!NOTE]
> * La machine virtuelle ne génère pas de frais supplémentaires au-delà du coût de calcul de la taille de serveur que vous avez choisie à l’étape de définition du paramètre **Taille**.
> * Le provisionnement prend 10 à 20 minutes. Vous pouvez voir l’état de votre machine virtuelle sur le portail Azure.

## <a name="access-the-dsvm"></a>Accéder à la DSVM

Une fois la machine virtuelle créée et provisionnée, vous pouvez y accéder par le biais d’une connexion Bureau à distance. Utilisez les informations d’identification du compte administrateur que vous avez configurées à l’étape **de base** de la création d’une machine virtuelle. 

Vous êtes maintenant prêt à utiliser les outils qui sont installés et configurés sur la machine virtuelle. La plupart des outils sont accessibles par le biais des icônes du Bureau et des vignettes du menu **Démarrer**.

Vous pouvez également attacher une DSVM à Azure Notebooks pour exécuter des notebooks Jupyter sur la machine virtuelle et contourner les limitations du niveau de service gratuit. Pour plus d’informations, consultez [Gérer et configurer des projets Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

<a name="tools"></a>

## <a name="tools-installed-on-the-dvsm"></a>Outils installés sur la DVSM

Dans les sections suivantes, vous allez découvrir les outils qui sont installés sur la DSVM.

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server - Édition Développeur

Vous pouvez utiliser Microsoft Enterprise Library pour votre analytique, car Machine Learning Server - Édition Développeur est installé sur la machine virtuelle. Anciennement Microsoft R Server, Machine Learning Server est une plateforme d’analytique que vous pouvez déployer à grande échelle. Elle est scalable et prise en charge de façon commerciale.

Machine Learning Server prend en charge différentes statistiques Big Data, la modélisation prédictive et les tâches de machine learning. Il prend en charge la gamme complète d’analyses : exploration, analyse, visualisation et modélisation. En utilisant et en étendant les langages R et Python open source, Machine Learning Server est compatible avec les fonctions et scripts R et Python. Il est également compatible avec les packages Conda, pip et CRAN pour analyser les données au niveau de l’entreprise.

Machine Learning Server traite également les limitations en mémoire du langage R open source en ajoutant le traitement des données en parallèle et mémorisé en bloc. Cela signifie que vous pouvez exécuter une analytique sur des données plus volumineuses que ce que peut contenir la mémoire principale. 

Visual Studio Community est inclus sur la machine virtuelle. Il est doté des outils R pour Visual Studio et des extensions PTVS (Python Tools pour Visual Studio) qui fournissent un IDE complet pour travailler avec R ou Python. Nous fournissons également d’autres IDE comme [RStudio](https://www.rstudio.com) et l’[édition PyCharm Community](https://www.jetbrains.com/pycharm/) sur la machine virtuelle.

### <a name="python"></a>Python

Pour un développement basé sur Python, les versions 2.7 et 3.6 des distributions Anaconda Python sont installées. Ces distributions contiennent le langage Python de base avec environ 300 packages de mathématiques, d’ingénierie et d’analytique données figurant parmi les plus populaires. Vous pouvez utiliser PTVS, qui est installé dans Visual Studio Community 2017. Ou vous pouvez utiliser un des IDE fournis avec Anaconda, comme IDLE ou Spyder. Recherchez et ouvrez l’un de ces packages (touche Windows + S).

> [!NOTE]
> Pour pointer Python Tools pour Visual Studio sur Anaconda Python 2.7, vous devez créer des environnements personnalisés pour chaque version. Pour définir les chemins de ces environnements dans Visual Studio 2017 Community, accédez à **Outils** > **Outils Python** > **Environnements Python**. Sélectionnez ensuite **+ Personnalisé**.

Anaconda Python 3.6 est installé sous C:\Anaconda. Anaconda Python 2.7 est installé sous C:\Anaconda\envs\python2. Pour obtenir des instructions détaillées, consultez la [documentation de PTVS](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

### <a name="the-jupyter-notebook"></a>Jupyter Notebook

La distribution Anaconda est également fournie avec Jupyter Notebook, environnement conçu pour le partage de code et d’analyses. Le serveur Jupyter Notebook est préconfiguré avec les noyaux Python 2.7, Python 3.x, PySpark, Julia et R. Pour démarrer le serveur Jupyter et ouvrir le navigateur afin d’accéder au serveur Notebook, utilisez l’icône du Bureau **Jupyter Notebook**.

Nous plaçons dans un package plusieurs exemples de notebooks en langage Python et R. Après avoir accédé à Jupyter, les notebooks vous montrent comment utiliser les technologies suivantes :

* Machine Learning Server
* SQL Server Machine Learning Services, pour l’analytique dans la base de données
* Python
* Microsoft Cognitive Toolkit
* TensorFlow
* Autres technologies Azure

Après vous être authentifié auprès de Jupyter Notebook avec le mot de passe que vous avez créé, vous pouvez voir le lien vers les exemples dans la page d’accueil du notebook.

### <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

La DSVM comprend Visual Studio Community. Vous pouvez utiliser cette version gratuite du très connu IDE de Microsoft à des fins d’évaluation et pour des petites équipes. Consultez les [termes du contrat de licence du logiciel Microsoft](https://www.visualstudio.com/support/legal/mt171547).

Ouvrez Visual Studio à l’aide de l’icône du Bureau ou du menu **Démarrer**. Recherchez des programmes (en tapant touche Windows + S), suivi de **Visual Studio**. À partir d’ici, vous pouvez créer des projets dans des langages comme C#, Python, R et Node.js. Les plug-ins permettent d’utiliser facilement les services Azure suivants :

* Azure Data Catalog
* Azure HDInsight pour Hadoop et Spark
* Azure Data Lake

Un plug-in appelé Azure Machine Learning for Visual Studio Code s’intègre également à Azure Machine Learning et vous permet de créer rapidement des applications IA.

> [!NOTE]
> Il est possible que vous receviez un message indiquant que votre période d’évaluation a expiré. Entrez les informations d’identification de votre compte Microsoft. Vous pouvez aussi créer un compte gratuit pour accéder à Visual Studio Community.

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer Edition

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

### <a name="azure"></a>Azure

Plusieurs outils Azure sont installés sur la machine virtuelle :

* Un raccourci sur le bureau redirige vers la documentation du Kit de développement logiciel (SDK) Azure.
* Utilisez AzCopy pour déplacer des données dans et en dehors de votre compte de stockage Azure. Pour afficher l’utilisation, entrez **Azcopy** dans une invite de commandes.
* Utilisez l’Explorateur Stockage Azure pour parcourir les objets que vous stockez dans votre compte de stockage Azure. Il permet aussi de copier des données vers et à partir de Stockage Azure. Pour accéder à cet outil, entrez **Explorateur Stockage** dans le champ **Recherche**. Ou recherchez-le dans le menu **Démarrer** de Windows.
* AdlCopy copie des données vers Azure Data Lake. Pour afficher l’utilisation, entrez **adlcopy** dans une invite de commandes.
* L’outil dtui copie des données vers et à partir d’Azure Cosmos DB, une base de données NoSQL dans le cloud. Entrez **dtui** dans une invite de commandes.
* Le runtime d’intégration copie des données entre des sources de données locales et le cloud. Elle est utilisée dans les outils tels que Azure Data Factory.
* Utilisez Azure PowerShell pour gérer vos ressources Azure dans le langage de script PowerShell. Il est également installé sur votre machine virtuelle.

### <a name="power-bi"></a>Power BI

Power BI Desktop est installé sur la DSVM pour vous aider à créer des tableaux de bord et des visualisations. Utilisez cet outil pour extraire des données de différentes sources, créer vos tableaux de bord et vos rapports, puis les publier sur le cloud. Pour plus d’informations, consultez le [site de Power BI](https://powerbi.microsoft.com). Power BI Desktop est accessible à partir du menu **Démarrer**.

> [!NOTE]
> Vous devez disposer d’un compte Microsoft Office 365 pour accéder à Power BI.

### <a name="azure-machine-learning-sdk-for-python"></a>Kit SDK Azure Machine Learning pour Python

Les scientifiques des données et les développeurs d’IA utilisent le SDK Azure Machine Learning pour Python afin de générer et d’exécuter des workflows de machine learning avec [Azure Machine Learning service](../service/overview-what-is-azure-ml.md). Vous pouvez interagir avec le service dans Jupyter Notebooks ou un autre IDE Python à l’aide de frameworks open source tels que TensorFlow et scikit-learn.

Le SDK Python est préinstallé sur la machine virtuelle Data Science Virtual Machine. Pour commencer à utiliser le SDK Python, consultez [Utiliser Python pour démarrer avec Azure Machine Learning](../service/quickstart-create-workspace-with-python.md).

## <a name="more-microsoft-development-tools"></a>Outils de développement Microsoft supplémentaires

Vous pouvez utiliser le programme [Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) pour rechercher et télécharger d’autres outils de développement Microsoft. Il existe également un raccourci vers l’outil sur le bureau Microsoft Data Science Virtual Machine.  

## <a name="important-directories-on-the-vm"></a>Répertoires importants sur la machine virtuelle

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

* Explorez les outils sur la DSVM en ouvrant le menu **Démarrer**.
* Apprenez-en plus sur le service Azure Machine Learning en lisant [Qu’est-ce que le service Azure Machine Learning ?](../service/overview-what-is-azure-ml.md) et en suivant ces [tutoriels](../index.yml).
* Dans l’Explorateur de fichiers, accédez à C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts pour obtenir des exemples utilisant la bibliothèque RevoScaleR en langage R, qui prend en charge l’analytique données à l’échelle de l’entreprise. 
* Lisez l’article intitulé [Dix choses que vous pouvez effectuer sur la machine virtuelle Windows Data Science Virtual Machine](https://aka.ms/dsvmtenthings).
* Découvrez comment créer des solutions analytiques de bout en bout systématiquement à l’aide du [processus TDSP (Team Data Science Process)](../team-data-science-process/index.yml).
* Visitez [Azure AI Gallery](https://gallery.cortanaintelligence.com) pour obtenir des exemples de machine learning et d’analytique données qui utilisent Azure Machine Learning et des services de données connexes sur Azure. Nous avons également inclus une icône dans le menu **Démarrer** et sur le bureau de la machine virtuelle pour accéder à cette galerie.
