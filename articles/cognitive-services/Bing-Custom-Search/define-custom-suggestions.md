---
title: 'Recherche personnalisée Bing : Définir les suggestions personnalisées de la Suggestion automatique | Microsoft Docs'
description: Décrit comment configurer la Suggestion automatique avec des suggestions personnalisées
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: a41b4e5b6c268ec68488c6764d4192cf8d2345a4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368820"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Configurer votre expérience de Suggestion automatique personnalisée
Si vous êtes abonné au niveau approprié de la Recherche personnalisée (voir les [Tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), vous pouvez personnaliser les suggestions de recherche élaborées dans votre expérience de Recherche personnalisée. La Suggestion automatique personnalisée retourne une liste de requêtes suggérées qui s’appuient sur une chaîne de requête partielle fournie par l’utilisateur. Avec la Suggestion automatique personnalisée, vous fournissez des suggestions de recherche personnalisées pertinentes par rapport à votre expérience de recherche. Vous spécifiez s’il faut retourner uniquement des suggestions personnalisées ou inclure également des suggestions Bing. Si vous incluez les suggestions Bing, les suggestions personnalisées apparaissent avant celles-ci. Les suggestions Bing sont limitées au contexte de votre instance de Recherche personnalisée.

## <a name="configure-custom-autosuggest"></a>Configurer la Suggestion automatique personnalisée
Utilisez les instructions suivantes pour configurer la Suggestion automatique personnalisée de votre instance de Recherche personnalisée.

1.  Connectez-vous à [Recherche personnalisée](https://customsearch.ai).
2.  Cliquez sur une instance de Recherche personnalisée. Pour créer une instance, consultez [Créer votre première instance de Recherche personnalisée Bing](quick-start.md).
3.  Cliquez sur l’onglet **Autosuggest** (Suggestion automatique).

## <a name="enable-bing-suggestions"></a>Activer les suggestions Bing
Pour activer les suggestions Bing, placez le curseur **Automatic Bing suggestions** (Suggestions automatiques Bing) sur la position d’activation. Le curseur devient bleu.

## <a name="add-suggestions"></a>Ajouter des suggestions
Pour ajouter une suggestion, entrez-la dans la zone de texte. Appuyez sur la touche Entrée ou cliquez sur l’icône **+**. Les suggestions personnalisées peuvent se faire dans n’importe quelle langue et s’affichent avant les suggestions Bing.

## <a name="upload-suggestions"></a>Charger des suggestions
Vous pouvez charger une liste de suggestions à partir d’un fichier. Placez chaque suggestion sur une ligne distincte. Cliquez sur l’icône de chargement et sélectionnez votre fichier.

## <a name="remove-suggestions"></a>Supprimer des suggestions
Pour supprimer une suggestion, cliquez sur l’icône de suppression en regard de la suggestion à éliminer.

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

  >[!NOTE]  
  >Les modifications de configuration de la Suggestion automatique personnalisée peuvent demander jusqu’à 24 heures pour prendre effet.

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir des suggestions personnalisées](./get-custom-suggestions.md)
- [Rechercher votre instance personnalisée](./search-your-custom-view.md)
- [Configurer et consommer l’interface utilisateur hébergée personnalisée](./hosted-ui.md)