---
title: Les relations d’ancrage et recherche moyen dans ancres Spatial Azure | Microsoft Docs
description: En savoir plus sur le modèle conceptuel derrière les relations d’ancrage. Apprenez à connecter les points d’ancrage dans un espace et pour utiliser l’API à proximité pour répondre à un scénario de recherche de façon.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 619cd051eccce3434469ae909f69496a254d0d9a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565126"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Les relations d’ancrage et recherche moyen dans Azure des ancres Spatial

En utilisant les relations d’ancrage, vous pouvez créer des ancres connectées dans un espace et puis poser des questions comme celles-ci :

* Existe-t-il des points d’ancrage de proximité ?
* L’éloignement sont-ils ?

## <a name="examples"></a>Exemples

Vous pouvez utiliser des ancres connectés dans ce cas :

* Un employé a besoin effectuer une tâche qui implique la visite de différents emplacements dans une fabrique industrielle. La fabrique a ancres spatiales à chaque emplacement. Une application mobile ou HoloLens permet de guider le processus de travail à partir d’un emplacement à l’autre. L’application demande tout d’abord les ancres spatiales à proximité et guide ensuite le processus de travail à l’emplacement suivant. Visuellement, l’application affiche la direction générale et la distance à l’emplacement suivant.

* Musée crée des ancres spatiales à affiche publique. Ensemble, ces points d’ancrage forment une visite guidée d’une heure d’affichages publics essentielles du musée. À un affichage public, les visiteurs peuvent ouvrir application de réalité mixte du musée sur son appareil mobile. Puis, ils pointent leur appareil photo du téléphone autour de l’espace pour afficher la direction générale et la distance pour les autres affichages publics sur la visite guidée. Comme un utilisateur parcourt vers un affichage public, l’application met à jour la direction générale et la distance de guider l’utilisateur.

## <a name="set-up-way-finding"></a>Configurer la recherche de façon

À l’aide d’une application qui utilise la direction de ligne de vue et la distance entre les points d’ancrage de fournir des conseils *recherche de façon*. Recherche de façon est différent de navigation de l’étape par étape. Dans le volet de navigation étape par étape, les utilisateurs sont dirigés autour des murs, par le biais des portes et entre les étages. Avec la recherche de façon, l’utilisateur obtient les indicateurs sur la direction générale de la destination. Mais d’inférence ou de base de connaissances de l’espace également aide l’utilisateur à naviguer dans la structure vers la destination.

Pour créer une expérience de recherche de façon, tout d’abord préparer un espace pour une expérience et développer une application qui interagiront avec les utilisateurs. Il s’agit de la procédure conceptuelle :

1. **Planifier l’espace de**: Décidez quels emplacements au sein de l’espace destiné à être membre de l’expérience de recherche de façon. Dans notre cas, le superviseur de l’usine ou le coordinateur de la visite guidée musée pouvez décider des emplacements à inclure dans l’expérience de recherche de façon.
2. **Connecter des ancres**: Visitez les emplacements choisis pour créer des ancres spatiales. Vous pouvez faire dans un mode d’administration de l’application de l’utilisateur final ou dans une autre application entièrement. Vous allez vous connecter ou se rapportent chaque point d’ancrage pour les autres. Le service gère ces relations.
3. **Démarrer l’expérience utilisateur final**: Les utilisateurs exécutent l’application pour rechercher un point d’ancrage, ce qui peut être dans un des emplacements choisis. Votre conception globale doit déterminer les emplacements où les utilisateurs peuvent entrer l’expérience.
4. **Rechercher à proximité des ancres**: Une fois que l’utilisateur trouve un point d’ancrage, l’application peut demander à proximité des ancres. Cette procédure retourne une pose entre l’appareil et ces points d’ancrage.
5. **Guide de l’utilisateur**: L’application peut utiliser la pose à chacun de ces points d’ancrage à donner des conseils sur la direction générale de l’utilisateur et à distance. Par exemple, l’appareil photo de flux dans l’application peut afficher une icône et une flèche pour représenter chaque destination potentielle, comme le montre l’image suivante.
6. **Affiner les conseils**: Lorsque l’utilisateur, l’application peut calculer régulièrement une nouvelle pose entre l’appareil et le point d’ancrage de destination. L’application continue à affiner les indicateurs des conseils qui permettent à l’utilisateur arrivent à destination.

    ![Un exemple d’une application peut afficher des conseils de recherche de façon](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Connecter les points d’ancrage

Pour créer une expérience de recherche de façon, vous devez d’abord placer les ancres dans les emplacements choisis. Dans cette section, nous supposerons qu’administrateur de l’application est déjà finie ce travail.

### <a name="connect-anchors-in-a-single-session"></a>Connecter des points d’ancrage dans une seule session

Pour connecter des ancres :

1. Remonter au premier emplacement et créer l’ancre A à l’aide d’une CloudSpatialAnchorSession.
2. Guide pour le deuxième emplacement. La plateforme MR/AR sous-jacente effectue le suivi du mouvement.
3. Créer d’ancrage B à l’aide de la même CloudSpatialAnchorSession. Ancres A et B sont maintenant connectés. Le service d’ancres Spatial maintient cette relation.
4. Continuer la procédure pour les ancres restantes.

### <a name="connect-anchors-in-multiple-sessions"></a>Connecter des ancres dans plusieurs sessions

Vous pouvez connecter des ancres spatiales via plusieurs sessions. À l’aide de cette méthode, vous pouvez créer et connecter certains points d’ancrage en même temps et puis plus tard créer et connecter plusieurs points d’ancrage. 

Pour connecter les points d’ancrage sur plusieurs sessions :

1. L’application crée des points d’ancrage dans un CloudSpatialAnchorSession. 
2. À un autre moment, l’application localise un de ces points d’ancrage (par exemple, d’ancrage A) à l’aide d’une nouveau CloudSpatialAnchorSession.
3. Remonter vers un nouvel emplacement. La plateforme sous-jacente de réalité mixte ou réalité augmentée effectue le suivi du mouvement.
4. Créer d’ancrage C à l’aide de la même CloudSpatialAnchorSession. Ancres A, B et C sont maintenant connectés. Le service d’ancres Spatial maintient cette relation.

Vous pouvez continuer cette procédure pour plusieurs points d’ancrage et plus de sessions au fil du temps.

### <a name="verify-anchor-connections"></a>Vérifier les connexions de point d’ancrage

L’application peut vérifier que les deux points d’ancrage sont connectés en émettant une requête d’ancrage à proximité. Lorsque les résultats de la requête contient le point d’ancrage cible, la connexion de point d’ancrage est vérifiée. Si les points d’ancrage ne sont pas connectés, l’application peut essayer afin de les connecter à nouveau. 

Voici quelques raisons pourquoi les ancres peuvent échouer pour se connecter :

* La réalité mixte ou réalité augmentée plateforme sous-jacente perdu suivi pendant le processus de connexion des points d’ancrage.
* En raison d’une erreur réseau pendant la communication avec le service ancres spatiales, la connexion de point d’ancrage n’a pas pu être persistante.

### <a name="find-sample-code"></a>Exemple de code

Pour obtenir un exemple de code qui montre comment connecter les points d’ancrage et effectuer des requêtes de proximité, consultez [ancres Spatial exemples d’applications](https://github.com/Azure/azure-spatial-anchors-samples).
