---
title: 'Tutoriel JavaScript : Déployer un site web avec fonctionnalité de recherche'
titleSuffix: Azure Cognitive Search
description: Déployez un site web avec fonctionnalité de recherche sur l’application web statique Azure.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: a49ede283899cec42898672f5a376221265dea10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104723467"
---
# <a name="3---deploy-the-search-enabled-website"></a>3 - Déployer le site web avec fonctionnalité de recherche

Déployez le site web avec fonctionnalité de recherche en tant qu’application web statique Azure. Ce déploiement comprend l’application React et l’application Azure Function.  

L’application web statique extrait les informations et les fichiers en vue du déploiement à partir de GitHub à l’aide de votre duplication (fork) du référentiel d’exemple.  

## <a name="create-a-static-web-app-in-visual-studio-code"></a>Créer une application web statique dans Visual Studio Code

1. Sélectionnez **Azure** dans la barre d’activités, puis sélectionnez **Applications web statiques** dans la barre latérale. 
1. Cliquez avec le bouton droit sur le nom de l’abonnement, puis sélectionnez **Créer une application web statique (avancé)** .    

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-create-static-web-app-resource-advanced.png" alt-text="Cliquez avec le bouton droit sur le nom de l’abonnement, puis sélectionnez **Créer une application web statique (avancé)**.":::

1. Suivez les invites pour fournir les informations suivantes :

    |Prompt|Entrez|
    |--|--|
    |Comment voulez-vous créer une application web statique ?|Utiliser le référentiel GitHub existant|
    |Choisir une organisation|Sélectionnez votre _propre_ alias GitHub en tant qu’organisation.|
    |Choisir un référentiel|Sélectionnez **azure-search-javascript-samples** dans la liste. |
    |Choisir une branche pour le référentiel|Dans la liste, sélectionnez **master**. |
    |Entrez le nom de la nouvelle application web statique.|Créez un nom unique pour votre ressource. Vous pouvez ajouter votre nom au nom du référentiel, par exemple, `joansmith-azure-search-javascript-samples`. |
    |Sélectionnez un groupe de ressources pour les nouvelles ressources.|Utilisez le groupe de ressources que vous avez créé pour ce tutoriel.|
    |Choisissez Générer une présélection pour configurer la structure de projet par défaut.|Sélectionnez **Personnalisée**.|
    |Sélectionnez l’emplacement du code de votre application.|`search-website`|
    |Sélectionnez l’emplacement de votre code Azure Function.|`search-website/api`|
    |Entrez le chemin d’accès de la sortie générée...|build|
    |Sélectionnez un emplacement pour les nouvelles ressources.|Sélectionnez une région proche de chez vous.|

1. La ressource est créée. Sélectionnez **Actions d’ouverture dans GitHub** dans les notifications. Cela ouvre une fenêtre de navigateur pointée vers votre référentiel dupliqué. 

    La liste d’actions indique que votre application web (à la fois le client et les fonctions) a été correctement envoyée à votre application web statique Azure. 

    Attendez que les opérations de génération et de déploiement soient terminées avant de poursuivre. Ceci peut prendre une à deux minutes.

## <a name="get-cognitive-search-query-key-in-visual-studio-code"></a>Obtenir la clé de requête Recherche cognitive dans Visual Studio Code

1. Dans Visual Studio Code, ouvrez la [barre d’activités](https://code.visualstudio.com/docs/getstarted/userinterface) et sélectionnez l’icône Azure. 

1. Dans la barre latérale, sélectionnez votre abonnement Azure dans la zone **Azure : Recherche cognitive**, cliquez avec le bouton droit sur votre ressource de recherche, puis sélectionnez **Copier la clé de requête**. 

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-query-key.png" alt-text="Dans la barre latérale, sélectionnez votre abonnement Azure dans la zone **Azure : Recherche cognitive**, cliquez avec le bouton droit sur votre ressource de recherche, puis sélectionnez **Copier la clé de requête**.":::

1. Conservez cette clé de requête, vous devrez l’utiliser dans la section suivante. La clé de requête est capable d’interroger votre index. 

## <a name="add-configuration-settings-in-visual-studio-code"></a>Ajouter des paramètres de configuration dans Visual Studio Code

L’application Azure Function ne retourne pas de données de recherche tant que les secrets de recherche ne figurent pas dans les paramètres. 

1. Sélectionnez **Azure** dans la barre d’activités, puis sélectionnez **Applications web statiques** dans la barre latérale. 
1. Développez votre nouvelle application web statique jusqu’à ce que les **Paramètres d’application** s’affichent.
1. Cliquez avec le bouton droit sur **Paramètres de l’application**, puis sélectionnez **Ajouter un nouveau paramètre**.

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-static-web-app-configure-settings.png" alt-text="Cliquez avec le bouton droit sur **Paramètres de l’application**, puis sélectionnez **Ajouter un nouveau paramètre**.":::

1. Ajoutez les paramètres suivants :

    |Paramètre|Valeur de votre ressource de recherche|
    |--|--|
    |SearchApiKey|Clé de votre requête de recherche|
    |SearchServiceName|Nom de votre ressource de recherche|
    |SearchIndexName|`good-books`|
    |SearchFacets|`authors*,language_code`|

## <a name="use-search-in-your-static-web-app"></a>Utiliser la recherche dans votre application web statique

1. Dans Visual Studio Code, ouvrez la [barre d’activités](https://code.visualstudio.com/docs/getstarted/userinterface) et sélectionnez l’icône Azure.
1. Dans la barre latérale, **cliquez avec le bouton droit sur votre abonnement Azure** sous la zone `Static web apps` et recherchez l’application web statique que vous avez créée pour ce tutoriel.
1. Cliquez avec le bouton droit sur le nom de l’application web statique et sélectionnez **Parcourir le site**.
    
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-browse-static-web-app.png" alt-text="Cliquez avec le bouton droit sur le nom de l’application web statique et sélectionnez **Parcourir le site**.":::    

1. Sélectionnez **Ouvrir** dans la boîte de dialogue de la fenêtre contextuelle.
1. Dans la barre de recherche du site web, entrez une requête de recherche telle que `code` _lentement_ pour que la fonctionnalité de suggestion suggère des titres de livres. Sélectionnez une suggestion ou continuez à entrer votre requête. Appuyez sur Entrée une fois votre requête de recherche saisie. 
1. Passez en revue les résultats, puis sélectionnez l’un des livres pour obtenir plus de détails. 

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer les ressources créées dans ce tutoriel, supprimez le groupe de ressources.

1. Dans Visual Studio Code, ouvrez la [barre d’activités](https://code.visualstudio.com/docs/getstarted/userinterface) et sélectionnez l’icône Azure. 

1. Dans la barre latérale, **cliquez avec le bouton droit sur votre abonnement Azure** sous la zone `Resource Groups` et recherchez le groupe de ressources que vous avez créé pour ce tutoriel.
1. Cliquez avec le bouton droit sur le nom du groupe de ressources, puis sélectionnez **Supprimer**.
    Cela supprime à la fois les ressources de la recherche et de l’application web statique.
1. Si vous ne souhaitez plus utiliser la duplication (fork) GitHub de l’exemple, n’oubliez pas de la supprimer sur GitHub. Accédez aux **Paramètres** de votre duplication, puis supprimez-la. 


## <a name="next-steps"></a>Étapes suivantes

* [Comprendre l’intégration de la recherche pour le site web avec fonctionnalité de recherche](tutorial-javascript-search-query-integration.md)
