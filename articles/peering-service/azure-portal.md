---
title: Créer une connexion Azure Peering Service – Portail Azure
description: Découvrez comment créer une connexion Azure Peering Service à partir du portail Azure.
services: peering-service
author: gthareja
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 04/07/2021
ms.author: gatharej
ms.openlocfilehash: b75c0ec5be4ac26189a0e847a58cbe81461c5ff2
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952457"
---
# <a name="create-peering-service-connection-using-the-azure-portal"></a>Créer une connexion Peering Service à partir du portail Azure

Azure Peering Service est un service réseau qui améliore la connectivité des clients avec les services de cloud public Microsoft, tels que Microsoft 365, Dynamics 365, les services SaaS (Software as a Service), Azure ou les services Microsoft accessibles via l’Internet public.

Dans cet article, vous allez découvrir comment créer une connexion Peering Service à partir du portail Azure.

Si vous n’avez pas d’abonnement Azure, créez un [compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) maintenant.

> 

## <a name="prerequisites"></a>Prérequis

Vous devez disposer des éléments suivants :

### <a name="azure-account"></a>Compte Azure

Vous devez avoir un compte Microsoft Azure actif et valide. Sinon, vous ne pourrez pas configurer la connexion Peering Service. La connexion Peering Service est une ressource intégrée aux abonnements Azure. 

### <a name="connectivity-provider"></a>Fournisseur de connectivité

Vous pouvez faire appel à un [fournisseur Azure Peering Service](./location-partners.md) pour obtenir une connectivité Peering Service qui vous permettra de connecter votre réseau au réseau Microsoft de manière optimale.




## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Dans un navigateur, accédez au portail Azure et connectez-vous avec votre compte Azure.

## <a name="create-a-peering-service-connection"></a>Créer une connexion Peering Service

1. Pour créer une connexion Peering Service, sélectionnez  **Créer une ressource** > **Peering Service**.

    ![Créer une connexion Peering Service](./media/peering-service-portal/peering-servicecreate.png)

1. Entrez les informations suivantes sous l’onglet **Basics** (Général) dans la page **Create a peering service connection** (Créer une connexion Peering Service).

 
1. Sélectionnez l’abonnement et le groupe de ressources associé à l’abonnement.

   ![Onglet Informations de base dans la fenêtre de création de peering](./media/peering-service-portal/peering-servicebasics.png)

1. Entrez le **nom** sous lequel vous voulez inscrire l’instance Peering Service.
 
1. À présent, sélectionnez le bouton **Suivant : Configuration** au bas de la page. La page **Configuration** s’affiche.

## <a name="configure-the-peering-service-connection"></a>Configurer la connexion Peering Service

1. Dans la page **Configuration**, choisissez l’emplacement réseau du client où Peering Service doit être activé en le sélectionnant dans la liste déroulante **Peering service location** (Emplacement du service de peering). 

1. Sélectionnez le fournisseur de services auprès duquel obtenir Peering Service, en sélectionnant un nom de fournisseur dans la liste déroulante **Peering service provider** (Fournisseur Peering Service).

1. Sélectionnez l’**emplacement principal de peering du fournisseur** le plus proche de l’emplacement réseau du client. Il s’agit de l’emplacement du service de peering entre Microsoft et le partenaire.

1. Sélectionnez l’**emplacement de peering de secours du fournisseur** comme étant le deuxième emplacement réseau du client le plus proche. Les services de peering ne seront actifs via l’emplacement de secours qu’en cas de défaillance de l’emplacement principal du service de peering pour la reprise d’activité. Si l’option « None » (Aucun) est sélectionnée, Internet sera la route de basculement par défaut en cas d’échec de l’emplacement principal du service de peering.

 
1. Sélectionnez **Create new prefix** (Créer un préfixe) en bas de la section **Prefixes** (Préfixes). Plusieurs zones de texte s’affichent. À présent, entrez le nom de la ressource de préfixe ainsi que les préfixes associés au fournisseur de services.

1. Sélectionnez **Prefix Key** (Clé de préfixe) et ajoutez la clé de préfixe attribuée par votre fournisseur (ISP ou IXP). Cette clé permet à Microsoft de valider le préfixe et le fournisseur qui a alloué votre préfixe IP.
   > ![La capture d’écran montre l’onglet Configuration de la page Créer une connexion au service de peering, où vous pouvez entrer la clé de préfixe.](./media/peering-service-portal/peering-serviceconfiguration.png)

1. Sélectionnez le bouton **Vérifier + créer** en bas à gauche de la page. La page **Vérifier + créer** apparaît, et Azure valide votre configuration.
    

1. Quand vous voyez le message **Validation réussie**, sélectionnez **Créer**.

   > ![La capture d’écran montre l’onglet Vérifier + créer de la page Créer une connexion au service de peering.](./media/peering-service-portal/peering-service-prefix.png)


1. Une fois que vous avez créé une connexion Peering Service, une validation supplémentaire est effectuée au niveau des préfixes inclus. Vous pouvez vérifier l’état de la validation sous la section **Préfixes** du nom de la ressource. Si la validation échoue, l’un des messages d’erreur suivants s’affiche :

   - Préfixe Peering Service non valide ; le préfixe doit être dans un format valide, seul le préfixe IPv4 est pris en charge actuellement.
   - Aucun préfixe n’ayant été reçu du fournisseur Peering Service, contactez-le.
   - L’annonce du préfixe n’a pas de communauté BGP valide. Contactez le fournisseur Peering Service.
   - Le préfixe reçu a un chemin AS plus long (>3) ; contactez le fournisseur Peering Service.
   - Le préfixe reçu a un AS privé dans le chemin. Contactez le fournisseur Peering Service.

### <a name="add-or-remove-a-prefix"></a>Ajouter ou supprimer un préfixe

Sélectionnez **Ajouter des préfixes** dans la page **Préfixes** pour ajouter des préfixes.

Sélectionnez les points de suspension (...) à côté du préfixe indiqué, puis sélectionnez l’option **Supprimer**.

### <a name="delete-a-peering-service-connection"></a>Supprimer une connexion Peering Service

Dans la page **Toutes les ressources**, cochez la case de la connexion Peering Service et sélectionnez l’option **Supprimer** en haut de la page.

> [!NOTE]
> Vous ne pouvez pas modifier un préfixe existant.
>

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la connexion Peering Service, consultez [Connexion Peering Service](connection.md).
- Pour en savoir plus sur la télémétrie des connexions Peering Service, consultez [Télémétrie des connexions Peering Service](connection-telemetry.md).
- Pour mesurer les données de télémétrie, consultez [Mesurer les données de télémétrie des connexions](measure-connection-telemetry.md).
- Pour créer la connexion Peering Service à l’aide d’Azure PowerShell, consultez [Créer une connexion Peering Service – Azure PowerShell](powershell.md).
- Pour créer la connexion à l’aide d’Azure CLI, consultez [Créer une connexion Peering Service – Azure CLI](cli.md).