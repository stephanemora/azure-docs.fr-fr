---
title: Comment activer votre ressource Azure VMware Solution
description: Découvrez comment envoyer une demande de support pour activer votre ressource Azure VMware Solution. Vous pouvez également demander plus de nœuds dans votre cloud privé Azure VMware Solution.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 72a7aca97067ce4e9ed0e901e4016c82b3549eb1
ms.sourcegitcommit: 0fd1f3fe7817ad44d878d580ec167e1508051795
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2020
ms.locfileid: "90817880"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Comment activer la ressource Azure VMware Solution
Découvrez comment envoyer une demande de support pour activer votre ressource Azure VMware Solution. Vous pouvez également demander plus de nœuds dans votre cloud privé Azure VMware Solution.

## <a name="eligibility-criteria"></a>Critères d’éligibilité

* Vous aurez besoin d’un [Contrat Entreprise (EA) Azure](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-agreements) avec Microsoft.
* Vous devez disposer d’un compte Azure dans un abonnement Azure.


## <a name="enable-azure-vmware-solution-resource"></a>Activer la ressource Azure VMware Solution
Avant de créer votre ressource Azure VMware Solution, vous devez soumettre un ticket de support pour que des nœuds vous soient alloués. Une fois que l’équipe du support technique reçoit votre demande, il lui faut jusqu’à cinq jours ouvrés pour confirmer votre demande et vous allouer des nœuds. Si vous disposez d’un cloud privé Azure VMware Solution et que vous souhaitez que davantage de nœuds vous soient alloués, vous passerez par le même processus.


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