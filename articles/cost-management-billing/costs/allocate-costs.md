---
title: Affecter des coûts Azure
description: Cet article explique comment créer des règles d'affectation des coûts pour répartir les coûts des abonnements, des groupes de ressources ou des étiquettes.
author: bandersmsft
ms.author: banders
ms.date: 03/23/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: e7afef7e0a10bb4be3c30112fc207467167e4a17
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726517"
---
# <a name="create-and-manage-azure-cost-allocation-rules-preview"></a>Créer et gérer des règles d'affectation des coûts Azure (préversion)

Les grandes entreprises disposent souvent de services ou de ressources Azure gérés de manière centralisée, mais utilisés par différents départements internes ou unités commerciales. En règle générale, l'équipe de gestion centralisée souhaite réaffecter les coûts des services partagés aux départements internes ou aux unités commerciales de l'organisation qui utilisent activement les services. Cet article vous aidera à comprendre et à utiliser la fonctionnalité d'affectation des coûts d'Azure Cost Management.

Avec l'affectation des coûts, vous pouvez réattribuer ou répartir les coûts des services partagés des abonnements, des groupes de ressources ou des étiquettes au sein d'autres abonnements, groupes de ressources ou étiquettes de votre organisation. L'affectation des coûts permet de transférer les coûts des services partagés vers d'autres abonnements, groupes de ressources ou étiquettes appartenant aux départements internes ou aux unités commerciales consommateurs. En d'autres termes, l'affectation des coûts permet de gérer et d'afficher la _responsabilité des coûts_ d'un emplacement à un autre.

L'affectation des coûts ne se répercute pas sur votre facture. Les responsabilités liées à la facturation restent inchangées. L'objectif premier de l'affectation des coûts est de vous aider à répercuter les coûts sur d'autres. Tous les processus de rétrofacturation de votre organisation interviennent en dehors d'Azure. L'affectation des coûts vous permet de répercuter les coûts en les présentant tels qu'ils sont réattribués ou répartis.

Les coûts affectés sont présentés dans l'analyse des coûts. Ils sont présentés en tant qu'éléments supplémentaires associés aux abonnements, groupes de ressources ou étiquettes ciblés que vous spécifiez lorsque vous créez une règle d'affectation des coûts.

> [!NOTE]
> La fonctionnalité d'affectation des coûts d'Azure Cost Management est actuellement en préversion publique. Certaines fonctionnalités de Cost Management peuvent être limitées ou non prises en charge.

## <a name="prerequisites"></a>Prérequis

- Pour le moment, la fonctionnalité d'affectation des coûts prend uniquement en charge les clients disposant d'un [Contrat client Microsoft](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) ou d'un [Contrat Entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/).
- Pour créer ou gérer une règle d'affectation des coûts, vous devez utiliser un compte Administrateur Entreprise associé à un [Contrat Entreprise](../manage/understand-ea-roles.md). Ou vous devez disposer d'un [compte de facturation](../manage/understand-mca-roles.md) associé à un Contrat client Microsoft.

## <a name="create-a-cost-allocation-rule"></a>Créer une règle d'affectation des coûts

1. Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com/).
2. Accédez à **Gestion des coûts + facturation** > **Gestion des coûts**.
3. Sous **Paramètres** > **Configuration**, sélectionnez **Affectation des coûts (préversion)** .
4. Assurez-vous que le bon compte d'inscription ou de facturation EA est sélectionné.
5. Sélectionnez **+Ajouter**.
6. Entrez un texte descriptif pour le nom de la règle d'affectation des coûts.

:::image type="content" source="./media/allocate-costs/rule-name.png" alt-text="Exemple illustrant la création d'un nom de règle" lightbox="./media/allocate-costs/rule-name.png" :::

La date de début de l'évaluation de la règle est utilisée pour générer les pourcentages d'affectation des coûts préremplis.

