---
title: 'Recherche personnalisée Bing : Rechercher une vue personnalisée | Microsoft Docs'
description: Décrit comment rechercher une vue personnalisée du web
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 75f6c8d299c7eed901dda0631fca74b040f72e30
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368821"
---
# <a name="call-your-custom-search"></a>Appeler votre recherche personnalisée
Avant d’effectuer votre premier appel à l’API Recherche personnalisée pour obtenir les résultats de recherche de votre instance, vous devez obtenir une clé d’abonnement Cognitive Services. Pour obtenir une clé pour l’API Recherche personnalisée, consultez [Essayer Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).

> [!NOTE]
> Les clients actuels de la Recherche personnalisée Bing qui ont provisionné une clé en préversion avant le 15 octobre 2017 (inclus) peuvent l’utiliser jusqu’au 30 novembre 2017 ou jusqu’à l’expiration du nombre maximal de requêtes autorisées. Après cette date, ils doivent migrer vers la version mise à la disposition générale sur Azure.

## <a name="try-it-out"></a>Faites un essai
Une fois que vous avez configuré votre expérience de recherche personnalisée, vous pouvez tester sa configuration depuis le portail de la Recherche personnalisée. Connectez-vous à [Recherche personnalisée](https://customsearch.ai), cliquez sur une instance de Recherche personnalisée, puis sur l’onglet **Production**. L’onglet **Endpoints** (Points de terminaison) s’affiche. Votre abonnement détermine quels points de terminaison sont disponibles pour l’essai, consultez les[Tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/). Pour tester un point de terminaison, sélectionnez-le dans la liste déroulante et définissez les options de configuration associées. 

Voici les options disponibles.

- **Query** (Requête) : le terme de recherche à rechercher. Disponible uniquement pour les points de terminaison web, Image et Suggestion automatique.
- **Custom Configuration ID** (ID de configuration personnalisée) : l’ID de configuration de l’instance Recherche personnalisée sélectionnée. Ce champ est en lecture seule.
- **Market** (Marché) : le marché d’où proviennent les résultats. Disponible uniquement pour les points de terminaison web, Image et Interface utilisateur hébergée.
- **Subscription Key** (Clé d’abonnement) : la clé d’abonnement pour le test. Vous pouvez sélectionner une clé dans la liste déroulante ou en entrer une manuellement.
- **Safe Search** (Recherche sécurisée) : un filtre servant à exclure le contenu pour adultes des pages web. Disponible uniquement pour les points de terminaison web, Image et Interface utilisateur hébergée.
- **Count** (Nombre de) : le nombre de résultats de recherche à retourner dans la réponse. Disponible uniquement pour les points de terminaison Web et Image.
- **Offset** (Offset) : le nombre de résultats de recherche à retourner dans la réponse. Disponible uniquement pour les points de terminaison Web et Image.

Après avoir spécifié toutes les options nécessaires à Web, Image ou Suggestion automatique, cliquez sur **Call** (Appeler) pour afficher la réponse JSON dans le volet droit. 

Si vous sélectionnez le point de terminaison IU hébergée, vous pouvez tester l’expérience de recherche dans le volet droit.

## <a name="next-steps"></a>Étapes suivantes
- [Appeler votre vue personnalisée avec C#](./call-endpoint-csharp.md)
- [Appeler votre vue personnalisée avec Java](./call-endpoint-java.md)
- [Appeler votre vue personnalisée avec NodeJs](./call-endpoint-nodejs.md)
- [Appeler votre vue personnalisée avec Python](./call-endpoint-python.md)