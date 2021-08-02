---
title: Demander un quota hôte pour Azure VMware Solution
description: Découvrez comment demander un quota ou une capacité d’hôte pour Azure VMware Solution. Vous pouvez également demander plus d’hôtes dans un cloud privé Azure VMware Solution.
ms.topic: how-to
ms.custom: contperf-fy21q3
ms.date: 05/13/2021
ms.openlocfilehash: ceb32d7e09e6c595a6cddf844c713093253b8994
ms.sourcegitcommit: ef950cf37f65ea7a0f583e246cfbf13f1913eb12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111421649"
---
# <a name="request-host-quota-for-azure-vmware-solution"></a>Demander un quota hôte pour Azure VMware Solution

Dans le cadre de ce guide pratique, vous allez demander un quota ou une capacité d’hôte pour [Azure VMware Solution](introduction.md). Vous allez soumettre un ticket de support pour que vos hôtes soient alloués. Si vous avez un cloud privé Azure VMware Solution et que vous voulez que davantage d’hôtes vous soient alloués, vous allez suivre le même processus.

>[!IMPORTANT]
>L’allocation des hôtes peut prendre quelques jours, en fonction du nombre demandé.  Demandez donc l’approvisionnement nécessaire, de façon à ne pas devoir demander souvent une augmentation du quota.

## <a name="eligibility-criteria"></a>Critères d’éligibilité

Vous devez disposer d’un compte Azure dans un abonnement Azure. L’abonnement Azure doit remplir un des critères suivants :

- Un abonnement régi par un [Contrat Entreprise (EA) Azure](../cost-management-billing/manage/ea-portal-agreements.md) conclu avec Microsoft.
- Un abonnement managé par un fournisseur de solutions cloud (CSP) dans le cadre d’un plan Azure ou d’un contrat d’offres Azure CSP.

## <a name="request-host-quota-for-ea-customers"></a>Demander un quota d’hôte pour des clients Contrat Entreprise

1. Dans votre portail Azure, sous **Aide et support**, créez une **[Nouvelle demande de support](https://rc.portal.azure.com/#create/Microsoft.Support)** et fournissez les informations suivantes :
   - **Type de problème :** Techniques
   - **Abonnement :** Sélectionnez votre abonnement
   - **Service :** Tous les services > Azure VMware Solution
   - **Ressource :** Question générale 
   - **Résumé :** Capacité requise
   - **Type de problème :** Problèmes de gestion de la capacité
   - **Sous-type de problème :** Demande client de capacité/quota d’hôtes supplémentaire

1. Dans la **Description** du ticket de support, sous l’onglet **Détails**, fournissez les informations suivantes :

   - POC ou Production 
   - Nom de la région
   - Nombre d’hôtes
   - Autres détails

   >[!NOTE]
   >Le service Azure VMware Solution requiert au minimum trois hôtes, et recommande une redondance de N+1 hôtes. 

1. Sélectionnez **Vérifier + Créer** pour envoyer la demande.


## <a name="request-host-quota-for-csp-customers"></a>Demander un quota d’hôte pour des clients CSP 

Les fournisseurs de solutions Cloud doivent utiliser l’[Espace partenaires Microsoft](https://partner.microsoft.com) pour activer Azure VMware Solution pour leurs clients. Cet article utilise le [plan Azure CSP](/partner-center/azure-plan-lp) comme exemple afin d’illustrer la procédure d’achat pour les partenaires.

Accédez au portail Azure en utilisant la procédure **Administrateur pour le compte de** depuis l’Espace partenaires.

>[!IMPORTANT] 
>Le service Azure VMware Solution ne fournit pas l’architecture mutualisée requise. Les partenaires d’hébergement qui en ont besoin ne sont pas pris en charge. 

1. Configurez le plan Azure CSP :

   1. Dans l’**Espace partenaires**, sélectionnez **CSP** pour accéder à la zone **Clients** .
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Zone Clients de l’Espace partenaires Microsoft" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::
   
   1. Sélectionnez votre client, puis **Ajouter des produits**.
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Espace partenaires Microsoft" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::
   
   1. Sélectionnez **Plan Azure**, puis **Ajouter au panier**. 
   
   1. Passez en revue et terminez la configuration générale de l’abonnement au plan Azure pour votre client. Pour plus d’informations, consultez la [Documentation de l’Espace partenaires Microsoft](/partner-center/azure-plan-manage).

1. Une fois le plan Azure configuré et les [autorisations Azure RBAC](/partner-center/azure-plan-manage) nécessaires en place pour l’abonnement, vous demandez le quota pour l’abonnement de votre plan Azure. 

   1. Accédez au portail Azure à partir de l’[Espace partenaires Microsoft](https://partner.microsoft.com) en utilisant la procédure **Administrateur pour le compte de**.
   
   1. Sélectionnez **CSP** pour accéder à la zone **Clients**.
   
   1. Développez les détails du client, puis sélectionnez **Portail de gestion Microsoft Azure**.
   
   1. Dans le portail Azure, sous **Aide et support**, créez une **[Nouvelle demande de support](https://rc.portal.azure.com/#create/Microsoft.Support)** et fournissez les informations suivantes :
      - **Type de problème :** Techniques
      - **Abonnement :** Sélectionnez votre abonnement
      - **Service :** Tous les services > Azure VMware Solution
      - **Ressource :** Question générale 
      - **Résumé :** Capacité requise
      - **Type de problème :** Problèmes de gestion de la capacité
      - **Sous-type de problème :** Demande client de capacité/quota d’hôtes supplémentaire
   
   1. Dans la **Description** du ticket de support, sous l’onglet **Détails**, fournissez les informations suivantes :
   
      - POC ou Production 
      - Nom de la région
      - Nombre d’hôtes
      - Autres détails
      - Est destiné à héberger plusieurs clients ?
   
      >[!NOTE]
      >Le service Azure VMware Solution requiert au minimum trois hôtes, et recommande une redondance de N+1 hôtes. 
   
   1. Sélectionnez **Vérifier + Créer** pour envoyer la demande.


## <a name="next-steps"></a>Étapes suivantes

Avant de pouvoir déployer le service VMware Azure Solution, vous devez [inscrire le fournisseur de ressources](deploy-azure-vmware-solution.md#step-1-register-the-microsoftavs-resource-provider) auprès de votre abonnement pour activer le service.   

