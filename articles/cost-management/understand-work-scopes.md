---
title: Comprendre et utiliser les étendues de gestion des coûts Azure | Microsoft Docs
description: Cet article vous aide à comprendre la facturation et des ressources étendues de gestion disponibles dans Azure et comment utiliser les étendues dans la gestion des coûts et des API.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 4e7956e8873b552fcd73c51a51f51d99f21af324
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002935"
---
# <a name="understand-and-work-with-scopes"></a>Comprendre et utiliser des étendues

Cet article vous aide à comprendre la facturation et des ressources étendues de gestion disponibles dans Azure et comment utiliser les étendues dans la gestion des coûts et des API.

## <a name="scopes"></a>Étendues

Un _étendue_ est un nœud dans la hiérarchie des ressources Azure où les utilisateurs Azure AD accédant et gérer des services. Ressources plus Azure sont créés et déployés dans les groupes de ressources qui font partie des abonnements. Microsoft propose également deux hiérarchies au-dessus des abonnements Azure qui ont spécialisé des rôles pour gérer les données de facturation :
- Données de facturation, telles que les factures et paiements
- Services cloud, comme le coût et la stratégie de gouvernance

Les étendues sont où gérer les données de facturation, avez spécifique de rôles à des paiements, affichage des factures et mener une gestion de compte général. Les rôles de compte et la facturation sont gérés séparément de celles utilisées pour la gestion des ressources, qui utilisent [Azure RBAC](../role-based-access-control/overview.md). Pour distinguer clairement l’intention des zones distinctes, y compris les différences de contrôle d’accès, ils sont désignés comme _facturation étendues_ et _étendues RBAC_, respectivement.

## <a name="how-cost-management-uses-scopes"></a>Utilisation de la gestion des coûts étendues

Coût fonctionnement de la gestion à tous les étendues de ressources pour permettre aux organisations de gérer les coûts au niveau auquel ils ont accès, que ce soit le compte de facturation entier ou un seul groupe de ressources ci-dessus. Bien que les étendues de facturation varient en fonction de votre contrat de Microsoft (type d’abonnement), les étendues RBAC ne peuvent pas.

## <a name="azure-rbac-scopes"></a>Étendues d’Azure RBAC

Azure prend en charge trois étendues pour la gestion des ressources. Chaque étendue prend en charge la gestion des accès et gouvernance, y compris, mais ne pas exhaustive, gestion des coûts.

