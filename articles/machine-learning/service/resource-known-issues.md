---
title: Problèmes connus et résolutions
titleSuffix: Azure Machine Learning service
description: Liste des problèmes connus, des solutions de contournement et des résolutions pour Azure Machine Learning service.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: e143c0c8ef09af49aed656d479bcad4dd35e2211
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54351796"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Problèmes connus et dépannage du service Azure Machine Learning
 
Cet article vous permet de rechercher et de corriger les erreurs ou les défaillances rencontrées lors de l’utilisation du service Azure Machine Learning. 

## <a name="sdk-installation-issues"></a>Problèmes d’installation de Kit de développement logiciel (SDK)

**Message d’erreur : Impossible de désinstaller « PyYAML »** 

Kit SDK Azure Machine Learning pour Python : PyYAML est un projet installé distutils. Par conséquent, nous ne pouvons pas déterminer avec précision les fichiers qui lui appartiennent en cas de désinstallation partielle. Pour poursuivre l’installation du kit de développement logiciel (SDK) en ignorant cette erreur, utilisez :
```Python 
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problèmes à la création de la Capacité de calcul Azure Machine Learning
Il peut arriver, dans de rares cas, que des utilisateurs ayant créé leur espace de travail Azure Machine Learning sur le Portail Azure avant la disponibilité générale ne puissent pas créer de Capacité de calcul Azure Machine Learning dans cet espace de travail. Vous pouvez soulever une demande de support auprès du service ou créer un espace de travail sur le Portail ou avec le kit SDK pour vous débloquer sans délai. 

## <a name="image-building-failure"></a>Échec de génération d’image

Échec de génération d’image lors du déploiement de service web. La solution de contournement consiste à ajouter « pynacl==1.2.1 » en tant que dépendance pip au fichier Conda pour la configuration d’image.  

## <a name="fpgas"></a>FPGA
Vous ne serez pas en mesure de déployer des modèles sur des FPGA tant que vous n’aurez pas demandé un quota FPGA et qu’il n’aura pas été approuvé. Pour demander un accès, remplissez le formulaire de demande de quota : https://aka.ms/aml-real-time-ai

## <a name="databricks"></a>Databricks

Problèmes Databricks et Azure Machine Learning
 
1. Échec de l’installation du SDK AML sur Databricks quand d’autres packages sont installés.

   Certains packages, comme `psutil`, peuvent provoquer des conflits. Pour éviter les erreurs d’installation, installez les packages en bloquant la version des bibliothèques. Ce problème est lié à Databricks et non au kit SDK Azure ML. Il peut aussi se rencontrer avec d’autres bibliothèques. Exemple :
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
   ```
   Vous pouvez également utiliser des scripts init si les problèmes d’installation persistent avec les bibliothèques Python. Cette approche n’est pas prise en charge officiellement. Vous pouvez consulter [ce document](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

2. Si vous utilisez Machine Learning sur Databricks et souhaitez annuler une exécution pour en démarrer une nouvelle, redémarrez votre cluster Azure Databricks.

3. Dans les paramètres de ML automatisé, en présence de > 10 itérations, définissez show_output sur False lors de la soumission de votre exécution.


## <a name="azure-portal"></a>Portail Azure
Si vous accédez directement à votre espace de travail à partir d’un lien de partage provenant du kit SDK ou du portail, vous ne pourrez pas afficher la page Vue d’ensemble normale comportant des informations sur l’abonnement dans l’extension. Vous ne pourrez pas non plus basculer sur un autre espace de travail. Si vous souhaitez afficher un autre espace de travail, la solution de contournement consiste à accéder directement au [portail Azure](https://portal.azure.com) et à rechercher le nom de l’espace de travail.

## <a name="diagnostic-logs"></a>Journaux de diagnostic
Parfois, fournir des informations de diagnostic quand vous demandez de l’aide peut se révéler utile. Les fichiers journaux se trouvent aux emplacements suivants :

## <a name="resource-quotas"></a>Quotas de ressources

Découvrez plus d’informations sur les [quotas des ressources](how-to-manage-quotas.md) que vous pouvez rencontrer quand vous utilisez Azure Machine Learning.

## <a name="get-more-support"></a>Obtenir plus de support

Vous pouvez envoyez des demandes de support et obtenir de l’aide auprès du support technique, des forums, etc. [En savoir plus…](support-for-aml-services.md)
