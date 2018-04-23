---
title: Ajouter un exemple de modèle tabulaire pour votre serveur Azure Analysis Services | Microsoft Docs
description: Découvrez comment ajouter un exemple de modèle dans Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 27353ff8c05f44b76304279e09a8a8d817041d78
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-add-a-sample-model"></a>Didacticiel : Ajouter un exemple de modèle

Dans ce didacticiel, vous ajoutez un exemple de modèle Adventure Works à votre serveur. L’exemple de modèle est une version complète du tutoriel de modélisation des données Adventure Works Internet Sales (1200). Un exemple de modèle sert à tester la gestion des modèles, à se connecter à des outils et des applications clientes, ainsi qu’à interroger des données de modèle.

## <a name="before-you-begin"></a>Avant de commencer

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

- Un serveur Azure Analysis Services
- Des autorisations d’administrateur du serveur

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-a-sample-model"></a>Créer un exemple de modèle

1. Dans **Vue d’ensemble** du serveur, cliquez sur **Nouveau modèle**.

    ![Créer un exemple de modèle](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. Dans **Nouveau modèle** > **Choisir une source de données**, vérifiez que l’option **Exemple de données** est sélectionnée, puis cliquez sur **Ajouter**.

    ![Sélectionner un exemple de données](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. Dans **Vue d’ensemble**, vérifiez que l’exemple `adventureworks` est créé.

    ![Sélectionner un exemple de données](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Votre exemple de modèle utilise des ressources en mémoire cache. Si vous n’utilisez pas votre exemple de modèle à des fins de test, il est préférable de le supprimer de votre serveur.

> [!NOTE]
> Ces étapes décrivent comment supprimer un modèle d’un serveur à l’aide de SSMS. Vous pouvez également supprimer un modèle à l’aide de la fonctionnalité du concepteur web en préversion.

1. Dans SSMS > **Explorateur d’objets**, cliquez sur **Se connecter** > **Analysis Services**.

2. Dans **Se connecter au serveur**, collez le nom du serveur, puis dans **Authentification**, choisissez **Active Directory - Authentification universelle avec prise en charge de MFA**, entrez votre nom d’utilisateur, puis cliquez sur **Se connecter**.

    ![Se connecter](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. Dans l’**Explorateur d’objets**, cliquez avec le bouton droit sur l’exemple de base de données `adventureworks`, puis cliquez sur **Supprimer**.

    ![Supprimer l’exemple de base de données](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>Étapes suivantes 

[Se connecter à Power BI Desktop](analysis-services-connect-pbi.md)   
[Gérer les utilisateurs et rôles de bases de données](analysis-services-database-users.md)


