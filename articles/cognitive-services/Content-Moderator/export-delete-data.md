---
title: Exporter ou supprimer vos données dans Content Moderator - Azure Cognitive Services | Microsoft Docs
description: Découvrez comment exporter ou supprimer vos données dans Content Moderator.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 05/25/2018
ms.author: v-jaswel
ms.openlocfilehash: fb496837560fe28f1a2e53d8c4ca67e23ada8f64
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576797"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Exporter ou supprimer vos données dans Content Moderator

Content Moderator collecte des données utilisateur pour faire fonctionner le service, mais les clients contrôlent totalement l’affichage, l’exportation et la suppression des données à l’aide de la [l’interface utilisateur de révision](http://contentmoderator.cognitive.microsoft.com/) et des [API](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Pour plus d’informations sur la façon d’exporter et supprimer des données utilisateur dans Content Moderator, voir le tableau suivant.

| Données | Opération d’exportation | Opération de suppression |
| ---- | ---------------- | ---------------- |
| Informations sur le compte (Clés d’abonnement) | N/A | Supprimer via le portail Azure (Abonnements Azure). Vous pouvez également utiliser le bouton **Supprimer l’équipe** dans la page des paramètres d’équipe de [l’interface utilisateur de révision](http://contentmoderator.cognitive.microsoft.com/). |
| Images pour la mise en correspondance personnalisée | [Obtenir les ID d’image](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). Les images sont stockées dans un format de hachage propriétaire unidirectionnel, et il n’existe aucun moyen d’extraire les images réelles. | [Supprimer toutes les images](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Vous pouvez également supprimer la ressource Content Moderator via le portail Azure. |
| Conditions pour la mise en correspondance personnalisée | [Obtenir toutes les conditions](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | [Supprimer toutes les conditions](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Vous pouvez également supprimer la ressource Content Moderator via le portail Azure. |
| Balises | N/A | Utilisez l’icône **Supprimer** disponible pour chaque balise dans la page des paramètres de balise de l’interface utilisateur de révision. Vous pouvez également utiliser le bouton **Supprimer l’équipe** dans la page des paramètres d’équipe de [l’interface utilisateur de révision](http://contentmoderator.cognitive.microsoft.com/). |
| Révisions | [Obtenir la révision](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Utiliser le bouton **Supprimer l’équipe** dans la page des paramètres d’équipe de [l’interface utilisateur de révision](http://contentmoderator.cognitive.microsoft.com/).
| Utilisateurs | N/A | Utiliser l’icône **Supprimer** disponible pour chaque utilisateur dans la page des paramètres d’équipe de l’[interface utilisateur de révision](http://contentmoderator.cognitive.microsoft.com/). Vous pouvez également utiliser le bouton **Supprimer l’équipe** dans la page des paramètres d’équipe de [l’interface utilisateur de révision](http://contentmoderator.cognitive.microsoft.com/). |

