---
title: Détection du contenu spécifique à un domaine - Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Concepts liés à la description d’images à l’aide de l’API Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: a9c71fa7e5d86cfeb4fe6fab44bbce241546ccb8
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342551"
---
# <a name="detecting-domain-specific-content"></a>Détection du contenu spécifique à un domaine

Outre le balisage et la catégorisation très précise, Vision par ordinateur prend également en charge les informations spécialisées (ou spécifiques à un domaine). Les informations spécialisées peuvent être implémentées en tant que méthode autonome ou à l’aide de la catégorisation précise. Elles permettent d’affiner la taxonomie des 86 catégories via l’ajout de modèles spécifiques aux domaines.

Deux options existent pour utiliser les modèles spécifiques à un domaine :

* Analyse élargie  
  Analysez uniquement un modèle choisi en lançant un appel HTTP POST. Si vous savez quel modèle vous souhaitez utiliser, spécifiez le nom du modèle. Vous obtiendrez uniquement des informations pertinentes pour ce modèle. Par exemple, vous pouvez utiliser cette option pour ne rechercher que la reconnaissance de célébrités. La réponse contient une liste de célébrités susceptibles de correspondre, accompagnées de scores de confiance.
* Analyse améliorée  
  Effectuez une analyse pour fournir des informations supplémentaires en lien avec les catégories de la taxonomie des 86 catégories. Cette option peut être utilisée dans les applications où les utilisateurs souhaitent obtenir une analyse d’image générique en plus des détails issus d’un ou de plusieurs modèles spécifique à un domaine. Lorsque cette méthode est appelée, le classifieur de la taxonomie des 86 catégories est appelé en premier. Si aucune des catégories ne correspond à celle des modèles connus ou correspondants, une deuxième session d’appels du classifieur est lancée. Par exemple, si le paramètre `details`de l’appel HTTP POST est défini sur « all » ou inclut « celebrities », la méthode appelle le classifieur de célébrités après l’appel au classifieur des 86 catégories. Si l’image est classée en tant que `people_` ou sous-catégorie de cette catégorie, le classifieur de célébrités est appelé.

## <a name="listing-domain-specific-models"></a>Liste des modèles spécifiques à un domaine

Vous pouvez répertorier les modèles spécifiques à un domaine et pris en charge par Vision par ordinateur. Actuellement, Vision par ordinateur prend en charge les modèles spécifiques à un domaine ci-après pour la détection de contenu spécifique à un domaine :

| NOM | Description |
|------|-------------|
| celebrities | Reconnaissance des célébrités, prise en charge pour les images classées dans la catégorie `people_` |
| landmarks | Reconnaissance des points de repère, prise en charge pour les images classées dans les catégories `outdoor_` ou `building_` |

### <a name="domain-model-list-example"></a>Exemple de liste de modèles de domaine

La réponse JSON suivante répertorie les modèles spécifiques à un domaine et pris en charge par Vision par ordinateur.

```json
{
    "models": [
        {
            "name": "celebrities",
            "categories": ["people_", "人_", "pessoas_", "gente_"]
        },
        {
            "name": "landmarks",
            "categories": ["outdoor_", "户外_", "屋外_", "aoarlivre_", "alairelibre_",
                "building_", "建筑_", "建物_", "edifício_"]
        }
    ]
}
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts liés à la [catégorisation des images](concept-categorizing-images.md).