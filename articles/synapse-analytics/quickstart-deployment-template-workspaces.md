---
title: 'Démarrage rapide : Créer un espace de travail Azure Synapse avec un modèle Azure Resource Manager'
description: Découvrez comment créer un espace de travail Synapse à l’aide d’un modèle Azure Resource Manager.
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 08/07/2020
ms.openlocfilehash: dc6d36f2316e0ae19ce8b813fa9eb127b1a9cf1f
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569003"
---
# <a name="quickstart-create-an-azure-synapse-workspace-using-a-deployment-template"></a>Démarrage rapide : Créer un espace de travail Azure Synapse en utilisant un modèle de déploiement

Ce modèle Azure Resource Manager (modèle ARM) crée un espace de travail Azure Synapse avec Data Lake Storage sous-jacent. L’espace de travail Azure Synapse est une limite de collaboration sécurisable pour les processus analytiques dans Azure Synapse Analytics.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

[![Déployer sur Azure - 1](../media/template-deployments/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="review-the-template"></a>Vérifier le modèle

Vous pouvez examiner le modèle en sélectionnant le lien **Visualiser** comme suit :

[![Visualiser](../media/template-deployments/template-visualize-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

Le modèle définit deux ressources :

- Compte de stockage
- Espace de travail

## <a name="deploy-the-template"></a>Déployer le modèle

1. Sélectionnez l’image ci-après pour vous connecter à Azure et ouvrir le modèle. Ce modèle crée un espace de travail Synapse.
   
   [![Déployer sur Azure 2](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

1. Entrez ou mettez à jour les valeurs suivantes :

   * **Abonnement**: Sélectionnez un abonnement Azure.
   * **Groupe de ressources** : Sélectionnez **Créer**, entrez un nom unique pour le groupe de ressources et sélectionnez **OK**. La création d’un nouveau groupe de ressources facilitera le nettoyage des ressources.
   * **Région** : sélectionnez une région.  Par exemple, **USA Centre**.
   * **Nom** : attribuez un nom à votre espace de travail.
   * **Connexion de l’administrateur SQL** : entrez le nom d’administrateur pour le serveur SQL.
   * **Mot de passe de l’administrateur SQL** : entrez le mot de passe d’administrateur pour le serveur SQL.
   * **Valeurs des balises** : acceptez la valeur par défaut. 
   * **Examiner et créer** : cochez la case.
   * **Créer** : cochez la case.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure Synapse Analytics et Azure Resource Manager, passez aux articles ci-dessous.

- Consulter la [Vue d’ensemble d’Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 
- En savoir plus sur [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- [Créer et déployer votre premier modèle Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
