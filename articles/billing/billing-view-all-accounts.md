---
title: Afficher tous vos comptes de facturation dans le portail Azure | Microsoft Docs
description: Découvrez comment afficher vos comptes de facturation dans le portail Azure.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 952cf89a4e1f6c5ed82a817b81d32b61ab673fe4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449255"
---
# <a name="billing-accounts-and-scopes-in-the-azure-portal"></a>Comptes et étendues de facturation dans le portail Azure

Un compte de facturation est créé lorsque vous vous inscrivez pour utiliser Azure. Votre compte de facturation vous permet de gérer vos factures et paiements, et de suivre les coûts. Vous pouvez accéder à plusieurs comptes de facturation. Par exemple, vous pouvez vous être inscrit à Azure pour vos projets personnels. Vous pouvez aussi avoir accès à Azure via le Contrat Entreprise de votre organisation ou le Contrat client Microsoft. Pour chacun de ces scénarios, vous disposez d’un compte de facturation distinct.

Le portail Azure prend en charge le type suivant de comptes de facturation :

- **Programme Microsoft Online Services** : Un compte de facturation pour le Programme Microsoft Online Services est créé lorsque vous souscrivez à Azure via le site web Azure. Par exemple, quand vous souscrivez à un [compte gratuit Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), un [compte avec tarifs de paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) ou en tant qu’[abonné Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Contrat Entreprise** : Un compte de facturation pour un Contrat Entreprise est créé quand votre organisation signe un [Contrat Entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) pour utiliser Azure.

- **Contrat client Microsoft** : Un compte de facturation pour un Contrat client Microsoft est créé lorsque votre organisation signe un Contrat client Microsoft avec un commercial Microsoft. Des clients dans certaines régions, qui s’inscrivent via le site web Azure pour un [compte avec tarifs de paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) ou un [compte gratuit Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) peuvent aussi avoir un compte de facturation pour un Contrat client Microsoft. Pour plus d’informations, voir [Prise en main de votre compte de facturation dans le cadre d’un Contrat client Microsoft](billing-mca-overview.md).

- **Contrat Partenaire Microsoft** : Un compte de facturation pour un Contrat Partenaire Microsoft est créé pour les partenaires du fournisseur de solutions cloud (CSP) afin de gérer leurs clients dans la nouvelle expérience de commerce. Les partenaires doivent avoir au moins un client avec un [plan Azure](https://docs.microsoft.com/partner-center/purchase-azure-plan) pour gérer leur compte de facturation dans le portail Azure. Pour plus d’informations, consultez [Prise en main de votre compte de facturation dans le cadre d’un Contrat Partenaire Microsoft](mpa-overview.md).

Pour déterminer le type de votre compte de facturation, voir [Vérifier le type de votre compte de facturation](#check-the-type-of-your-account).

## <a name="scopes-for-billing-accounts"></a>Étendues pour les comptes de facturation
Une étendue est un nœud à l’intérieur d’un compte de facturation que vous utilisez pour afficher et gérer la facturation. C’est là que vous gérez les données de facturation, les paiements, les factures, et effectuez la gestion des comptes de manière générale. 

### <a name="microsoft-online-services-program"></a>Programme Microsoft Online Services

 ![Capture d’écran montrant la hiérarchie MOSP](./media/billing-view-all-accounts/mosp-hierarchy.png)

|Étendue  |Définition  |
|---------|---------|
|Compte de facturation     | Représente un contrat qu’un client accepte pour utiliser Azure. Il contient un ou plusieurs abonnements.  |
|Subscription     |  Représente un regroupement de ressources Azure. La facture est générée dans cette étendue. D’autres informations de facturation telles que les modes de paiement et l’adresse d’utilisation sont associées à cette étendue.|

### <a name="enterprise-agreement"></a>Contrat Entreprise

![Capture d’écran montrant la hiérarchie EA](./media/billing-view-all-accounts/ea-hierarchy.png)

|Étendue  |Définition  |
|---------|---------|
|Compte de facturation    | Représente l’inscription à un Contrat Entreprise. Il contient un ou plusieurs services et comptes. La facture est générée dans cette étendue. |
|department     |  Regroupement facultatif de comptes pour segmenter les coûts en regroupements logiques et définir le budget.     |
|Compte     |  Représente un seul propriétaire de compte. Les propriétaires de comptes disposent d’autorisations pour créer et gérer des abonnements Azure qui sont facturés à l’inscription. |

### <a name="microsoft-customer-agreement"></a>Contrat client Microsoft

![Capture d’écran montrant la hiérarchie MCA](./media/billing-view-all-accounts/mca-hierarchy.png)

|Étendue  |Tâches  |
|---------|---------|
|Compte de facturation     |   Représente un contrat qu’un client accepte pour utiliser des produits et services Microsoft. Il contient un ou plusieurs profils de facturation. |
|Profil de facturation     |   Représente une facture et les informations de facturation associées, telles que les modes de paiement et l’adresse de facturation. Il contient une ou plusieurs sections de facture. |
|Section de facture     |   Représente un regroupement de coûts dans une facture. Les abonnements Azure et d’autres achats tels que les produits de la Place de marché Azure et AppSource sont associés à cette étendue.    |

### <a name="microsoft-partner-agreement"></a>Contrat Partenaire Microsoft

![Capture d’écran montrant la hiérarchie MPA](./media/billing-view-all-accounts/mpa-hierarchy.png)

|Étendue  |Tâches  |
|---------|---------|
|Compte de facturation     |   Représente un Contrat Partenaire pour gérer les produits et services Microsoft des clients dans la nouvelle expérience de commerce. Il contient un ou plusieurs profils de facturation et clients.   |
|Profil de facturation     |   Représente une facture pour une devise.     |
|Customer    |   Représente un client pour un partenaire du fournisseur de solutions cloud (CSP).  Les abonnements Azure et d’autres achats tels que les produits de la Place de marché Azure et AppSource sont associés à cette étendue.  |
|Reseller    |   Revendeur qui fournit des services à un client. Il s’agit d’un champ facultatif pour un abonnement, qui s’applique uniquement aux fournisseurs indirects dans le modèle CSP à deux niveaux.     |

## <a name="switch-billing-scope-in-the-azure-portal"></a>Basculement d’étendue de facturation dans le Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Recherchez **Gestion des coûts + facturation**.

   ![Capture d’écran montrant la recherche dans le portail Azure](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. Dans la page Vue d’ensemble, sélectionnez **Changer d’étendue**.

   ![Capture d’écran montrant les étendues de facturation](./media/billing-view-all-accounts/overview-select-scopes.png)

   > [!Note]
    >
    > Vous ne verrez pas Changer d’étendue si vous n’avez accès qu’à une étendue.

4. Sélectionnez une étendue pour afficher les détails.

   ![Capture d’écran montrant les étendues de facturation](./media/billing-view-all-accounts/list-of-scopes.png)

## <a name="check-the-type-of-your-account"></a>Vérifier le type de votre compte
[!INCLUDE [billing-check-mca](../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment commencer à [analyser vos coûts](../cost-management/quick-acm-cost-analysis.md).