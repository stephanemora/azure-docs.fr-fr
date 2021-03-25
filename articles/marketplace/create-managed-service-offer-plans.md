---
title: Comment créer des plans pour votre offre Service managé sur la Place de marché Azure
description: Découvrez comment créer des plans pour votre offre Service managé sur la Place de marché Azure à l’aide de l’Espace partenaires Microsoft.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 9b5526af03bdbefeb54633c49bbd43743555f60b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383226"
---
# <a name="how-to-create-plans-for-your-managed-service-offer"></a>Comment créer des plans pour votre offre Service managé

Les offres Service managé vendues par le biais de la place de marché commerciale Microsoft doivent avoir au moins un plan. Vous pouvez créer divers plans avec différentes options dans la même offre. Ces plans (parfois appelés références SKU) peuvent différer en termes de version, de monétisation ou de niveaux de service. Pour obtenir des conseils détaillés sur les plans, consultez [Plans et tarification des offres de la Place de marché commerciale](./plans-pricing.md).

## <a name="create-a-plan"></a>Créer un plan

1. Dans l’onglet **Vue d’ensemble du plan** de votre offre dans l’Espace partenaires, sélectionnez **+ Créer un plan**.
2. Dans la boîte de dialogue qui s’affiche, sous **ID du plan**, entrez un ID de plan unique. Utilisez jusqu’à 50 caractères alphanumériques en minuscules, tirets ou traits de soulignement. Vous ne pouvez pas modifier l’ID de plan une fois que vous avez sélectionné **Créer**. Cet ID sera visible par vos clients.
3. Dans la zone **Nom du plan**, entrez un nom unique pour ce plan. Utilisez un maximum de 50 caractères. Ce nom sera visible par vos clients.
4. Sélectionnez **Create** (Créer).

## <a name="define-the-plan-listing"></a>Définir la liste des plans

Dans l’onglet **Liste des plans**, définissez le nom et la description du plan comme vous souhaitez qu’ils apparaissent dans la place de marché commerciale.

1. La zone **Nom du plan** affiche le nom que vous avez fourni précédemment pour ce plan. Vous pouvez le modifier à tout moment. Ce nom apparaîtra dans la place de marché commerciale comme titre du plan de votre offre.
2. Dans la zone **Résumé du plan**, fournissez une description courte de votre plan à utiliser dans les résultats de recherche de la place de marché.
3. La zone **Description du plan** permet d’expliquer ce qui rend ce plan unique et de souligner les différences par rapport aux autres plans dans votre offre.
4. Avant de passer à l’onglet suivant, sélectionnez **Enregistrer le brouillon**.

## <a name="define-pricing-and-availability"></a>Définir la tarification et la disponibilité

Le seul modèle de tarification disponible pour les offres Service managé est **BYOL (apportez votre propre licence)** . Cela signifie que vous facturez directement à vos clients les coûts liés à cette offre, et que Microsoft ne vous facture aucuns frais.

Vous pouvez configurer chaque plan de sorte qu’il soit visible de tous (public) ou d’un public spécifique (privé).

> [!NOTE]
> Les plans privés ne sont pas pris en charge par les abonnements souscrits via un revendeur participant au programme des fournisseurs de solutions cloud (CSP).

> [!IMPORTANT]
> Une fois que vous avez publié un plan public, vous ne pouvez plus le changer en plan privé. Pour contrôler les clients qui peuvent accepter votre offre et déléguer des ressources, utilisez un plan privé. Avec un plan public, vous ne pouvez pas limiter la disponibilité à des clients spécifiques ou à un certain nombre de clients (en revanche, vous pouvez arrêter complètement la vente du plan si vous le souhaitez). Vous pouvez supprimer l’accès à une délégation après qu’un client a accepté une offre uniquement si vous avez inclus une Autorisation avec la Définition de rôle définie sur Inscription des services managés, attribution Supprimer le rôle lors de la publication de l’offre. Vous pouvez également contacter le client et lui demander de supprimer votre accès.

