---
title: Gérer une adresse IP publique avec une instance Azure Application Gateway
titleSuffix: Azure Virtual Network
description: Découvrez les méthodes d’utilisation d’une adresse IP publique avec une instance Azure Application Gateway, ainsi que comment modifier et gérer la configuration.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 8139d6c6309bf971ed9a955f5ef02f0c674b152b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368358"
---
# <a name="manage-a-public-ip-address-with-an-azure-application-gateway"></a>Gérer une adresse IP publique avec une instance Azure Application Gateway

Azure Application Gateway est un équilibreur de charge de trafic web qui gère le trafic vers vos applications web. Application Gateway prend des décisions de routage basées sur les attributs d’une requête HTTP. Exemples d’attributs tels que le chemin d’URI ou les en-têtes d’hôte.  Le serveur frontal d’une Application Gateway est le point de connexion pour les applications dans son pool principal. 

Le serveur frontal d’une Application Gateway peut être une adresse IP privée, une adresse IP publique ou les deux.  La référence SKU V1 d’Application Gateway prend en charge les adresses IP publiques de base, statiques ou dynamiques.  La référence SKU V2 prend en charge les adresses IP publiques de référence SKU standard qui sont statiques uniquement. La référence SKU V2 d’Application Gateway ne prend pas en charge d’adresse IP interne, car il ne s’agit que d’un serveur frontal.  Pour plus d’informations, consultez [Configuration d’adresse IP frontale d’Azure Application Gateway](../../application-gateway/configuration-front-end-ip.md).  

Dans cet article, vous allez apprendre à créer une Application Gateway à l’aide d’une adresse IP publique existante dans votre abonnement. 

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Deux IP publiques du niveau tarifaire Standard dans votre abonnement. Les adresses IP ne peuvent pas être associées à des ressources. Pour plus d’informations sur la création d’une adresse IP publique de référence SKU standard, consultez [Créer une adresse IP publique - Portail Azure](../../virtual-network/create-public-ip-portal.md).
    - Dans le cadre des exemples de cet article, nommez les nouvelles adresses IP publiques **myStandardPublicIP-1** et **myStandardPublicIP-2**.

## <a name="create-application-gateway-existing-public-ip"></a>Créer une IP publique existante pour une Application Gateway

Dans cette section, vous allez créer une ressource Application Gateway. Vous allez sélectionner l’adresse IP que vous avez créée dans la section de conditions préalables en tant qu’adresse IP publique de l’Application Gateway.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Passerelle applicative**.

3. Dans les résultats, sélectionnez **Passerelles applicatives**.

4. Sélectionnez **+ Créer**.

5. Dans **Créer une passerelle applicative**, entrez ou sélectionnez les informations suivantes.

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **Créer nouveau**.<ul><li>Entrez **myResourceGroupAppGW**.</li></ul>Sélectionnez **OK**. |
    | **Détails de l’instance** |   |
    | Nom de passerelle applicative | Entrez **myAppGateway**. |
    | Région | Sélectionnez **(USA) USA Ouest 2**. |
    | Niveau | Conservez la valeur par défaut **Standard V2**. |
    | Activer la mise à l’échelle automatique | Conservez la valeur par défaut **Oui**. |
    | Nombre d’instances minimal | Conservez la valeur par défaut **0**. |
    | Nombre d’instances maximal | Conservez la valeur par défaut **10**. |
    | Zone de disponibilité | Conservez la valeur par défaut **Aucune**. |
    | HTTP2 | Conservez la valeur par défaut **Désactivé**. |
    | Réseau virtuel | Sélectionnez **Créer nouveau**. <ul><li>Dans **Créer un réseau virtuel**, saisissez le nom **myVNet**.</li><li>Conservez l’espace d’adressage par défaut dans **ESPACE D’ADRESSAGE**.</li><li>Dans **SOUS-RÉSEAUX**, remplacer la valeur **par défaut** par **myAGSubnet**.</li><li>Dans le champ de nom du deuxième sous-réseau, entrez **myBackendSubnet**.</li><li>Dans **Plage d’adresses**, saisissez une plage dans les limites de l’espace d’adressage par défaut.</li></ul>Sélectionnez **OK**.|

6. Sélectionnez **Suivant : Serveurs frontaux**.

7. Sélectionnez **myStandardPublicIP-1** pour **Adresse IP publique** dans l’onglet **Serveurs frontaux** ou votre adresse IP publique.

8. Sélectionnez **Suivant : Back-ends**. 

9. Sélectionnez **Ajouter un pool principal**.

10. Entrez **myBackendPool** comme nom dans **Ajouter un pool principal**.

11. Sélectionnez **Suivant : Configuration**.

12. Sélectionnez **+ Ajouter une règle de routage**. Entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom de la règle | Entrez **myRoutingRule**. |
    | **Port d'écoute** |    |
    | Nom de l’écouteur | Entrez **myListener**. |
    | Adresse IP du front-end | Sélectionnez **Public**. |
    | Protocole IP frontal | Conservez la valeur par défaut **HTTP**. |
    | Port | Conservez la valeur par défaut **80**. |
    | **Paramètres supplémentaires** |   |
    | Type d’écouteur | Conservez la valeur par défaut **De base**. |
    | URL de page d’erreur | Conservez la valeur par défaut **Non**. |
    | **Cibles de back-end** |    |
    | Type cible | Conservez la valeur par défaut **Pool principal**. |
    | Cible de back-end | Sélectionnez **MyBackendPool**. |
    | Paramètres HTTP | Sélectionnez **Ajouter**.<ul><li>Entrez **myHTTPsetting** dans **Nom**.</li><li>Laissez les autres paramètres sur les valeurs par défaut.</li></ul>Sélectionnez **Ajouter**.|

13. Sélectionnez **Ajouter**.

14. Sélectionnez **Suivant : Balises**, puis **Suivant : Vérifier + créer**.

15. Sélectionnez **Create** (Créer).

> [!NOTE]
> Il s’agit d’un déploiement simple d’une Application Gateway. Pour en savoir plus sur la configuration avancée, consultez [Démarrage rapide : Diriger le trafic web avec Azure Application Gateway - Portail Azure](../../application-gateway/quick-create-portal.md)
>
> Pour plus d’informations sur Azure Application Gateway, consultez [Qu’est-ce qu’Azure Application Gateway ?](../../application-gateway/overview.md).

## <a name="change-or-remove-public-ip-address"></a>Ajouter ou supprimer l’adresse IP publique

La passerelle applicative ne prend pas en charge la modification de l’adresse IP publique après la création.

## <a name="more-information"></a>Autres informations

* Si une adresse IP dynamique de la référence SKU de base est associée au serveur frontal d’une Application Gateway, elle change uniquement lorsque la passerelle est arrêtée ou démarrée. Le nom DNS associé au serveur frontal d’une Application Gateway ne change pas. 

## <a name="caveats"></a>Mises en garde

* Pour l’instant, les adresses IPv6 publiques ne sont pas prises en charge sur les Application Gateways.  

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer une Application Gateway et à utiliser une adresse IP publique existante. 

- Pour plus d’informations sur le service NAT de réseau virtuel Azure, consultez [Qu’est-ce que le service NAT de réseau virtuel ?](../nat-gateway/nat-overview.md)
- Pour en savoir plus sur les adresses IP publiques dans Azure, consultez [Adresses IP publiques](../../virtual-network/public-ip-addresses.md).