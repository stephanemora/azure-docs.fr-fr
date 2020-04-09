---
title: Explorer les données et le modèle sur Windows
titleSuffix: Azure Data Science Virtual Machine
description: Effectuez des tâches de modélisation et d’exploration des données sur Data Science Virtual Machine Windows.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 8da8cd7110cd17d0aadd52cce1263c7c0fcfdf5c
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632173"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Dix choses que vous pouvez effectuer sur la machine virtuelle Science des données de Windows

Windows Data Science Virtual Machine (DSVM) est un puissant environnement de développement pour la science des données dans lequel vous pouvez effectuer des tâches de modélisation et d’exploration des données. L’environnement est déjà généré et fourni avec plusieurs outils d’analytique de données courants qui facilitent la prise en main de votre analyse pour les déploiements locaux, cloud ou hybrides. 

L’environnement DSVM opère en étroite collaboration avec les services Azure. Il peut lire et traiter les données déjà stockées sur Azure, dans Azure SQL Data Warehouse, Azure Data Lake, Stockage Azure ou Azure Cosmos DB. Il peut aussi tirer parti d’autres outils d’analytique comme Azure Machine Learning et Azure Data Factory.

Dans cet article vous allez découvrir comment utiliser votre DSVM afin d’effectuer des tâches de science des données et d’interagir avec d’autres services Azure. Voici quelques-unes des tâches que vous pouvez effectuer sur la DSVM :

- Explorer les données et développer des modèles localement sur la DSVM avec Microsoft Machine Learning Server et Python.
- Utiliser un notebook Jupyter pour mener des expériences avec vos données dans un navigateur à l’aide de Python 2, Python 3 et Microsoft R. (Microsoft R est une version d’entreprise de R conçue pour les performances.)
- Déployer des modèles créés par le biais de R et Python sur Azure Machine Learning afin que les applications clientes puissent accéder à vos modèles à l’aide d’une interface de services web simple.
- Administrer vos ressources Azure à l’aide du portail Azure ou de PowerShell.
- Augmenter votre espace de stockage et partager des jeux de données/du code à grande échelle avec toute votre équipe en créant un partage Azure Files comme lecteur montable sur votre DSVM.
- Partager du code avec votre équipe à l’aide de GitHub. Accéder à votre dépôt à l’aide des clients Git préinstallés : Git Bash et Git GUI
- Accéder aux services de données et d’analytique Azure tels que Stockage Blob Azure, Azure Data Lake, Azure HDInsight (Hadoop), Azure Cosmos DB, Azure SQL Data Warehouse et Azure SQL Database
- Générer des rapports et un tableau de bord à l’aide de l’instance de Power BI Desktop préinstallée sur la DSVM, et les déployer dans le cloud.
- Mettre à l’échelle dynamiquement votre DSVM pour répondre aux besoins de votre projet.
- Installer des outils supplémentaires sur votre machine virtuelle.   

