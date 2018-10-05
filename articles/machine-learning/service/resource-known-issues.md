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
ms.date: 09/24/2018
ms.openlocfilehash: d84040dc440c373ae9bae6dbac7a95109a387ba7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162744"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Problèmes connus et dépannage du service Azure Machine Learning
 
Cet article vous permet de rechercher et de corriger les erreurs ou les défaillances rencontrées lors de l’utilisation du service Azure Machine Learning. 


## <a name="databricks"></a>Databricks

Problèmes Databricks et Azure Machine Learning

1. Recommandation pour le cluster Databricks :
   
   Créez votre cluster Azure Databricks en version 4.x avec Python 3. Nous recommandons un cluster avec accès concurrentiel élevé.
 
1. Échec de l’installation du SDK AML sur Databricks quand d’autres packages sont installés.

   Certains packages, comme `psutil upgrade libs`, peuvent provoquer des conflits. Pour éviter les erreurs d’installation, installez les packages en bloquant la version des bibliothèques. Ce problème est lié à Databricks et non pas au SDK AML. Exemple :
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython=2.2.0
   ```

## <a name="gather-diagnostics-information"></a>Collecter des informations de diagnostic
Parfois, fournir des informations de diagnostic quand vous demandez de l’aide peut se révéler utile. Les fichiers journaux se trouvent aux emplacements suivants :

## <a name="resource-quotas"></a>Quotas de ressources

Découvrez plus d’informations sur les [quotas des ressources](how-to-manage-quotas.md) que vous pouvez rencontrer quand vous utilisez Azure Machine Learning.

## <a name="get-more-support"></a>Obtenir plus de support

Vous pouvez envoyez des demandes de support et obtenir de l’aide auprès du support technique, des forums, etc. [En savoir plus…](support-for-aml-services.md)