1. Sélectionnez **Ajouter des sources**, puis sélectionnez des abonnements, des groupes de ressources ou des étiquettes pour choisir les coûts à répartir.
2. Sélectionnez **Ajouter des cibles**, puis sélectionnez les abonnements, les groupes de ressources ou les étiquettes qui recevront les coûts affectés.
3. Si vous souhaitez créer d'autres règles d'affectation des coûts, répétez ce processus.

## <a name="configure-the-allocation-percentage"></a>Configurer le pourcentage d'affectation

Configurez le pourcentage d'affectation pour définir le mode de répartition proportionnelle entre les cibles spécifiées. Vous pouvez définir manuellement des pourcentages en nombres entiers pour créer une règle d'affectation. Vous pouvez également effectuer une répartition proportionnelle des coûts sur la base de l'utilisation actuelle du calcul, du stockage ou du réseau sur les cibles spécifiées.

Lors de la répartition des coûts par coût de calcul, coût de stockage ou coût réseau, le pourcentage proportionnel est dérivé en évaluant les coûts de la cible sélectionnée. Les coûts sont associés au type de ressource pour le mois de facturation en cours.

Lors de la répartition des coûts en proportion du coût total, le pourcentage proportionnel est alloué par la somme ou le coût total des cibles sélectionnées pour le mois de facturation en cours.

:::image type="content" source="./media/allocate-costs/cost-distribution.png" alt-text="Exemple illustrant le pourcentage d'affectation" lightbox="./media/allocate-costs/cost-distribution.png" :::

Une fois définis, les pourcentages préremplis sont fixes. Ils sont utilisés pour toutes les affectations en cours. Les pourcentages changent uniquement lorsque la règle est mise à jour manuellement.