## <a name="make-your-plan-public"></a>Rendre votre plan public

1. Sous **Visibilité du plan**, sélectionnez **Public**.
2. Sélectionnez **Enregistrer le brouillon**. Pour revenir à l’onglet Vue d’ensemble du plan, sélectionnez **Vue d’ensemble du plan** en haut à gauche.
3. Pour créer un autre plan pour cette offre, sélectionnez **+ Créer un plan** dans l’onglet **Vue d’ensemble du plan**.

## <a name="make-your-plan-private"></a>Rendre votre plan privé

Vous accordez l’accès à un plan privé à l’aide des ID d’abonnement Azure. Vous pouvez ajouter jusqu’à 10 ID d’abonnement manuellement ou jusqu’à 10 000 ID d’abonnement à l’aide d’un fichier .CSV.

Pour ajouter jusqu’à 10 ID d’abonnement manuellement :

1. Sous **Visibilité du plan**, sélectionnez **Privé**.
2. Entrez l’ID d’abonnement Azure du public auquel vous souhaitez accorder l’accès.
3. Vous pouvez aussi entrer une description de ce public dans la zone **Description**.
4. Pour ajouter un autre ID, sélectionnez **Ajouter un ID (10 max.)** .
5. Quand vous avez terminé l’ajout des ID, sélectionnez **Enregistrer le brouillon**.

Pour ajouter jusqu’à 10 000 ID d’abonnement avec un fichier .CSV :

1. Sous **Visibilité du plan**, sélectionnez **Privé**.
2. Sélectionnez le lien **Exporter le public (CSV)** . Cela permet de télécharger un fichier .CSV.
3. Ouvrez le fichier .CSV. Dans la colonne **Id**, entrez les ID d’abonnement Azure auxquels vous souhaitez accorder l’accès.
4. Dans la colonne  **Description** , vous avez la possibilité d’ajouter une description pour chaque entrée.
5. Dans la colonne  **Type** , ajoutez  **SubscriptionId**  à chaque ligne contenant un ID.
6. Enregistrez le fichier au format .CSV.
7. Dans l’Espace partenaires, sélectionnez le lien **Importer le public (csv)** .
8. Dans la boîte de dialogue  **Confirmer** , sélectionnez  **Oui**, puis chargez le fichier .CSV.
9. Sélectionnez  **Enregistrer le brouillon**.

## <a name="technical-configuration"></a>Configuration technique

Cette section crée un manifeste contenant des informations d’autorisation pour la gestion des ressources du client. Ces informations sont nécessaires pour activer la [gestion des ressources déléguées Azure](../lighthouse/concepts/azure-delegated-resource-management.md).

Consultez [Locataires, rôles et utilisateurs dans les scénarios Azure Lighthouse](../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) afin de comprendre quels rôles sont pris en charge, ainsi que les bonnes pratiques pour la définition de vos autorisations.

> [!NOTE]
> Les utilisateurs et les rôles de vos entrées Autorisation s’appliquent à tous les clients qui activent le plan. Si vous souhaitez limiter l’accès à un client spécifique, vous devez publier un plan privé pour son usage exclusif.

### <a name="manifest"></a>Manifeste

1. Sous **Manifeste**, fournissez une **Version** pour le manifeste. Utilisez le format n.n.n (par exemple, 1.2.5).
2. Entrez votre **ID de locataire**. Il s’agit d’un GUID associé à l’ID de locataire Azure Active Directory (Azure AD) de votre organisation, autrement dit le locataire gérant à partir duquel vous allez accéder aux ressources de vos clients. Si vous ne l’avez pas sous la main, vous pouvez le trouver en pointant sur le nom de votre compte dans l’angle supérieur droit du portail Azure ou en sélectionnant **Changer de répertoire**.

Si vous publiez une nouvelle version de votre offre et que vous devez créer un manifeste mis à jour, sélectionnez **+ Nouveau manifeste**. Veillez à augmenter le numéro de la version précédente du manifeste.

