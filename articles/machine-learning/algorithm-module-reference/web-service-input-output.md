---
title: Entrée/sortie du service web
description: En savoir plus sur les modules de service web disponibles dans le concepteur Azure Machine Learning (préversion)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: be940e3ba693270707e22ffc7b9377dbea1df5f8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81462021"
---
# <a name="web-service-inputoutput"></a>Entrée/sortie du service web

Cet article décrit les modules **Web Service Input** et **Web Service Output** dans le concepteur Azure Machine Learning (préversion).

Le module **Web Service Input** peut uniquement se connecter au port d’entrée de type **DataFrameDirectory**. Et le module **Web Service Output** peut uniquement être connecté à partir du port de sortie de type **DataFrameDirectory**. Les deux modules se trouvent dans l’arborescence du module, sous la catégorie **Service Web**. 

Le module **Entrée du service web** indique l’emplacement où les données utilisateur sont entrées dans le pipeline, et le module **Sortie du service web** indique l’emplacement où les données utilisateur sont retournées dans un pipeline d’inférence en temps réel.

## <a name="how-to-use-web-service-inputoutput"></a>Utilisation des modules Entrée/sortie du service web

- Lorsque vous créez un pipeline d’inférence en temps réel à partir de votre pipeline d’apprentissage, les modules **Entrée du service web** et **Sortie du service web** sont automatiquement ajoutés pour indiquer l’emplacement où les données utilisateur sont entrées dans le pipeline et l’emplacement où elles sont retournées. 

    En savoir plus sur la [création d’un pipeline d’inférence en temps réel](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline).

    > [!NOTE]
    > La génération automatique du pipeline d’inférence en temps réel est un processus de meilleur effort basé sur une règle qui ne fournit aucune garantie quant à son exactitude. Vous pouvez ajouter ou supprimer manuellement les modules **Entrée/sortie du service web** pour répondre à vos besoins. Assurez-vous qu’il existe au moins un module **Entrée du service web** et un module **Sortie du service web** dans votre pipeline d’inférence en temps réel. Si vous avez plusieurs modules **Entrée du service web** ou **Sortie du service web**, assurez-vous qu’ils ont des noms uniques, que vous pouvez entrer dans le volet droit du module.

- Vous pouvez également créer manuellement un pipeline d’inférence en temps réel en ajoutant les modules **Entrée du service web** et **Sortie du service web** à votre pipeline avant de l’envoyer.

    > [!NOTE]
    >  Le type de pipeline sera déterminé au premier envoi du pipeline. Veillez donc à ajouter les modules **Entrée du service web** et **Sortie du service web** avant le premier envoi du pipeline si vous souhaitez créer un pipeline d’inférence en temps réel.

   L’exemple ci-dessous montre comment créer manuellement un pipeline d’inférence en temps réel à partir du module **Exécuter un script Python**. 

   ![ Exemple](media/module/web-service-input-output-example.png)
   
   Une fois le pipeline envoyé et l’exécution terminée avec succès, vous pourrez déployer le point de terminaison en temps réel.
   
   > [!NOTE]
   >  Dans l’exemple ci-dessus, l’option **Entrer des données manuellement** fournit le schéma de données pour l’entrée du service web et elle est nécessaire pour déployer le point de terminaison en temps réel. En règle générale, vous devez toujours connecter un module ou un jeu de données au port avec lequel le module **Entrée de service web** est connecté pour fournir le schéma de données.
   
## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur le [déploiement du point de terminaison en temps réel](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint).
Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning.