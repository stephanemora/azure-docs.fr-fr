---
title: Connecter plusieurs clouds privés Azure VMware Solution dans la même région
description: Découvrez comment créer une connexion réseau entre deux clouds privés Azure VMware Solution ou plus situés dans la même région.
ms.topic: how-to
ms.date: 09/20/2021
ms.openlocfilehash: daa4b60c0e42c77f61054d6d9a77898cc0448c37
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128567838"
---
# <a name="connect-multiple-azure-vmware-solution-private-clouds-in-the-same-region"></a>Connecter plusieurs clouds privés Azure VMware Solution dans la même région

La fonctionnalité d’**interconnexion AVS** vous permet de créer une connexion réseau entre deux clouds privés Azure VMware Solution ou plus situés dans la même région. Elle crée un lien de routage entre les réseaux de gestion et de charge de travail des clouds privés pour permettre la communication réseau entre les clouds.

Vous pouvez connecter un cloud privé à plusieurs clouds privés de manière non transitive. Par exemple, si le _cloud privé 1_ est connecté au _cloud privé 2_ et que le _cloud privé 2_ est connecté au _cloud privé 3_, les clouds privés 1 et 3 ne peuvent pas communiquer tant qu’ils ne sont pas directement connectés.

Vous pouvez uniquement connecter des clouds privés situés dans la même région. Pour connecter des clouds privés situés dans des régions différentes, [utilisez ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) afin de les connecter de la même façon que vous connectez votre cloud privé à votre circuit local. 

## <a name="supported-regions"></a>Régions prises en charge

La fonctionnalité d’interconnexion AVS est disponible dans toutes les régions sauf USA Centre Sud (SAT20), Europe Nord (DUB21), Asie Sud-Est (SG2) et Royaume-Uni Ouest (CWL20). 

## <a name="prerequisites"></a>Configuration requise

- Accès en écriture à chaque cloud privé que vous connectez
- Les espaces d’adressage IP routés dans chaque cloud sont uniques et ne se chevauchent pas

>[!NOTE]
>La fonctionnalité d’**interconnexion AVS** ne vérifie pas le chevauchement d’espace IP comme le fait l’appairage de réseau virtuel Azure natif avant la création de l’appairage. Il vous incombe donc de vérifier qu’il n’y a pas de chevauchement entre les clouds privés.
>
>Dans les environnements Azure VMware Solution, il est possible de configurer des déploiements IP non routés et qui se chevauchent sur des segments NSX qui ne sont pas routés vers Azure.  Ils n’entraînent pas de problèmes avec la fonctionnalité d’interconnexion AVS, car ils sont routés uniquement entre les NSX T0 de chaque cloud privé.


## <a name="add-connection-between-private-clouds"></a>Ajouter une connexion entre des clouds privés

1. Dans votre cloud privé Azure VMware Solution, sous **Gérer**, sélectionnez **Connectivité**.

2. Sélectionnez l’onglet **Interconnexion AVS**, puis **Ajouter**.

   :::image type="content" source="media/networking/private-cloud-to-private-cloud-no-connections.png" alt-text="Capture d’écran montrant l’onglet Interconnexion AVS sous Connectivité." border="true" lightbox="media/networking/private-cloud-to-private-cloud-no-connections.png":::

3. Sélectionnez les informations et le cloud privé Azure VMware Solution pour la nouvelle connexion.

   >[!NOTE]
   >Vous pouvez vous connecter uniquement à des clouds privés situés dans la même région. Pour vous connecter à des clouds privés situés dans des régions différentes, [utilisez ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) afin de les connecter de la même façon que vous connectez votre cloud privé à votre circuit local. 

   :::image type="content" source="media/networking/add-connection-to-other-private-cloud.png" alt-text="Capture d’écran montrant les informations nécessaires pour ajouter une connexion à un autre cloud privé." border="true":::


4. Activez la case à cocher **Je confirme** pour confirmer qu’il n’y a pas d’espaces IP routés qui se chevauchent dans les deux clouds privés. 

5. Sélectionnez **Create** (Créer).  Vous pouvez vérifier l’état de la création de la connexion.

   :::image type="content" source="media/networking/add-connection-to-other-private-cloud-notification.png" alt-text="Capture d’écran montrant les informations de notification pour la connexion en cours et une connexion existante." border="true":::

   Toutes vos connexions s’affichent sous **Cloud privé AVS**.
   
   :::image type="content" source="media/networking/private-cloud-to-private-cloud-two-connections.png" alt-text="Capture d’écran montrant l’onglet Interconnexion AVS sous Connectivité et deux connexions de cloud privé établies." border="true" lightbox="media/networking/private-cloud-to-private-cloud-two-connections.png":::


## <a name="remove-connection-between-private-clouds"></a>Supprimer une connexion entre des clouds privés

1. Dans votre cloud privé Azure VMware Solution, sous **Gérer**, sélectionnez **Connectivité**.

2. À droite de la connexion que vous souhaitez supprimer, sélectionnez **Supprimer** (corbeille), puis **Oui**.


## <a name="next-steps"></a>Étapes suivantes

Vous avez connecté plusieurs clouds privés dans la même région. À présent, vous souhaitez peut-être en savoir plus sur les opérations suivantes :

- [Déplacer des ressources Azure VMware Solution vers une autre région](move-azure-vmware-solution-across-regions.md)
- [Déplacer l’abonnement Azure VMware Solution vers un autre abonnement](move-ea-csp-subscriptions.md)
