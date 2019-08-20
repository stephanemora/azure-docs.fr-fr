---
title: Fichier Include
description: Fichier Include
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 07/31/2019
ms.openlocfilehash: e571c65e64fad73c646aa05366cab685aa745caa
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968862"
---
1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec les informations d’identification de l’abonnement Azure que vous utilisez. 

1. En haut à gauche du portail Azure, sélectionnez **Créer une ressource**.

1. Utilisez la barre de recherche pour rechercher **Espace de travail Machine Learning service**.

1. Sélectionnez **Espace de travail Machine Learning service**.

1. Dans le volet **Espace de travail du service Machine Learning**, sélectionnez **Créer** pour commencer.

1. Configurez votre nouvel espace de travail en fournissant le nom de l’espace de travail, l’abonnement, le groupe de ressources et l’emplacement.

    ![Créer un espace de travail](./media/aml-create-in-portal/workspace-create-main-tab.png)

   Champ|Description 
   ---|---
   Nom de l’espace de travail |Entrez un nom unique qui identifie votre espace de travail. Dans cet exemple, nous allons utiliser **docs-ws**. Dans le groupe de ressources, les noms doivent être uniques. Utilisez un nom dont il est facile de se rappeler et que vous pouvez facilement différencier des autres espaces de travail.  
   Subscription |Sélectionnez l’abonnement Azure que vous souhaitez utiliser.
   Resource group | Utilisez un groupe de ressources existant dans votre abonnement, ou entrez un nom pour créer un groupe de ressources. Un groupe de ressources contient les ressources associées d’une solution Azure. Dans cet exemple, nous allons utiliser **docs-aml**. 
   Location | Sélectionnez l’emplacement le plus proche de vos utilisateurs et des ressources de données pour créer votre espace de travail.

1. Une fois que vous avez terminé de configurer l’espace de travail, sélectionnez **Créer**. 

   La création de l’espace de travail peut prendre quelques instants.

   Une fois le processus terminé, un message indiquant la réussite du déploiement s’affiche. Ce message s’affiche également dans la section des notifications. Pour afficher le nouvel espace de travail, sélectionnez **Accéder à la ressource**.

   ![État de création de l’espace de travail](./media/aml-create-in-portal/notifications.png)

1. Si vous prévoyez d’utiliser une option sans code à partir du portail, comme les expériences d’interface visuelle ou de ML automatisées, vous avez terminé. Si vous prévoyez de créer une [machine virtuelle Notebook](../articles/machine-learning/service/tutorial-1st-experiment-sdk-setup.md#azure), vous avez également terminé. 

1. Si vous prévoyez d’utiliser du code sur votre environnement local qui référence cet espace de travail, sélectionnez **Télécharger config.json** dans la section **Vue d’ensemble** de l’espace de travail.  

   ![Télécharger config.json](./media/aml-create-in-portal/configure.png)
   
   Placez le fichier dans la structure de répertoires avec vos scripts Python ou vos notebooks Jupyter. Il peut se trouver dans le même répertoire, dans un sous-répertoire nommé *.azureml* ou dans un répertoire parent. Quand vous créez une machine virtuelle Notebook, ce fichier est ajouté pour vous au répertoire approprié sur la machine virtuelle.

    

