---
title: Démarrage rapide - Créer un budget avec un modèle Azure Resource Manager
description: Démarrage rapide montrant comment créer un budget avec un modèle Azure Resource Manager.
author: bandersmsft
ms.author: banders
tags: azure-resource-manager
ms.service: cost-management-billing
ms.topic: quickstart
ms.date: 04/22/2020
ms.custom: subject-armqs
ms.openlocfilehash: de24895334ec4c864e6daae84a6aab47a47d7b9b
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103631"
---
# <a name="quickstart-create-a-budget-with-an-azure-resource-manager-template"></a>Démarrage rapide : Créer un budget avec un modèle Azure Resource Manager

Les budgets dans Cost Management vous aident à planifier et à suivre la comptabilité de l’organisation. Avec les budgets, vous pouvez prendre en compte les services Azure que vous consommez ou auxquels vous vous abonnez pendant une période spécifique. Ils vous permettent d’informer les autres utilisateurs de leurs dépenses pour gérer les coûts de manière proactive, ainsi que pour superviser la progression des dépenses. En cas de dépassement des seuils budgétaires que vous avez créés, des notifications sont déclenchées. Aucune de vos ressources n’est affectée et votre consommation n’est pas arrêtée. Vous pouvez utiliser des budgets pour comparer et suivre les dépenses lors de l’analyse des coûts. Ce guide de démarrage rapide vous montre comment créer un budget à l’aide d’un modèle Resource Manager.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Prérequis

Le modèle Azure Resource Manager prend uniquement en charge les abonnements Azure pour les Contrats Entreprise (EA). Les autres types d’abonnements ne sont pas pris en charge par le modèle.

Pour créer et gérer des budgets, vous devez disposer d’une autorisation de contributeur. Vous pouvez créer des budgets individuels pour les abonnements EA et les groupes de ressources. Vous ne pouvez cependant pas créer des budgets pour les comptes de facturation Contrat Entreprise. Dans le cadre des abonnements Azure EA, vous devez disposer d'un accès en lecture pour afficher les budgets.

Une fois qu’un budget est créé, vous devez disposer au minimum d’un accès en lecture à votre compte Azure pour voir les budgets.

Si vous disposez d’un nouvel abonnement, vous ne pouvez pas créer un budget ou utiliser les autres fonctionnalités de Cost Management tout de suite. Vous risquez de devoir attendre jusqu’à 48 heures avant de pouvoir utiliser toutes les fonctionnalités de Cost Management.

Les autorisations, ou étendues, Azure suivantes sont prises en charge par abonnement aux budgets par utilisateur et par groupe. Pour plus d’informations sur les étendues, consultez [Comprendre et utiliser les étendues](understand-work-scopes.md).

- Propriétaire : peut créer, modifier ou supprimer des budgets pour un abonnement.
- Contributeur et Contributeur Cost Management : peut créer, modifier ou supprimer ses propres budgets. Peut modifier le montant des budgets créés par d’autres utilisateurs.
- Lecteur et Lecteur Cost Management : peut afficher les budgets pour lesquels ils disposent des autorisations adéquates.

Pour plus d’informations sur l’affectation d’une autorisation d’accès aux données Cost Management, consultez [Affecter une autorisation d’accès aux données Cost Management](assign-access-acm-data.md).

## <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce guide de démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/create-budget).

:::code language="json" source="~/quickstart-templates/create-budget/azuredeploy.json":::

Une seule ressource Azure est définie dans le modèle :

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets) : Créer un budget Azure.

## <a name="deploy-the-template"></a>Déployer le modèle

