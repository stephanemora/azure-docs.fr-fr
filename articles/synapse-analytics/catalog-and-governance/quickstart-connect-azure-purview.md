---
title: Se connecter à un compte Azure Purview 
description: Connectez un compte Azure Purview à un espace de travail Synapse.
author: Jejiang
ms.service: synapse-analytics
ms.subservice: purview
ms.topic: quickstart
ms.date: 12/16/2020
ms.author: jejiang
ms.reviewer: jrasnick
ms.openlocfilehash: f0af3b571b1a6d793668c33d0c76e19a3d0c9e62
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109716057"
---
# <a name="quickstartconnect-an-azure-purview-account-to-a-synapse-workspace"></a>Démarrage rapide : Connecter un compte Azure Purview à un espace de travail Synapse 


Dans ce guide de démarrage rapide, vous allez inscrire un compte Azure Purview auprès d’un espace de travail Synapse. Cette connexion vous permet de découvrir des ressources Azure Purview et d’interagir avec celles-ci en utilisant les fonctionnalités de Synapse. 

Vous pouvez effectuer les tâches suivantes dans Synapse : 
- Utiliser la zone de recherche en haut pour rechercher des ressources Purview en fonction de mots clés 
- Comprendre les données en fonction des métadonnées, de la traçabilité, des annotations 
- Connecter ces données à votre espace de travail avec des services liés ou des jeux de données d’intégration 
- Analyser ces jeux de données avec Synapse Apache Spark, Synapse SQL et Data Flow 

## <a name="prerequisites"></a>Prérequis 
- [Compte Azure Purview](../../purview/create-catalog-portal.md) 
- [Espace de travail Synapse](../quickstart-create-workspace.md) 

## <a name="signin-toa-synapse-workspace"></a>Se connecter à un espace de travail Synapse 

Accédez à [https://web.azuresynapse.net](https://web.azuresynapse.net) et connectez-vous à votre espace de travail. 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>Autorisations pour la connexion d’un compte Azure Purview 

- Pour connecter un compte Azure Purview à un espace de travail Synapse, vous devez disposer d’un rôle **Contributeur** dans l’espace de travail Synapse provenant de la gestion des identités et des accès (IAM) du portail Azure et d’un accès à ce compte Azure Purview. Pour plus d’informations, consultez [Autorisations Azure Purview](../../purview/catalog-permissions.md).

## <a name="connect-an-azure-purview-account"></a>Se connecter à un compte Azure Purview  

- Dans l’espace de travail Synapse, accédez à **Gérer** -> **Azure Purview**. Sélectionnez **Se connecter à un compte Purview**. 
- Vous pouvez choisir **Depuis un abonnement Azure** ou **Entrer manuellement**. **Depuis un abonnement Azure**, vous pouvez sélectionner le compte auquel vous avez accès. 
- Une fois connecté, vous devez être en mesure de voir le nom du compte Purview sous l’onglet **Compte Azure Purview**. 
- Vous pouvez utiliser la barre de recherche située en haut au centre de l’espace de travail Synapse pour rechercher des données. 

## <a name="nextsteps"></a>Étapes suivantes 

[Inscrire et analyser des ressources Azure Synapse dans Azure Purview](../../purview/register-scan-azure-synapse-analytics.md)

[Découvrir, connecter et explorer des données dans Synapse en utilisant Azure Purview](how-to-discover-connect-analyze-azure-purview.md)   