- [**Groupes d’administration** ](../governance/management-groups/index.md) -conteneurs hiérarchiques, jusqu'à huit niveaux, organiser les abonnements Azure.

    Type de ressource : [Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **Abonnements** -conteneurs principaux pour les ressources Azure.

    Type de ressource : [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**Groupes de ressources** ](../azure-resource-manager/resource-group-overview.md#resource-groups) -regroupements logiques de ressources liées à une solution Azure qui partagent le même cycle de vie. Par exemple les ressources qui sont déployées et les supprimer simultanément.

    Type de ressource : [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

Groupes d’administration permettent d’organiser les abonnements dans une hiérarchie. Par exemple, vous pouvez créer une hiérarchie d’organisation logique à l’aide de groupes d’administration. Ensuite, donner équipes abonnements pour les charges de travail de développement/test et de production. Puis créez des groupes de ressources dans les abonnements pour gérer chaque sous-système ou un composant.

Création de qu'une hiérarchie d’organisation permet de coût et la conformité aux stratégies cumuléesent qu’organisationnel. Ensuite, chaque responsable peut afficher et analyser leurs coûts en cours. Et puis ils peuvent créer des budgets pour maîtriser le mauvais dépenses et optimiser les coûts avec les recommandations du conseiller au niveau plus bas.

Accorder l’accès pour afficher les coûts et éventuellement gérer une configuration de coût, telles que les budgets et les exportations, est effectuée sur les étendues de gouvernance à l’aide d’Azure RBAC. Vous utilisez Azure RBAC pour accorder aux utilisateurs Azure AD et accéder à des groupes pour effectuer un ensemble prédéfini d’actions qui sont définies dans un rôle sur une étendue spécifique et en dessous. Par exemple, un rôle affecté à une étendue de groupe de gestion accorde également les mêmes autorisations sur les groupes de ressources et abonnements imbriquées.

Gestion des coûts prend en charge les rôles intégrés suivants pour chacune des étendues suivantes :

- [**Propriétaire** ](../role-based-access-control/built-in-roles.md#owner) : peut afficher les coûts et tout gérer, y compris la configuration de coût.
- [**Contributeur** ](../role-based-access-control/built-in-roles.md#contributor) : peut afficher les coûts et tout gérer, y compris la configuration de coût, mais à l’exclusion de contrôle d’accès.
- [**Lecteur** ](../role-based-access-control/built-in-roles.md#reader) : peut afficher tout, y compris les données de coût et la configuration, mais ne peut pas apporter de modifications.
- [**Collaborateur de gestion des coûts** ](../role-based-access-control/built-in-roles.md#cost-management-contributor) : peut afficher les coûts et gérer la configuration de coût.
- [**Lecteur de la gestion des coûts** ](../role-based-access-control/built-in-roles.md#cost-management-reader) – afficher les données de coût et la configuration.

Collaborateur de gestion des coûts est le rôle de privilège minimum recommandé. Il permet aux utilisateurs l’accès créer et gérer des budgets et exporte plus efficacement analyse et rapports sur les coûts. Contributeurs de coût gestion peut également nécessiter des rôles supplémentaires pour prendre en charge les scénarios de gestion des coûts de bout en bout. Examinez les scénarios suivants :

- **Agir de cas de dépassement des budgets** – contributeurs de coût gestion ont également besoin d’accéder à créer et/ou gérer des groupes d’actions automatiquement pour réagir aux dépassements. Envisagez d’accorder [contributeur de surveillance](../role-based-access-control/built-in-roles.md#monitoring-contributor) à un groupe de ressources qui contient le groupe d’actions à utiliser lorsque dépassement des seuils budgétaires. Automatisation des actions spécifiques nécessite des rôles supplémentaires pour les services spécifiques utilisés, tels que l’Automation et Azure Functions.
- **Exportation des données de coût de planification** – contributeurs de coût gestion ont également besoin d’accéder à gérer les comptes de stockage pour planifier une exportation pour copier des données dans un compte de stockage. Envisagez d’accorder [collaborateur de compte de stockage](../role-based-access-control/built-in-roles.md#storage-account-contributor) vers un groupe de ressources qui contient le stockage où les données de coût de compte est exporté.
- **Affichage des recommandations d’économies** – lecteurs de gestion des coûts et les collaborateurs n’ont pas accès aux recommandations par défaut. Accès aux recommandations nécessite un accès en lecture à des ressources individuelles. Envisagez d’accorder [lecteur](../role-based-access-control/built-in-roles.md#reader) ou un [rôle spécifiques au service](../role-based-access-control/built-in-roles.md#built-in-role-descriptions).

## <a name="enterprise-agreement-scopes"></a>Étendues de contrat entreprise

Comptes de facturation accord entreprise (EA), également appelés les inscriptions, ont les étendues suivantes :

- [**Compte de facturation** ](../billing/billing-view-all-accounts.md) -représente une inscription EA. Les factures sont générées dans cette étendue. Achats qui ne sont pas basés sur l’utilisation, telles que la place de marché et les réservations, sont uniquement disponibles dans cette étendue. Elles ne sont pas représentées dans les services ou des comptes d’inscription.

    Type de ressource : `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Département** - facultatif de regroupement des comptes d’inscription.

    Type de ressource : `Billing/billingAccounts/departments`

- **Compte d’inscription** -représente un seul propriétaire de compte. Ne gère pas accorder l’accès à plusieurs personnes.

    Type de ressource : `Microsoft.Billing/billingAccounts/enrollmentAccounts`

Bien que les étendues de gouvernance sont liés à un seul répertoire, étendues de facturation contrat entreprise ne sont pas. Un compte de facturation contrat entreprise peut avoir des abonnements sur n’importe quel nombre de répertoires Azure AD.

Étendues de facturation contrat entreprise prennent en charge les rôles suivants :

- **Administrateur d’entreprise** : peut gérer les paramètres de compte de facturation et des accès, peut afficher tous les coûts et pouvez gérer la configuration de coût. Par exemple, de budgets et l’exporte. Dans la fonction, le contrat entreprise étendue de facturation est identique à [rôle RBAC d’Azure Cost Management contributeur](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Utilisateur en lecture seule d’entreprise** – afficher les paramètres de compte de facturation, les données de coût et la configuration de coût. Par exemple, de budgets et l’exporte. Dans la fonction, le contrat entreprise étendue de facturation est identique à la [rôle RBAC d’Azure Cost Management lecteur](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Administrateur de service** : peut gérer les paramètres de service, telles que le centre de coût et peut accéder, afficher tous les coûts et gérer la configuration de coût. Par exemple, de budgets et l’exporte.  Le **d’affichage des frais** paramètre de compte de facturation doit être activée pour les administrateurs de service et des utilisateurs en lecture seule pour afficher les coûts. Si **d’affichage des frais** est désactivé, les utilisateurs du service ne peut pas afficher les coûts à tout niveau, même si elles sont un propriétaire de compte ou abonnement.
- **Utilisateur en lecture seule de département** – afficher les paramètres de service, les données de coût et la configuration de coût. Par exemple, de budgets et l’exporte. Si **d’affichage des frais** est désactivé, les utilisateurs du service ne peut pas afficher les coûts à tout niveau, même si elles sont un propriétaire de compte ou abonnement.
- **Propriétaire du compte** : peut gérer les paramètres de compte d’inscription (par exemple, le centre de coût), afficher tous les coûts et gérer la configuration de coût (par exemple, les budgets et les exportations) pour le compte d’inscription. Le **d’affichage des frais** paramètre de compte de facturation doit être activée pour les propriétaires de comptes et utilisateurs RBAC pour afficher les coûts.

Utilisateurs de compte de facturation contrat entreprise n’ont pas un accès direct aux factures. Les factures sont disponibles à partir d’un système de licence en volume externe.

Abonnements Azure sont imbriquées sous des comptes d’inscription. Les utilisateurs de facturation ont accès aux données de coût pour les abonnements et les groupes de ressources qui sont sous leurs portées respectives. Ils n’ont pas accès pour afficher ou gérer les ressources dans le portail Azure. Les utilisateurs de facturation peuvent afficher les coûts en accédant à **gestion des coûts + facturation** dans la liste de portail Azure de services. Ensuite, ils peuvent filtrer les coûts pour les groupes de ressources sur que dont ils ont besoin pour créer des rapports des abonnements spécifiques.

Les utilisateurs de facturation n’ont pas accès aux groupes d’administration, car elles ne relèvent pas explicitement un compte de facturation spécifique. Accès doit être accordé explicitement aux groupes d’administration. Cumul des coûts aux abonnements imbriqués groupes d’administration. Toutefois, ils ne comprennent que basée sur l’utilisation des achats. Ils n’incluent pas les achats telles que les réservations et les offres du Marketplace tiers. Pour afficher ces coûts, utilisez le compte de facturation contrat entreprise.

## <a name="individual-agreement-pay-as-you-go-scopes"></a>Étendues de contrat individuel (paiement à l’utilisation)

Abonnements de paiement à l’utilisation (PAYG), y compris les types associés comme gratuit/essai et offres dev/test, n’ont pas une portée de compte de facturation explicite. Au lieu de cela, chaque abonnement a un propriétaire du compte ou l’administrateur de compte, comme le propriétaire du compte EA.

- [**Compte de facturation** ](../billing/billing-view-all-accounts.md) -représente un seul propriétaire de compte pour un ou plusieurs abonnements Azure. Il ne prend en charge actuellement lui accordant l’accès à plusieurs personnes ou l’accès aux vues de coût agrégé.

    Type de ressource : Non applicable

Administrateurs de compte d’abonnement PAYG peuvent afficher et gérer les données de facturation, telles que les factures et paiements, à partir de la [centre des comptes Azure](https://account.azure.com/subscriptions). Toutefois, ils ne peuvent pas afficher les données de coût ou gérer les ressources dans le portail Azure. Pour accorder l’accès à l’administrateur de compte, utilisez les rôles de gestion des coûts mentionnés précédemment.

Contrairement au contrat entreprise, administrateurs de compte d’abonnement paiement à l’utilisation peuvent voir leurs factures dans le portail Azure. N’oubliez pas que les rôles de lecteur de gestion des coûts et collaborateur de gestion des coûts ne fournissent pas accès aux factures. Pour plus d’informations, consultez [comment accorder l’accès aux factures PAYG](../billing/billing-manage-access.md#give-access-to-billing).

## <a name="customer-agreement-scopes"></a>Étendues de contrat de client

Comptes de facturation de contrat de client de Microsoft ont les étendues suivantes :

- **Compte de facturation** -représente un contrat de client pour plusieurs produits et services Microsoft. Les comptes de facturation de contrat client ne sont pas fonctionnellement le même que les inscriptions contrat entreprise. Les inscriptions contrat entreprise sont plus étroitement lié aux profils de facturation.

    Type de ressource : `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Profil de facturation** -définit les abonnements qui sont inclus dans une facture. Profils de facturation sont l’équivalent fonctionnel d’une inscription EA, car il s’agit de l’étendue que les factures sont générées au. De même, les achats qui ne sont pas basés sur l’utilisation (par exemple, les réservations et de la place de marché) sont uniquement disponibles dans cette étendue. Ils ne sont pas inclus dans les sections de facture.

    Type de ressource : `Microsoft.Billing/billingAccounts/billingProfiles`

- **Section de facture** -représente un groupe d’abonnements dans une facture ou d’un profil de facturation. Les sections de facture ressemblent à des départements, plusieurs personnes peuvent avoir accès à une section de la facture.

    Type de ressource : `Microsoft.Billing/billingAccounts/invoiceSections`

Contrairement à EA facturation étendues, comptes de facturation de contrat de client _sont_ lié à un répertoire unique et ne peut pas avoir des abonnements sur plusieurs annuaires Azure AD.

Étendues de facturation de contrat client prennent en charge les rôles suivants :

- **Propriétaire** : peut gérer les paramètres de facturation et des accès, afficher tous les coûts et gérer la configuration de coût. Par exemple, de budgets et l’exporte. Dans la fonction, cette étendue de facturation des contrats de client est le même que le [rôle RBAC d’Azure Cost Management contributeur](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Contributeur** : peut gérer les paramètres de facturation, sauf les accès, afficher tous les coûts et gérer la configuration de coût. Par exemple, de budgets et l’exporte. Dans la fonction, cette étendue de facturation des contrats de client est le même que le [rôle RBAC d’Azure Cost Management contributeur](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Lecteur** – afficher les paramètres de facturation, les données de coût et la configuration de coût. Par exemple, de budgets et l’exporte. Dans la fonction, cette étendue de facturation des contrats de client est le même que le [rôle RBAC d’Azure Cost Management lecteur](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Gestionnaire de factures** : peut afficher et payer des factures et peuvent afficher les données et la configuration de coût. Par exemple, de budgets et l’exporte. Dans la fonction, cette étendue de facturation des contrats de client est le même que le [rôle RBAC d’Azure Cost Management lecteur](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Créateur de l’abonnement Azure** : peut créer des abonnements Azure, afficher les coûts et gérer la configuration de coût. Par exemple, de budgets et l’exporte. Dans la fonction, cette étendue de facturation du contrat de client est le même que le rôle de propriétaire de compte d’inscription EA.

Abonnements Azure sont imbriquées sous les sections de facture, telles que la façon dont ils sont sous des comptes d’inscription EA. Les utilisateurs de facturation ont accès aux données de coût pour les abonnements et les groupes de ressources qui se trouvent sous leurs portées respectives. Toutefois, ils n’ont accès pour afficher ou gérer les ressources dans le portail Azure. Les utilisateurs de facturation peuvent afficher les coûts en accédant à **gestion des coûts + facturation** dans la liste de portail Azure de services. Ensuite, filtrez les coûts pour les groupes de ressources sur que dont ils ont besoin pour créer des rapports des abonnements spécifiques.

Les utilisateurs de facturation n’ont pas accès aux groupes d’administration, car ils n’appartiennent pas explicitement sous le compte de facturation. Toutefois, lorsque des groupes d’administration sont activés pour l’organisation, tous les coûts d’abonnement sont cumulés pour le compte de facturation et le groupe d’administration racine, car elles sont limitées à la fois dans un répertoire unique. Groupes d’administration incluent uniquement les achats qui sont basés sur l’utilisation. Achats telles que les réservations et les offres du Marketplace tiers ne sont pas inclus dans les groupes d’administration. Par conséquent, le groupe d’administration racine et le compte de facturation peut signaler des totaux différents. Pour afficher ces coûts, utilisez le compte de facturation ou le profil de facturation respectif.

## <a name="cloud-solution-provider-csp-scopes"></a>Étendues de cloud Solution Provider (CSP)

Les partenaires cloud Solution Provider (CSP) ne sont pas pris en charge dans Cost Management. Au lieu de cela, vous pouvez utiliser [partenaires](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview).

## <a name="switch-between-scopes-in-cost-management"></a>Basculer entre les étendues dans Cost Management

Toutes les vues de gestion des coûts dans le portail Azure incluent un **étendue** pilule en haut à gauche de la vue. Il permet de modifier rapidement l’étendue. Cliquez sur le **étendue** pilule pour ouvrir le sélecteur d’étendue. Il montre les comptes de facturation, le groupe d’administration racine et les abonnements qui ne sont pas imbriqués sous le groupe d’administration racine. Pour sélectionner une étendue, cliquez sur l’arrière-plan pour mettre en surbrillance, puis cliquez sur **sélectionnez** en bas. Pour l’extraction dans des étendues imbriquées, telles que les groupes de ressources dans un abonnement, cliquez sur le lien de nom d’étendue. Pour sélectionner l’étendue parente à tous les niveaux imbriqué, cliquez sur **Sélectionnez cette option &lt;étendue&gt;**  en haut du sélecteur de portée.

## <a name="identify-the-resource-id-for-a-scope"></a>Identifier l’ID de ressource pour une étendue

Lorsque vous travaillez avec l’API de gestion des coûts, en sachant que l’étendue est critique. Utilisez les informations suivantes pour générer l’URI d’étendue appropriée pour l’API de gestion des coûts.

### <a name="billing-accounts"></a>Comptes de facturation

1. Ouvrez le portail Azure et accédez à **gestion des coûts + facturation** dans la liste des services.
2. Sélectionnez **propriétés** dans le menu de compte de facturation.
3. Copiez l’ID de compte facturation.
4. Votre étendue est : `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Profils de facturation

1. Ouvrez le portail Azure et accédez à **gestion des coûts + facturation** dans la liste des services.
2. Sélectionnez **profils de facturation** dans le menu de compte de facturation.
3. Cliquez sur le nom du profil de facturation souhaité.
4. Sélectionnez **propriétés** dans le menu de profil de facturation.
5. Copiez le compte de facturation et l’ID de profil de facturation.
6. Votre étendue est : `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Sections de facture

1. Ouvrez le portail Azure et accédez à **gestion des coûts + facturation** dans la liste des services.
2. Sélectionnez **sections de facture** dans le menu de compte de facturation.
3. Cliquez sur le nom de la section de la facture de votre choix.
4. Sélectionnez **propriétés** dans le menu de la section facture.
5. Copiez le compte de facturation et de facture de section ID.
6. Votre étendue est : `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>Départements EA

1. Ouvrez le portail Azure et accédez à **gestion des coûts + facturation** dans la liste des services.
2. Sélectionnez **départements** dans le menu de compte de facturation.
3. Cliquez sur le nom du service souhaité.
4. Sélectionnez **propriétés** dans le menu de service.
5. Copiez les ID de service et le compte de facturation.
6. Votre étendue est : `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>Compte d’inscription EA

1. Ouvrez le portail Azure et accédez à **gestion des coûts + facturation** dans la liste des services.
2. Sélectionnez **comptes d’inscription** dans le menu de compte de facturation.
3. Cliquez sur le nom du compte d’inscription de votre choix.
4. Sélectionnez **propriétés** dans le menu du compte d’inscription.
5. Copiez le compte de facturation et l’ID de compte d’inscription.
6. Votre étendue est : `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Groupe d’administration

1. Ouvrez le portail Azure et accédez à **groupes d’administration** dans la liste des services.
2. Accédez au groupe d’administration souhaité.
3. Copiez l’ID de groupe de gestion de la table.
4. Votre étendue est : `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Abonnement

1. Ouvrez le portail Azure et accédez à **abonnements** dans la liste des services.
2. Copiez l’ID d’abonnement à partir de la table.
3. Votre étendue est : `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Groupes de ressources

1. Ouvrez le portail Azure et accédez à **groupes de ressources** dans la liste des services.
2. Cliquez sur le nom du groupe de ressources souhaité.
3. Sélectionnez **propriétés** dans le menu de groupe de ressources.
4. Copiez la valeur du champ ID ressource.
5. Votre étendue est : `"/subscriptions/{id}/resourceGroups/{name}"`

Gestion des coûts ne prend actuellement en charge [Azure Global](https://management.azure.com) et [Azure Government](https://management.usgovcloudapi.net). Pour plus d’informations sur Azure Government, consultez [points de terminaison Azure Global et Government API](../azure-government/documentation-government-developer-guide.md#endpoint-mapping)_._

## <a name="next-steps"></a>Étapes suivantes

- Si vous n’avez pas encore effectué le premier guide de démarrage rapide relatif à Cost Management, lisez-le à partir de [Démarrer l’analyse des coûts](quick-acm-cost-analysis.md).
