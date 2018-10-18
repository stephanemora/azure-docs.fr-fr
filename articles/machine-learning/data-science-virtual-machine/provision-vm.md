---
title: Provisionner la machine virtuelle DSVM Windows sur Azure | Microsoft Docs
description: Configurez et créez une machine virtuelle pour la science des données sur Microsoft Azure pour vos besoins d’analyse et d’apprentissage automatique.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 08/30/2018
ms.author: gokuma
ms.openlocfilehash: 37a30055e567b27f4e215dac162642d4447a8e49
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44298199"
---
# <a name="provision-the-windows-data-science-virtual-machine-on-azure"></a>Provisionner la machine virtuelle DSVM Windows sur Azure
La machine virtuelle Microsoft Data Science Virtual Machine (DSVM) est une image de machine virtuelle Azure. Elle est préinstallée et configurée avec plusieurs outils utilisés dans le cadre de l’analytique données et du machine learning. Les outils suivants sont inclus :

* [Azure Machine Learning](../service/index.yml) Workbench.
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) - Édition Développeur.
* Distribution Anaconda Python.
* Jupyter Notebook avec noyaux R, Python et PySpark.
* Microsoft Visual Studio Community.
* Microsoft Power BI desktop.
* Microsoft SQL Server 2017 - Édition Développeur.
* Une instance autonome Apache Spark pour le développement et test locaux.
* [JuliaPro](https://juliacomputing.com/products/juliapro.html).
* Outils de machine learning et d’analytique données :
  * Infrastructures d’apprentissage profond. Un ensemble complet d’infrastructures d’IA est inclus dans la machine virtuelle : [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), mxNet et Keras.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit). Un système de machine learning rapide prenant en charge des techniques comme le hachage en ligne, les réductions allreduce, learning2search et l’apprentissage actif et interactif.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/). Un outil qui offre une implémentation rapide et précise des arborescences optimisées.
  * [Rattle](https://togaware.com/rattle/), l’outil analytique R pour apprendre facilement. Un outil qui facilite la prise en main de l’analytique données et du machine learning en langage R. Il inclut une exploration des données basée sur une interface graphique utilisateur et une modélisation utilisant la génération automatique de code R.
  * [Weka](http://www.cs.waikato.ac.nz/ml/weka/). Un logiciel de machine learning et d’exploration de données visuelle en langage Java.
  * [Apache Drill](https://drill.apache.org/). Un moteur de requêtes SQL sans schéma pour Apache Hadoop, NoSQL et le stockage cloud.  Il prend en charge les interfaces ODBC et JDBC pour l’interrogation de NoSQL et des fichiers à partir d’outils décisionnels standard tels que Power BI, Excel et Tableau.
* Bibliothèques dans les langages R et Python à utiliser dans Azure Machine Learning et d’autres services Azure.
* Git, incluant Git Bash, pour travailler avec les référentiels de code source, notamment GitHub et Azure DevOps. Git fournit plusieurs utilitaires en ligne de commande Linux populaires, qui sont accessibles à la fois sur Git Bash et à partir d’une invite de commandes. Awk, sed, perl, grep, find, wget et curl en sont des exemples.

La science des données consiste à itérer sur une séquence de tâches :

1. Rechercher, charger et prétraiter les données.
1. Générer et tester les modèles.
1. Déployer les modèles à des fins d’utilisation dans des applications intelligentes.

Les scientifiques des données utilisent plusieurs outils pour effectuer ces tâches. La recherche des versions adéquates des logiciels, puis leur téléchargement et leur installation peuvent prendre du temps. La machine virtuelle Microsoft Data Science Virtual Machine peut vous faire gagner du temps en fournissant une image prête à l’emploi qui peut être approvisionnée sur Azure avec des outils populaires préalablement installés et configurés. 

Le renvoi de la machine virtuelle pour la science des données démarre votre projet d’analyse. Vous pouvez travailler sur des tâches basées sur différents langages, notamment R, Python, SQL et C#. Visual Studio fournit un environnement de développement intégré (IDE) facile à utiliser pour développer et tester votre code. Le Kit de développement logiciel (SDK) Azure est inclus dans la machine virtuelle. Par conséquent, vous pouvez créer vos applications à l’aide de divers services de plateforme cloud de Microsoft. 

Cette image de machine virtuelle de science des données ne génère pas de frais. Vous payez uniquement les frais d’utilisation pour Azure. Ces derniers dépendent de la taille de la machine virtuelle que vous approvisionnez. Vous trouverez plus d’informations sur les frais de calcul dans la section **Abonnements + tarification** de la page [Data Science Virtual Machine - Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice). 

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Autres versions de la machine virtuelle Data Science Virtual Machine
* Une image [Ubuntu](dsvm-ubuntu-intro.md). Elle possède de nombreux outils similaires à la machine virtuelle DSVM et compte quelques infrastructures d’apprentissage profond supplémentaires. 
* Une image [Linux CentOS](linux-dsvm-intro.md).
* [L’édition Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm) de la machine virtuelle Data Science Virtual Machine. Certains outils sont disponibles uniquement sur l’édition Windows Server 2016. Sinon, cet article s’applique également à l’édition Windows Server 2012.

## <a name="prerequisite"></a>Configuration requise
Pour créer une machine virtuelle Microsoft Data Science Virtual Machine, vous devez disposer d’un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](http://azure.com/free).


## <a name="create-your-microsoft-data-science-virtual-machine"></a>Création d’une machine virtuelle pour la science des données
Pour créer une instance de la machine virtuelle de sciences des données Microsoft, suivez ces étapes :

1. Accédez à la liste des machines virtuelles présentes sur le [portail Azure](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016). Si vous n’êtes pas déjà connecté, vous pouvez être invité à vous connecter à votre compte Azure.
1. Sélectionnez le bouton **Créer** au bas de l’écran pour accéder à un Assistant.

  ![configure-data-science-vm](./media/provision-vm/configure-data-science-virtual-machine.png) 

1. L’Assistant qui crée la machine virtuelle Data Science Virtual Machine a besoin d’une **entrée**. L’entrée suivante est nécessaire pour configurer chacune des étapes présentées à droite de la figure :

  a. **Paramètres de base**:

    i. **Nom**. Nom du serveur de science des données que vous créez.  

    ii. **Type de disque de machine virtuelle**. Choisissez **SSD** ou **HDD**. Pour une instance GPU NC_v1, par exemple une instance basée sur NVidia Tesla K80, choisissez le type de disque **HDD**.   

    iii. **Nom d’utilisateur**. ID de compte administrateur pour vous connecter.   

    iv. **Mot de passe**. Mot de passe du compte administrateur.  

    v. **Abonnement**. Si vous disposez de plusieurs abonnements, sélectionnez celui qui sera associé à la création et à la facturation de la machine.   

    vi. **Groupe de ressources**. Vous pouvez en créer un nouveau ou utiliser un groupe existant.   

    vii. **Emplacement**. Sélectionnez le centre de données qui convient le mieux. Pour un accès réseau plus rapide, il s’agit du centre de données qui héberge la plupart de vos données ou du centre de données le plus proche de votre emplacement physique.   

  b. **Taille**. Sélectionnez l’un des types de serveur qui répond à vos exigences fonctionnelles et à vos contraintes de coût. Sélectionnez **Afficher tout** pour afficher d’autres tailles de machines virtuelles.  

  c. **Paramètres**:  

    i. **Utiliser des disques gérés**. Choisissez **Géré** si vous voulez confier à Azure la gestion des disques de la machine virtuelle. Dans le cas contraire, vous devez spécifier un compte de stockage nouveau ou existant.  

    ii. **Autres paramètres**. Vous pouvez utiliser les valeurs par défaut. Si vous ne souhaitez pas utiliser les valeurs par défaut, survolez le lien d’informations pour obtenir de l’aide sur les différents champs.  

  d. **Résumé**. Vérifiez que toutes les informations que vous avez saisies sont correctes. Sélectionnez **Créer**. 

> [!NOTE]
> * La machine virtuelle ne génère pas de frais supplémentaires au-delà du coût de calcul de la taille de serveur que vous avez choisie à l’étape de définition du paramètre **Taille**. 
> * L’approvisionnement prend environ 10 à 20 minutes. Son état s’affiche sur le Portail Azure.
> 
> 

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Accès à une machine virtuelle pour la science des données
Une fois la machine virtuelle créée et approvisionnée, vous pouvez vous y connecter à l’aide du bureau distant en utilisant les informations d’identification du compte administrateur que vous avez configurées dans la section **Paramètres de base** précédente. Vous êtes maintenant prêt à utiliser les outils qui sont installés et configurés sur la machine virtuelle. La plupart des outils ont des icônes de bureau et des vignettes dans le menu de démarrage. 


## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Outils installés sur la machine virtuelle de science des données

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server - Édition Développeur
Vous pouvez utiliser Microsoft Enterprise Library pour les langages R ou Python évolutifs, afin d’effectuer vos analyses, car Machine Learning Server - Édition Développeur est installé sur la machine virtuelle. Anciennement connu sous le nom de Microsoft R Server, Machine Learning Server est une plateforme d’analyse d’entreprise que vous pouvez largement déployer. Elle est disponible pour R et Python, et est évolutive, prise en charge et sécurisée. 

Machine Learning Server prend en charge différentes statistiques Big Data, la modélisation prédictive et les tâches de machine learning. Il prend en charge la gamme complète d’analyses : exploration, analyse, visualisation et modélisation. En utilisant et en étendant les langages R et Python open source, Machine Learning Server est compatible avec les fonctions et scripts R et Python. Il est également compatible avec les packages Conda, pip et CRAN pour analyser les données au niveau de l’entreprise. 

Machine Learning Server traite également les limitations en mémoire du langage R open source en ajoutant le traitement des données en parallèle et mémorisé en bloc. Ainsi, vous pouvez exécuter des analyses de données plus volumineuses que ce que peut contenir la mémoire principale. Visual Studio Community est inclus sur la machine virtuelle. Il est doté des outils R pour Visual Studio et des outils Python Tools pour l’extension Visual Studio (PTVS) qui fournissent un IDE complet pour travailler avec R ou Python. Nous fournissons également d’autres IDE comme [RStudio](http://www.rstudio.com) et [l’édition PyCharm Community](https://www.jetbrains.com/pycharm/) sur la machine virtuelle. 

### <a name="python"></a>Python
Pour un développement basé sur Python, les versions 2.7 et 3.6 des distributions Anaconda Python ont été installées. Ces distributions contiennent le langage Python de base avec environ 300 packages de mathématiques, d’ingénierie et d’analytique données figurant parmi les plus populaires. Vous pouvez utiliser PTVS, qui est installé dans Visual Studio Community 2017. Ou vous pouvez utiliser un des IDE fournis avec Anaconda comme IDLE ou Spyder. Recherchez et exécutez un de ces packages (Win+S).

> [!NOTE]
> Pour pointer Python Tools pour Visual Studio sur Anaconda Python 2.7, vous devez créer des environnements personnalisés pour chaque version. Pour définir les chemins d’accès à ces environnements dans Visual Studio 2017 Community, accédez à **Outils** > **Outils Python** > **Environnements Python**. Sélectionnez ensuite **+ Personnalisé**. 
> 
> 

Anaconda Python 3.6 est installé sous **C:\Anaconda**. Anaconda Python 2.7 est installé sous **c:\Anaconda\envs\python2**. Pour obtenir des instructions détaillées, consultez la [documentation de PTVS](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters). 

### <a name="the-jupyter-notebook"></a>Jupyter Notebook
La distribution Anaconda est également fournie avec Jupyter Notebook, un environnement conçu pour le partage de code et d’analyses. Le serveur Jupyter Notebook est préconfiguré avec les noyaux Python 2.7, Python 3.x, PySpark, Julia et R. Pour démarrer le serveur Jupyter et lancer le navigateur pour accéder au serveur Jupyter Notebook, cliquez sur une icône du bureau nommée **Jupyter Notebook**. 

Nous plaçons dans un package plusieurs exemples de notebooks en langage Python et R. Après avoir accédé à Jupyter, les notebooks vous montrent comment utiliser les technologies suivantes :

* Machine Learning Server.
* SQL Server Machine Learning Services, pour l’analyse dans la base de données. 
* Python.
* Microsoft Cognitive Toolkit.
* Tensorflow.
* Autres technologies Azure. 

Après vous être authentifié auprès de Jupyter Notebook avec le mot de passe créé à l’étape précédente, vous pouvez voir le lien vers les exemples sur la page d’accueil du notebook. 

### <a name="visual-studio-community-2017"></a>Visual Studio Community 2017
Visual Studio Community est installé sur la machine virtuelle. Vous pouvez utiliser cette version gratuite de l’IDE populaire de Microsoft à des fins d’évaluation et dans le cadre de projets en petites équipes. Consultez les [conditions des licences](https://www.visualstudio.com/support/legal/mt171547). 

Ouvrez Visual Studio en double-cliquant sur l’icône du bureau ou via le menu **Démarrer** . Recherchez des programmes en tapant Win + S, suivi de **Visual Studio**. À partir d’ici, vous pouvez créer des projets dans des langages comme C#, Python, R et node.js. Les plug-ins permettent d’utiliser facilement les services Azure suivants :
* Azure Data Catalog
* Azure HDInsight Hadoop et Spark
* Azure Data Lake 

Il existe également un plug-in appelé ```Visual Studio Tools for AI```, qui s’intègre en toute transparence à Azure Machine Learning et vous permet de créer rapidement des applications IA. 

> [!NOTE]
> Il est possible que vous receviez un message indiquant que votre période d’évaluation a expiré. Entrez les informations d’identification de votre compte Microsoft. Vous pouvez aussi créer un compte gratuit pour accéder à Visual Studio Community. 
> 
> 

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer Edition
Une version développeur de SQL Server 2017 avec Machine Learning Services pour exécuter l’analyse dans la base de données est fournie sur la machine virtuelle en langage R ou Python. Machine Learning Services est doté d’une plateforme pour développer et déployer des applications intelligentes. Pour créer des modèles et générer des prédictions pour vos données SQL Server, vous pouvez utiliser ces langages et les nombreux packages issus de la communauté. Vous pouvez conserver les analyses à proximité des données, car Machine Learning Services, dans la base de données, intègre à la fois les langages R et Python à SQL Server. Cette intégration permet d’éliminer les coûts et les risques de sécurité liés au déplacement des données.

> [!NOTE]
> L’édition SQL Server Développeur est uniquement destinée à être utilisée à des fins de test et de développement. Vous devez posséder une licence pour l’exécuter en production. 
> 
> 

Vous pouvez accéder à SQL Server en lançant Microsoft SQL Server Management Studio. Le nom de votre machine virtuelle correspond au **Nom du serveur**. Utilisez l’authentification Windows pour vous connecter à Windows en tant qu’administrateur. Dans SQL Server Management Studio, vous pouvez créer d’autres utilisateurs, créer des bases de données, importer des données et exécuter des requêtes SQL. 

Pour activer l’analyse dans la base de données à l’aide de SQL Machine Learning Services, exécutez la commande suivante en tant qu’action unique dans SQL Server Management Studio après vous être connecté en tant qu’administrateur du serveur : 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure
Plusieurs outils Azure sont installés sur la machine virtuelle :

* Un raccourci sur le bureau redirige vers la documentation du Kit de développement logiciel (SDK) Azure. 
* **AzCopy** permet de déplacer des données dans et en dehors de votre compte stockage Azure. Pour afficher l’utilisation, entrez **Azcopy** dans une invite de commandes. 
* Utilisez **l’Explorateur Stockage Azure** pour parcourir les objets que vous stockez dans votre compte stockage Azure. Il permet aussi de transférer les données vers et depuis Stockage Azure. Pour accéder à cet outil, vous pouvez entrer **Explorateur Stockage** dans le champ **Recherche**. Ou recherchez-le dans le menu **Démarrer** de Windows. 
* **Adlcopy** déplace des données vers Azure Data Lake. Pour afficher l’utilisation, entrez **adlcopy** dans une invite de commandes. 
* **dtui** déplace des données en direction et en provenance d’Azure Cosmos DB, une base de données NoSQL sur le cloud. Entrez **dtui** dans une invite de commandes. 
* **Azure Data Factory Integration Runtime** déplace des données entre des sources de données locales et le cloud. Elle est utilisée dans les outils tels que Azure Data Factory. 
* **Microsoft Azure PowerShell** est un outil qui permet de gérer vos ressources Azure dans le langage de script PowerShell. Il est également installé sur votre machine virtuelle. 

### <a name="power-bi"></a>Power BI
**Power BI Desktop** est installé pour vous aider à créer des tableaux de bord et des visualisations. Utilisez cet outil pour extraire des données de différentes sources, créer vos tableaux de bord et vos rapports, puis les publier sur le cloud. Pour plus d’informations, consultez le site de [Power BI](http://powerbi.microsoft.com). Vous pouvez trouver Power BI desktop dans le menu **Démarrer**. 

> [!NOTE]
> Vous devez disposer d’un compte Microsoft Office 365 pour accéder à Power BI. 
> 
> 

### <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench

Azure Machine Learning Workbench est une application de bureau et une interface de ligne de commande. Workbench intègre une préparation des données qui apprend au fur et à mesure vos étapes de préparation des données. Il fournit également un outil de gestion de projets, un historique des exécutions et une intégration de notebooks pour booster votre productivité. 

Vous pouvez utiliser des infrastructures open source, telles que TensorFlow, Cognitive Toolkit, Spark ML et scikit-learn pour développer vos modèles. Sur la DSVM, nous fournissons une icône de bureau pour installer Azure Machine Learning Workbench dans le répertoire **%LOCALAPPDATA%** de chaque utilisateur. 

Chaque utilisateur de Workbench doit effectuer une action unique. Double-cliquez sur l’icône du bureau ```AzureML Workbench Setup``` pour installer l’instance de Workbench. Azure Machine Learning crée et utilise également un environnement Python par utilisateur qui est extrait dans le répertoire **%LOCALAPPDATA%\amlworkbench\python**.

## <a name="more-microsoft-development-tools"></a>Outils de développement Microsoft supplémentaires
Le programme [Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) vous permet de rechercher et de télécharger d’autres outils de développement Microsoft. Il existe également un raccourci vers l’outil fourni sur le bureau de la machine virtuelle Microsoft Data Science Virtual Machine.  

## <a name="important-directories-on-the-vm"></a>Répertoires importants sur la machine virtuelle
| Item | Répertoire |
| --- | --- |
| Configurations du serveur Jupyter Notebook | C:\ProgramData\jupyter |
| Répertoire des exemples de Jupyter Notebook | c:\dsvm\notebooks et c:\users\<username>\notebooks |
| Autres exemples | c:\dsvm\samples |
| Anaconda, valeur par défaut : Python 3.6 | c:\Anaconda |
| Environnement Anaconda Python 2.7 | c:\Anaconda\envs\python2 |
| Microsoft Machine Learning Server (autonome) Python | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Instance R par défaut, Machine Learning Server (autonome) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| Répertoire de l’instance dans la base de données SQL Machine Learning Services | C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Azure Machine Learning Workbench, par utilisateur | %localappdata%\amlworkbench | 
| Outils divers | c:\dsvm\tools |

> [!NOTE]
> Dans l’édition Windows Server 2012 de la machine virtuelle DSVM et l’édition Windows Server 2016 antérieure à mars 2018, l’environnement Anaconda par défaut est Python 2.7. L’environnement secondaire est Python 3.5, à l’emplacement **c:\Anaconda\envs\py35**. 
> 
> 

## <a name="next-steps"></a>Étapes suivantes

* Découvrez les outils sur la machine virtuelle de science des données en sélectionnant le menu **Démarrer**.
* Pour en savoir plus sur Azure Machine Learning Services et le Workbench, visitez la [page de démarrage rapide et des didacticiels](../service/index.yml) du produit. 
* Accédez à **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** pour obtenir des exemples utilisant la bibliothèque RevoScaleR en langage R, qui prend en charge l’analytique données à l’échelle de l’entreprise.  
* Lisez l’article intitulé [Dix choses que vous pouvez effectuer sur la machine virtuelle Windows Data Science Virtual Machine](http://aka.ms/dsvmtenthings).
* Découvrez comment créer des solutions analytiques de bout en bout systématiquement à l’aide du [processus TDSP (Team Data Science Process)](../team-data-science-process/index.yml).
* Visitez [Azure AI Gallery](http://gallery.cortanaintelligence.com) pour obtenir des exemples de machine learning et d’analytique données qui utilisent Azure Machine Learning et des services de données connexes sur Azure. Nous avons également inclus une icône dans le menu **Démarrer** et sur le bureau de la machine virtuelle pour accéder à cette galerie.

