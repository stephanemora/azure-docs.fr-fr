---
title: Gérer les stratégies d’abonnement Azure
description: Découvrez comment gérer les stratégies d’abonnement Azure pour contrôler le déplacement d’abonnements Azure entre des annuaires.
author: anuragdalmia
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/10/2021
ms.reviewer: banders
ms.author: andalmia
ms.openlocfilehash: 4d0ebd73065c538a78c950ab48254cb06d113897
ms.sourcegitcommit: 2cb7772f60599e065fff13fdecd795cce6500630
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108804517"
---
# <a name="manage-azure-subscription-policies"></a>Gérer les stratégies d’abonnement Azure

Cet article vous aide à configurer des stratégies d’abonnement Azure appropriées pour les opérations d’abonnement. Ces stratégies vous permettront de contrôler le déplacement d’abonnements Azure entre des annuaires.

## <a name="prerequisites"></a>Prérequis

- Seuls les [administrateurs généraux](../../active-directory/roles/permissions-reference.md#global-administrator) d’annuaires peuvent modifier les stratégies d’abonnement. Avant de modifier des stratégies d’abonnement, l’administrateur général doit [élever l’accès pour gérer tous les abonnements et groupes d’administration Azure](../../role-based-access-control/elevate-access-global-admin.md). Il peut ensuite apporter les modifications souhaitées aux stratégies d’abonnement.
- Tous les autres utilisateurs peuvent uniquement lire les paramètres de stratégie actuels.

## <a name="available-subscription-policy-settings"></a>Paramètres de stratégie d’abonnement disponibles

Utilisez les paramètres de stratégie suivants pour contrôler le déplacement d’abonnements Azure entre des annuaires.

### <a name="subscriptions-leaving-aad-directory"></a>Abonnements retirés d’un annuaire AAD

La stratégie autorise ou interdit le déplacement d’abonnements en dehors de l’annuaire actuel par les utilisateurs. Les [propriétaires d’abonnement](../../role-based-access-control/built-in-roles.md#owner) peuvent [changer l’annuaire d’un abonnement Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) par un autre annuaire dont ils sont membres. En raison des problèmes de gouvernance que cela pose, les administrateurs généraux peuvent autoriser ou interdire les changements d’annuaire par les utilisateurs.

### <a name="subscriptions-entering-aad-directory"></a>Abonnements ajoutés à un annuaire AAD

La stratégie autorise ou interdit le déplacement d’abonnements vers l’annuaire actuel par des utilisateurs d’autres annuaires qui ont accès à l’annuaire actuel. Les [propriétaires d’abonnement](../../role-based-access-control/built-in-roles.md#owner) peuvent [changer l’annuaire d’un abonnement Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) par un autre annuaire dont ils sont membres. En raison des problèmes de gouvernance que cela pose, les administrateurs généraux peuvent autoriser ou interdire les changements d’annuaire par les utilisateurs.

### <a name="exempted-users"></a>Utilisateurs exemptés

Pour les besoins de gouvernance, les administrateurs généraux ont la possibilité de bloquer tous les déplacements d’abonnements vers ou en dehors de l’annuaire actuel. Toutefois, ils peuvent vouloir autoriser des utilisateurs spécifiques à effectuer l’un ou l’autre de ces déplacements. Dans les deux cas, ils peuvent créer une liste d’utilisateurs exemptés, lesquels peuvent alors ignorer le paramètre de stratégie qui s’applique à tous les autres utilisateurs.

## <a name="setting-subscription-policy"></a>Définir une stratégie d’abonnement

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Accéder aux **Abonnements**. Vous voyez la commande **Gérer les stratégies** dans la barre de commandes.  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" alt-text="Capture d’écran montrant Gérer les stratégies dans Abonnements." lightbox="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" :::
1. Sélectionnez **Gérer les stratégies** pour afficher les détails des stratégies d’abonnement actuellement définies pour l’annuaire. Un administrateur général qui a des [autorisations élevées](../../role-based-access-control/elevate-access-global-admin.md) peut modifier les paramètres, notamment ajouter ou supprimer des utilisateurs exemptés.  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies.png" alt-text="Capture d’écran montrant des paramètres de stratégie spécifiques et des utilisateurs exemptés." lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies.png" :::
1. Sélectionnez **Enregistrer les modifications** en bas. Les modifications prennent effet immédiatement.

## <a name="read-subscription-policy"></a>Lire la stratégie d’abonnement

Les administrateurs non généraux peuvent accéder à la zone de la stratégie d’abonnement pour voir les paramètres de stratégie de l’annuaire. En revanche, ils n’ont pas la possibilité de modifier ces paramètres. Pour des raisons de confidentialité, ils ne peuvent pas non plus voir la liste des utilisateurs exemptés. Ils peuvent voir leurs administrateurs généraux et leur soumettre des demandes de modification de stratégie, à condition que les paramètres d’annuaire les y autorisent.

:::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" alt-text="Capture d’écran montrant Gérer les stratégies dans Abonnements en tant que lecteur." lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" :::

## <a name="next-steps"></a>Étapes suivantes

- Lire la [documentation Cost Management + Billing](../index.yml)