### <a name="authorizations"></a>Autorisations

Les autorisations définissent les entités dans votre locataire gérant qui peuvent accéder aux ressources et aux abonnements pour les clients qui achètent le plan. Chaque entité se voit attribuer un rôle intégré qui accorde des niveaux d’accès spécifiques.

Vous pouvez créer jusqu’à 20 autorisations pour chaque plan.

> [!TIP]
> Dans la plupart des cas, vous affectez des rôles à un groupe d’utilisateurs ou à un principal de service Azure AD, plutôt qu’à une série de comptes d’utilisateur individuels. Cela vous permet d’ajouter ou de supprimer l’accès d’utilisateurs individuels sans devoir mettre à jour et republier le plan lorsque vos conditions d’accès changent. Quand vous attribuez des rôles à des groupes Azure AD, le [type de groupe doit être Sécurité et non Office 365](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Pour obtenir des recommandations supplémentaires, consultez [Locataires, rôles et utilisateurs dans les scénarios Azure Lighthouse](../lighthouse/concepts/tenants-users-roles.md).

Pour chaque autorisation, vous devrez fournir les informations suivantes. Vous pouvez ensuite sélectionner **+ Ajouter une autorisation** autant de fois que nécessaire pour ajouter des utilisateurs et des définitions de rôles.

* **ID d’objet AAD** : identificateur Azure AD d’un utilisateur, d’un groupe d’utilisateurs ou d’une application auxquels certaines autorisations seront accordées (comme défini dans la Définition de rôle) sur les ressources de votre client.
* **Nom d’affichage d’objet AAD** : nom convivial destiné à aider le client à comprendre l’objectif de cette autorisation. Le client verra ce nom lors de la délégation de ressources.
* **Définition de rôle** : sélectionnez l’un des rôles intégrés Azure AD disponibles dans la liste. Ce rôle détermine les autorisations sur les ressources de vos clients dont disposera l’utilisateur spécifié dans le champ **ID du principal**. Pour obtenir une description de ces rôles, consultez [Rôles intégrés](../role-based-access-control/built-in-roles.md) et [Prise en charge des rôles pour Azure Lighthouse](../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-lighthouse).

> [!NOTE]
> À mesure que de nouveaux rôles intégrés applicables sont ajoutés à Azure, ils seront disponibles ici. Il peut y avoir un certain délai avant qu’ils n’apparaissent.

* **Rôles attribuables** : cette option apparaît uniquement si vous avez sélectionné Administrateur de l’accès utilisateur dans la **Définition de rôle** pour cette autorisation. Si tel est le cas, vous devez ajouter un ou plusieurs rôles attribuables ici. L’utilisateur indiqué dans le champ **ID d’objet Azure AD** sera en mesure d’attribuer ces rôles à des identités managées, ce qui est nécessaire pour [déployer des stratégies qui peuvent être corrigées](../lighthouse/how-to/deploy-policy-remediation.md). Aucune autre autorisation normalement associée au rôle Administrateur de l’accès utilisateur ne s’appliquera à cet utilisateur.

> [!TIP]
> Pour être sûr de pouvoir [supprimer l’accès à une délégation](../lighthouse/how-to/remove-delegation.md) en cas de nécessité, incluez une **Autorisation** avec la **Définition de rôle** définie sur [Inscription des services managés, attribution Supprimer le rôle](../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Si ce rôle n’est pas attribué, les ressources déléguées ne peuvent être supprimées que par un utilisateur dans le locataire du client.

Une fois que vous avez renseigné toutes les sections de votre plan, vous pouvez sélectionner **+ Créer un plan** pour créer des plans supplémentaires. Lorsque vous avez terminé, sélectionnez **Enregistrer le brouillon**.

## <a name="next-steps"></a>Étapes suivantes

* [Vérifier et publier](review-publish-offer.md)