1. Cliquez sur l’image ci-après pour vous connecter à Azure et ouvrir un modèle. Le modèle crée un budget.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json"><img src="./media/quick-create-budget-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Sélectionnez ou entrez les valeurs suivantes.

   [![Modèle Resource Manager, Créer un budget, portail de déploiement](./media/quick-create-budget-template/create-budget-using-template-portal.png)](./media/quick-create-budget-template/create-budget-using-template-portal.png#lightbox)

    * **Abonnement** : sélectionnez un abonnement Azure.
    * **Groupe de ressources** : sélectionnez **Créer**, entrez un nom unique pour le groupe de ressources, puis cliquez sur **OK**, ou sélectionnez un groupe de ressources existant.
    * **Emplacement** : sélectionnez un emplacement. Par exemple, **USA Centre**.
    * **Nom du budget** : entrez le nom pour le budget. Il doit être unique au sein d’un groupe de ressources. Seuls les caractères alphanumériques, le trait de soulignement et les traits d’union sont autorisés.
    * **Montant** : entrez le montant total du coût ou la quantité totale d’utilisation que le budget doit suivre.
    * **Catégorie de budget** : sélectionnez la catégorie de budget. Le budget effectue le suivi soit du **Coût**, soit de l’**Utilisation**.
    * **Fragment de temps** : entrez la durée couverte par un budget. Les valeurs autorisées sont Mensuel, Trimestriel ou Annuel. Le budget se réinitialise à la fin du fragment de temps.
    * **Date de début** : entrez la date de début avec le premier jour du mois au format AAAA-MM-JJ. La date de début ultérieure ne doit pas être supérieure à trois mois à compter du jour actuel. Vous pouvez spécifier une date de début passée avec la période Fragment de temps.
    * **Date de fin** : entrez la date de fin du budget au format AAAA-MM-JJ. Si cette valeur n’est pas fournie, la valeur par défaut est définie sur 10 ans à compter de la date de début.
    * **Opérateur** : sélectionnez un opérateur de comparaison. Les valeurs possibles sont EqualTo, GreaterThan ou GreaterThanOrEqualTo.
    * **Seuil** : entrez une valeur de seuil pour la notification. Une notification est envoyée quand le coût dépasse le seuil. Il est toujours exprimé en pourcentage et doit être compris entre 0 et 1 000.
    * **E-mails de contact** : entrez une liste d’adresses e-mail auxquelles envoyer la notification de budget quand le seuil est dépassé. Le format attendu est `["user1@domain.com","user2@domain.com"]`.
    * **Rôles de contact** : entrez une liste de rôles de contact auxquels envoyer la notification de budget quand le seuil est dépassé. Les valeurs par défaut sont Propriétaire, Contributeur et Lecteur. Le format attendu est `["Owner","Contributor","Reader"]`.
    * **Groupes de contacts** : entrez une liste de groupes d’actions auxquels envoyer la notification de budget quand le seuil est dépassé. Elle accepte un tableau de chaînes. Le format attendu est `["Action Group Name1","Action Group Name2"]`. Si vous ne voulez pas utiliser de groupes d’actions, entrez `[]`.
    * **Filtre de ressources** : entrez une liste de filtres pour les ressources. Le format attendu est `["Resource Filter Name1","Resource Filter Name2"]`. Si vous ne voulez pas appliquer de filtre, entrez `[]`. Si vous entrez un filtre de ressources, vous devez également entrer des valeurs de **filtres de compteurs**.
    * **Filtre de compteurs** : entrez une liste de filtres sur les compteurs, obligatoires pour les budgets ayant la catégorie de budget **Utilisation**. Le format attendu est `["Meter Filter Name1","Meter Filter Name2"]`. Si vous n’avez pas entré de **filtre de ressources**, entrez `[]`.
    * **J’accepte les termes et conditions mentionnés ci-dessus** : cochez la case.

3. Sélectionnez **Achat**. Une fois le budget correctement déployé, vous recevez une notification :

   ![Modèle Resource Manager, budget, notification du portail de déploiement](./media/quick-create-budget-template/resource-manager-template-portal-deployment-notification.png)

Le portail Azure est utilisé pour déployer le modèle. Outre le portail Azure, vous pouvez également utiliser Azure PowerShell, Azure CLI et l’API REST. Pour découvrir d’autres modèles de déploiement, consultez [Déployer des modèles](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Valider le déploiement

Vous pouvez utiliser le portail Azure pour vérifier que le budget est créé en accédant à **Cost Management + Facturation** > sélectionnez une étendue > **Budgets**. Vous pouvez également utiliser les scripts Azure CLI ou Azure PowerShell suivants pour voir le budget.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

```azurecli-interactive
az consumption budget list
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzConsumptionBudget
```

---

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un budget Azure pour le déploiement. Pour en savoir plus sur Azure Cost Management + Facturation et Azure Resource Manager, passez aux articles ci-dessous.

- Lire la vue d’ensemble d’[Azure Cost Management + Facturation](../cost-management-billing-overview.md)
- [Créer des budgets](tutorial-acm-create-budgets.md) dans le portail Azure
- En savoir plus sur [Azure Resource Manager](../../azure-resource-manager/management/overview.md)