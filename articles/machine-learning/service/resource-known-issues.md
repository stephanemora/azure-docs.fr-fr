---
title: Problèmes connus et dépannage du service Azure Machine Learning
description: Obtenir une liste des problèmes connus, des solutions de contournement et des résolutions des problèmes
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 02cee5a3e088c919ec94aee6f46ef6f428b9bb48
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249415"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Problèmes connus et dépannage du service Azure Machine Learning
 
Cet article vous permet de rechercher et de corriger les erreurs ou les défaillances rencontrées lors de l’utilisation du service Azure Machine Learning. 

## <a name="sdk-installation-issues"></a>Problèmes d’installation de Kit de développement logiciel (SDK)

**Message d’erreur : Impossible de désinstaller « PyYAML »** 

PyYAML est un projet installé distutils. Par conséquent, nous ne pouvons pas déterminer avec précision les fichiers qui lui appartiennent en cas de désinstallation partielle. Pour poursuivre l’installation du kit de développement logiciel (SDK) en ignorant cette erreur, utilisez :
```Python 
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="image-building-failure"></a>Échec de génération d’image

Échec de génération d’image lors du déploiement de service web. La solution de contournement consiste à ajouter « pynacl==1.2.1 » en tant que dépendance pip au fichier Conda pour la configuration d’image.  

## <a name="pipelines"></a>Pipelines
Une erreur se produit lors d’appels répétés à PythonScriptStep dans une ligne sans modifier le script ou les paramètres. La solution de contournement consiste à régénérer l’objet PipelineData.

## <a name="fpgas"></a>FPGA
Vous ne serez pas en mesure de déployer des modèles sur des FPGA tant que vous n’aurez pas demandé un quota FPGA et qu’il n’aura pas été approuvé. Pour demander un accès, remplissez le formulaire de demande de quota : https://aka.ms/aml-real-time-ai

## <a name="databricks"></a>Databricks

Problèmes Databricks et Azure Machine Learning

1. Recommandation pour le cluster Databricks :
   
   Créez votre cluster Azure Databricks en version 4.x avec Python 3. Nous recommandons un cluster avec accès concurrentiel élevé.
 
1. Échec de l’installation du SDK AML sur Databricks quand d’autres packages sont installés.

   Certains packages, comme `psutil upgrade libs`, peuvent provoquer des conflits. Pour éviter les erreurs d’installation, installez les packages en bloquant la version des bibliothèques. Ce problème est lié à Databricks et non pas au SDK AML. Exemple :
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython=2.2.0
   ```

## <a name="diagnostic-logs"></a>Journaux de diagnostic
Parfois, fournir des informations de diagnostic quand vous demandez de l’aide peut se révéler utile. Les fichiers journaux se trouvent aux emplacements suivants :

## <a name="resource-quotas"></a>Quotas de ressources

Découvrez plus d’informations sur les [quotas des ressources](how-to-manage-quotas.md) que vous pouvez rencontrer quand vous utilisez Azure Machine Learning.

## <a name="get-more-support"></a>Obtenir plus de support

Vous pouvez envoyez des demandes de support et obtenir de l’aide auprès du support technique, des forums, etc. [En savoir plus…](support-for-aml-services.md)
