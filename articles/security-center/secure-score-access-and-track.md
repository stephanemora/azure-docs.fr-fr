---
title: Suivi de votre degré de sécurisation dans Azure Security Center
description: Apprenez-en davantage sur les différentes façons d’accéder à votre degré de sécurisation et de le suivre dans Azure Security Center.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.openlocfilehash: 50ab34a73a7709a47efdf6904acf005cd5db9da5
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111753590"
---
# <a name="access-and-track-your-secure-score"></a>Accéder et suivre votre score de sécurisation

Vous pouvez connaître votre niveau de sécurité global et votre niveau de sécurité par abonnement sur le Portail Azure ou programmatiquement.

> [!TIP]
> Pour une explication détaillée de la façon dont vos scores sont calculés, consultez [Calculs : comprendre votre score](secure-score-security-controls.md#calculations---understanding-your-score).

## <a name="get-your-secure-score-from-the-portal"></a>Obtenir votre score de sécurité à partir du portail

Security Center indique le niveau de sécurité de manière bien visible sur le portail puisqu'il s'agit de la première vignette que l'on voit sur la page de présentation. En sélectionnant cette vignette, vous accédez à la page consacrée au niveau de sécurité, où celui-ci est présenté par abonnement. Sélectionnez un abonnement pour voir la liste détaillée des recommandations classées par ordre de priorité ainsi que l'impact que peut avoir leur prise en compte sur le niveau de sécurité de l'abonnement. 

Pour récapituler, votre niveau de sécurité apparaît aux emplacements suivants sur les pages du portail de Security Center.

- Sur une vignette de la **page de présentation** de Security Center (tableau de bord principal) :

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="Niveau de sécurité sur le tableau de bord de Security Center":::

- La page **Score sécurisé** dédiée affiche le score sécurisé pour votre abonnement et vos groupes d’administration :

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="Niveau de sécurité des abonnements sur la page Niveau de sécurité de Security Center":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Niveau de sécurité des groupes d’administration sur la page Niveau de sécurité de Security Center":::

    > [!NOTE]
    > Les groupes d’administration pour lesquels vous ne disposez pas d’autorisations suffisantes affichent leur score comme « restreint ». 

- En haut de la page **Recommandations** :

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="Niveau de sécurité sur la page Recommandations de Security Center":::

## <a name="get-your-secure-score-from-the-rest-api"></a>Obtenir votre score de sécurité à partir de l’API REST

Vous pouvez accéder à votre degré de sécurisation par le biais de l’API Degré de sécurisation. Les méthodes de l’API offrent la flexibilité nécessaire pour interroger les données et créer votre propre mécanisme de création de rapports sur vos degrés de sécurisation au fil du temps. Par exemple, vous pouvez utiliser l’API [Degré de sécurisation](/rest/api/securitycenter/securescores) pour obtenir le degré de sécurisation d’un abonnement spécifique. En outre, vous pouvez utiliser l’API [Contrôles du degré de sécurisation](/rest/api/securitycenter/securescorecontrols) pour répertorier les contrôles de sécurité et le degré de sécurisation actuel de vos abonnements.

![Récupération d’un score sécurisé unique via l’API](media/secure-score-security-controls/single-secure-score-via-api.png)

Pour obtenir des exemples d’outils créés sur la base de l’API de degré de sécurisation, consultez [la zone consacrée au degré de sécurisation de notre communauté GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score). 

## <a name="get-your-secure-score-from-azure-resource-graph"></a>Obtenir le niveau de sécurité à partir d’Azure Resource Graph

Azure Resource Graph fournit un accès instantané aux informations relatives aux ressources de vos environnements cloud avec des fonctionnalités robustes de filtrage, de regroupement et de tri. Il s’agit d’un moyen rapide et efficace de demander des informations dans les abonnements Azure par programmation ou depuis le Portail Azure. [Apprenez-en davantage sur Azure Resource Graph](../governance/resource-graph/index.yml).

Pour accéder au degré de sécurisation de plusieurs abonnements avec Azure Resource Graph :

1. Sur le portail Azure, ouvrez l'**Explorateur Azure Resource Graph**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Lancement de la page de recommandations de l’Explorateur Azure Resource Graph**" :::

1. Entrez votre requête Kusto (en utilisant les exemples ci-dessous à titre indicatif).

    - Cette requête renvoie l'ID d'abonnement, le niveau de sécurité actuel en points et en pourcentage, et le niveau de sécurité maximum de l'abonnement. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - Cette requête renvoie l'état de tous les contrôles de sécurité. Pour chaque contrôle, vous obtiendrez le nombre de ressources non saines, le niveau de sécurité actuel et le niveau de sécurité maximum. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. Sélectionnez **Exécuter la requête**.


## <a name="tracking-your-secure-score-over-time"></a>Suivi de votre score sécurisé au fil du temps

### <a name="secure-score-over-time-report-in-workbooks-page"></a>Rapport sur le degré de sécurisation au fil du temps dans la page Classeurs

La page Classeurs du Security Center inclut un rapport prêt à l’emploi pour suivre visuellement les scores de vos abonnements, contrôles de sécurité et bien plus encore. Pour en savoir plus, consultez [Créer des rapports riches et interactifs de données de Security Center](custom-dashboards-azure-workbooks.md).

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Section du rapport sur le degré de sécurisation au fil du temps extrait de la galerie de classeurs de l’Azure Security Center":::

### <a name="power-bi-pro-dashboards"></a>Tableaux de bord Power BI Pro

Si vous êtes un utilisateur Power BI avec un compte Pro, vous pouvez utiliser le tableau de bord Power BI **Score sécurisé au fil du temps** pour suivre votre score sécurisé au fil du temps et examiner les modifications.

> [!TIP]
> Ce tableau de bord ainsi que d’autres outils permettant de travailler programmatiquement avec le niveau de sécurité sont disponibles dans la partie dédiée de la communauté Azure Security Center sur GitHub : https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score.

Le tableau de bord contient les deux rapports suivants pour vous aider à analyser votre état de sécurité :

- **Résumé des ressources** : fournit des données résumées concernant l’intégrité de vos ressources.
- **Synthèse des scores sécurisés** : fournit des données résumées concernant la progression de votre score. Utilisez le graphique « Score sécurisé dans le temps par abonnement » pour afficher les modifications apportées au score. Si vous remarquez une modification spectaculaire de votre score, consultez la table « modifications détectées qui peuvent affecter votre score sécurisé » pour les éventuelles modifications susceptibles d’avoir provoqué la modification. Ce tableau présente les ressources supprimées, les ressources récemment déployées ou les ressources dont le statut de sécurité a changé pour l’une des recommandations.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="Le tableau de bord Power BI de score sécurisé facultatif pour le suivi de vos scores sécurisés au fil du temps et l’examen des modifications":::


## <a name="next-steps"></a>Étapes suivantes

Cet article vous a montré comment accéder à votre degré de sécurisation et le suivre. Pour des informations connexes, consultez les articles suivants :

- [En savoir plus sur les différents éléments d’une recommandation](security-center-recommendations.md)
- [Découvrez comment appliquer les recommandations](security-center-remediate-recommendations.md)
- [Consulter les outils basés sur GitHub pour travailler par programmation avec un score sécurisé](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)