> [!NOTE]
> Des frais d’utilisation supplémentaires s’appliquent pour la plupart des services de stockage et d’analytique des données mentionnés dans cet article. Pour plus d’informations, consultez la page des [tarifs Azure](https://azure.microsoft.com/pricing/).
> 
> 

## <a name="prerequisites"></a>Prérequis

* Vous avez besoin d’un abonnement Azure. Vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/free/).
* Vous trouverez des instructions pour le provisionnement d’une Data Science Virtual Machine dans le portail Azure dans [Création d’une machine virtuelle](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Explorer les données et développer des modèles avec Microsoft Machine Learning Server
Vous pouvez utiliser des langages tels que R et Python pour effectuer des analyses de données directement sur la DSVM.

Pour R, vous pouvez utiliser un IDE comme RStudio, accessible à partir du menu Démarrer ou sur le Bureau. Vous pouvez aussi utiliser Outils R pour Visual Studio. Microsoft a fourni des bibliothèques supplémentaires en plus d’Open source/CRAN-R pour permettre de mettre à l’échelle l’analytique et d’analyser des volumes de données plus importants que la taille de mémoire autorisée lors d’une analyse parallèle par blocs. 

Pour Python, vous pouvez utiliser un IDE comme Visual Studio Community Edition, dans lequel l'extension Outils Python pour Visual Studio (PTVS) est préinstallée. Par défaut, seul Python 3.6, l’environnement racine de Conda, est configuré sur PTVS. Pour activer Anaconda Python 2.7, effectuez les étapes suivantes :

1. Créez des environnements personnalisés pour chaque version en accédant à **Outils** > **Outils Python** > **Environnements Python**, puis en sélectionnant **+ Personnalisé** dans Visual Studio Community Edition.
1. Donnez une description et définissez **c:\anaconda\envs\python2** comme chemin de préfixe d’environnement pour Anaconda Python 2.7.
1. Sélectionnez **Détection automatique** > **Appliquer** pour enregistrer l’environnement.

Pour plus d’informations sur la façon de créer des environnements Python, consultez la [Documentation de PTVS](https://aka.ms/ptvsdocs).

Vous êtes maintenant prêt à créer un nouveau projet Python. Accédez à **Fichier** > **Nouveau** > **Projet** > **Python** et sélectionnez le type d’application Python que vous créez. Vous pouvez définir l’environnement Python pour le projet actuel sur la version souhaitée (Python 2.7 ou 3.6) en cliquant avec le bouton droit sur **Environnements Python** et en sélectionnant **Ajouter/supprimer des environnements Python**. Vous trouverez plus d’informations sur l’utilisation de PTVS dans la [documentation produit](https://aka.ms/ptvsdocs).

## <a name="use-jupyter-notebooks"></a>Utiliser des blocs-notes Jupyter
Jupyter Notebook fournit un IDE basé sur navigateur pour l’exploration et la modélisation des données. Vous pouvez utiliser Python 2, Python 3 ou R (Open Source et Microsoft R Server) dans un notebook Jupyter.

Pour démarrer Jupyter Notebook, sélectionnez l’icône **Jupyter Notebook** dans le menu **Démarrer** ou sur le Bureau. À l’invite de commande DSVM, vous pouvez également exécuter la commande ```jupyter notebook``` à partir du répertoire où se trouvent vos notebooks, ou bien à partir de l’emplacement où vous souhaitez en créer d’autres.  

Une fois que vous avez démarré Jupyter, vous devriez voir un répertoire contenant quelques exemples de notebooks pré-intégrés dans la DSVM. Vous pouvez désormais :

* Sélectionner le notebook pour visualiser le code.
* Exécuter chaque cellule en sélectionnant Maj+Entrée.
* Exécuter l’intégralité du notebook en sélectionnant **Cellule** > **Exécuter**.
* Créer un notebook en sélectionnant l’icône Jupyter (coin supérieur gauche), le bouton **Nouveau** sur la droite, puis en choisissant la langue du notebook (également appelé noyau).   

> [!NOTE]
> Actuellement, les noyaux Python 2.7, Python 3.6, R, Julia et PySpark dans Jupyter sont pris en charge. Le noyau R prend en charge la programmation dans R et Microsoft R Open Source.   
> 
> 

Une fois dans le notebook, vous pouvez explorer vos données, générer le modèle et le tester avec les bibliothèques de votre choix.

## <a name="train-and-deploy-models-by-using-azure-machine-learning"></a>Entraîner et déployer des modèles à l’aide d’Azure Machine Learning
Une fois que vous avez créé et validé votre modèle, l’étape suivante consiste généralement à le déployer en production. Cette étape permet à vos applications clientes d’appeler les prédictions de modèle en temps réel ou par lots. Azure Machine Learning fournit un mécanisme permettant de faire fonctionner un modèle généré avec R ou Python.

Quand vous opérationnalisez votre modèle dans Azure Machine Learning, un service web est exposé. Il permet aux clients d’effectuer des appels REST qui passent des paramètres d’entrée et reçoivent des prédictions du modèle en tant que sorties.

### <a name="build-and-operationalize-python-models"></a>Générer et opérationnaliser des modèles Python
Voici un extrait de code développé dans un notebook Jupyter Python qui génère un modèle simple à l’aide de la bibliothèque Scikit-learn :

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

La méthode utilisée pour déployer vos modèles Python sur Azure Machine Learning intègre la prédiction du modèle dans une fonction et lui ajoute des attributs fournis par la bibliothèque Python Azure Machine Learning préinstallée. Les attributs indiquent votre ID d’espace de travail Azure Machine Learning, la clé API et les paramètres d’entrée et de retour.  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

Un client peut désormais effectuer des appels au service web. Des wrappers tout préparés construisent les requêtes d’API REST. Voici un exemple de code pour utiliser le service web :

```python
# Consume through web service URL and keys
from azureml import services
@services.service(url, api_key)
@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
@services.returns(float)
def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
pass

IrisPredictor(3,2,3,4)
```

> [!NOTE]
> Actuellement, la bibliothèque Azure Machine Learning est prise en charge uniquement sur Python 2.7.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Générer et opérationnaliser des modèles R
Vous pouvez déployer des modèles R générés sur la DSVM ou ailleurs sur Azure Machine Learning de la même façon que pour Python. Voici la procédure à suivre :

1. Créez un fichier settings.json pour fournir votre ID d’espace de travail et votre jeton d’authentification 
2. Écrivez un wrapper pour la fonction de prédiction du modèle.
3. Appelez ```publishWebService``` dans la bibliothèque Azure Machine Learning pour transmettre le wrapper de la fonction.  

Utilisez la procédure et les extraits de code suivants pour configurer, générer, publier et utiliser un modèle en tant que service web dans Azure Machine Learning.

#### <a name="set-up"></a>Configurer

Créez un fichier settings.json sous un répertoire nommé ```.azureml``` dans votre répertoire de base. Entrez les paramètres de votre espace de travail Azure Machine Learning.

Voici la structure du fichier settings.json :

```json
{"workspace":{
"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
}}
```

#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Générer un modèle dans R et le publier dans Azure Machine Learning

```r
library(AzureML)
ws <- workspace(config="~/.azureml/settings.json")

if(!require("lme4")) install.packages("lme4")
library(lme4)
set.seed(1)
train <- sleepstudy[sample(nrow(sleepstudy), 120),]
m <- lm(Reaction ~ Days + Subject, data = train)

# Define a prediction function to publish based on the model:
sleepyPredict <- function(newdata){
        predict(m, newdata=newdata)
}

ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)
```

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Utiliser le modèle déployé dans Azure Machine Learning
Pour utiliser le modèle à partir d’une application cliente, utilisez la bibliothèque Azure Machine Learning pour rechercher le service web publié par nom. Utilisez l’appel d’API `services` afin de déterminer le point de terminaison. Il vous suffit ensuite d’appeler la fonction `consume` et de transmettre la trame de données à prédire.

Utilisez le code suivant pour consommer le modèle publié en tant que service web Azure Machine Learning :

```r
library(AzureML)
library(lme4)
ws <- workspace(config="~/.azureml/settings.json")

s <-  services(ws, name = "sleepy lm")
s <- tail(s, 1) # use the last published function, in case of duplicate function names

ep <- endpoints(ws, s)

# Try this out, and compare with raw data
ans = consume(ep, sleepstudy)$ans
```

Apprenez-en davantage sur les [packages R dans Machine Learning Studio](/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning).

## <a name="manage-azure-resources"></a>Gérer des ressources Azure
La DSVM ne vous permet pas uniquement de créer votre solution d’analytique localement sur la machine virtuelle. Elle vous permet aussi d’accéder à des services sur la plateforme cloud Azure. Azure fournit plusieurs services de calcul, de stockage, d’analytique de données et autres, que vous pouvez administrer et auxquels vous pouvez accéder à partir de votre DSVM.

Pour gérer vos ressources cloud et votre abonnement Azure, deux options s’offrent à vous :
+ Utiliser votre navigateur web et accéder au [portail Azure](https://portal.azure.com)

+ Utiliser des scripts PowerShell. Exécuter Azure PowerShell à partir d’un raccourci sur le Bureau ou à partir du menu **Démarrer**. Pour plus d’informations, voir la [documentation Microsoft Azure PowerShell](../../powershell-azure-resource-manager.md) 

## <a name="extend-storage-by-using-shared-file-systems"></a>Étendre le stockage à l’aide de systèmes de fichiers partagés
Les scientifiques des données peuvent partager des jeux de données volumineux, du code ou d’autres ressources au sein de leur équipe. La DSVM dispose d’environ 45 Go d’espace disponible. Pour étendre votre espace de stockage, vous pouvez utiliser Azure Files et le monter sur une ou plusieurs instances DSVM ou y accéder par le biais de l’API REST. Vous pouvez également utiliser le [portail Azure](../../virtual-machines/windows/attach-managed-disk-portal.md) ou [Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) pour ajouter des disques de données supplémentaires dédiés. 

> [!NOTE]
> L’espace maximal sur le partage Azure Files est de 5 To. La taille maximale de chaque fichier est de 1 To. 

Vous pouvez utiliser ce script dans Azure PowerShell pour créer un partage Azure Files :

```powershell
# Authenticate to Azure.
Connect-AzAccount
# Select your subscription
Get-AzSubscription –SubscriptionName "<your subscription name>" | Select-AzSubscription
# Create a new resource group.
New-AzResourceGroup -Name <dsvmdatarg>
# Create a new storage account. You can reuse existing storage account if you want.
New-AzStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
# Set your current working storage account
Set-AzCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

# Create an Azure Files share
$s = New-AzStorageShare <<teamsharename>>
# Create a directory under the file share. You can give it any name
New-AzStorageDirectory -Share $s -Path <directory name>
# List the share to confirm that everything worked
Get-AzStorageFile -Share $s
```

Maintenant que vous avez créé un partage Azure Files, vous pouvez le monter sur n’importe quelle machine virtuelle dans Azure. Nous vous recommandons vivement de placer la machine virtuelle dans le même centre de données Azure que le compte de stockage, pour éviter toute latence et tout frais de transfert de données. Voici les commandes Azure PowerShell permettant de monter le lecteur sur la DSVM :

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Vous pouvez désormais accéder à ce lecteur comme à n’importe quel lecteur normal sur la machine virtuelle.

## <a name="share-code-in-github"></a>Partager du code dans GitHub
GitHub est un dépôt de code où vous trouverez des exemples de code et des sources pour différents outils utilisant des technologies partagées par la communauté des développeurs. Il utilise la technologie Git pour suivre et stocker les versions des fichiers de code. GitHub est également une plateforme qui vous permet de créer votre propre dépôt pour stocker le code et la documentation partagés de votre équipe, d’implémenter la gestion des versions et de contrôler les accès pour afficher le code et y contribuer. 

Visitez les [pages d’aide GitHub](https://help.github.com/) pour plus d’informations sur l’utilisation de Git. Vous pouvez utiliser GitHub pour collaborer avec votre équipe, utiliser le code développé par la communauté et apporter une contribution au code pour la communauté.

La DSVM est livrée avec des outils clients en ligne de commande et dans l’interface graphique utilisateur pour accéder au dépôt GitHub. L’outil en ligne de commande qui fonctionne avec Git et GitHub se nomme Git Bash. Visual Studio est installé sur la DSVM et comprend les extensions Git. Vous trouverez les icônes de ces outils dans le menu **Démarrer** et sur le Bureau.

Pour télécharger du code à partir d’un référentiel GitHub, utilisez la commande ```git clone```. Par exemple, pour télécharger le dépôt de science des données publié par Microsoft dans le répertoire actif, vous pouvez exécuter la commande suivante dans Git Bash :

    git clone https://github.com/Azure/DataScienceVM.git

Dans Visual Studio, vous pouvez effectuer la même opération de clonage. La capture d’écran suivante montre comment accéder aux outils Git et GitHub dans Visual Studio :

![Capture d’écran de Visual Studio avec la connexion GitHub affichée](./media/vm-do-ten-things/VSGit.PNG)

Vous trouverez plus d’informations sur l’utilisation de Git pour travailler avec votre dépôt GitHub en consultant les ressources disponibles sur github.com. L’ [aide-mémoire](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) constitue une référence utile.

## <a name="access-azure-data-and-analytics-services"></a>Accéder aux services de données et d’analytique Azure
### <a name="azure-blob-storage"></a>Stockage Blob Azure
Stockage Blob Azure est un service de stockage cloud fiable et économique pour tous les types de données. Cette section décrit comment déplacer des données vers le stockage d’objets blob et accéder à des données stockées dans un objet blob Azure.

#### <a name="prerequisites"></a>Prérequis

* Créez votre compte Stockage Blob Azure dans le [portail Azure](https://portal.azure.com).

   ![Capture d’écran du processus de création d’un compte de stockage dans le portail Azure](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Vérifiez que l’outil en ligne de commande AzCopy est préinstallé : ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. Le répertoire qui contient le fichier azcopy.exe se trouve déjà sur votre variable d’environnement PATH. Ainsi, vous n’êtes pas obligé de taper tout le chemin de la commande lors de l’exécution de cet outil. Pour plus d’informations sur l’outil AzCopy, consultez la [documentation d’AzCopy](../../storage/common/storage-use-azcopy.md).
* Lancez l’outil Azure Storage Explorer. Vous pouvez le télécharger à partir de la page web [Explorateur Stockage Azure](https://storageexplorer.com/). 

   ![Capture d’écran de l’Explorateur Stockage Azure accédant à un compte de stockage](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Déplacer des données d’une machine virtuelle vers un objet blob Azure : AzCopy

Pour déplacer des données entre vos fichiers locaux et le stockage d’objets blob, vous pouvez utiliser AzCopy sur la ligne de commande ou dans PowerShell :

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Remplacez **C:\myfolder** par le chemin de votre fichier, **mystorageaccount** par le nom de votre compte Stockage Blob, **mycontainer** par le nom du conteneur et **storage account key** par votre clé d’accès Stockage Blob. Vous trouverez les informations d’identification de votre compte de stockage dans le [portail Azure](https://portal.azure.com).

Exécutez la commande AzCopy dans PowerShell ou à partir d’une invite de commandes. Voici un exemple d’utilisation de la commande AzCopy :

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

Une fois que vous avez exécuté la commande AzCopy pour copier vers un objet blob Azure, votre fichier apparaît dans l’Explorateur Stockage Azure.

![Capture d’écran du compte de stockage affichant le fichier CSV chargé](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Déplacer des données d’une machine virtuelle vers un objet blob Azure : Explorateur de stockage Azure

Vous pouvez également charger des données du fichier local vers votre machine virtuelle à l’aide de l’Explorateur Stockage Azure :

* Pour charger des données vers un conteneur, sélectionnez le conteneur cible, puis le bouton **Charger**.![Capture d’écran du bouton Charger dans l’Explorateur Stockage Azure](./media/vm-do-ten-things/storage-accounts.png)
* Sélectionnez **...** à droite de la zone **Fichiers**, sélectionnez un ou plusieurs fichiers à charger à partir du système de fichiers, puis sélectionnez **Charger** pour lancer le chargement des fichiers.![Capture d’écran de la boîte de dialogue Charger des fichiers](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-machine-learning-reader-module"></a>Lire des données à partir d’un objet blob : module Lecteur Machine Learning

Dans Azure Machine Learning Studio, vous pouvez utiliser le module Importer les données pour lire des données à partir de votre objet blob.

![Capture d’écran du module Importer des données dans Machine Learning Studio](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Lire des données à partir d’un objet blob : Python ODBC

Vous pouvez utiliser la bibliothèque BlobService pour lire les données directement à partir de l’objet blob dans un notebook Jupyter ou un programme Python.

Commencez par importer les packages requis :

```python
import pandas as pd
from pandas import Series, DataFrame
import numpy as np
import matplotlib.pyplot as plt
from time import time
import pyodbc
import os
from azure.storage.blob import BlobService
import tables
import time
import zipfile
import random
```

Ensuite, entrez les informations d’identification de votre compte Stockage Blob et lisez les données de l’objet Blob :

```python
CONTAINERNAME = 'xxx'
STORAGEACCOUNTNAME = 'xxxx'
STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
localfilename = 'trip_data_1.csv'
LOCALDIRECTORY = os.getcwd()
LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

#download from blob
t1 = time.time()
blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
t2 = time.time()
print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

#unzip downloaded files if needed
#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
#    z.extractall(LOCALDIRECTORY)

df1 = pd.read_csv(LOCALFILE, header=0)
df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
print 'the size of the data is: %d rows and  %d columns' % df1.shape
```

Les données sont lues en tant que trame de données :

![Capture d’écran des 10 premières lignes de données](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake Storage est un référentiel hyperscale pour les charges de travail d’analytique des Big Data, compatible avec le système de fichiers HDFS (Hadoop Distributed File System). Il fonctionne avec Hadoop et Azure Data Lake Analytics. Dans cette section, vous allez découvrir comment déplacer des données dans Azure Data Lake Storage et exécuter l’analytique à l’aide d’Azure Data Lake Analytics.

#### <a name="prerequisites"></a>Prérequis

* Créez votre instance d’Azure Data Lake Analytics dans le [portail Azure](https://portal.azure.com).

   ![Capture d’écran de la création de l’instance de Data Lake Analytics à partir du portail Azure](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* Le [plug-in Outils Azure Data Lake et Stream Analytics pour Visual](https://www.microsoft.com/download/details.aspx?id=49504) est déjà installé dans Visual Studio Community Edition sur la machine virtuelle. Après avoir lancé Visual Studio et vous être connecté à votre abonnement Azure, vous devriez voir votre compte et votre stockage Azure Data Analytics dans le volet gauche de Visual Studio.

   ![Capture d’écran du plug-in pour Data Lake Tools dans Visual Studio](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

#### <a name="move-data-from-a-vm-to-data-lake-azure-data-lake-explorer"></a>Déplacer des données d’une machine virtuelle vers Data Lake : Explorateur Azure Data Lake

Vous pouvez utiliser l’Explorateur Azure Data Lake pour [charger des données à partir des fichiers locaux de votre machine virtuelle vers Data Lake Storage](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

Vous pouvez également créer un pipeline de données pour opérationnaliser le déplacement des données vers ou à partir d’Azure Data Lake à l’aide d’[Azure Data Factory](https://azure.microsoft.com/services/data-factory/). [Cet article](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) explique les étapes permettant de créer les pipelines de données.

#### <a name="read-data-from-an-azure-blob-to-data-lake-u-sql"></a>Lire des données à partir d’un objet blob Azure vers Data Lake : U-SQL

Si vos données se trouvent dans Stockage Blob Azure, vous pouvez les lire directement à partir d’un objet blob dans une requête U-SQL. Avant de composer votre requête U-SQL, vérifiez que votre compte Stockage Blob est lié à votre instance d’Azure Data Lake. Accédez au portail Azure, recherchez votre tableau de bord Azure Data Lake Analytics, sélectionnez **Ajouter une source de données**, sélectionnez le type de stockage **Stockage Azure** et entrez votre nom et votre clé de compte Stockage Azure. Vous pouvez ensuite référencer les données stockées dans le compte de stockage.

![Capture d’écran de la boîte de dialogue Ajouter une source de données](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

Dans Visual Studio, vous pouvez lire les données à partir d’un stockage d’objets blob, manipuler des données, concevoir des fonctionnalités et envoyer les données résultantes vers Azure Data Lake ou Stockage Blob Azure. Quand vous référencez les données dans le stockage d’objets blob, utilisez **wasb://** . Quand vous référencez les données dans Azure Data Lake, utilisez **swbhdfs://** .

Vous pouvez utiliser les requêtes U-SQL suivantes dans Visual Studio :

```usql
@a =
    EXTRACT medallion string,
            hack_license string,
            vendor_id string,
            rate_code string,
            store_and_fwd_flag string,
            pickup_datetime string,
            dropoff_datetime string,
            passenger_count int,
            trip_time_in_secs double,
            trip_distance double,
            pickup_longitude string,
            pickup_latitude string,
            dropoff_longitude string,
            dropoff_latitude string

    FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
    USING Extractors.Csv();

@b =
    SELECT vendor_id,
    COUNT(medallion) AS cnt_medallion,
    SUM(passenger_count) AS cnt_passenger,
    AVG(trip_distance) AS avg_trip_dist,
    MIN(trip_distance) AS min_trip_dist,
    MAX(trip_distance) AS max_trip_dist,
    AVG(trip_time_in_secs) AS avg_trip_time
    FROM @a
    GROUP BY vendor_id;

OUTPUT @b   
TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
USING Outputters.Csv();

OUTPUT @b   
TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
USING Outputters.Csv();
```

Une fois votre requête envoyée au serveur, un diagramme indique l’état de votre travail.

![Capture d’écran du diagramme d’état du travail](./media/vm-do-ten-things/USQL_Job_Status.PNG)

#### <a name="query-data-in-data-lake-u-sql"></a>Interroger des données dans Data Lake : U-SQL

Une fois le jeu de données réceptionné dans Azure Data Lake, vous pouvez utiliser le [langage U-SQL](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) pour interroger et explorer les données. Le langage U-SQL est similaire à T-SQL, mais combine certaines fonctionnalités de C# afin de permettre aux utilisateurs d’écrire des modules personnalisés et des fonctions définies par l’utilisateur. Vous pouvez utiliser les scripts de l'étape précédente.

Une fois la requête envoyée au serveur, tripdata_summary.CSV s’affiche dans l’Explorateur Azure Data Lake. Vous pouvez afficher un aperçu des données en cliquant avec le bouton droit sur le fichier.

![Capture d’écran du fichier CSV dans l’Explorateur Azure Data Lake](./media/vm-do-ten-things/USQL_create_summary.png)

Les informations du fichier s’affichent :

![Capture d’écran des informations de résumé du fichier](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>Clusters HDInsight Hadoop
Azure HDInsight est un service Apache Hadoop, Spark, HBase et Storm géré dans le cloud. Vous pouvez travailler facilement avec les clusters Azure HDInsight à partir de la DSVM.

#### <a name="prerequisites"></a>Prérequis

* Créez votre compte Stockage Blob Azure dans le [portail Azure](https://portal.azure.com). Ce compte de stockage est utilisé pour stocker les données des clusters HDInsight.

   ![Capture d’écran de la création d’un compte de stockage à partir du portail Azure](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Personnalisez les clusters Azure HDInsight Hadoop à partir du [portail Azure](../team-data-science-process/customize-hadoop-cluster.md).
  
   Liez le compte de stockage créé à votre cluster HDInsight une fois sa création terminée. Ce compte de stockage est utilisé pour accéder aux données qui peuvent être traitées au sein du cluster.

   ![Sélections pour la liaison du compte de stockage créé avec un cluster HDInsight](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Activez l’accès Bureau à distance au nœud principal du cluster après sa création. Retenez les informations d’identification pour l’accès à distance que vous spécifiez ici, car vous en aurez besoin pour la procédure suivante.

   ![Bouton Bureau à distance pour activer l’accès à distance au cluster HDInsight](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Créez un espace de travail Machine Learning. Vos expériences Machine Learning sont stockées dans cet espace de travail Machine Learning. Sélectionnez les options en surbrillance dans le portail, comme indiqué dans la capture d’écran suivante :

   ![Création d’un espace de travail Microsoft Azure Machine Learning](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Entrez les paramètres de votre espace de travail.

   ![Entrer les paramètres de l'espace de travail Machine Learning](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Chargez des données à l’aide d’IPython Notebook. Importez les packages requis, tapez vos informations d’identification, créez une base de données dans votre compte de stockage, puis chargez les données dans les clusters HDI.

```python
# Import required packages
import pyodbc
import time as time
import json
import os
import urllib
import urllib2
import warnings
import re
import pandas as pd
import matplotlib.pyplot as plt
from azure.storage.blob import BlobService
warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


# Create the connection to Hive by using ODBC
SERVER_NAME = 'xxx.azurehdinsight.net'
DATABASE_NAME = 'nyctaxidb'
USERID = 'xxx'
PASSWORD = 'xxxx'
DB_DRIVER = 'Microsoft Hive ODBC Driver'
driver = 'DRIVER={' + DB_DRIVER + '}'
server = 'Host=' + SERVER_NAME + ';Port=443'
database = 'Schema=' + DATABASE_NAME
hiveserv = 'HiveServerType=2'
auth = 'AuthMech=6'
uid = 'UID=' + USERID
pwd = 'PWD=' + PASSWORD
CONNECTION_STRING = ';'.join(
    [driver, server, database, hiveserv, auth, uid, pwd])
connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
cursor = connection.cursor()


# Create the Hive database and tables
queryString = "create database if not exists nyctaxidb;"
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.trip
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    rate_code string,
                    store_and_fwd_flag string,
                    pickup_datetime string,
                    dropoff_datetime string,
                    passenger_count int,
                    trip_time_in_secs double,
                    trip_distance double,
                    pickup_longitude double,
                    pickup_latitude double,
                    dropoff_longitude double,
                    dropoff_latitude double)  
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.fare
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    pickup_datetime string,
                    payment_type string,
                    fare_amount double,
                    surcharge double,
                    mta_tax double,
                    tip_amount double,
                    tolls_amount double,
                    total_amount double)
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)


# Upload data from Blob storage to an HDI cluster
for i in range(1, 13):
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
```

Vous pouvez également suivre cette [procédure pas à pas](../team-data-science-process/hive-walkthrough.md) pour charger les données NYC Taxi vers le cluster HDI. Voici les grandes étapes :
  
* Utiliser AzCopy pour télécharger les fichiers CSV compressés de l’objet blob public vers votre dossier local
* Utiliser AzCopy pour charger les fichiers CSV décompressés du dossier local vers un cluster HDI
* Se connecter au nœud principal du cluster Hadoop et préparer une analyse exploratoire des données

Une fois les données chargées dans le cluster HDI, vous pouvez les vérifier dans l’Explorateur Stockage Azure. Et la base de données nyctaxidb a été créée dans le cluster HDI.

#### <a name="data-exploration-hive-queries-in-python"></a>Exploration des données : requêtes Hive dans Python

Les données se trouvant dans un cluster Hadoop, vous pouvez utiliser le package pyodbc pour vous connecter aux clusters Hadoop et envoyer des requêtes à la base de données avec Hive afin d’effectuer l’exploration et la conception des fonctionnalités. Vous pouvez afficher les tables existantes que vous avez créées à l’étape des prérequis.

```python
queryString = """
    show tables in nyctaxidb2;
    """
pd.read_sql(queryString, connection)
```

![Afficher les tables existantes](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Nous allons examiner le nombre d'enregistrements chaque mois et la fréquence des pourboires dans la table de trajet :

```python
queryString = """
    select month, count(*) from nyctaxidb.trip group by month;
    """
results = pd.read_sql(queryString,connection)

%matplotlib inline

results.columns = ['month', 'trip_count']
df = results.copy()
df.index = df['month']
df['trip_count'].plot(kind='bar')
```

![Tracer le nombre d’enregistrements de chaque mois](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

```python
queryString = """
    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;
    """
results = pd.read_sql(queryString, connection)

results.columns = ['tipped', 'trip_count']
df = results.copy()
df.index = df['tipped']
df['trip_count'].plot(kind='bar')
```

![Traçage des fréquences de pourboires](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Vous pouvez également calculer la distance entre les emplacements de départ et d’arrivée, puis les comparer à la distance du trajet.

```python
queryString = """
                select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
                    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
                    *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
                    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
                    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
                    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
                    pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance
                    from nyctaxidb.trip
                    where month=1
                        and pickup_longitude between -90 and -30
                        and pickup_latitude between 30 and 90
                        and dropoff_longitude between -90 and -30
                        and dropoff_latitude between 30 and 90;
            """
results = pd.read_sql(queryString, connection)
results.head(5)
```

![Premières lignes de la table des départs et des arrivées](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

```python
results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
df = results.loc[results['trip_distance'] <= 100]  # remove outliers
df = df.loc[df['direct_distance'] <= 100]  # remove outliers
plt.scatter(df['direct_distance'], df['trip_distance'])
```

![Traçage de la distance des départs/arrivées à la distance des trajets](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Nous allons maintenant préparer un jeu de données sous-échantillonné (1 pour cent) pour la modélisation. Vous pouvez utiliser ces données dans le module lecteur Machine Learning.

```python
queryString = """
create  table if not exists nyctaxi_downsampled_dataset_testNEW (
medallion string,
hack_license string,
vendor_id string,
rate_code string,
store_and_fwd_flag string,
pickup_datetime string,
dropoff_datetime string,
pickup_hour string,
pickup_week string,
weekday string,
passenger_count int,
trip_time_in_secs double,
trip_distance double,
pickup_longitude double,
pickup_latitude double,
dropoff_longitude double,
dropoff_latitude double,
direct_distance double,
payment_type string,
fare_amount double,
surcharge double,
mta_tax double,
tip_amount double,
tolls_amount double,
total_amount double,
tipped string,
tip_class string
)
row format delimited fields terminated by ','
lines terminated by '\\n'
stored as textfile;
"""
cursor.execute(queryString)
```

Insérez maintenant le contenu de la jointure dans la table interne précédente.

```python
queryString = """
insert overwrite table nyctaxi_downsampled_dataset_testNEW
select
t.medallion,
t.hack_license,
t.vendor_id,
t.rate_code,
t.store_and_fwd_flag,
t.pickup_datetime,
t.dropoff_datetime,
hour(t.pickup_datetime) as pickup_hour,
weekofyear(t.pickup_datetime) as pickup_week,
from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
t.passenger_count,
t.trip_time_in_secs,
t.trip_distance,
t.pickup_longitude,
t.pickup_latitude,
t.dropoff_longitude,
t.dropoff_latitude,
t.direct_distance,
f.payment_type,
f.fare_amount,
f.surcharge,
f.mta_tax,
f.tip_amount,
f.tolls_amount,
f.total_amount,
if(tip_amount>0,1,0) as tipped,
if(tip_amount=0,0,
if(tip_amount>0 and tip_amount<=5,1,
if(tip_amount>5 and tip_amount<=10,2,
if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
from
(
select
medallion,
hack_license,
vendor_id,
rate_code,
store_and_fwd_flag,
pickup_datetime,
dropoff_datetime,
passenger_count,
trip_time_in_secs,
trip_distance,
pickup_longitude,
pickup_latitude,
dropoff_longitude,
dropoff_latitude,
3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
+cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
rand() as sample_key

from trip
where pickup_latitude between 30 and 90
    and pickup_longitude between -90 and -30
    and dropoff_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
)t
join
(
select
medallion,
hack_license,
vendor_id,
pickup_datetime,
payment_type,
fare_amount,
surcharge,
mta_tax,
tip_amount,
tolls_amount,
total_amount
from fare
)f
on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
where t.sample_key<=0.01
"""
cursor.execute(queryString)
```

Après un certain temps, vous pouvez constater que les données ont été chargées dans les clusters Hadoop :

```python
queryString = """
    select * from nyctaxi_downsampled_dataset limit 10;
    """
cursor.execute(queryString)
pd.read_sql(queryString, connection)
```

![Premières lignes de données de la table](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

### <a name="azure-sql-data-warehouse-and-databases"></a>Azure SQL Data Warehouse et bases de données
Azure SQL Data Warehouse est un entrepôt de données élastique en tant que service offrant une expérience SQL Server professionnelle.

Vous pouvez provisionner votre entrepôt de données Azure SQL en suivant les instructions de [cet article](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Une fois votre entrepôt de données Azure SQL provisionné, vous pouvez utiliser [cette procédure pas à pas](../team-data-science-process/sqldw-walkthrough.md) pour le chargement de données, mais aussi pour l’exploration et la modélisation à l’aide de données dans l’entrepôt de données SQL.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB est une base de données NoSQL sur le cloud. Vous pouvez l’utiliser pour travailler avec des documents comme JSON, et pour stocker et interroger les documents.

Effectuez les étapes prérequises suivantes pour accéder à Azure Cosmos DB à partir de la DSVM :

1. Le Kit de développement logiciel (SDK) Azure Cosmos DB Python est déjà installé sur la DSVM. Pour le mettre à jour, exécutez ```pip install pydocumentdb --upgrade``` à partir d’une invite de commandes.
2. Créez un compte et une base de données Azure Cosmos DB à partir du [portail Azure](https://portal.azure.com).
3. Téléchargez l’outil de migration de données Azure Cosmos DB à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=53595) et extrayez-le dans le répertoire de votre choix.
4. Importez des données JSON (données volcano) stockées dans un [objet blob public](https://cahandson.blob.core.windows.net/samples/volcano.json) dans Azure Cosmos DB avec les paramètres de commande suivants dans l’outil de migration. (Utilisez dtui.exe à partir du répertoire où vous avez installé l’outil de migration de données Azure Cosmos DB.) Entrez les paramètres d'emplacement source et cible suivant :
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Une fois les données importées, vous pouvez accéder à Jupyter et ouvrir le notebook intitulé *DocumentDBSample*. Il contient du code Python pour accéder à Azure Cosmos DB et effectuer des requêtes de base. Pour en savoir plus sur Azure Cosmos DB, consultez la [page de documentation](https://docs.microsoft.com/azure/cosmos-db/) du service.

## <a name="use-power-bi-reports-and-dashboards"></a>Utiliser des rapports et des tableaux de bord Power BI 
Vous pouvez visualiser le fichier JSON Volcano de l’exemple Azure Cosmos DB précédent dans Power BI Desktop afin d’obtenir des insights visuels des données. Les étapes détaillées sont présentées dans [l’article Power BI](../../cosmos-db/powerbi-visualize.md). Les étapes principales sont les suivantes :

1. Ouvrez Power BI Desktop et sélectionnez **obtenir les données**. Spécifiez l’URL suivante : `https://cahandson.blob.core.windows.net/samples/volcano.json`.
2. Vous devez voir les enregistrements JSON importés sous forme de liste. Convertissez la liste en table afin que Power BI puisse l’utiliser.
4. Développez les colonnes en sélectionnant l’icône de développement (flèche).
5. Notez que cet emplacement est un champ **Enregistrement**. Développez l'enregistrement et sélectionnez uniquement les coordonnées. Une **coordonnée** est une colonne de liste.
6. Ajoutez une nouvelle colonne pour convertir la colonne de coordonnées de liste en colonne **LatLong** séparée par des virgules. Concaténez les deux éléments dans le champ de liste de coordonnées à l’aide de la formule ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Convertissez la colonne **Elevation** au format décimal et sélectionnez les boutons **Fermer** et **Appliquer**.

Au lieu des étapes précédentes, vous pouvez coller le code suivant. Il écrit les étapes utilisées dans l’Éditeur avancé dans Power BI afin d’écrire les transformations de données dans un langage de requête.

```pqfl
let
    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
in
    #"Changed Type"
```

Vous disposez maintenant des données dans votre modèle de données Power BI. Votre instance de Power BI Desktop doit apparaître comme suit :

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Vous pouvez commencer à générer des rapports et des visualisations à l’aide du modèle de données. Vous pouvez appliquer la procédure décrite dans [cet article Power BI](../../cosmos-db/powerbi-visualize.md#build-the-reports) pour générer un rapport.

## <a name="scale-the-dsvm-dynamically"></a>Mettre à l’échelle la DSVM de manière dynamique 
Vous pouvez effectuer un scale-up ou un scale-down de la DSVM pour répondre aux besoins de votre projet. Si vous n’avez pas besoin d’utiliser la machine virtuelle le soir ou le week-end, vous pouvez simplement l’arrêter à partir du [portail Azure](https://portal.azure.com).

> [!NOTE]
> Vous encourez des frais de calcul si vous utilisez simplement le bouton d’arrêt du système d’exploitation sur la machine virtuelle.  
> 
> 

Vous devrez peut-être gérer une analyse à grande échelle, et il vous faudra peut-être davantage de capacité de processeur, de mémoire ou de disque. Si c’est le cas, vous trouverez un choix de tailles de machine virtuelle en termes de cœurs de processeur, d’instances basées sur GPU pour le deep learning, de capacité de mémoire et de types de disques (notamment les disques SSD) qui répondent à vos besoins budgétaires et de calcul. La liste complète des machines virtuelles, ainsi que leur tarif horaire de calcul, sont disponibles dans la page [Tarification des machines virtuelles Azure](https://azure.microsoft.com/pricing/details/virtual-machines/).

De même, votre besoin en matière de capacité de traitement des machines virtuelles peut diminuer (par exemple si vous avez déplacé une charge de travail importante vers un cluster Hadoop ou Spark). Vous pouvez alors effectuer un scale-down du cluster à partir du [portail Azure](https://portal.azure.com) et accéder aux paramètres de votre instance de machine virtuelle. 

## <a name="add-more-tools"></a>Ajouter d’autres outils
Les outils préintégrés dans la DSVM peuvent répondre à de nombreux besoins d’analytique de données courants. Cela vous fait gagner du temps, car vous n’avez pas besoin d’installer et de configurer vos environnements un par un. C’est également synonyme d’économies, car vous payez uniquement pour les ressources que vous utilisez.

Vous pouvez utiliser d’autres services de données et d’analytique Azure présentés dans cet article pour améliorer votre environnement d’analytique. Dans certains cas, vous pouvez avoir besoin d’outils supplémentaires, notamment certains outils partenaires propriétaires. Vous disposez d’un accès administratif total sur la machine virtuelle pour installer les nouveaux outils dont vous avez besoin. Vous pouvez également installer des packages supplémentaires non préinstallés en Python et R. Pour Python, vous pouvez utiliser ```conda``` ou ```pip```. Pour R, vous pouvez utiliser ```install.packages()``` dans la console R, ou utiliser l’IDE et sélectionner **Packages** > **Installer les packages**.

## <a name="deep-learning"></a>Apprentissage approfondi

Outre les exemples basés sur les frameworks, vous pouvez obtenir un ensemble de procédures détaillées qui ont été validées sur la DSVM. Ces procédures vous permettent de vous lancer dans le développement d’applications de deep learning dans des domaines tels que la reconnaissance vocale et la compréhension d’image et de texte.   


- [Exécution de réseaux neuronaux sur différents frameworks](https://github.com/ilkarman/DeepLearningFrameworks) : cette procédure pas à pas montre comment migrer du code d’un framework vers un autre. Elle montre également comment comparer les performances des modèles et du runtime entre les frameworks. 

- [Guide pratique pour créer une solution de bout en bout afin de détecter des produits dans des images](https://github.com/Azure/cortana-intelligence-product-detection-from-images) : la détection d’image est une technique qui peut localiser des objets dans les images et les classer. Les retombées concrètes de cette technologie semblent très prometteuses dans de nombreux domaines professionnels. Par exemple, un détaillant peut utiliser cette technique pour déterminer quel produit un client a choisi dans un rayon. Il peut ensuite utiliser ces informations pour gérer le stock des produits. 

- [Deep learning pour l’audio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/) : ce tutoriel montre comment entraîner un modèle de deep learning pour la détection d’événements audio sur le [jeu de données de sons urbains](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html). Il fournit également une vue d’ensemble de l’utilisation des données audio.

- [Classification de documents texte](https://github.com/anargyri/lstm_han) : cette procédure pas à pas montre comment créer et entraîner deux architectures de réseau neuronal : réseau d’attention hiérarchisée et réseau à mémoire à long ou court terme. Ces réseaux neuronaux utilisent l’API Keras de l’apprentissage profond pour classer les documents texte. Keras facilite l’utilisation de trois des frameworks de deep learning les plus populaires, à savoir Microsoft Cognitive Toolkit, TensorFlow et Theano.

## <a name="summary"></a>Résumé
Cet article décrit certaines des actions possibles sur la Microsoft Data Science Virtual Machine. Il existe bien d’autres actions que vous pouvez effectuer pour faire de la DSVM un environnement d’analytique efficace.

