---
title: Prise en main d’Azure Cost Management pour les partenaires
description: Cet article explique aux partenaires comment utiliser les fonctionnalités d’Azure Cost Management et comment autoriser l’accès à Cost Management à leurs clients.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: 9d95e23cf92c7ee98291831d60088d610c3e5c52
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377430"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Prise en main d’Azure Cost Management pour les partenaires

Azure Cost Management est disponible de façon native pour les partenaires qui ont intégré leurs clients à un contrat client Microsoft. Cet article explique aux partenaires comment utiliser les fonctionnalités d’[Azure Cost Management](https://docs.microsoft.com/azure/cost-management/). Il leur explique également comment autoriser l’accès à Cost Management à leurs clients. Les clients CSP peuvent utiliser les fonctionnalités de Cost Management si elles sont activées par leur partenaire CSP.

Les partenaires CSP utilisent Cost Management pour :

- Comprendre les coûts facturés et associer les coûts au client, aux abonnements, aux groupes de ressources et aux services.
- Bénéficier d’une présentation intuitive des coûts liés à Azure avec la vue d’[analyse des coûts](quick-acm-cost-analysis.md), qui offre des fonctionnalités d’analyse par client, par abonnement, par groupe de ressources, par ressource, par compteur, par service et selon de nombreux autres aspects.
- Visualiser les coûts des ressources pour lesquels un crédit Partenaires (PEC, Partner Earned Credit) est appliqué dans l’analyse des coûts.
- Configurer des notifications et une automatisation à l’aide de [budgets](tutorial-acm-create-budgets.md) programmatiques ainsi que des alertes quand les coûts dépassent les budgets.
- Activer la stratégie Azure Resource Manager qui accorde aux clients l’accès aux données de Cost Management. Les clients peuvent ensuite voir les données de coût de consommation pour leurs abonnements selon les [tarifs de paiement à l’utilisation](https://azure.microsoft.com/pricing/calculator/).

Toutes les fonctionnalités disponibles dans Azure Cost Management sont également disponibles avec les API REST. Utilisez les API pour automatiser les tâches de gestion des coûts.

## <a name="prerequisites"></a>Prérequis

Azure Cost Management nécessite un accès en lecture à votre abonnement ou votre compte de facturation. L’accès peut être accordé à n’importe quel niveau au-dessus de vos ressources : du compte de facturation ou d’un groupe d’administration jusqu’aux groupes de ressources individuels où vous gérez vos applications. Pour que les utilisateurs d’abonnement puissent voir les tarifs et les coûts, vous devez autoriser l’accès à la visualisation des frais pour votre compte de facturation. Pour plus d’informations sur l’autorisation et l’attribution de l’accès à Azure Cost Management, consultez [Assigner l’accès aux données](assign-access-acm-data.md). Pour accéder à la liste complète des types de comptes pris en charge, consultez [Comprendre les données de Cost Management](understand-cost-mgt-data.md).


## <a name="how-cost-management-uses-scopes"></a>Utilisation des étendues par Cost Management

Les étendues vous permettent de gérer les données de facturation, de définir des rôles propres aux paiements, d’afficher les factures et de gérer les comptes de manière générale. Les rôles de facturation et de compte sont gérés séparément des étendues utilisées pour la gestion des ressources, qui utilisent le contrôle d’accès en fonction du rôle (RBAC). Pour distinguer clairement l’intention des différentes étendues, y compris les différences en termes de contrôle d’accès, elles sont désignées comme étendues de facturation et étendues RBAC, respectivement.

Pour comprendre les étendues de facturation, les étendues RBAC et le fonctionnement de la gestion des coûts avec les étendues, consultez [Comprendre et utiliser des étendues](understand-work-scopes.md).

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Gérer les coûts avec les étendues de facturation de locataire partenaire

Une fois que vous avez intégré vos clients à un contrat client Microsoft, les _étendues de facturation_ suivantes sont disponibles dans votre locataire. Utilisez les étendues pour gérer les coûts dans Cost Management.

### <a name="billing-account-scope"></a>Étendue de compte de facturation

Utilisez l’étendue de compte de facturation afin de voir les coûts avant impôts pour l’ensemble de vos clients et profils de facturation. Vous pouvez également visualiser les coûts facturés liés aux produits basés sur la consommation pour les clients bénéficiant du contrat client Microsoft. Les coûts facturés sont également indiqués pour les produits basés sur les achats pour les clients bénéficiant du contrat client Microsoft et de l’offre CSP. Actuellement, la devise utilisée par défaut pour l’affichage des coûts dans l’étendue est le dollar américain. Les budgets définis pour l’étendue sont également exprimés en USD.

Quelle que soit la devise de facturation du client, les partenaires utilisent l’étendue afin de définir les budgets et de gérer les coûts en USD pour les clients, les abonnements, les ressources et les groupes de ressources.

Les partenaires filtrent également les coûts selon une devise de facturation spécifique pour les clients dans la vue d’analyse des coûts. Sélectionnez la liste **Coût réel** pour voir les coûts dans les devises de facturation client prises en charge.

![Exemple montrant le choix de devises pour le coût réel](./media/get-started-partners/actual-cost-selector.png)

Utilisez la [vue des coûts amortis](quick-acm-cost-analysis.md#customize-cost-views) dans les étendues de facturation pour voir les coûts amortis des instances réservées sur une période de réservation.

### <a name="billing-profile-scope"></a>Étendue de profil de facturation

Utilisez l’étendue de profil de facturation pour voir les coûts avant impôts dans la devise de facturation pour tous vos clients et pour tous les produits et abonnements inclus dans une facture. Vous pouvez filtrer les coûts dans un profil de facturation pour une facture spécifique à l’aide du filtre **InvoiceID**. Le filtre permet d’afficher les coûts de consommation et d’achat de produits pour une facture spécifique. Vous pouvez également filtrer les coûts pour un client spécifique sur la facture pour voir les coûts avant impôts.

Une fois que vous avez intégré des clients à un contrat client Microsoft, vous recevez une facture client qui indique les frais liés aux produits de droit et aux produits achetés (SaaS, Place de marché Azure et réservations, par exemple). Quand elle est établie avec la même devise de facturation, la facture indique également les frais facturés au client, qui ne sont pas inclus dans le nouveau contrat client Microsoft.

Pour simplifier le rapprochement des frais avec la facture client, l’étendue de profil de facturation vous permet de voir tous les coûts s’accumulant sur une facture pour vos clients. À l’instar de la facture, l’étendue indique les coûts pour chaque client bénéficiant du nouveau contrat client Microsoft. L’étendue indique également tous les frais pour les produits de droit client toujours inclus dans l’offre CSP actuelle.

Les étendues de profil et de compte de facturation sont les seules qui indiquent les frais pour les produits de droit et basés sur les achats.

Les profils de facturation définissent les abonnements inclus dans une facture. Les profils de facturation sont l’équivalent fonctionnel d’une inscription à un contrat Entreprise. Une inscription correspond à l’étendue dans laquelle les factures sont générées. De même, les achats qui ne sont pas basés sur l’utilisation (par exemple, Place de marché Azure et réservations) sont uniquement disponibles dans l’étendue de profil de facturation.

Actuellement, la devise de facturation du client est la devise utilisée par défaut quand vous consultez les coûts dans l’étendue de profil de facturation. Les budgets définis dans l’étendue de profil de facturation sont exprimés dans la devise de facturation.

Les partenaires peuvent utiliser l’étendue pour effectuer un rapprochement avec les factures. Par ailleurs, ils utilisent l’étendue afin de définir des budgets dans la devise de facturation pour les éléments suivants :

- Facture filtrée spécifique
- Client
- Subscription
- Resource group
- Ressource
- Service Azure
- Compteur
- ResellerMPNID

### <a name="customer-scope"></a>Étendue de client

Les partenaires utilisent l’étendue pour gérer les coûts associés aux clients intégrés au contrat client Microsoft. L’étendue permet aux partenaires de voir les coûts avant impôts pour un client spécifique. Vous pouvez également filtrer les coûts avant impôts pour un abonnement, un groupe de ressources ou une ressource spécifique.

L’étendue de client n’inclut pas les clients bénéficiant de l’offre CSP actuelle. Les coûts de droit (et non les coûts d’utilisation d’Azure) pour les clients de l’offre CSP actuelle sont disponibles dans les étendues de compte de facturation et de profil de facturation quand vous appliquez le filtre client.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Accès des partenaires aux étendues de facturation dans Cost Management

Seuls les utilisateurs ayant les rôles **Administrateur général** et **Agent d’administration** peuvent gérer et voir les coûts pour les comptes de facturation, les profils de facturation et les clients directement dans le locataire Azure du partenaire. Pour plus d’informations sur les rôles de l’espace Partenaires, consultez [Affecter des rôles et des autorisations aux utilisateurs](/partner-center/permissions-overview).

### <a name="enable-cost-management-in-the-customer-tenant"></a>Autoriser l’accès à la gestion des coûts dans le locataire client

Les partenaires peuvent autoriser l’accès à Cost Management une fois les clients intégrés à un contrat client Microsoft. Ils peuvent ainsi activer une stratégie permettant aux clients de voir leurs coûts calculés selon les tarifs de paiement à l’utilisation au détail. Les coûts sont indiqués dans la devise de facturation du client pour sa consommation dans les étendues de groupe de ressources et d’abonnement RBAC.

Quand la stratégie de visualisation des coûts est activée par le partenaire, tout utilisateur disposant d’un accès Azure Resource Manager à l’abonnement peut gérer et analyser les coûts selon les tarifs de paiement à l’utilisation. En effet, les revendeurs et les clients qui disposent de l’accès RBAC approprié aux abonnements Azure peuvent voir les coûts.

Quelle que soit la stratégie appliquée, les partenaires peuvent également voir les coûts s’ils ont accès à l’abonnement et au groupe de ressources.

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>Activer la stratégie de visualisation des frais d’utilisation d’Azure

Les partenaires utilisent les informations suivantes pour activer la stratégie de visualisation des frais d’utilisation d’Azure pour leurs clients.

Dans le portail Azure, connectez-vous au locataire partenaire, puis cliquez sur **Gestion des coûts + facturation**. Sélectionnez un compte de facturation, puis cliquez sur **Clients**. La liste des clients est associée au compte de facturation.

Dans la liste des clients, sélectionnez le client auquel vous souhaitez donner l’autorisation de voir les coûts.

![Sélectionner des clients dans Cost Management](./media/get-started-partners/customer-list.png)

Sous **Paramètres**, cliquez sur **Stratégies**.

La stratégie de visualisation des coûts actuelle est indiquée pour les frais d’**utilisation d’Azure** associés aux abonnements du client sélectionné.
![Stratégie permettant aux clients de voir les frais de paiement à l’utilisation](./media/get-started-partners/cost-management-billing-policies.png)

Quand la stratégie est définie sur **Non**, Azure Cost Management n’est pas disponible pour les utilisateurs d’abonnement associés au client. La stratégie de visualisation des coûts est désactivée par défaut pour tous les utilisateurs d’abonnement sauf si elle est activée par un partenaire.

Quand la stratégie de coût est définie sur **Oui**, les utilisateurs d’abonnement associés au locataire client peuvent voir les frais d’utilisation selon les tarifs de paiement à l’utilisation.

Quand la stratégie de visualisation des coûts est activée, tous les services associés à l’utilisation d’abonnements indiquent les coûts selon les tarifs de paiement à l’utilisation. L’utilisation de réservations apparaît avec des frais nuls pour les coûts réels et amortis. Les achats et les droits ne sont pas associés à un abonnement spécifique. Ainsi, les achats ne sont pas affichés dans l’étendue d’abonnement.

Pour voir les coûts relatifs au locataire client, ouvrez la vue Gestion des coûts + facturation, puis cliquez sur Comptes de facturation. Cliquez sur un compte dans la liste des comptes de facturation.

![Sélectionner un compte de facturation](./media/get-started-partners/select-billing-account.png)

Sous **Facturation**, cliquez sur **Abonnements Azure**, puis cliquez sur un client.

![Sélectionner un client d’abonnement Azure](./media/get-started-partners/subscriptions-select-customer.png)

Cliquez sur **Analyse des coûts** et commencez à examiner les coûts.
L’analyse des coûts, les budgets et les alertes sont désormais disponibles pour les étendues RBAC de groupe de ressources et d’abonnement selon les tarifs de paiement à l’utilisation.

![Voir l’analyse des coûts en tant que client ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

Les vues amorties et les coûts réels pour les instances réservées dans les étendues RBAC indiquent des frais nuls. Les coûts des instances réservées sont indiqués uniquement dans les étendues de facturation dans lesquelles les achats ont été effectués.

## <a name="analyze-costs-in-cost-analysis"></a>Analyser les coûts dans la vue d’analyse des coûts

Les partenaires peuvent explorer et analyser les coûts dans la vue d’analyse des coûts pour l’ensemble des clients, pour un client spécifique ou pour une facture. Les fonctionnalités de filtre et l’option Grouper par vous permettent d’analyser les coûts en fonction de plusieurs champs, dont certains sont présentés dans le tableau suivant :

| **Champ** | **Description** | **Colonne équivalente dans l’espace Partenaires** |
| --- | --- | --- |
| PartnerTenantID | Identificateur du locataire Azure Active Directory du partenaire | ID de locataire Azure Active Directory du partenaire appelé ID partenaire. Au format GUID. |
| PartnerName | Nom du locataire Azure Active Directory du partenaire | Nom du partenaire |
| CustomerTenantID | Identificateur du locataire Azure Active Directory de l’abonnement du client | ID d’organisation du client. Par exemple, ID de locataire Azure Active Directory du client. |
| CustomerName | Nom du locataire Azure Active Directory contenant l’abonnement du client | Nom de l’organisation du client comme indiqué dans l’espace Partenaires. Cet élément est important pour le rapprochement de la facture avec vos informations système. |
| ResellerMPNID | MPNID du revendeur associée à l’abonnement | ID MPN du revendeur de l’enregistrement pour l’abonnement. Non disponible pour l’activité en cours. |
| ID d’abonnement | Identificateur unique de l’abonnement Azure généré par Microsoft | N/A |
| subscriptionName | Nom de l’abonnement Azure | N/A |
| billingProfileID | Identificateur du profil de facturation. Il regroupe les coûts relatifs aux différentes factures selon une devise de facturation unique pour l’ensemble des clients. | ID de groupe de facturation du partenaire MCAPI. Utilisé dans les requêtes d’API, mais non inclus dans les réponses. |
| invoiceID | ID de facture sur la facture où apparaît la transaction spécifique | Numéro de facture sur lequel figure la transaction spécifiée. |
| resourceGroup | Nom du groupe de ressources Azure. Utilisé pour la gestion du cycle de vie des ressources. | Nom du groupe de ressources. |
| partnerEarnedCreditRate | Taux de remise appliqué en présence d’un crédit Partenaires basé sur l’accès au lien d’administrateur partenaire. | Taux de remise pour le crédit Partenaires. Par exemple, 0 % ou 15 %. |
| partnerEarnedCreditApplied | Indique si le crédit Partenaires a été appliqué. | N/A |

Dans la vue d’[analyse des coûts](quick-acm-cost-analysis.md), vous pouvez également [enregistrer des vues](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) et exporter des données dans des fichiers [CSV et PNG](quick-acm-cost-analysis.md#automation-and-offline-analysis).

## <a name="view-partner-earned-credit-pec-resource-costs"></a>Voir les coûts des ressources associés au crédit Partenaires

Dans Azure Cost Management, les partenaires peuvent utiliser l’analyse des coûts pour voir les coûts qui ont bénéficié des avantages du crédit Partenaires.

Dans le portail Azure, connectez-vous au locataire partenaire, puis sélectionnez **Gestion des coûts + facturation**. Sous **Gestion des coûts**, cliquez sur **Analyse des coûts**.

La vue d’analyse des coûts présente les coûts du compte de facturation du partenaire. Sous **Étendue**, sélectionnez l’étendue requise pour le partenaire, un client spécifique ou un profil de facturation afin de rapprocher les factures.

Dans un graphique en anneau, cliquez sur la liste déroulante et sélectionnez **PartnerEarnedCreditApplied** pour analyser les coûts relatifs au crédit PEC.

![Exemple illustrant comment voir le crédit Partenaires](./media/get-started-partners/cost-analysis-pec1.png)

Quand la propriété **PartnerEarnedCreditApplied** a la valeur _True_, le coût associé bénéficie de l’accès administrateur pour le crédit Partenaires.

Quand la propriété **PartnerEarnedCreditApplied** a la valeur _False_, le coût associé n’est pas éligible au crédit ou le service acheté n’est pas éligible au crédit Partenaires.

L’affichage des données d’utilisation des services prend généralement entre 8 et 24 heures dans Cost Management. Pour plus d’informations, consultez la section [La fréquence de mise à jour des données d’utilisation varie](understand-cost-mgt-data.md#usage-data-update-frequency-varies). Les crédits PEC s’affichent dans un délai de 48 heures à compter de l’heure d’accès dans Azure Cost Management.


Vous pouvez également grouper et filtrer sur la propriété **PartnerEarnedCreditApplied** à l’aide des options **Grouper par**. Utilisez les options pour examiner les coûts avec et sans crédit PEC.

![Grouper ou filtrer selon le crédit Partenaires](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="cost-management-rest-apis"></a>API REST Cost Management

Les partenaires, les fournisseurs indirects et les clients peuvent utiliser les API Cost Management décrites dans les sections suivantes pour les tâches courantes.

### <a name="azure-cost-management-apis-for-partners"></a>API Azure Cost Management pour les partenaires

Les partenaires et les utilisateurs ayant accès aux étendues de facturation d’un locataire partenaire peuvent utiliser les API suivantes.

#### <a name="to-get-a-list-of-billing-accounts"></a>Pour obtenir une liste des comptes de facturation

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>Pour obtenir une liste des clients

```
armclient get "providers/Microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers?api-version=2019-10-01-preview"
```
#### <a name="to-get-a-list-of-subscriptions"></a>Pour obtenir une liste des abonnements

```
armclient get "/providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/billingSubscriptions?api-version=2019-10-01-preview"
```

#### <a name="to-create-new-subscription"></a>Pour créer un abonnement

```
armclient post "/providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview" @createsub.json -verbose
```

#### <a name="to-get-or-download-usage-for-azure-services"></a>Pour obtenir ou télécharger les données d’utilisation des services Azure

```
armclient GET /providers/Microsoft.Billing/BillingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-get-a-list-of-billing-profiles"></a>Pour obtenir une liste des profils de facturation

```
armclient get "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/billingProfiles?api-version=2019-10-01-preview
```

#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>Pour obtenir ou télécharger la grille tarifaire des services Azure consommés

```
armclient post "/providers/Microsoft.Billing/BillingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/BillingProfiles/JUT6-EU3Q-BG7-TGB/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

#### <a name="to-get-customer-costs-for-the-last-two-months-sorted-by-month"></a>Pour obtenir les coûts client sur les deux derniers mois, triés par mois

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryCustomer.json
```

#### <a name="to-get-azure-subscription-costs-for-the-last-two-months-sorted-by-month"></a>Pour obtenir les coûts d’abonnement Azure sur les deux derniers mois, triés par mois

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQuerySubscription.json
```

#### <a name="to-get-daily-costs-for-the-current-month"></a>Pour obtenir les coûts quotidiens sur le mois en cours

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>Pour obtenir la stratégie permettant aux clients de voir les coûts

```
armclient get "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/policies/default?api-version=2019-10-01-preview"
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>Pour définir la stratégie permettant aux clients de voir les coûts

```
armclient put "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/policies/default?api-version=2019-10-01-preview" @policy.json
```

### <a name="azure-cost-management-apis-for-indirect-providers"></a>API Azure Cost Management pour les fournisseurs indirects

Les fournisseurs indirects disposant d’un accès aux étendues RBAC dans un locataire client peuvent utiliser les API suivantes. Pour commencer, connectez-vous en tant qu’utilisateur ou avec un principal de service.

#### <a name="to-get-the-billing-account-information"></a>Pour obtenir les informations relatives au compte de facturation

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>Pour obtenir une liste des clients

```
armclient get "providers/Microsoft.billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-resellers-associated-with-the-customer"></a>Pour obtenir une liste des revendeurs associés au client

```
armclient get "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db?api-version=2019-10-01-preview&$expand=resellers
```

#### <a name="to-get-a-list-of-subscriptions-with-reseller-information"></a>Pour obtenir une liste des abonnements avec les informations du revendeur

```
armclient get "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-create-a-subscription"></a>Pour créer un abonnement

```
armclient post "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview" @createsub_reseller.json
```

### <a name="azure-cost-management-apis-for-customers"></a>API Azure Cost Management pour les clients

Les clients utilisent les informations suivantes pour accéder aux API. Pour commencer, connectez-vous en tant qu’utilisateur.

#### <a name="to-get-or-download-azure-consumption-usage-information-with-retail-rates"></a>Pour obtenir ou télécharger des informations sur l’utilisation de la consommation Azure avec des tarifs au détail

```
armclient post /subscriptions/66bada28-271e-4b7a-aaf5-c0ead63923d7/providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

## <a name="next-steps"></a>Étapes suivantes
- [Démarrer l’analyse des coûts](quick-acm-cost-analysis.md) dans Cost Management
- [Créer et gérer des budgets](tutorial-acm-create-budgets.md) dans Cost Management
