---
title: 'Référence : Instance CentOS de Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Détails sur les outils inclus dans CentOS Data Science Virtual Machine
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: db49a9f5e0e6675d93cb58d6af9c92fac21e8b74
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525836"
---
# <a name="reference-centos-linux-data-science-virtual-machine"></a>Référence : CentOS (Linux) Data Science Virtual Machine

La machine virtuelle DSVM Linux est une machine virtuelle Azure basée sur CentOS. La machine DSVM Linux est fournie avec une collection d’outils préinstallés que vous pouvez utiliser pour l’analytique données et le Machine Learning. 

Les principaux composants logiciels inclus dans une machine DSVM Linux sont les suivants :

* Un système d’exploitation de distribution Linux CentOS
* Microsoft Machine Learning Server
* Une distribution Anaconda Python (versions 3.5 et 2.7), comprenant les bibliothèques courantes d’analyse des données
* JuliaPro, une distribution organisée du langage Julia avec des bibliothèques scientifiques et d’analytique données.
* Une instance Spark autonome et Hadoop à nœud unique (HDFS, YARN)
* JupyterHub, un serveur de notebooks Jupyter multi-utilisateur prenant en charge les noyaux R, Python, PySpark et Julia
* Azure Storage Explorer.
* Azure CLI, interface de ligne de commande Azure pour la gestion des ressources Azure
* Une base de données PostgresSQL
* Outils Machine Learning :
  * [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK) (CNTK), un kit de ressources logicielles d’apprentissage profond Microsoft Research
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit), un système de Machine Learning rapide prenant en charge des techniques comme l’apprentissage de type hachage, en ligne, allreduce, réductions, learning2search, actif et interactif
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/), un outil qui offre une implémentation rapide et précise des arborescences optimisées
  * [Rattle](https://togaware.com/rattle/), un outil graphique qui permet de bien démarrer avec l’analytique données et le Machine Learning en R. Rattle propose des fonctions de modélisation et d’exploration des données basée sur l’interface utilisateur graphique par le biais de la génération automatique de code R.
* SDK Azure dans Java, Python, node.js, Ruby et PHP.
* Bibliothèques dans les langages R et Python à utiliser dans Azure Machine Learning et d’autres services Azure
* Outils de développement et éditeurs (RStudio, PyCharm, IntelliJ, Emacs, gedit, vi)

La science des données consiste à itérer sur une séquence de tâches :

1. Rechercher, charger et prétraiter les données.
1. Générer et tester les modèles.
1. Déployer les modèles à des fins d’utilisation dans des applications intelligentes.

Les scientifiques de données utilisent différents outils pour effectuer ces tâches. Le fait de rechercher les versions adéquates des logiciels, puis de les télécharger, de les compiler et de les installer peut prendre un certain temps.

La machine virtuelle DSVM Linux peut alléger considérablement ces tâches. Utilisez-la pour démarrer rapidement votre projet d’analytique. La machine virtuelle DSVM vous permet de travailler sur des tâches basées sur différents langages, notamment R, Python, SQL, Java et C++. Eclipse fournit un environnement de développement intégré (IDE) facile à utiliser pour le développement et le test de votre code. Le SDK Azure inclus dans la machine virtuelle DSVM vous permet de créer des applications à l’aide de divers services dans Linux pour la plateforme cloud Microsoft. D’autres langages sont préinstallés, notamment Ruby, Perl, PHP et node.js.

L’image DSVM n’entraîne pas de frais logiciels. Vous payez uniquement les frais d’utilisation matérielle Azure qui sont basés sur la taille de la machine virtuelle provisionnée avec l’image DSVM. Pour plus d’informations sur les frais de calcul, consultez la [description de l’application Data Science Virtual Machine for Linux (CentOS)](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) dans la Place de marché Azure.


## <a name="machine-learning-server"></a>Machine Learning Server

R est le langage le plus répandu pour l’analyse des données et l’apprentissage automatique. Si vous souhaitez utiliser R pour votre analytique, la machine virtuelle DSVM comprend Machine Learning Server avec Microsoft R Open et Math Kernel Library. Math Kernel Library optimise les opérations mathématiques courantes dans des algorithmes d’analytique. R Open est entièrement compatible avec CRAN-R. Toutes les bibliothèques R publiées dans CRAN peuvent être installées sur R Open. 

Vous pouvez utiliser Machine Learning Server pour mettre à l’échelle et faire fonctionner les modèles R dans des services web. Vous pouvez modifier vos programmes R dans l’un des éditeurs par défaut, comme RStudio, vi ou Emacs. L’éditeur Emacs est préinstallé sur la machine DSVM. Le package Emacs ESS (Emacs Speaks Statistics) simplifie l’utilisation des fichiers R dans l’éditeur Emacs.

Pour ouvrir la console R, entrez **R** dans l’interpréteur de commandes. Cette commande vous permet d’accéder à un environnement interactif. Pour développer votre programme R, vous utilisez généralement un éditeur comme Emacs ou vi, puis exécutez les scripts en R. RStudio fournit un environnement de développement graphique intégré et complet pour développer votre programme R.

Un script R que vous pouvez utiliser pour installer les [20 premiers packages R](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) est inclus dans la machine virtuelle DSVM. Une fois dans l’interface interactive R, vous pouvez exécuter ce script. Comme mentionné précédemment, pour ouvrir cette interface, vous devez entrer **R** dans l’interpréteur de commandes.  

## <a name="python"></a>Python

Anaconda Python est installé avec les environnements Python 3.5 et 2.7. L’environnement 2.7 est appelé _root_, et l’environnement 3.5 est appelé _py35_. Cette distribution contient le langage Python de base avec environ 300 packages de mathématiques, d’ingénierie et d’analyse de données figurant parmi les plus populaires.

L’environnement py35 est l’environnement par défaut. Pour activer l’environnement root (2.7), exécutez la commande suivante :

```bash
source activate root
```

Pour réactiver l’environnement racine py35, exécutez la commande suivante :

```bash
source activate py35
```

Pour appeler la session interactive Python, entrez **python** dans l’interpréteur de commandes. 

Utilisez Conda ou pip pour installer des bibliothèques Python supplémentaires. Pour pip, activez d’abord l’environnement approprié si vous ne voulez pas utiliser l’environnement par défaut :

```bash
source activate root
pip install <package>
```

Vous pouvez également spécifier le chemin complet de pip :

```bash
/anaconda/bin/pip install <package>
```

Pour Conda, vous devez toujours spécifier le nom de l’environnement (py35 ou root) :

```bash
conda install <package> -n py35
```

Si vous travaillez sur une interface graphique ou si le transfert X11 est configuré, vous pouvez entrer **pycharm** pour ouvrir l’IDE Python PyCharm. Vous pouvez utiliser les éditeurs de texte par défaut. En outre, vous pouvez utiliser Spyder, un IDE Python fourni avec les distributions Anaconda Python. Spyder requiert un bureau graphique ou le transfert X11. L’environnement de bureau graphique fournit un raccourci vers Spyder.

## <a name="jupyter-notebook"></a>Jupyter Notebook

La distribution Anaconda est également fournie avec Jupyter Notebook, qui est un environnement conçu pour le partage de code et d’analyses. Accédez à Jupyter Notebook via JupyterHub. Vous vous connectez en utilisant votre nom d’utilisateur Linux local et votre mot de passe.

Le serveur Jupyter Notebook a été préconfiguré avec Python 2, Python 3 et les noyaux R. Utilisez l’icône de bureau **Jupyter Notebook** pour ouvrir le navigateur et accéder au serveur Jupyter Notebook. Si vous accédez à la machine virtuelle DSVM par le biais du client SSH ou X2Go, vous pouvez également accéder au serveur Jupyter Notebook à l’adresse suivante : https:\//localhost:8000/.

> [!NOTE]
> Si vous recevez des avertissements relatifs au certificat, vous pouvez les ignorer.

Vous pouvez accéder au serveur Jupyter Notebook à partir de n’importe quel hôte. Entrez **https:\//\<nom DNS ou adresse IP de la machine DSVM\>:8000/** .

> [!NOTE]
> Le port 8000 est ouvert par défaut dans le pare-feu lorsque la machine virtuelle DSVM est provisionnée. 

Microsoft a inclus deux exemples de notebooks : l’un en Python et l’autre en R. Une fois que vous vous serez authentifié auprès du serveur Jupyter Notebook avec votre nom d’utilisateur Linux local et votre mot de passe, vous verrez le lien vers les exemples dans la page d’accueil Jupyter Notebook. Pour créer un notebook, sélectionnez **Nouveau**, puis sélectionnez le noyau de langage que vous souhaitez utiliser. Si vous ne voyez pas le bouton **Nouveau**, sélectionnez l’icône **Jupyter** en haut à gauche pour accéder à la page d’accueil du serveur de notebook.

## <a name="spark-standalone"></a>Instance autonome de Spark 

Une instance autonome de Spark est préinstallée sur la machine DSVM Linux afin de vous aider à développer des applications Spark localement avant de procéder aux tests et aux déploiements sur des clusters de grande taille. 

Vous pouvez exécuter des programmes PySpark via le noyau Jupyter. Lorsque vous ouvrez Jupyter, sélectionnez le bouton **Nouveau** pour afficher la liste des noyaux disponibles. **Spark – Python** est le noyau PySpark qui vous permet de créer des applications Spark à l’aide du langage Python. Vous pouvez également utiliser un IDE Python comme PyCharm ou Spyder pour créer votre programme Spark. 

Dans cette instance autonome, la pile Spark s’exécute dans le programme client appelant. Il est ainsi plus rapide et plus facile de résoudre les problèmes que dans le cadre d’un développement sur un cluster Spark.

Jupyter fournit un exemple de notebook PySpark. Il se trouve dans le répertoire SparkML, sous le répertoire de base de Jupyter ($HOME/notebooks/SparkML/pySpark). 

Si vous programmez en R pour Spark, vous pouvez utiliser Machine Learning Server, SparkR ou sparklyr. 

Avant toute exécution dans un contexte Spark dans Machine Learning Server, vous devez effectuer une opération de configuration unique pour activer une instance YARN et HDFS Hadoop à nœud unique locale. Par défaut, les services Hadoop sont installés mais désactivés sur la DSVM. Pour activer les services Hadoop, exécutez les commandes suivantes en tant que racine la première fois :

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Vous pouvez arrêter les services liés à Hadoop lorsque vous n’en avez pas besoin en exécutant `systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn`.

Le répertoire/dsvm/samples/MRS fournit un exemple qui montre comment développer et tester Machine Learning Server dans un contexte Spark distant (l’instance Spark autonome sur la machine DSVM).

## <a name="ides-and-editors"></a>IDE et éditeurs

Vous pouvez choisir parmi plusieurs éditeurs de code, notamment vi/VIM, Emacs, gedit, PyCharm, RStudio, Eclipse, LaTeX et IntelliJ. 

* gedit, Eclipse, IntelliJ, R Studio et PyCharm sont des éditeurs graphiques. Pour les utiliser, vous devez être connecté à un environnement de bureau graphique. Utilisez les raccourcis du menu de l’application et du bureau pour les ouvrir.

* Vim et Emacs sont des éditeurs de texte. Sur Emacs, le package du module complémentaire ESS facilite l’utilisation du langage R dans l’éditeur Emacs. Pour plus d’informations, consultez le [site web d’ESS](https://ess.r-project.org/).

* Eclipse est un IDE open source et extensible qui prend en charge plusieurs langages. La version de l’IDE Eclipse qui est installée sur la machine DSVM est la version pour les développeurs Java. Si vous souhaitez étendre l’environnement, vous pouvez installer des plug-ins pour plusieurs langages populaires. 

  Le plug-in Azure Toolkit for Eclipse est installé avec Eclipse sur la machine DSVM. Vous pouvez utiliser Azure Toolkit for Eclipse pour créer, développer, tester et déployer des applications Azure à l’aide de l’environnement de développement Eclipse qui prend en charge des langages comme Java.

  Le SDK Azure pour Java est également installé avec Azure Toolkit for Eclipse sur la machine DSVM. Le SDK Azure pour Java permet d’accéder à différents services Azure à partir d’un environnement Java. 
  
  Pour plus d’informations, consultez [Kit de ressources Azure pour Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

* LaTeX est installé via le package texlive, en même temps qu’un package de module complémentaire Emacs appelé [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Ce package simplifie la création de vos documents LaTeX dans Emacs. 

## <a name="databases"></a>Bases de données

La machine DSVM Linux vous donne accès à plusieurs bases de données et outils en ligne de commande.

### <a name="postgressql"></a>PostgresSQL

La base de données open source PostgresSQL est disponible sur la machine virtuelle DSVM, avec les services en cours d’exécution et la commande initdb terminée. Vous devez créer des bases de données et des utilisateurs. Pour plus d’informations, consultez la [documentation PostgresSQL](https://www.postgresql.org/docs/).  

### <a name="squirrel-sql"></a>SQuirreL SQL

Le client SQL graphique SQuirrel SQL peut se connecter à différentes bases de données (comme SQL Server, PostgresSQL et MySQL) et exécuter des requêtes SQL. Vous pouvez exécuter SQuirrel SQL dans une session d’environnement de bureau graphique (dans un client X2Go, par exemple) à l’aide d’une icône de bureau. Vous pouvez également exécuter le client à l’aide de la commande suivante dans l’interpréteur de commandes :

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh /usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Avant la première utilisation, configurez vos pilotes et alias de bases de données. Les pilotes JDBC se trouvent sous /usr/share/Java/jdbcdrivers.

Pour plus d’informations, consultez [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-sql-server"></a>Outils en ligne de commande pour l’accès à SQL Server

Le package de pilotes ODBC pour SQL Server est également fourni avec deux outils en ligne de commande :

* **bcp** : cet outil copie les données en bloc entre une instance de SQL Server et un fichier de données dans un format spécifié par l’utilisateur. Vous pouvez utiliser l’utilitaire bcp pour importer un grand nombre de nouvelles lignes dans des tables SQL Server, ou pour exporter des données hors des tables sous forme de fichiers de données. Pour importer des données dans une table, vous devez utiliser un fichier de format créé pour cette table. Sinon, vous devez comprendre la structure de la table et les types de données qui sont valides pour ses colonnes.

  Pour plus d’informations, consultez [Se connecter avec bcp](https://msdn.microsoft.com/library/hh568446.aspx).

* **sqlcmd** : vous pouvez vous servir de l’utilitaire sqlcmd pour entrer des instructions Transact-SQL, des procédures système et des fichiers de script à l’invite de commandes. L’utilitaire sqlcmd exécute des lots Transact-SQL à l’aide d’ODBC.

  Pour plus d’informations, consultez [Se connecter avec sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

  > [!NOTE]
  > L’outil présente de légères différences entre les plateformes Linux et Windows. Consultez la documentation pur plus d'informations.

### <a name="database-access-libraries"></a>Bibliothèques pour l’accès aux bases de données

Les bibliothèques permettant d’accéder aux bases de données sont disponibles en R et en Python :

* Pour R, le package RODBC ou dplyr permet d’interroger ou d’exécuter des instructions SQL sur le serveur de bases de données.
* Pour Python, la bibliothèque pyodbc fournit l’accès aux bases de données avec ODBC en tant que couche sous-jacente.

## <a name="azure-tools"></a>Outils Azure

Les outils Azure suivants sont installés sur la machine virtuelle DSVM :

* **Azure CLI** : vous pouvez utiliser l’interface de ligne de commande Azure pour créer et gérer des ressources Azure par le biais de commandes dans un interpréteur. Pour ouvrir les outils Azure, entrez **azure help**. Pour plus d’informations, consultez la [page de documentation relative à l’interface CLI Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Explorateur Stockage Azure** : il s’agit d’un outil graphique permettant de parcourir les objets stockés dans votre compte de stockage Azure, et de charger et télécharger des données dans des objets blob Azure. Vous pouvez accéder à l’Explorateur de stockage à partir de l’icône de raccourci sur le bureau. Vous pouvez également l’ouvrir à partir d’une invite de commandes en entrant **StorageExplorer**. Vous devez être connecté à partir d’un client X2Go ou avoir configuré le transfert X11.
* **Bibliothèques Azure** : Les bibliothèques suivantes sont préinstallées sur la machine DSVM :
  
  * **Python** : les bibliothèques Azure Python sont *azure*, *azureml*, *pydocumentdb* et *pyodbc*. Avec les trois premières bibliothèques, vous pouvez accéder aux services de stockage Azure, à Azure Machine Learning et à Azure Cosmos DB (base de données NoSQL sur Azure). La quatrième bibliothèque, pyodbc (avec le pilote Microsoft ODBC pour SQL Server), permet l’accès à SQL Server, Azure SQL Database et Azure SQL Data Warehouse à partir de Python à l’aide d’une interface ODBC. Entrez **pip list** pour voir la liste de toutes les bibliothèques. Veillez à exécuter cette commande dans les environnements Python 2.7 et 3.5.
  * **R** : les bibliothèques Azure R sont AzureML et RODBC.
  * **Java** : la liste des bibliothèques Java Azure est disponible dans le répertoire /dsvm/sdk/AzureSDKJava de la machine virtuelle DSVM. Les bibliothèques principales sont les API de gestion et de stockage Azure, Azure Cosmos DB et les pilotes JDBC pour SQL Server.  

Vous pouvez accéder au [portail Azure](https://portal.azure.com) à partir du navigateur Firefox préinstallé. Sur le portail Azure, vous pouvez créer, gérer et superviser les ressources Azure.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning est un service cloud complètement managé permettant de créer, déployer et partager facilement des solutions d’analyse prédictive. Créez vos expériences et modèles dans Azure Machine Learning Studio (classique). Pour accéder à Azure Machine Learning à partir d’un navigateur web sur la machine DSVM, accédez à [Microsoft Azure Machine Learning](https://studio.azureml.net).

Une fois connecté à Azure Machine Learning Studio (classique), vous pouvez utiliser un canevas d’expérimentation permettant de générer un flux logique pour les algorithmes d’apprentissage automatique. Vous avez également accès à une instance de Jupyter Notebook hébergée dans Azure Machine Learning. L’utilisation du notebook s’intègre parfaitement à celle des expériences dans Azure Machine Learning Studio (classique). 

Exploitez les modèles de Machine Learning que vous avez créés en les wrappant dans une interface de service web. La mise en place des modèles Machine Learning permet aux clients écrits dans n’importe quel langage d’appeler des prévisions à partir de ces modèles. Pour plus d’informations, voir la [documentation Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Vous pouvez également générer vos modèles en R ou Python sur la machine virtuelle DSVM, puis les déployer en production sur Azure Machine Learning. Microsoft a installé des bibliothèques en R (**AzureML**) et en Python (**azureml**) pour prendre en charge cette fonctionnalité.

Pour plus d’informations sur le déploiement des modèles en R et en Python dans Azure Machine Learning, consultez [Dix choses que vous pouvez effectuer sur une machine DSVM (Data Science Virtual Machine)](vm-do-ten-things.md).

> [!NOTE]
> Les instructions contenues dans [Dix choses que vous pouvez effectuer sur une machine DSVM (Data Science Virtual Machine)](vm-do-ten-things.md) ont été écrites pour la version Windows de la machine DSVM. Toutefois, les informations concernant le déploiement des modèles dans Azure Machine Learning s’appliquent également à la machine DSVM Linux.

## <a name="machine-learning-tools"></a>Outils de Machine Learning

La machine virtuelle DSVM est fournie avec quelques outils et algorithmes de Machine Learning qui ont été précompilés et installés localement. notamment :

* **Microsoft Cognitive Toolkit** : kit de ressources d’apprentissage profond.
* **Vowpal Wabbit** : algorithme d’apprentissage en ligne rapide.
* **XGBoost** : outil qui fournit des algorithmes d’arborescence optimisés.
* **Python** : Anaconda Python est fourni avec des algorithmes de machine learning et des bibliothèques comme Scikit-learn. Vous pouvez installer d’autres bibliothèques à l’aide de la commande `pip install` .
* **R** : bibliothèque étendue de fonctions de Machine Learning disponibles pour le langage R. Parmi les bibliothèques préinstallées figurent lm, glm, randomForest et rpart. Vous pouvez installer d’autres bibliothèques en exécutant `install.packages(<lib name>)`.

Microsoft Cognitive Toolkit, Vowpal Wabbit et XGBoost sont décrits plus en détail dans les sections suivantes.

### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

Microsoft Cognitive Toolkit est un kit de ressources d’apprentissage profond open source. Cet outil en ligne de commande (CNTK) se trouve déjà dans le chemin.

Pour exécuter un exemple de base, exécutez les commandes suivantes dans l’interpréteur de commandes :

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Pour plus d’informations, consultez le [dépôt CNTK sur GitHub](https://github.com/Microsoft/CNTK) et le wiki [CNTK](https://github.com/Microsoft/CNTK/wiki).

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit est un système de Machine Learning utilisant des techniques (apprentissage en ligne, hachage, allreduce, réductions, learning2search, actif et interactif).

Pour exécuter l’outil sur un exemple très simple, exécutez les commandes suivantes :

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Le répertoire de démonstration Vowpal Wabbit comprend d’autres démonstrations de taille plus importante. Pour plus d’informations sur Vowpal Wabbit, consultez le [dépôt GitHub Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) et le [wiki Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki).

### <a name="xgboost"></a>XGBoost

La bibliothèque XGBoost a été conçue et optimisée pour les algorithmes (d’arborescence) optimisés. Son objectif est de repousser les limites de calcul des machines de manière à fournir une optimisation des arborescences à grande échelle qui soit scalable, portable et précise.

XGBoost est fourni sous forme de ligne de commande et de bibliothèque R.

Pour utiliser la bibliothèque XGBoost en R, démarrez une session R interactive (dans l’interpréteur de commandes, entrez **R**), puis chargez la bibliothèque.

Voici un exemple simple que vous pouvez exécuter dans l’invite R :

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

Pour exécuter la ligne de commande XGBoost, exécutez les commandes suivantes dans l’interpréteur de commandes :

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Un fichier .model est écrit dans le répertoire spécifié. Pour plus d’informations sur cet exemple de démonstration GitHub, consultez [Classification binaire](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Pour plus d’informations sur XGBoost, consultez la [documentation XGBoost](https://xgboost.readthedocs.org/en/latest/) et le [dépôt GitHub](https://github.com/dmlc/xgboost) XGBoost.

### <a name="rattle"></a>Rattle

Rattle (*R* *A*nalytical *T*ool *T*o *L*earn *E*asily, « outil analytique pour apprendre facilement ») utilise la modélisation et l’exploration des données via une interface graphique utilisateur. Rattle :
- Présente des récapitulatifs statistiques et visuels des données.
- Transforme des données qui peuvent être facilement modélisées.
- Génère des modèles non supervisés et supervisés à partir de données.
- Présente les performances des modèles de manière graphique.
- Évalue les nouveaux jeux de données.
- Génère du code R.
- Réplique des opérations dans l’interface utilisateur qui peuvent être exécutées directement en R ou utilisées comme point de départ pour une analyse plus approfondie.

Pour exécuter Rattle, vous devez être connecté à une session d’environnement de bureau graphique. Dans un terminal, entrez **R** pour ouvrir l’environnement R. À l’invite R, entrez les commandes suivantes :

```R
library(rattle)
rattle()
```

Une interface graphique comprenant un ensemble d’onglets s’ouvre. Effectuez les étapes de démarrage rapide suivantes dans Rattle pour utiliser un exemple de jeu de données météorologiques et créer un modèle. Dans certaines étapes, vous êtes invité à installer et charger automatiquement tous les packages R nécessaires qui ne sont pas déjà installés sur le système.

> [!NOTE]
> Si vous ne disposez pas des autorisations pour installer le package dans le répertoire système (valeur par défaut), la fenêtre de console R peut afficher une invite vous demandant d’installer les packages dans votre bibliothèque personnelle. Si vous voyez ces invites, entrez **o**.

1. Sélectionnez **Exécuter**.
1. Une boîte de dialogue vous invite à charger l’exemple de jeu de données météorologiques. Sélectionnez **Yes** (Oui) pour charger l’exemple.
1. Sélectionner l’onglet **Model** (Modèle).
1. Sélectionnez **Execute** (Exécuter) pour créer un arbre de décision.
1. Sélectionnez **Draw** (Dessiner) pour afficher l’arbre de décision.
1. Sélectionnez l’option **Forest** (Forêt), puis sélectionnez **Execute** (Exécuter) pour créer une forêt aléatoire.
1. Sélectionnez l’onglet **Evaluate** (Évaluer).
1. Sélectionnez l’option **Risk** (Risque), puis sélectionnez **Execute** pour afficher deux tracés de performances **Risk (Cumulative)** (Risque - Cumulatif).
1. Sélectionnez l’onglet **Log** (Journal) pour afficher le code R généré pour les opérations précédentes (en raison d’un bogue dans la version actuelle de Rattle, vous devez insérer un caractère **#** devant **Export this log** dans le texte du journal).
1. Sélectionnez le bouton **Export** (Exporter) pour enregistrer le fichier de script R nommé *weather_script.R* dans le dossier de base.

Vous pouvez quitter Rattle et R, et modifier le script R généré. Vous pouvez aussi utiliser le script tel quel, puis l’exécuter à tout moment pour répéter tout ce qui a été fait dans l’interface utilisateur Rattle. Pour les débutants en langage R notamment, c’est un moyen facile d’effectuer rapidement des analyses et du Machine Learning dans une interface graphique simple, tout en générant automatiquement du code R pour le modifier ou en tirer des enseignements.

## <a name="next-steps"></a>Étapes suivantes

Vous avez d’autres questions ? N’hésitez pas à créer un [ticket de support](https://azure.microsoft.com/support/create-ticket/).