1. Sélectionnez l'une des options suivantes dans la liste **Préremplir le pourcentage sur**.
    - **Répartir uniformément** : chacune des cibles reçoit une proportion égale en pourcentage du coût total.
    - **Coût total** : crée un ratio proportionnel aux cibles sur la base de leur coût total. Le ratio est utilisé pour répartir les coûts à partir des sources sélectionnées.
    - **Coût de calcul** : crée un ratio proportionnel aux cibles sur la base de leur coût de calcul Azure (types de ressources de l'espace de noms [Microsoft.Compute](/azure/templates/microsoft.compute/allversions). Le ratio est utilisé pour répartir les coûts à partir des sources sélectionnées.
    - **Coût de stockage** : crée un ratio proportionnel aux cibles sur la base de leur coût de stockage Azure (types de ressources de l'espace de noms [Microsoft.Storage](/azure/templates/microsoft.storage/allversions)). Le ratio est utilisé pour répartir les coûts à partir des sources sélectionnées.
    - **Coût réseau**  : crée un ratio proportionnel aux cibles sur la base de leur coût réseau Azure (types de ressources de l'espace de noms [Microsoft.Network](/azure/templates/microsoft.network/allversions)). Le ratio est utilisé pour répartir les coûts à partir des sources sélectionnées.
    - **Personnalisé** : permet de spécifier manuellement un pourcentage en nombre entier. Le total spécifié doit être égal à 100 %.
1. Une fois la règle configurée, sélectionnez **Créer**.

Le traitement de la règle d'affectation démarre. Lorsque la règle est active, tous les coûts de la source sélectionnée sont affectés aux cibles spécifiées.

> [!NOTE] 
> Le traitement de la nouvelle règle peut prendre jusqu'à deux heures.

## <a name="verify-the-cost-allocation-rule"></a>Vérifier la règle d'affectation des coûts

Lorsque la règle d'affectation des coûts est active, les coûts des sources sélectionnées sont répartis entre les cibles d'affectation spécifiées. Utilisez les informations suivantes pour vérifier que les coûts sont correctement affectés aux cibles.

### <a name="view-cost-allocation-for-a-subscription"></a>Visualiser l'affectation des coûts pour un abonnement

Vous pouvez visualiser l'impact de la règle d'affectation dans l'analyse des coûts. Dans le portail Azure, accédez à [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Sélectionnez un abonnement dans la liste ciblée par une règle d'affectation des coûts active. Sélectionnez ensuite **Analyse des coûts** dans le menu. Dans Analyse des coûts, sélectionnez **Regrouper par**, puis **Affectation des coûts**. L'affichage qui en résulte présente une répartition rapide des coûts générée par l'abonnement. Les coûts affectés à l'abonnement sont également affichés, comme dans l'image suivante.

:::image type="content" source="./media/allocate-costs/cost-breakdown.png" alt-text="Exemple illustrant la répartition des coûts" lightbox="./media/allocate-costs/cost-breakdown.png" :::

### <a name="view-cost-allocation-for-a-resource-group"></a>Visualiser l'affectation des coûts pour un groupe de ressources

Utilisez un processus similaire pour visualiser l'impact d'une règle d'affectation des coûts sur un groupe de ressources. Sur le portail Azure, accédez [Groupes de ressources](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups). Sélectionnez un groupe de ressources dans la liste ciblée par une règle d'affectation des coûts active. Sélectionnez ensuite **Analyse des coûts** dans le menu. Dans Analyse des coûts, sélectionnez **Regrouper par**, puis **Affectation des coûts**. L'affichage présente une répartition rapide des coûts générée par le groupe de ressources. Les coûts affectés au groupe de ressources sont également affichés.

### <a name="view-cost-allocation-for-tags"></a>Visualiser l'affectation des coûts pour des étiquettes

Sur le portail Azure, accédez à **Gestion des coûts + facturation** > **Gestion des coûts** > **Analyse des coûts**. Dans Analyse des coûts, sélectionnez **Ajouter un filtre**. Sélectionnez **Étiquette**, choisissez la clé et les valeurs d'étiquette auxquelles un coût est affecté.

:::image type="content" source="./media/allocate-costs/tagged-costs.png" alt-text="Exemple illustrant les coûts liés aux éléments marqués" lightbox="./media/allocate-costs/tagged-costs.png" :::

Voici une vidéo qui montre comment créer une règle d’affectation des coûts.

>[!VIDEO https://www.youtube.com/embed/nYzIIs2mx9Q]


## <a name="edit-an-existing-cost-allocation-rule"></a>Modifier une règle d'affectation de coûts existante

Vous pouvez modifier une règle d'affectation des coûts pour changer la source ou la cible, ou lorsque vous souhaitez mettre à jour le pourcentage prérempli pour les options de calcul, de stockage ou de réseau. Pour modifier les règles, procédez comme pour leur création. La modification des règles existantes peut nécessiter un maximum de deux heures de retraitement.

## <a name="current-limitations"></a>Limites actuelles

Actuellement, dans Cost Management, la fonctionnalité d'affectation des coûts est prise en charge par les affichages d'analyse des coûts, des budgets et des prévisions. Les coûts affectés apparaissent également dans la liste des abonnements et sur la page de présentation de ceux-ci.

Les éléments suivants ne sont actuellement pas pris en charge par la préversion publique de la fonctionnalité d'affectation des coûts :

- Exportations [planifiées](tutorial-export-acm-data.md)
- Données exposées par l'API [Détails d'utilisation](/rest/api/consumption/usagedetails/list)
- Zone des abonnements de facturation
- [Application Power BI Cost Management](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)
- [Connecteur Power BI Desktop](/power-bi/connect-data/desktop-connect-azure-cost-management)


## <a name="next-steps"></a>Étapes suivantes

- Consultez les [questions fréquentes (FAQ) sur Cost Management + Billing](../cost-management-billing-faq.yml) pour voir les questions et réponses sur l’affectation des coûts.
- Créer ou mettre à jour des règles d'affectation à l'aide de l'[API Rest Affectation des coûts](/rest/api/cost-management/costallocationrules)
- En savoir plus sur l'[Optimisation de votre investissement dans le cloud avec Azure Cost Management](cost-mgt-best-practices.md)