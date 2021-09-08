---
author: cherylmc
ms.author: cherylmc
ms.date: 08/17/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 563de3b6c57f38d63f2759c62fccc5a17d17dae9
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778472"
---
1. Accédez à votre **WAN virtuel**.

1. Sélectionnez **Connexions de réseau virtuel**.

1. Dans la page de connexion de réseau virtuel, sélectionnez **+ Ajouter une connexion**.

   :::image type="content" source="./media/virtual-wan-connect-vnet-hub/add.png" alt-text="Capture d’écran montrant un ajout.":::

1. Dans la page **Ajouter une connexion**, configurez les paramètres nécessaires. Pour plus d’informations sur le type de routage, consultez [À propos du routage](../articles/virtual-wan/about-virtual-hub-routing.md).
 
   :::image type="content" source="./media/virtual-wan-connect-vnet-hub/connection.png" alt-text="Capture d’écran montrant la page de connexion au VNet.":::

   * **Nom de la connexion**: nommez votre connexion.
   * **Hubs** : sélectionnez le hub à associer à cette connexion.
   * **Abonnement**: Vérifiez l’abonnement.
   * **Groupe de ressources** : groupe de ressources qui contient le VNet.
   * **Réseau virtuel** : sélectionnez le réseau virtuel à connecter à ce hub. Le réseau virtuel que vous sélectionnez ne doit pas comporter de passerelle de réseau virtuel.
   * **Propagate to none** (Propager à aucun) : par défaut, la valeur est **Non**. Si vous changez le commutateur en choisissant **Oui**, les options de configuration relatives à **Propagate to Route Tables** (Propager aux tables de routage) et **Propagate to labels** (Propager aux étiquettes) ne sont pas disponibles.
   * **Associate Route Table** (Associer une table de routage) : vous pouvez sélectionner la table de routage à associer.
   * **Static routes** (Routes statiques) : vous pouvez utiliser ce paramètre pour spécifier le tronçon suivant.

1. Une fois que vous avez fini de configurer les paramètres souhaités, sélectionnez **Créer** pour créer la connexion.