---
title: Comment activer votre ressource Azure VMware Solution
description: Découvrez comment envoyer une demande de support pour activer votre ressource Azure VMware Solution. Vous pouvez également demander plus de nœuds dans votre cloud privé Azure VMware Solution.
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: c9c31d8ffbf62a511f4f2ab19f995b6bc0ee0ca4
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695144"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Comment activer la ressource Azure VMware Solution
Découvrez comment envoyer une demande de support pour activer votre ressource [Azure VMware Solution](introduction.md). Vous pouvez également demander plus de nœuds dans votre cloud privé Azure VMware Solution.

## <a name="eligibility-criteria"></a>Critères d’éligibilité

Vous devez disposer d’un compte Azure dans un abonnement Azure. L’abonnement Azure doit être conforme à l’un des critères suivants :

* Un abonnement régi par un [Contrat Entreprise (EA) Azure](../cost-management-billing/manage/ea-portal-agreements.md) conclu avec Microsoft.
* Un abonnement géré par un fournisseur de solutions Cloud (CSP) dans le cadre d’un plan Azure.


## <a name="enable-azure-vmware-solution-for-ea-customers"></a>Activer Azure VMware Solution pour les clients EA
Avant de créer votre ressource Azure VMware Solution, vous devez soumettre un ticket de support pour que des nœuds vous soient alloués. Une fois que l’équipe du support technique reçoit votre demande, il lui faut jusqu’à cinq jours ouvrés pour confirmer votre requête et vous allouer des nœuds. Si vous disposez d’un cloud privé Azure VMware Solution et que vous souhaitez que davantage de nœuds vous soient alloués, vous passerez par le même processus.


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
   - Nombre de nœuds
   - Autres détails

   >[!NOTE]
   >Azure VMware Solution recommande un minimum de trois nœuds pour mettre en place votre cloud privé et pour la redondance des nœuds N+1. 

1. Sélectionnez **Vérifier + Créer** pour envoyer la demande.

   Il faut compter jusqu’à cinq jours ouvrés pour qu’un représentant du support technique confirme votre demande.

   >[!IMPORTANT] 
   >Si vous disposez déjà d’une instance Azure VMware Solution et que vous demandez des nœuds supplémentaires, veuillez noter que cinq jours ouvrés sont nécessaires pour allouer les nœuds. 

1. Avant d’approvisionner vos nœuds, veillez à inscrire le fournisseur de ressources **Microsoft.AVS** sur le portail Azure.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Pour d’autres façons d’inscrire un fournisseur de ressources, consultez [Fournisseurs et types de ressources Azure](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="enable-azure-vmware-solution-for-csp-customers"></a>Activer Azure VMware Solution pour les clients CSP 

Les fournisseurs de solutions Cloud doivent utiliser l’[Espace partenaires Microsoft](https://partner.microsoft.com) pour activer Azure VMware Solution pour leurs clients. 

   >[!IMPORTANT] 
   >Le service Azure VMware Solution ne fournissant pas d’environnement mutualisé, les partenaires d’hébergement ne sont pas encore pris en charge. 

1. Dans l’**Espace partenaires**, sélectionnez **CSP** pour accéder à la zone **Clients** .

   :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Zone Clients de l’Espace partenaires Microsoft" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::

1. Sélectionnez votre client, puis **Ajouter des produits**.

   :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Espace partenaires Microsoft" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::

1. Sélectionnez **Plan Azure**, puis **Ajouter au panier**. 

1. Passez en revue et terminez la configuration générale de l’abonnement au plan Azure pour votre client. Pour plus d’informations, consultez la [Documentation de l’Espace partenaires Microsoft](https://docs.microsoft.com/partner-center/azure-plan-manage).

Une fois le plan Azure configuré et les autorisations RBAC nécessaires en place en tant que CSP, vous devez contacter Microsoft afin d’activer le quota pour un abonnement à un plan Azure. Accédez au portail Azure à partir de l’Espace partenaires à l’aide de la procédure **Administrateur pour le compte de**.

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com).

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
   - Nombre de nœuds
   - Autres détails
   - Est destiné à héberger plusieurs clients ?

   >[!NOTE]
   >Azure VMware Solution recommande un minimum de trois nœuds pour mettre en place votre cloud privé et pour la redondance des nœuds N+1. 

1. Sélectionnez **Vérifier + Créer** pour envoyer la demande.

   Il faut compter jusqu’à cinq jours ouvrés pour qu’un représentant du support technique confirme votre demande.

   >[!IMPORTANT] 
   >Si vous disposez déjà d’une instance Azure VMware Solution et que vous demandez des nœuds supplémentaires, veuillez noter que cinq jours ouvrés sont nécessaires pour allouer les nœuds. 

1. Une fois ajouté au plan Azure et doté d’un quota activé, le client ou l’administrateur partenaire peuvent déployer un cloud privé Azure VMware Solution via le portail Azure. Avant d’approvisionner vos nœuds, veillez à inscrire le fournisseur de ressources **Microsoft.AVS** sur le portail Azure.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Pour d’autres façons d’inscrire un fournisseur de ressources, consultez [Fournisseurs et types de ressources Azure](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez activé votre ressource Azure VMware Solution et que vous disposez de la mise en réseau appropriée, vous pouvez [créer un cloud privé](tutorial-create-private-cloud.md).