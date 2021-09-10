---
title: Gérer une adresse IP publique avec une passerelle VPN
titleSuffix: Azure Virtual Network
description: Découvrez comment utiliser une adresse IP publique avec une passerelle VPN et modifier la configuration.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 5333408e59edfacd34d1e4dd4b14d0c598f8f268
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113439048"
---
# <a name="manage-a-public-ip-address-with-a-vpn-gateway"></a>Gérer une adresse IP publique avec une passerelle VPN

Les adresses IP publiques sont disponibles dans deux références (SKU), Standard et De base. La sélection de la référence (SKU) détermine les fonctionnalités de l’adresse IP. La référence (SKU) fixe les ressources auxquelles l’adresse IP peut être associée. 

Une passerelle VPN est une passerelle de réseau virtuel qui permet d’échanger du trafic chiffré entre un réseau virtuel Azure et un emplacement local via l’Internet public. Vous pouvez également utiliser des passerelles VPN pour envoyer du trafic chiffré entre les réseaux virtuels Azure sur le réseau Microsoft. Chaque réseau virtuel ne peut posséder qu’une seule passerelle VPN. Une passerelle VPN prend en charge les adresses IP publiques de référence (SKU) Standard et De base en fonction de la référence (SKU) de la passerelle VPN. Les préfixes d’adresse IP publique ne sont pas pris en charge.

Une passerelle VPN nécessite une adresse IP publique pour sa configuration. Une adresse IP publique est utilisée en tant que point de connexion externe du VPN. 

Cet article explique comment apprendre à créer une passerelle VPN à l’aide d’une adresse IP publique existante dans votre abonnement. 

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Une adresse IP publique de référence (SKU) Standard dans votre abonnement. L’adresse IP ne peut pas être associée à des ressources. Pour plus d’informations sur la création d’une adresse IP publique de référence (SKU) Standard, consultez [Créer une adresse IP publique – Portail Azure](create-public-ip-portal.md).
    - Pour les besoins des exemples de cet article, nommez la nouvelle adresse IP publique **myStandardPublicIP**.

## <a name="create-vpn-gateway-existing-public-ip"></a>Créer une passerelle VPN avec une adresse IP publique existante

Dans cette section, vous allez créer une passerelle VPN. Vous allez sélectionner l’adresse IP que vous avez créée en guise de prérequis en tant qu’adresse IP publique pour la passerelle VPN.

### <a name="create-virtual-network"></a>Création d’un réseau virtuel

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Réseau virtuel**.

3. Dans les résultats de recherche, sélectionnez **Réseaux virtuels**.

4. Sélectionnez **+ Créer**.

5. Dans **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes.

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **Créer nouveau**. </br> Entrez **myResourceGroupVPN**. </br> Sélectionnez **OK**. |
    | **Détails de l’instance** |   |
    | Nom | Entrez **myVNet**. |
    | Région | Sélectionnez **(USA) USA Ouest 2**. |
    
6. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer**.

7. Sélectionnez **Create** (Créer).

8. Dans la zone de recherche située en haut du portail, entrez **Réseau virtuel**.

9. Dans les résultats de recherche, sélectionnez **Réseaux virtuels**.

10. Sélectionnez **myVNet** dans **Réseaux virtuels**.

11. Dans les **Paramètres** de **myVNET**, sélectionnez **Sous-réseaux**.

12. Sélectionnez **+ Sous-réseau de passerelle**.

13. Dans **Sous-réseau de passerelle**, modifiez la **Plage d’adresses de sous-réseau** de **/24** en **/27**.

14. Sélectionnez **Enregistrer**.

### <a name="create-vpn-gateway"></a>Créer la passerelle VPN


2. Dans la zone de recherche située en haut du portail, entrez **Passerelle de réseau virtuel**.

3. Dans les résultats de recherche, sélectionnez **Passerelles de réseau virtuel**.

4. Sélectionnez **+ Créer**.

5. Dans **Créer une passerelle de réseau virtuel**, entrez ou sélectionnez les informations suivantes.

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | **Détails de l’instance** |   |
    | Nom | Entrez **myNATgateway**. |
    | Région | Sélectionnez **USA Ouest 2**. |
    | Type de passerelle | Conservez la valeur par défaut de **VPN**. |
    | Type de VPN | Conservez la valeur par défaut de **Basé sur un itinéraire**. |
    | Référence SKU | Sélectionnez **VpnGw1AZ**. |
    | Réseau virtuel | Sélectionnez **myVNet**. |
    | Subnet | Cette entrée sélectionne automatiquement le **Sous-réseau de passerelle** que vous avez créé précédemment. |
    | **Adresse IP publique** |   |
    | Adresse IP publique | Sélectionnez **Utiliser l’existant**. |
    | Choisir une adresse IP publique | Sélectionnez **myStandardPublicIP** ou votre adresse IP publique. |
    | Activer le mode actif/actif | Conservez la valeur par défaut **Désactivé**. |
    | Configurer BGP | Conservez la valeur par défaut **Désactivé**. |

6. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer**.

7. Sélectionnez **Create** (Créer).

> [!NOTE]
> Ceci est un déploiement simple d’une passerelle VPN. Pour une configuration et une installation avancées, consultez [Didacticiel : Créer et gérer une passerelle VPN à l’aide du portail Azure](../vpn-gateway/tutorial-create-gateway-portal.md).
>
> Pour plus d’informations sur la passerelle VPN, consultez [Qu’est-ce qu’une passerelle VPN ?](../vpn-gateway/vpn-gateway-about-vpngateways.md).

## <a name="change-or-remove-public-ip-address"></a>Ajouter ou supprimer l’adresse IP publique

Une passerelle VPN ne prend pas en charge la modification de l’adresse IP publique après la création.

## <a name="caveats"></a>Mises en garde

* Pour l’instant, les adresses IPv6 publiques ne sont pas prises en charge sur les passerelles VPN.
 
## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer une passerelle VPN et à utiliser une adresse IP publique existante. 

- Pour en savoir plus sur les adresses IP publiques dans Azure, consultez [Adresses IP publiques](public-ip-addresses.md).
- Pour en savoir plus sur les passerelles VPN, consultez [Qu’est-ce qu’une passerelle VPN ?](../vpn-gateway/vpn-gateway-about-vpngateways.md).