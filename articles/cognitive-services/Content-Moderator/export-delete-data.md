---
title: Exporter ou supprimer vos données - Content Moderator
titlesuffix: Azure Cognitive Services
description: Découvrez comment exporter ou supprimer vos données dans Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 15a59bbdc4c93202f8906689100c24ba713ee487
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311254"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Exporter ou supprimer vos données dans Content Moderator

Content Moderator collecte des données utilisateur pour faire fonctionner le service, mais les clients contrôle totalement l’affichage, l’exportation et la suppression des données à l’aide de [l’outil Révision](https://contentmoderator.cognitive.microsoft.com/) et des [API Modération et Révision](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Pour plus d’informations sur la façon d’exporter et supprimer des données utilisateur dans Content Moderator, voir le tableau suivant.

| Données | Opération d’exportation | Opération de suppression |
| ---- | ---------------- | ---------------- |
| Informations sur le compte (Clés d’abonnement) | N/A | Supprimer via le portail Azure (Abonnements Azure). Vous pouvez également utiliser le bouton **Supprimer l’équipe** dans la page des paramètres d’équipe de [l’interface utilisateur de révision](https://contentmoderator.cognitive.microsoft.com/). |
| Images pour la mise en correspondance personnalisée | Appelez [l’API Obtenir les ID d’image](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). Les images sont stockées dans un format de hachage propriétaire unidirectionnel, et il n’existe aucun moyen d’extraire les images réelles. | Appelez [l’API Supprimer toutes les images](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Vous pouvez également supprimer la ressource Content Moderator via le portail Azure. |
| Conditions pour la mise en correspondance personnalisée | Appelez [l’API Obtenir toutes les conditions](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e). | Appelez [l’API Supprimer toutes les conditions](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Vous pouvez également supprimer la ressource Content Moderator via le portail Azure. |
| Balises | N/A | Utilisez l’icône **Supprimer** disponible pour chaque balise dans la page des paramètres de balise de l’interface utilisateur de révision. Vous pouvez également utiliser le bouton **Supprimer l’équipe** dans la page des paramètres d’équipe de [l’interface utilisateur de révision](https://contentmoderator.cognitive.microsoft.com/). |
| Révisions | Appelez [l’API Obtenir la révision](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2). | Utiliser le bouton **Supprimer l’équipe** dans la page des paramètres d’équipe de [l’interface utilisateur de révision](https://contentmoderator.cognitive.microsoft.com/).
| Utilisateurs | N/A | Utiliser l’icône **Supprimer** disponible pour chaque utilisateur dans la page des paramètres d’équipe de l’[interface utilisateur de révision](https://contentmoderator.cognitive.microsoft.com/). Vous pouvez également utiliser le bouton **Supprimer l’équipe** dans la page des paramètres d’équipe de [l’interface utilisateur de révision](https://contentmoderator.cognitive.microsoft.com/). |

