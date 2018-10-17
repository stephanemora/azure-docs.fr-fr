---
title: 'Démarrage rapide : Créer votre première instance Recherche personnalisée Bing'
titlesuffix: Azure Cognitive Services
description: Pour utiliser la Recherche personnalisée Bing, vous devez créer une instance de recherche personnalisée qui définit votre vue, ou section du web. L’instance contient les paramètres qui spécifient les domaines, sous-sites et pages web publics dans lesquels Bing doit effectuer les recherches, ainsi que les ajustements de classement.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2017
ms.author: v-brapel
ms.openlocfilehash: 866d32aa4de45076fcbc4e413d8c2e67d5346878
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816223"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Démarrage rapide : Créer votre première instance Recherche personnalisée Bing
Pour utiliser la Recherche personnalisée Bing, vous devez créer une instance de recherche personnalisée qui définit votre vue, ou section du web. L’instance contient les paramètres qui spécifient les domaines, sites web et pages web publics dans lesquels Bing doit effectuer les recherches, ainsi que les ajustements de classement. Pour créer l’instance, utilisez le [portail](https://customsearch.ai) de la Recherche personnalisée Bing. 

## <a name="create-a-custom-search-instance"></a>Création d’une instance de recherche personnalisée

Pour créer une instance de Recherche personnalisée Bing :

1.  Obtenez une clé pour l’API Recherche personnalisée. Consultez [Essayer Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
2.  Cliquez sur le bouton **Se connecter** et connectez-vous au portail en utilisant un compte Microsoft. 
    - Si vous n’avez pas de compte Microsoft, cliquez sur **Create a Microsoft account** (Créer un compte Microsoft). Des autorisations nécessaires au portail pour accéder à vos données vous sont demandées. Cliquez sur **Oui**.
    - Acceptez les conditions de Cognitive Services. Cochez la case **I agree** (J’accepte) et cliquez sur **Agree** (Accepter).  
3.  Une fois connecté, cliquez sur **New Instance** (Nouvelle instance) et nommez l’instance. Utilisez un nom explicite qui décrit le type de contenu retourné par la recherche. Vous pouvez modifier le nom à tout moment. 
4.  Sous l’onglet **Active** (Éléments actifs) sous **Search Experience** (Expérience de recherche), entrez l’URL d’un ou de plusieurs sites web à inclure dans votre recherche.
5.  Pour vérifier que votre instance retourne des résultats, entrez une requête dans le volet de visualisation situé à droite. Si vous n’obtenez aucun résultat, spécifiez un nouveau site web. Bing retourne des résultats uniquement pour les sites web publics qu’il a indexés.
6.  Cliquez sur **Publish** (Publier) pour publier les modifications de configuration apportées à la production. Lorsque vous y êtes invité, cliquez sur **Publish** (Publier) pour confirmer.
7.  Cliquez sur **Production**(Production) > **Endpoints** (Points de terminaison) et copiez l’ID **Custom Configuration ID** (ID de configuration personnalisée). Vous avez besoin de cet identificateur pour appeler l’API Recherche personnalisée.

## <a name="next-steps"></a>Étapes suivantes

Continuer à travailler avec l’instance de recherche personnalisée que vous venez de créer en suivant les instructions de ces guides pratiques :

- [Configurer votre expérience de recherche personnalisée](./define-your-custom-view.md)
- [Appeler votre recherche personnalisée](./search-your-custom-view.md)
- [Partager votre recherche personnalisée](./share-your-custom-search.md)
- [Configurer votre expérience d’interface utilisateur hébergée](./hosted-ui.md)
- [Utiliser des marqueurs d’ornement pour mettre en surbrillance du texte](./hit-highlighting.md)
- [Paginer des pages web](./page-webpages.md)
