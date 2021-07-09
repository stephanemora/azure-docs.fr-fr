---
title: Résoudre les problèmes liés aux images Docker prédéfinies
titleSuffix: Azure Machine Learning
description: Étapes de résolution des problèmes pour utiliser des images Docker prédéfinies pour l’inférence.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 05/25/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt, troubleshoot
ms.openlocfilehash: 598c578c445cbd7dc7086f22e3c8d4885d67b112
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110537328"
---
# <a name="troubleshooting-prebuilt-docker-images-for-inference-preview"></a>Résolution des problèmes liés aux images Docker prédéfinies pour l’inférence (préversion)

Découvrez comment résoudre les problèmes que vous pouvez rencontrer quand vous utilisez des images Docker prédéfinies pour l’inférence [(préversion)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) avec Azure Machine Learning.

## <a name="model-deployment-failed"></a>Échec du déploiement de modèle

Si le déploiement de modèle échoue, vous ne voyez pas de journaux dans le [studio Azure Machine Learning](https://ml.azure.com/) et `service.get_logs()` retourne None.
En cas de problème dans la fonction init() de score.py, `service.get_logs()` retourne les journaux correspondants.

Vous devez donc exécuter le conteneur localement avec l’une des commandes ci-dessous et remplacer `<MCR-path>` par un chemin d’image. Pour obtenir la liste des images et des chemins, consultez [Images Docker prédéfinies pour l’inférence](concept-prebuilt-docker-images-inference.md).

### <a name="mounting-extensibility-solution"></a>Montage de la solution d’extensibilité

Accédez au répertoire contenant `score.py` et exécutez :

```bash
docker run -it -v $(pwd):/var/azureml-app -e AZUREML_EXTRA_PYTHON_LIB_PATH="myenv/lib/python3.7/site-packages" <mcr-path>
```

### <a name="requirementstxt-extensibility-solution"></a>Solution d’extensibilité requirements.txt

Accédez au répertoire contenant `score.py` et exécutez :

```bash
docker run -it -v $(pwd):/var/azureml-app -e AZUREML_EXTRA_REQUIREMENTS_TXT="requirements.txt" <mcr-path>
```

## <a name="enable-local-debugging"></a>Activer le débogage local

Le serveur d’inférence local vous permet de déboguer rapidement votre script d’entrée (`score.py`). Si un bogue affecte le script de scoring sous-jacent, le serveur ne pourra pas initialiser ou traiter le modèle. Au lieu de cela, il lèvera une exception là où les problèmes se sont produits. [Apprenez-en davantage sur le serveur HTTP d’inférence Azure Machine Learning](how-to-inference-server-http.md)

## <a name="for-common-model-deployment-issues"></a>Pour les problèmes de déploiement de modèle courants

Pour les problèmes liés au déploiement d’un modèle à partir d’Azure Machine Learning vers ACI (Azure Container Instances) ou AKS (Azure Kubernetes Service), consultez [Résoudre les problèmes de déploiement de modèle](how-to-troubleshoot-deployment.md).

## <a name="init-or-run-failing-to-write-a-file"></a>Échec d’écriture de fichier par init() ou run()

Le serveur HTTP de nos images Docker prédéfinies s’exécute en tant qu’*utilisateur non-racine*. Il n’a peut-être pas de droit d’accès à tous les répertoires. Écrivez uniquement dans des répertoires pour lesquels vous disposez de droits d’accès. Par exemple, le répertoire `/tmp` dans le conteneur.

## <a name="extra-python-packages-not-installed"></a>Packages Python supplémentaires non installés

* Vérifiez que la variable d’environnement ou le nom de fichier ne contiennent pas de fautes de frappe.
* Consultez le journal du conteneur pour savoir si `pip install -r <your_requirements.txt>` est installé ou non.
* Vérifiez si le répertoire source est correctement défini dans le constructeur de la [configuration de l’inférence](/python/api/azureml-core/azureml.core.model.inferenceconfig#constructor).
* Si l’installation est introuvable et que le journal indique « file not found » (fichier introuvable), vérifiez que le nom de fichier indiqué dans le journal est correct.
* Si l’installation a démarré mais a échoué ou a expiré, essayez d’installer le même `requirements.txt` localement avec les mêmes versions de Python et pip dans un environnement propre (c’est-à-dire sans répertoire de cache ; `pip install --no-cache-dir -r requriements.txt`). Vérifiez si le problème peut être reproduit localement.

## <a name="mounting-solution-failed"></a>Échec du montage de la solution

* Vérifiez que la variable d’environnement ou le nom de répertoire ne contiennent pas de fautes de frappe.
* La variable d’environnement doit être définie sur le chemin relatif du fichier `score.py`.
* Vérifiez si le répertoire source est correctement défini dans le constructeur de la [configuration de l’inférence](/python/api/azureml-core/azureml.core.model.inferenceconfig#constructor).
* Le répertoire doit être le répertoire « site-packages » de l’environnement.
* Si `score.py` retourne toujours `ModuleNotFound` et que le module est supposé être dans le répertoire monté, essayez d’afficher le `sys.path` dans `init()` ou `run()` pour déterminer si un chemin est manquant.

## <a name="building-an-image-based-on-the-prebuilt-docker-image-failed"></a>Échec de la création d’une image basée sur une image Docker prédéfinie

* En cas d’échec durant l’installation du package apt, vérifiez si l’utilisateur a été défini comme utilisateur racine avant d’exécuter la commande apt. (Veillez à le redéfinir comme utilisateur non-racine) 

## <a name="image-built-based-on-the-prebuilt-docker-image-cant-boot-up"></a>L’image générée à partir de l’image Docker prédéfinie ne démarre pas

* L’utilisateur non-racine doit être `dockeruser`. Si ce n’est pas le cas, le propriétaire des répertoires suivants doit être défini sur le nom d’utilisateur que vous souhaitez utiliser quand vous exécutez l’image :

    ```bash
    /var/runit
    /var/log
    /var/lib/nginx
    /run
    /opt/miniconda
    /var/azureml-app
    ```

* Si le `ENTRYPOINT` a été modifié dans la nouvelle image générée, le serveur HTTP et les composants associés doivent être chargés avec `runsvdir /var/runit`

## <a name="next-steps"></a>Étapes suivantes

* [Ajoutez des packages Python aux images prédéfinies](how-to-prebuilt-docker-images-inference-python-extensibility.md).
* [Utilisez un package prédéfini comme base d’un nouveau Dockerfile](how-to-extend-prebuilt-docker-image-inference.md).