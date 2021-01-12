---
title: Résoudre les problèmes liés aux images d’environnement
titleSuffix: Azure Machine Learning
description: Découvrez comment résoudre les problèmes liés à la génération d’images d’environnement et à l’installation de packages.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 12/3/2020
ms.topic: troubleshooting
ms.custom: devx-track-python
ms.openlocfilehash: b452c24b4b2ed6021910f267b9941f3829acccd8
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733204"
---
# <a name="troubleshoot-environment-image-builds"></a>Résoudre les problèmes liés à la génération d’images d’environnement
Découvrez comment résoudre les problèmes liés à la génération d’images d’environnement et à l’installation de packages Docker.

## <a name="prerequisites"></a>Prérequis

* Un **abonnement Azure**. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree).
* Le [Kit de développement logiciel (SDK) Azure Machine Learning](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
* [Interface de ligne de commande Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* [Extension CLI pour Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Pour déboguer localement, vous devez avoir une installation opérationnelle de Docker sur votre système local.

## <a name="docker-image-build-failures"></a>Échecs de la génération d’images Docker
 
Pour la majorité des échecs de génération d’image, la cause racine provient du journal de génération d’images.
Vous pouvez trouver le journal de génération d’images dans le portail Azure Machine Learning (20\_image\_build\_log.txt) ou dans vos journaux d’exécution de tâches ACR.
 
Dans la plupart des cas, il est plus facile de reproduire les erreurs localement. Vérifiez le type d’erreur et essayez l’un des `setuptools` suivants :

- Installez la dépendance conda localement `conda install suspicious-dependency==X.Y.Z`
- Installez la dépendance pip localement `pip install suspicious-dependency==X.Y.Z`
- Essayez de matérialiser l’environnement entier `conda create -f conda-specification.yml`

> [!IMPORTANT]
> Assurez-vous que la plateforme et l’interpréteur sur votre calcul local correspondent à ceux sur le calcul distant. 

### <a name="timeout"></a>Délai d'expiration 
 
Des problèmes de délai d’attente peuvent se produire pour différents problèmes réseau :
- Bande passante Internet faible
- Problèmes de serveur
- Dépendance de grande taille qui ne peut pas être téléchargée avec les paramètres donnés de délai d’attente conda et pip
 
Des messages similaires à ce qui suit indiquent le problème :
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

Solutions possibles :
 
- Essayez une autre source de dépendance, si possible, telle que des miroirs, le stockage blob ou d’autres flux Python.
- Mettez à jour conda ou pip. Si un fichier Docker personnalisé est utilisé, mettez à jour les paramètres de délai d’attente.
- Certaines versions de pip présentent des problèmes connus. Envisagez d’ajouter une version spécifique de pip à des dépendances d’environnement.

### <a name="package-not-found"></a>Package introuvable

Il s’agit du cas le plus courant pour les échecs de génération d’image.

- Le package conda est introuvable
        ```
        ResolvePackageNotFound: 
          - not-existing-conda-package
        ```

- La version ou le package pip spécifiés sont introuvables
        ```
        ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
        ERROR: No matching distribution found for invalid-pip-package
        ```

- Dépendance pip imbriquée incorrecte
        ```
        ERROR: No matching distribution found for bad-backage==0.0 (from good-package==1.0)
        ```

Vérifiez si le package existe sur les sources spécifiées. Utilisez [pip search](https://pip.pypa.io/en/stable/reference/pip_search/) pour vérifier les dépendances pip.

`pip search azureml-core`

Pour les dépendances conda, utilisez [conda search](https://docs.conda.io/projects/conda/en/latest/commands/search.html).

`conda search conda-forge::numpy`

Pour plus d’options :
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>Notes du programme d’installation

Assurez-vous que la distribution requise existe pour la plateforme spécifiée et la version de l’interpréteur Python.

Pour les dépendances pip, accédez à `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` pour voir si la version requise est disponible. Par exemple : https://pypi.org/project/azureml-core/1.11.0/#files

Pour les dépendances conda, vérifiez le package sur le référentiel du canal.
Pour les canaux gérés par Anaconda, Inc., vérifiez [ici](https://repo.anaconda.com/pkgs/).

### <a name="pip-package-update"></a>Mise à jour du package pip

Pendant l’installation ou la mise à jour d’un package pip, le programme de résolution peut avoir besoin de mettre à jour un package déjà installé pour répondre aux nouvelles exigences.
La désinstallation peut échouer pour diverses raisons liées à la version de pip ou à la façon dont la dépendance a été installée.
Le scénario le plus courant est qu’une dépendance installée par conda n’a pas pu être désinstallée par pip.
Pour ce scénario, envisagez de désinstaller la dépendance à l’aide de `conda remove mypackage`.

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>Problèmes liés au programme d’installation

Certaines versions du programme d’installation présentent des problèmes dans les programmes de résolution de package, qui peuvent entraîner l’échec de la génération.

Si une image de base personnalisée est utilisée, ou le fichier dockerfile, nous vous recommandons d’utiliser conda version 4.5.4 ou supérieure.

Le package pip est requis pour installer les dépendances pip et si aucune version n’est spécifiée dans l’environnement, la version la plus récente est utilisée.
Nous vous recommandons d’utiliser une version connue de pip pour éviter des problèmes temporaires ou des changements cassants pouvant être provoqués par la dernière version de l’outil.

Envisagez d’épingler la version de pip dans votre environnement si vous voyez l’un des messages suivants :

`Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.`

`Pip subprocess error:
ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.`

En outre, l’installation de pip peut être bloquée dans une boucle infinie en cas de conflits insolubles dans les dépendances. Si vous travaillez localement, rétrogradez la version de pip à une version antérieure à 20.3. Dans un environnement conda créé à partir d’un fichier YAML, ce problème se produira seulement si conda-forge correspond au canal avec la priorité la plus élevée. Pour atténuer le problème, spécifiez explicitement pip < 20.3 (épingle !=20.3 ou =20.2.4 vers une autre version) en tant que dépendance conda dans le fichier de spécification conda.

## <a name="service-side-failures"></a>Échecs côté service

### <a name="unable-to-pull-image-from-mcraddress-could-not-be-resolved-for-container-registry"></a>Impossible d’extraire l’image à partir de MCR/L’adresse du serveur n’a pas pu être résolue pour Container Registry.
Problèmes possibles :
- Le nom du chemin au registre de conteneurs peut ne pas être résolu correctement. Vérifiez que les noms des images utilisent des doubles barres obliques et que la direction des barres obliques sur les hôtes Linux et Windows est correcte.
- Si le registre ACR situé derrière un réseau virtuel utilise un point de terminaison privé dans [une région non prise en charge](https://docs.microsoft.com/azure/private-link/private-link-overview#availability), configurez le registre ACR situé derrière un réseau virtuel à l’aide du point de terminaison de service (accès public) à partir du portail et réessayez.
- Après avoir placé le registre ACR derrière un réseau virtuel, assurez-vous que le [modèle ARM](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) est exécuté. Cela permet à l’espace de travail de communiquer avec l’instance ACR.

### <a name="401-error-from-workspace-acr"></a>Erreur 401 à partir du registre ACR de l’espace de travail
Resynchronisez les clés de stockage à l’aide de [ws.sync_keys()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#sync-keys--).

### <a name="environment-keeps-throwing-waiting-for-other-conda-operations-to-finish-error"></a>L’environnement continue à générer l’erreur « En attente de la fin d’autres opérations Conda… »
Quand une génération d’image est en cours, conda est verrouillé par le client du kit SDK. Si le processus s’est arrêté ou a été annulé de manière incorrecte par l’utilisateur, conda reste à l’état verrouillé. Pour résoudre cela, supprimez manuellement le fichier de verrouillage. 

### <a name="custom-docker-image-not-in-registry"></a>Image Docker personnalisée absente du registre
Vérifiez si l’[étiquette correcte](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments#create-an-environment) est utilisée et que `user_managed_dependencies = True`. `Environment.python.user_managed_dependencies = True` désactive conda et utilise les packages installés de l’utilisateur.

### <a name="common-vnet-issues"></a>Problèmes courants liés au réseau virtuel

1. Vérifiez que le compte de stockage, le cluster de calcul et Azure Container Registry soient tous dans le même sous-réseau du réseau virtuel.
2. Quand le registre ACR se trouve derrière un réseau virtuel, il ne peut pas être utilisé directement pour générer des images. Le cluster de calcul doit être utilisé pour générer les images.
3. Le stockage doit éventuellement être placé derrière un réseau virtuel dans les cas suivants :
    - Utilisation de l’inférence ou de la roue privée
    - Affichage d’erreurs de service 403 (non autorisé)
    - Impossibilité d’obtenir les détails d’image à partir du registre ACR/MCR

### <a name="image-build-fails-when-trying-to-access-network-protected-storage"></a>Échec de la génération d’image lors d’une tentative d’accès au stockage protégé du réseau
- Les tâches ACR ne fonctionnent pas derrière le réseau virtuel. Si l’utilisateur a son registre ACR derrière le réseau virtuel, il doit utiliser le cluster de calcul pour générer une image.
- Le stockage doit se trouver derrière un réseau virtuel pour pouvoir extraire les dépendances de celui-ci. 

### <a name="cannot-run-experiments-when-storage-has-network-security-enabled"></a>Impossible d’exécuter des expériences lorsque la sécurité réseau est activée pour le stockage
Lorsque vous utilisez les images Docker par défaut et que vous activez les dépendances gérées par l’utilisateur, vous devez utiliser les [étiquettes de service](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network) MicrosoftContainerRegistry et AzureFrontDoor.FirstParty pour mettre en liste verte le registre MCR et ses dépendances.

 Pour plus d’informations, consultez [Activation du réseau virtuel](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry).

### <a name="creating-an-icm"></a>Création d’un ICM

Lors de la création ou de l’affectation d’un ICM au metastore, incluez le ticket de support CSS pour que nous puissions mieux comprendre le problème.

## <a name="next-steps"></a>Étapes suivantes

- [Entraîner un modèle Machine Learning pour catégoriser les fleurs](how-to-train-scikit-learn.md)
- [Entraîner un modèle Machine Learning à l’aide d’une image Docker personnalisée](how-to-train-with-custom-image.md)