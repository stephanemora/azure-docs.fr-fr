---
title: Afficher tous vos comptes de facturation dans le portail Azure | Microsoft Docs
description: Découvrez comment afficher vos comptes de facturation dans le portail Azure.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2018
ms.author: banders
ms.openlocfilehash: 36430e9b0a4554761d53b537d3c32fa57068eabb
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490214"
---
# <a name="view-billing-accounts-in-azure-portal"></a>Afficher les comptes de facturation dans le Portail Azure  

Un compte de facturation est créé lorsque vous vous inscrivez pour utiliser Azure. Votre compte de facturation vous permet de gérer vos factures et paiements, et de suivre les coûts. Vous pouvez accéder à plusieurs comptes de facturation. Par exemple, vous pouvez vous être inscrit à Azure pour vos projets personnels. Vous pouvez aussi avoir accès à Azure via le Contrat Entreprise de votre organisation ou le Contrat client Microsoft. Pour chacun de ces scénarios, vous disposez d’un compte de facturation distinct.

Le portail Azure prend actuellement en charge le type suivant de comptes de facturation :

- **Programme Microsoft Online Services** : Un compte de facturation pour le Programme Microsoft Online Services est créé lorsque vous souscrivez à Azure via le site web Azure. Par exemple, quand vous souscrivez à un [compte gratuit Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), un [compte avec tarifs de paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) ou en tant qu’[abonné Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Contrat Entreprise** : Un compte de facturation pour un Contrat Entreprise est créé quand votre organisation signe un [Contrat Entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) pour utiliser Azure.

- **Contrat client Microsoft** : Un compte de facturation pour un Contrat client Microsoft est créé lorsque votre organisation signe un Contrat client Microsoft avec un commercial Microsoft. Des clients dans certaines régions, qui s’inscrivent via le site web Azure pour un [compte avec tarifs de paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) ou mettent à niveau leur [compte gratuit Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), peuvent avoir un compte de facturation pour un Contrat client Microsoft. Pour plus d’informations, voir [Prise en main de votre compte de facturation dans le cadre d’un Contrat client Microsoft](billing-mca-overview.md).

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>Étendues pour les comptes de facturation
Une étendue est un nœud à l’intérieur d’un compte de facturation que les utilisateurs utilisent pour afficher et gérer la facturation. C’est là que les utilisateurs gèrent les données de facturation, les paiements, les factures et réalisent la gestion du compte général. 

### <a name="microsoft-online-services-program"></a>Programme Microsoft Online Services

|Étendue  |Définition  |
|---------|---------|
|Compte de facturation     | Représente un seul propriétaire (administrateur de compte) pour un ou plusieurs abonnements Azure. Un administrateur de compte est autorisé à effectuer diverses tâches de facturation comme créer des abonnements, afficher des factures ou modifier la facturation des abonnements.  |
|Abonnement     |  Représente un regroupement de ressources Azure. La facture est générée dans cette étendue. Il a ses propres modes de paiement qui permettent de régler la facture associée.|


### <a name="enterprise-agreement"></a>Contrat Entreprise

|Étendue  |Définition  |
|---------|---------|
|Compte de facturation    | Représente l’inscription à un Contrat Entreprise. La facture est générée dans cette étendue. Il est structuré à l’aide des services et comptes d’inscription.  |
|department     |  Regroupement facultatif des comptes d’inscription.      |
|Compte d’inscription     |  Représente un seul propriétaire de compte. Les abonnements Azure sont créés dans cette étendue.  |


### <a name="microsoft-customer-agreement"></a>Contrat client Microsoft

|Étendue  |Tâches  |
|---------|---------|
|Compte de facturation     |   Représente un contrat client pour plusieurs produits et services Microsoft. Il est structuré à l’aide de profils de facturation et de sections de facture.   |
|Profil de facturation     |  Représente une facture et ses modes de paiement. La facture est générée dans cette étendue. Il peut avoir plusieurs sections de facture.      |
|Section de facture     |   Représente un groupe de coûts dans une facture. Les abonnements et autres achats sont associés à cette étendue.    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>Basculement d’étendue de facturation dans le Portail Azure


1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Recherchez **Gestion des coûts + facturation**.

   ![Capture d’écran montrant la recherche dans le portail Azure](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. Sélectionnez **Toutes les étendues de facturation** dans la partie gauche.

   ![Capture d’écran montrant toutes les étendues de facturation](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   ** Vous ne verrez pas **Toutes les étendues de facturation** si vous n’avez accès qu’à une étendue uniquement.

4. Sélectionnez une étendue pour afficher les détails.



## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment commencer à [analyser vos coûts](../cost-management/quick-acm-cost-analysis.md).