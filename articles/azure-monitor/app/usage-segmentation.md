---
title: Analyse des utilisateurs, des sessions et des événements dans Application Insights
description: Analyse démographique des utilisateurs de votre application web.
ms.topic: conceptual
author: mattmccleary
ms.author: mmcc
ms.date: 07/30/2021
ms.openlocfilehash: d3b6bac65cea13de078d91a6936aa8618d9f7423
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132661"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Analyse des utilisateurs, des sessions et des événements dans Application Insights

Découvrez quand des personnes utilisent votre application web, les pages qui les intéressent le plus, où vos utilisateurs se trouvent, ainsi que les navigateurs et les systèmes d’exploitation qu’ils utilisent. Analysez la télémétrie d’utilisation et d’activité à l’aide d’[Application Insights](./app-insights-overview.md).

:::image type="content" source="./media/usage-segmentation/users.png" alt-text="La capture d’écran montre l’onglet Utilisateurs avec un graphique en aires." lightbox="./media/usage-overview/users.png":::

## <a name="get-started"></a>Bien démarrer

Si aucune donnée n’apparaît dans les panneaux des utilisateurs, des sessions ou des événements du portail Application Insights, [découvrez comment prendre en main les outils d’utilisation](usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>L’outil de segmentation Utilisateurs, Sessions et Événements

Trois des panneaux d’utilisation exploitent le même outil pour segmenter et traiter les données de télémétrie fournies par votre application web selon trois perspectives. En filtrant et en segmentant les données, vous pouvez découvrir des informations sur l’utilisation relative des différentes fonctionnalités et pages.

* **Outil Utilisateurs** : nombre de personnes ayant utilisé votre application et ses fonctionnalités.  Les utilisateurs sont comptabilisés à l’aide des ID anonymes stockés dans les cookies du navigateur. Une seule personne utilisant plusieurs navigateurs ou ordinateurs est comptabilisée comme plusieurs utilisateurs.
* **Outil Sessions** : nombre de sessions d’activité utilisateur ayant inclus certaines pages et fonctionnalités de votre application. Une session est comptabilisée après une demi-heure d’inactivité de l’utilisateur ou après 24 heures d’utilisation continue.
* **Outil Événements** : fréquence à laquelle certaines pages et fonctionnalités de votre application sont utilisées. L’affichage d’une page est comptabilisé lorsqu’un navigateur charge la page à partir de votre application, à condition que vous l’ayez [instrumentée](./javascript.md). 

    Un événement personnalisé représente une occurrence de quelque chose qui se produit dans votre application, souvent une interaction utilisateur, comme la sélection d’un bouton ou l’achèvement d’une tâche. Vous insérez le code dans votre application pour [générer des événements personnalisés](./api-custom-events-metrics.md#trackevent).

## <a name="querying-for-certain-users"></a>Interrogation de certains utilisateurs

Explorez les différents groupes d’utilisateurs en ajustant les options d’interrogation en haut de l’outil Utilisateurs :

- Pendant : choisissez un intervalle de temps.
- Afficher : choisissez une cohorte d’utilisateurs à analyser.
- Qui a utilisé : choisissez les événements personnalisés, les requêtes et les vues de page.
- Événements : choisissez plusieurs événements, requêtes et vues de page qui montreront les utilisateurs qui sont à l’origine d’au moins un, mais pas nécessairement tous, des événements sélectionnés.
- Par valeur sur l’axe X : choisissez comment compartimenter les données, soit par intervalle de temps, soit par une autre propriété telle que le navigateur ou la ville.
- Fractionner par : choisissez une propriété selon laquelle fractionner ou segmenter les données. 
- Ajouter des filtres : limitez la requête à certains utilisateurs, certaines sessions ou certains événements en fonction de leurs propriétés, telles que le navigateur ou la ville. 
 
## <a name="meet-your-users"></a>Découvrir vos utilisateurs

La section **Meet your users** (Découvrir vos utilisateurs) fournit des informations sur cinq exemples d’utilisateurs mis en correspondance par la requête actuelle. L’exploration des comportements individuels et globaux peut fournir des informations sur la façon dont les usagers utilisent réellement votre application.

## <a name="next-steps"></a>Étapes suivantes

- Pour activer les expériences d’utilisation, commencez à envoyer des [événements personnalisés](./api-custom-events-metrics.md#trackevent) ou des [affichages de page](./api-custom-events-metrics.md#page-views).
- Si vous envoyez déjà des événements personnalisés ou des affichages de page, explorez les outils d’utilisation pour savoir comment les utilisateurs emploient votre service.
    - [Entonnoirs](usage-funnels.md)
    - [Rétention](usage-retention.md)
    - [Flux d’utilisateurs](usage-flows.md)
    - [Classeurs](../visualize/workbooks-overview.md)
    - [Ajouter du contexte utilisateur](./usage-overview.md)