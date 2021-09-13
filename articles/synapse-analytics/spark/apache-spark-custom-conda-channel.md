---
title: Créer un canal Conda personnalisé pour la gestion des packages
description: Découvrez comment créer un canal Conda personnalisé pour la gestion des packages
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 08/11/2021
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 937bfbf1ab1f874c6582b005ee0f7376ecf46235
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525337"
---
# <a name="create-a-custom-conda-channel-for-package-management"></a>Créer un canal Conda personnalisé pour la gestion des packages 
Lors de l’installation de packages Python, le gestionnaire de package Conda utilise des canaux pour rechercher des packages. Il se peut que vous deviez créer un canal Conda personnalisé pour diverses raisons. Par exemple, il se peut que :

- Votre espace de travail est protégé contre l’exfiltration de données et les connexions sortantes sont bloquées.  
- Vous avez des packages que vous ne souhaitez pas charger dans des référentiels publics.
- Vous souhaitez configurer un autre dépôt pour les utilisateurs au sein de votre espace de travail.

Cet article fournit un guide pas à pas pour vous aider à créer votre canal Conda personnalisé dans votre compte Azure Data Lake Storage.

## <a name="set-up-your-local-machine"></a>Configurer votre ordinateur local

1. Installez Conda sur votre ordinateur local. Vous pouvez vous référer au [Runtime Azure Synapse Spark](./apache-spark-version-support.md) pour identifier la version de Conda utilisée sur le même runtime.
   
2. Pour créer un canal personnalisé, installez une build Conda.
```
conda install conda-build
```
3. Organisez tous les packages pour la plateforme que vous souhaitez servir. Dans cet exemple, nous allons installer une archive Anaconda sur votre ordinateur local.

```
sudo wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh 
sudo chmod +x Anaconda3-4.4.0-Linux-x86_64.sh  
sudo bash Anaconda3-4.4.0-Linux-x86_64.sh -b -p /usr/lib/anaconda3 
export PATH="/usr/lib/anaconda3/bin:$PATH" 
sudo chmod 777 -R /usr/lib/anaconda3  
```

4. Pour créer un environnement similaire à celui qui est créé dans le runtime Azure Synapse, vous pouvez télécharger [ce modèle](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml). Il peut y avoir de légères différences entre le modèle et l’environnement Azure Synapse réel. Une fois le modèle téléchargé, vous pouvez exécuter la commande suivante :
```
apt-get -yq install gcc g++
conda env update --prune -f base_environment.yml
```

## <a name="mount-the-storage-account-onto-your-machine"></a>Monter le compte de stockage sur votre ordinateur
Ensuite, nous monterons le compte Azure Data Lake Storage Gen2 sur votre ordinateur local. Bien que vous puissiez également accomplir ce processus à l’aide d’un compte WASB, nous allons présenter un exemple pour le compte ADLSg2. 
 
Pour plus d’informations sur la façon de monter le compte de stockage sur votre ordinateur local, vous pouvez consulter [cette page](https://github.com/Azure/azure-storage-fuse#blobfuse ). 

1. Vous pouvez installer blobfuse à partir du référentiel de logiciels Linux pour les produits Microsoft.

```
wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb 
sudo dpkg -i packages-microsoft-prod.deb 
sudo apt-get update 
sudo apt-get install blobfuse fuse 
export AZURE_STORAGE_ACCOUNT=<storage-account-name>
export AZURE_STORAGE_SAS_TOKEN="<SAS>" 
export AZURE_STORAGE_BLOB_ENDPOINT=*.dfs.core.windows.net
```

2. Créez votre point de montage (```mkdir /path/to/mount```) et montez un conteneur d’objets blob avec blobfuse. Dans cet exemple, nous allons utiliser la valeur **privatechannel** pour la variable **mycontainer**.
   
```
sudo mkdir /home/trusted-service-user/privatechannel 
sudo mkdir -p /mnt/blobfusetmp 
blobfuse /home/trusted-service-user/privatechannel --container-name=privatechannel --tmp-path=/mnt/blobfusetmp --use-adls=true --log-level=LOG_DEBUG 
sudo chown trusted-service-user /mnt/blobfusetmp 
```
## <a name="create-the-channel"></a>Créer le canal
Dans les étapes suivantes, nous allons créer un canal Conda personnalisé.

1. Sur votre ordinateur local, créez un répertoire dans lequel organiser tous les packages pour votre canal personnalisé. Organisez tous les packages ```tar.bz2``` de https://repo.anaconda.com/pkgs/main/linux-64/ dans le sous-répertoire. Veillez à inclure également tous les packages tar.bz2 dépendants.
   
```

cd ~/privatechannel/ 
mkdir -P channel/linux64 

<Add all .tar.bz2 from https://repo.anaconda.com/pkgs/main/linux-64/> 
// Note: Add all dependent .tar.bz2 as well 

cd channel1 
mkdir noarch 
echo '{}' > noarch/repodata.json 
bzip2 -k noarch/repodata.json 

// Create channel 
conda index channel/noarch 
conda index channel/linux-64 
conda index channel
```

2. À présent, vous pouvez vérifier le compte de stockage dans lequel votre répertoire ```privatechannel/channel``` aurait été créé.

>[!Note]
> Conda ne respecte pas le jeton SAS associé à un conteneur. Par conséquent, vous devez marquer le conteneur « privatechannel » comme bénéficiant de l’accès public.


Pour plus d’informations, vous pouvez également [consulter le guide de l’utilisateur Conda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/create-custom-channels.html) pour créer des canaux personnalisés. 

## <a name="storage-account-permissions"></a>Autorisations du compte de stockage
À présent, nous devons valider les autorisations sur le compte de stockage. Pour définir ces autorisations, accédez au chemin d’accès dans lequel le canal personnalisé sera créé. Ensuite, créez un jeton SAP pour ```privatechannel```, qui dispose d’autorisations de lecture, de liste et d’exécution. 

Le nom du canal est désormais l’URL de SAP du blob générée à partir de ce processus.  

## <a name="create-a-sample-conda-environment-configuration-file"></a>Créer un exemple de fichier de configuration d’environnement Conda
Enfin, vérifiez le processus d’installation en créant un exemple de fichier ```environment.yml``` Conda. Si vous disposez d’un espace de travail avec la protection contre l’exfiltration de données activée, vous devez spécifier le canal ``nodefaults`` dans votre fichier d’environnement.

Voici un exemple de fichier de configuration de Conda :
```
name: sample 
channels: 
  - https://<<storage account name>>.blob.core.windows.net/privatechannel/channel?<<SAS Token>
  - nodefaults 
dependencies: 
  - openssl 
  - ncurses 
```
Après avoir créé l’exemple de fichier Conda, vous pouvez créer un environnement Conda virtuel. Vous pouvez le vérifier localement en exécutant les commandes suivantes :

```
conda env create –file sample.yml  
source activate env 
conda list 
```
À présent que vous avez vérifié votre canal personnalisé, vous pouvez utiliser le processus de [gestion du pool python](./apache-spark-manage-python-packages.md) pour mettre à jour les bibliothèques sur votre pool Apache Spark.

## <a name="next-steps"></a>Étapes suivantes
- Afficher les bibliothèques par défaut : [Prise en charge des versions d’Apache Spark](apache-spark-version-support.md)
- Gérer les packages Python : [gestion des packages Python](./apache-spark-manage-python-packages.md)

