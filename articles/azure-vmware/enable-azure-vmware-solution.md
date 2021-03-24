---
title: Demander un quota hôte et activer Azure VMware Solution
description: Découvrez comment demander un quota / une capacité d’hôte et activer le fournisseur de ressources Azure VMware Solution. Vous pouvez également demander plus d’hôtes dans un cloud privé Azure VMware Solution.
ms.topic: how-to
ms.custom: contperf-fy21q3
ms.date: 02/17/2021
ms.openlocfilehash: 5d154f5c63ffccdbf1729e641133b54be478d884
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653162"
---
# <a name="request-host-quota-and-enable-azure-vmware-solution"></a>Demander un quota hôte et activer Azure VMware Solution

Dans cette procédure, vous allez découvrir comment demander un quota / une capacité d’hôte et comment activer le fournisseur de ressources [Azure VMware Solution](introduction.md), qui active le service. Avant de pouvoir activer Azure VMware Solution, vous devez soumettre un ticket de support pour que des hôtes vous soient alloués. Si vous avez un cloud privé Azure VMware Solution et que vous voulez que davantage d’hôtes vous soient alloués, vous allez suivre le même processus.

>[!IMPORTANT]
>L’allocation des hôtes peut prendre quelques jours, en fonction du nombre demandé.  Vous devez donc demander le provisionnement de ce qui est nécessaire, de façon à ne pas avoir à demander souvent une augmentation du quota.


Le processus global est simple et comprend deux étapes :
- Demander un quota / une capacité d’hôte supplémentaire pour des [clients Contrat Entreprise](#request-host-quota-for-ea-customers) ou pour des [clients CSP](#request-host-quota-for-csp-customers) 
- Activer le fournisseur de ressources Microsoft.AVS

## <a name="eligibility-criteria"></a>Critères d’éligibilité

Vous devez disposer d’un compte Azure dans un abonnement Azure. L’abonnement Azure doit remplir un des critères suivants :

- Un abonnement régi par un [Contrat Entreprise (EA) Azure](../cost-management-billing/manage/ea-portal-agreements.md) conclu avec Microsoft.
- Un abonnement managé par un fournisseur de solutions cloud (CSP) dans le cadre d’un plan Azure ou d’un contrat d’offres Azure CSP.

## <a name="request-host-quota-for-ea-customers"></a>Demander un quota d’hôte pour des clients Contrat Entreprise

1. Sur votre portail Azure, sous **Aide + Support**, créez une **[nouvelle demande de support](https://rc.portal.azure.com/#create/Microsoft.Support)** et indiquez les informations suivantes pour le ticket :
   - **Type de problème :** Techniques
   - **Abonnement :** Sélectionnez votre abonnement
   - **Service :** Tous les services > Azure VMware Solution
   - **Ressource :** Question générale 
   - **Résumé :** Capacité requise
   - **Type de problème :** Problèmes de gestion de la capacité
   - **Sous-type de problème :** Demande client de capacité/quota d’hôtes supplémentaire

1. Dans la **description** du ticket de support, indiquez les informations suivantes sous l’onglet **Détails** :

   - POC ou Production 
   - Nom de la région
   - Nombre d’hôtes
   - Autres détails

   >[!NOTE]
   >Azure VMware Solution recommande un minimum de trois hôtes pour mettre en place votre cloud privé et pour la redondance des hôtes N+1. 

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
   
   1. Dans le portail Azure, sous **Aide + Support**, créez une **[Nouvelle demande de support](https://rc.portal.azure.com/#create/Microsoft.Support)** et fournissez les informations suivantes pour le ticket :
      - **Type de problème :** Techniques
      - **Abonnement :** Sélectionnez votre abonnement
      - **Service :** Tous les services > Azure VMware Solution
      - **Ressource :** Question générale 
      - **Résumé :** Capacité requise
      - **Type de problème :** Problèmes de gestion de la capacité
      - **Sous-type de problème :** Demande client de capacité/quota d’hôtes supplémentaire
   
   1. Dans la **description** du ticket de support, indiquez les informations suivantes sous l’onglet **Détails** :
   
      - POC ou Production 
      - Nom de la région
      - Nombre d’hôtes
      - Autres détails
      - Est destiné à héberger plusieurs clients ?
   
      >[!NOTE]
      >Azure VMware Solution recommande un minimum de trois hôtes pour mettre en place votre cloud privé et pour la redondance des hôtes N+1. 
   
   1. Sélectionnez **Vérifier + Créer** pour envoyer la demande.

## <a name="register-the-microsoftavs-resource-provider"></a>Inscrire le fournisseur de ressources **Microsoft.AVS**

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="next-steps"></a>Étapes suivantes

Après avoir activé la ressource, le réseau approprié étant en place, vous pouvez [créer un cloud privé](tutorial-create-private-cloud.md).
