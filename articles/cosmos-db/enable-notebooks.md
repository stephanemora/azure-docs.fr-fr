---
title: Activer les notebooks dans un compte Azure Cosmos DB (préversion)
description: Les notebooks intégrés d’Azure Cosmos DB vous permettent d’analyser et de visualiser vos données sur le portail. Cet article explique comment activer cette fonctionnalité pour des comptes Cosmos.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: 8c65b7d010e521ec78322de9807f741c856a2381
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264846"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts"></a>Activer les notebooks pour un compte Azure Cosmos

> [!IMPORTANT]
> Les notebooks intégrés pour Azure Cosmos DB sont actuellement disponibles dans les régions Azure suivantes : Australie Est, USA Est, USA Est 2, Europe Nord, USA Centre Sud, Asie Sud-Est, Royaume-Uni Sud, Europe Ouest et USA Ouest 2. Pour utiliser des notebooks, [créez un compte avec des notebooks](#enable-notebooks-in-a-new-cosmos-account) ou [activez les notebooks sur un compte existant](#enable-notebooks-in-an-existing-cosmos-account) dans l’une de ces régions.

Les notebooks Jupyter intégrés dans Azure Cosmos DB vous permettent d’analyser et de visualiser vos données via le portail Azure. Cet article décrit comment activer cette fonctionnalité pour votre compte Azure Cosmos DB.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Activer les notebooks dans un nouveau compte Azure Cosmos
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Sélectionnez **Créer une ressource** > **Bases de données** > **Azure Cosmos DB**.
1. Dans la page **Créer un compte Azure Cosmos DB**, sélectionnez **Notebooks**. 
 
    ![Sélectionner l’option Notebooks dans le panneau créer d’Azure Cosmos DB](media/enable-notebooks/create-new-account-with-notebooks.png)
1. Sélectionnez **Revoir + créer**. Vous pouvez ignorer les options **Réseau** et **Balises**. 
1. Passez en revue les paramètres du compte, puis sélectionnez **Créer**. La création du compte prend quelques minutes. Attendez que la page du portail affiche **Votre déploiement est terminé**. 

    ![Volet Notifications du portail Azure](media/enable-notebooks/create-new-account-with-notebooks-complete.png)
1. Sélectionnez **Accéder à la ressource** pour accéder à la page du compte Azure Cosmos DB. 

    ![Page du compte Azure Cosmos DB](../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png)

1. Accédez au volet **Explorateur de données**. Vous devez maintenant voir votre espace de travail Notebooks.

    ![Nouvel espace de travail Notebooks d’Azure Cosmos DB](media/enable-notebooks/new-notebooks-workspace.png)

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Activer les notebooks dans un compte Cosmos
Vous pouvez également activer les notebooks sur des comptes existants. Cette opération ne doit être effectuée qu’une seule fois par compte.

1. Accédez au volet **Explorateur de données** de votre compte Cosmos.
1. Sélectionnez **Activer les notebooks**.

    ![Créer un espace de travail Notebooks dans l’Explorateur de données](media/enable-notebooks/enable-notebooks-workspace.png)
1. Vous êtes invité à créer un espace de travail Notebooks. Sélectionnez **Terminer la configuration**.
1. Votre compte est désormais activé pour l’utilisation des notebooks.

## <a name="create-and-run-your-first-notebook"></a>Créer et exécuter votre premier notebook

Pour vérifier que vous pouvez utiliser les notebooks, sélectionnez l’un d’eux sous Exemples de notebooks. Cela a pour effet d’enregistrer une copie du notebook dans votre espace de travail et de l’ouvrir.

Dans cet exemple, nous allons utiliser **gettingstarted.ipynb**. 

![Afficher le notebook GettingStarted.ipynb](media/enable-notebooks/select-getting-started-notebook.png)

Pour exécuter le notebook :
1. Sélectionnez la première cellule de code contenant du code Python. 
1. Sélectionnez **Exécuter** pour exécuter la cellule. Dans la cellule, appuyez sur **Maj+Entrée** pour exécuter la cellule.
1. Actualisez le volet des ressources pour voir la base de données et le conteneur créés.

    ![Exécuter le notebook de prise en main](media/enable-notebooks/run-first-notebook-cell.png)

Vous pouvez également sélectionner **Nouveau notebook** pour créer un notebook ou charger un fichier de notebook (.ipynb) existant en sélectionnant **Charger le fichier** dans le menu **Mes notebooks**. 

![Créer ou charger un nouveau notebook](media/enable-notebooks/create-or-upload-new-notebook.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les avantages de [notebooks Jupyter d’Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
