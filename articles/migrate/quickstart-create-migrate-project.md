---
title: Démarrage rapide pour créer un projet Azure Migrate avec un modèle Azure Resource Manager.
description: Dans ce guide de démarrage rapide, vous allez apprendre à créer un projet Azure Migrate à l’aide d’un modèle ARM (Azure Resource Manager).
ms.date: 04/23/2021
author: rahulg1190
manager: bsiva
ms.author: rahugup
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 29bd42bedb9c7aa70769236d95339a510ae9dfe9
ms.sourcegitcommit: 0fd913b67ba3535b5085ba38831badc5a9e3b48f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113487486"
---
# <a name="quickstart-create-an-azure-migrate-project-using-an-arm-template"></a>Démarrage rapide : Créer un projet Azure Migrate à l’aide d’un modèle ARM

Ce guide de démarrage rapide explique comment configurer un projet Azure Migrate à l’aide d’un modèle ARM (Azure Resource Manager). Azure Migrate fournit un hub centralisé pour évaluer et migrer les serveurs, l’infrastructure, les applications et les données locales vers Azure. Azure Migrate prend en charge l’évaluation et la migration des machines virtuelles VMware, des machines virtuelles Hyper-V, des serveurs physiques, d’autres machines virtuelles virtualisées, des bases de données, des applications web et des bureaux virtuels de l’environnement local.

Ce modèle crée un projet Azure Migrate qui sera utilisé pour évaluer et migrer votre infrastructure, vos applications, vos données et vos serveurs locaux Azure.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.migrate%2Fmigrate-project-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure actif, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/migrate-project-create/).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.migrate/migrate-project-create/azuredeploy.json":::



## <a name="deploy-the-template"></a>Déployer le modèle

Pour déployer le modèle, vous devez définir les éléments suivants : **Abonnement**, **Groupe de ressources**, **Nom du projet** et **Emplacement**.

1. Pour vous connecter à Azure et ouvrir le modèle, sélectionnez l’image **Déployer sur Azure**.

   [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.migrate%2Fmigrate-project-create%2Fazuredeploy.json)

2. Sélectionnez ou entrez les valeurs suivantes :

   :::image type="content" source="media/quickstart-create-migrate-project-template/create-migrate-project.png" alt-text="Modèle pour la création d’un projet Azure Migrate":::.

   - **Abonnement**: Sélectionnez votre abonnement Azure.
   - **Groupe de ressources** : Sélectionnez un groupe existant ou sélectionnez **Créer** pour ajouter un groupe.
   - **Région** : Correspond par défaut à la localisation du groupe de ressources et devient indisponible dès qu’un groupe de ressources est sélectionné.
   - **Nom du projet de migration** : Spécifiez le nom du projet.
   - **Emplacement** : Sélectionnez l’emplacement où vous souhaitez déployer le projet Azure Migrate et ses ressources.

3. Cliquez sur le bouton **Vérifier + créer** pour démarrer le déploiement.

## <a name="validate-the-deployment"></a>Valider le déploiement

Pour vérifier que le projet Azure Migrate a été créé, utilisez le portail Azure.


1. Accédez à Azure Migrate en recherchant **Azure Migrate** dans la barre de recherche du portail Azure.
2. Cliquez sur le bouton **Découvrir,** **Évaluer** et **Migrer** sous la vignette Windows, Linux et SQL Server.
3. Sous **Abonnement Azure** et **Projet**, sélectionnez les valeurs spécifiées dans le déploiement.


## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un projet Azure Migrate. Pour en savoir plus sur Azure Migrate et ses fonctionnalités, passez à la vue d’ensemble d’Azure Migrate.

> [!div class="nextstepaction"]
> [Vue d’ensemble d’Azure Migrate](migrate-services-overview.md)
>
