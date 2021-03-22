---
title: Attribuer des rôles à des noms de principal du service Contrat Entreprise Azure
description: Cet article vous aide à attribuer des rôles aux noms de principal du service à l’aide de PowerShell et des API REST.
author: bandersmsft
ms.reviewer: ruturajd
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/07/2021
ms.author: banders
ms.openlocfilehash: e7f5370e1e387947d196959fef31043ea8f4d3bd
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508518"
---
# <a name="assign-roles-to-azure-enterprise-agreement-service-principal-names"></a>Attribuer des rôles à des noms de principal du service Contrat Entreprise Azure

Vous pouvez gérer votre inscription Contrat Entreprise (EA) dans [Microsoft Azure Enterprise Portal](https://ea.azure.com/). Vous pouvez créer différents rôles pour gérer votre organisation, afficher les coûts et créer des abonnements. Cet article vous aide à automatiser certaines de ces tâches à l’aide d’Azure PowerShell et d’API REST avec des noms de principal du service (SPN) Azure.

Avant de commencer, assurez-vous que vous êtes familiarisé avec les articles suivants :

- [Rôles du contrat Entreprise](understand-ea-roles.md)
- [Se connecter avec Azure PowerShell](/powershell/azure/authenticate-azureps)
- [Appeler des API REST avec Postman](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

## <a name="create-and-authenticate-your-service-principal"></a>Créer et authentifier votre principal de service

Pour automatiser les actions EA à l’aide d’un SPN, vous devez créer une application Azure Active Directory (Azure AD). Elle peut s’authentifier de manière automatisée. Lisez les articles suivants et suivez les étapes des procédures qu’ils proposent pour créer et authentifier votre principal de service.

1. [Créer un principal du service](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. [Obtenir les valeurs d’ID de locataire et d’ID d’application pour la connexion](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)

Voici un exemple de capture d’écran montrant l’inscription de l’application.

:::image type="content" source="./media/assign-roles-azure-service-principals/register-an-application.png" alt-text="Capture d’écran montrant la page Inscrire une application." lightbox="./media/assign-roles-azure-service-principals/register-an-application.png" :::

### <a name="find-your-spn-and-tenant-id"></a>Rechercher votre SPN et votre ID de locataire

Vous avez également besoin de l’ID d’objet du SPN et de l’ID de locataire de l’application. Vous avez besoin de ces informations pour les opérations d’attribution des autorisations dans les sections suivantes.

Vous pouvez trouver l’ID de locataire de l’application Azure AD dans la page de présentation de l’application. Pour le trouver dans le portail Azure, accédez à Azure Active Directory et sélectionnez **Applications d’entreprise**. Recherchez l’application.

:::image type="content" source="./media/assign-roles-azure-service-principals/enterprise-application.png" alt-text="Capture d’écran montrant un exemple d’application d’entreprise." lightbox="./media/assign-roles-azure-service-principals/enterprise-application.png" :::

Sélectionnez l’application. Voici un exemple montrant l’ID d’application et l’ID d’objet.

:::image type="content" source="./media/assign-roles-azure-service-principals/application-id-object-id.png" alt-text="Capture d’écran montrant un ID d’application et un ID d’objet pour une application d’entreprise." lightbox="./media/assign-roles-azure-service-principals/application-id-object-id.png" :::

Vous pouvez trouver l’ID de locataire dans la page de présentation de Microsoft Azure AD.

:::image type="content" source="./media/assign-roles-azure-service-principals/tenant-id.png" alt-text="Capture d’écran montrant où trouver votre ID de locataire." lightbox="./media/assign-roles-azure-service-principals/tenant-id.png" :::

L’ID de locataire de votre principal est également appelé ID de principal, SPN et ID d’objet selon où il apparaît. La valeur de votre ID de locataire Azure AD ressemble à un GUID au format suivant : `11111111-1111-1111-1111-111111111111`.

## <a name="permissions-that-can-be-assigned-to-the-spn"></a>Autorisations qui peuvent être attribuées au SPN

Pour les étapes suivantes, vous autorisez l’application Azure AD à effectuer des actions à l’aide d’un rôle EA. Vous pouvez uniquement attribuer les rôles suivants au SPN. L’ID de définition de rôle, exactement comme indiqué, est utilisé ultérieurement dans les étapes d’attribution.

| Rôle | Actions autorisées | ID de définition de rôle |
| --- | --- | --- |
| EnrollmentReader | Permet d’afficher l’utilisation et les frais pour tous les comptes et abonnements. Permet d’afficher le solde du paiement anticipé Azure (précédemment appelé engagement financier) associé à l’inscription. | 24f8edb6-1668-4659-b5e2-40bb5f3a7d7e |
| DepartmentReader | Télécharger les détails d’utilisation pour le service qu’il administre. Permet d’afficher l’utilisation et les frais associés à son service. | db609904-a47f-4794-9be8-9bd86fbffd8a |
| SubscriptionCreator | Créer des abonnements dans l’étendue de compte donnée. | a0bcee42-bf30-4d1b-926a-48d21664ef71 |

- Un lecteur d’inscription peut être attribué à un SPN uniquement par un utilisateur ayant le rôle d’auteur d’inscription.
- Un lecteur de service peut être attribué à un SPN uniquement par un utilisateur ayant le rôle d’auteur d’inscription ou le rôle d’auteur de service.
- Un rôle de créateur d’abonnement peut être attribué à un SPN uniquement par un utilisateur qui est le propriétaire du compte d’inscription.

## <a name="assign-enrollment-account-role-permission-to-the-spn"></a>Attribuer l’autorisation du rôle de compte d’inscription au SPN

Lisez l’article sur l’API REST [Attributions de rôles – PUT](/rest/api/billing/2019-10-01-preview/roleassignments/put).

Pendant la lecture de l’article, sélectionnez **Essayer** pour commencer à utiliser le SPN.

:::image type="content" source="./media/assign-roles-azure-service-principals/put-try-it.png" alt-text="Capture d’écran montrant l’option Essayer dans l’article Put." lightbox="./media/assign-roles-azure-service-principals/put-try-it.png" :::

Connectez-vous avec votre compte au locataire qui a accès à l’inscription à laquelle vous souhaitez attribuer un accès.

Fournissez les paramètres suivants dans le cadre de la demande d’API.

**billingAccountName**

Le paramètre correspond à l’ID du compte de facturation. Vous pouvez le trouver dans le portail Azure sur la page de présentation de Cost Management and Billing.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Capture d’écran montrant l’ID du compte de facturation." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

Le paramètre est un GUID unique que vous devez fournir. Vous pouvez générer un GUID à l’aide de la commande PowerShell [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid).

Vous pouvez également utiliser le site web [Online GUID / UUID Generator](https://guidgenerator.com/) pour générer un GUID unique.

**api-version**

Utilisez la version **2019-10-01-preview**.

Le corps de la demande contient du code JSON que vous devez utiliser.

Utilisez l’exemple de corps de demande fourni dans la section [Exemples dans Attributions de rôles – PUT](/rest/api/billing/2019-10-01-preview/roleassignments/put#examples).

Il existe trois paramètres que vous devez utiliser dans le cadre du JSON.

| Paramètre | Comment y accéder |
| --- | --- |
| properties.principalId | Voir [Rechercher votre SPN et votre ID de locataire](#find-your-spn-and-tenant-id). |
| properties.principalTenantId | Voir [Rechercher votre SPN et votre ID de locataire](#find-your-spn-and-tenant-id). |
| properties.roleDefinitionId | « /providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/24f8edb6-1668-4659-b5e2-40bb5f3a7d7e » |

Le nom du compte de facturation est le même paramètre que celui que vous avez utilisé dans les paramètres de l’API. Il s’agit de l’ID d’inscription que vous voyez dans le portail EA et le portail Azure.

Notez que `24f8edb6-1668-4659-b5e2-40bb5f3a7d7e` est un ID de définition de rôle de facturation pour un EnrollmentReader.

Sélectionnez **Exécuter** pour lancer la commande.

:::image type="content" source="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" alt-text="Capture d’écran montrant un exemple d’attribution de rôle PUT, avec le bouton Essayer mis en évidence et des exemples d’informations, prêt à être exécuté." lightbox="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" :::

Une réponse `200 OK` indique que le SPN a bien été ajouté.

Vous pouvez maintenant utiliser le SPN (Azure AD App avec l’ID d’objet) pour accéder aux API EA de manière automatisée. Le SPN a le rôle EnrollmentReader.

## <a name="assign-the-department-reader-role-to-the-spn"></a>Attribuer le rôle de lecteur de service au SPN

Avant de commencer, lisez l’article sur l’API REST [Attributions de rôles de service d’inscription – PUT](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put).

Pendant la lecture de l’article, sélectionnez **Essayer**.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Capture d’écran montrant l’option Essayer dans l’article Attributions de rôles de service d’inscription – PUT." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

Connectez-vous avec votre compte au locataire qui a accès à l’inscription à laquelle vous souhaitez attribuer un accès.

Fournissez les paramètres suivants dans le cadre de la demande d’API.

**billingAccountName**

Il s’agit de l’ID du compte de facturation. Vous pouvez le trouver dans le portail Azure sur la page de présentation de Cost Management and Billing.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Capture d’écran montrant l’ID du compte de facturation." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

Le paramètre est un GUID unique que vous devez fournir. Vous pouvez générer un GUID à l’aide de la commande PowerShell [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid).

Vous pouvez également utiliser le site web [Online GUID / UUID Generator](https://guidgenerator.com/) pour générer un GUID unique.

**departmentName**

Il s’agit de l’ID du service. Vous pouvez consulter les ID de service dans le portail Azure. Accédez à Cost Management and Billing > **Services**.

Pour cet exemple, nous avons utilisé le service ACE. L’ID de l’exemple est `84819`.

:::image type="content" source="./media/assign-roles-azure-service-principals/department-id.png" alt-text="Capture d’écran montrant un exemple d’ID de service." lightbox="./media/assign-roles-azure-service-principals/department-id.png" :::

**api-version**

Utilisez la version **2019-10-01-preview**.

Le corps de la demande contient du code JSON que vous devez utiliser.

Utilisez l’exemple dans[Attributions de rôles de service d’inscription – PUT](/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put). Il existe trois paramètres que vous devez utiliser dans le cadre du JSON.

| Paramètre | Comment y accéder |
| --- | --- |
| properties.principalId | Voir [Rechercher votre SPN et votre ID de locataire](#find-your-spn-and-tenant-id). |
| properties.principalTenantId | Voir [Rechercher votre SPN et votre ID de locataire](#find-your-spn-and-tenant-id). |
| properties.roleDefinitionId | « /providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/db609904-a47f-4794-9be8-9bd86fbffd8a » |

Le nom du compte de facturation est le même paramètre que celui que vous avez utilisé dans les paramètres de l’API. Il s’agit de l’ID d’inscription que vous voyez dans le portail EA et le portail Azure.

L’ID de définition de rôle de facturation `db609904-a47f-4794-9be8-9bd86fbffd8a` est destiné à un lecteur de service.

Sélectionnez **Exécuter** pour lancer la commande.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" alt-text="Capture d’écran montrant un exemple d’API REST Attributions de rôles de service d’inscription – PUT, avec le bouton Essayer mis en évidence et des exemples d’informations, prêt à être exécuté." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" :::

Une réponse `200 OK` indique que le SPN a bien été ajouté.

Vous pouvez maintenant utiliser le SPN (Azure AD App avec l’ID d’objet) pour accéder aux API EA de manière automatisée. Le SPN a le rôle DepartmentReader.

## <a name="assign-the-subscription-creator-role-to-the-spn"></a>Attribuer le rôle de créateur d’abonnement au SPN

Lisez l’article [Attributions de rôles de compte d’inscription – PUT](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put).

Pendant la lecture, sélectionnez **Essayer** pour attribuer le rôle de créateur d’abonnement au SPN.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Capture d’écran montrant l’option Essayer dans l’article Attributions de rôles de compte d’inscription – PUT." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

Connectez-vous avec votre compte au locataire qui a accès à l’inscription à laquelle vous souhaitez attribuer un accès.

Fournissez les paramètres suivants dans le cadre de la demande d’API. Lisez la section [Paramètres URI dans Attributions de rôles de compte d’inscription – PUT](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put#uri-parameters).

**billingAccountName**

Le paramètre correspond à l’ID du compte de facturation. Vous pouvez le trouver dans le portail Azure sur la page de présentation de Cost Management and Billing.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Capture d’écran montrant l’ID du compte de facturation." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

Le paramètre est un GUID unique que vous devez fournir. Vous pouvez générer un GUID à l’aide de la commande PowerShell [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid).

Vous pouvez également utiliser le site web [Online GUID / UUID Generator](https://guidgenerator.com/) pour générer un GUID unique.
**enrollmentAccountName**

Le paramètre est l’ID de compte. Recherchez l’ID de compte pour le nom de compte du portail Azure dans Cost Management and Billing dans l’étendue Inscription et service.

Pour cet exemple, nous avons utilisé le compte GTM Test. L’ID est `196987`.

:::image type="content" source="./media/assign-roles-azure-service-principals/account-id.png" alt-text="Capture d’écran montrant l’ID de compte." lightbox="./media/assign-roles-azure-service-principals/account-id.png" :::

**api-version**

Utilisez la version **2019-10-01-preview**.

Le corps de la demande contient du code JSON que vous devez utiliser.

Utilisez l’exemple fourni la section [Exemples dans Attributions de rôles de service d’inscription – PUT](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put#putenrollmentdepartmentadministratorroleassignment).

Il existe trois paramètres que vous devez utiliser dans le cadre du JSON.

| Paramètre | Comment y accéder |
| --- | --- |
| properties.principalId | Voir [Rechercher votre SPN et votre ID de locataire](#find-your-spn-and-tenant-id). |
| properties.principalTenantId | Voir [Rechercher votre SPN et votre ID de locataire](#find-your-spn-and-tenant-id). |
| properties.roleDefinitionId | « /providers/Microsoft.Billing/billingAccounts/{BillingAccountID}/enrollmentAccounts/196987/billingRoleDefinitions/a0bcee42-bf30-4d1b-926a-48d21664ef71 » |

Le nom du compte de facturation est le même paramètre que celui que vous avez utilisé dans les paramètres de l’API. Il s’agit de l’ID d’inscription que vous voyez dans le portail EA et le portail Azure.

L’ID de définition de rôle de facturation `a0bcee42-bf30-4d1b-926a-48d21664ef71` est destiné au rôle de créateur d’abonnement.

Sélectionnez **Exécuter** pour lancer la commande.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" alt-text="Capture d’écran montrant l’option Essayer dans l’article Attributions de rôles de compte d’inscription – PUT" lightbox="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" :::

Une réponse `200 OK` indique que le SPN a bien été ajouté.

Vous pouvez maintenant utiliser le SPN (Azure AD App avec l’ID d’objet) pour accéder aux API EA de manière automatisée. Le SPN a le rôle SubscriptionCreator.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur l’[administration du portail Azure EA](ea-portal-administration.md).