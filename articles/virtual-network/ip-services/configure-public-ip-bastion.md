---
title: Gérer une adresse IP publique avec Azure Bastion
titleSuffix: Azure Virtual Network
description: Découvrez les méthodes d’utilisation d’une adresse IP publique avec Azure Bastion, et le moyen de modifier la configuration.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 22fa5e1843cdff45a5bea9fb61393a0579278964
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368472"
---
# <a name="manage-a-public-ip-address-with-azure-bastion"></a>Gérer une adresse IP publique avec Azure Bastion

Les adresses IP publiques sont disponibles dans deux SKU : Standard et De base. La sélection de la référence SKU détermine les fonctionnalités de l’adresse IP. La référence SKU fixe les ressources auxquelles l’adresse IP peut être associée.

Azure Bastion est déployé pour fournir une connectivité de gestion sécurisée aux machines virtuelles dans un réseau virtuel. Le service Azure Bastion vous permet d’établir une connexion RDP et SSH sécurisée et fluide aux machines virtuelles de votre réseau virtuel. Azure Bastion active les connexions sans exposer d’adresse IP publique sur la machine virtuelle. Les connexions sont établies directement à partir du portail Azure, sans qu’un client/agent ou logiciel supplémentaire ne soit nécessaire. Azure Bastion prend en charge les adresses IP publiques de référence SKU Standard.

Un hôte Azure Bastion nécessite une adresse IP publique pour sa configuration.

Dans cet article, vous allez apprendre à créer un hôte Azure Bastion à l’aide d’une adresse IP publique existante dans votre abonnement. Azure Bastion ne prend pas en charge le changement de l’adresse IP publique après la création.  Azure Bastion ne prend pas en charge les préfixes d’adresses IP publiques.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Une adresse IP publique de référence SKU standard dans votre abonnement. L’adresse IP ne peut pas être associée à des ressources. Pour plus d’informations sur la création d’une adresse IP publique de référence SKU standard, consultez [Créer une adresse IP publique - Portail Azure](../../virtual-network/create-public-ip-portal.md).
    - Pour les besoins des exemples de cet article, nommez la nouvelle adresse IP publique **myStandardPublicIP**.

## <a name="create-azure-bastion-using-existing-ip"></a>Créer Azure Bastion à l’aide d’une IP existante

Dans cette section, vous allez créer un hôte Azure Bastion. Vous allez sélectionner l’adresse IP que vous avez créée à la section des prérequis en tant qu’IP publique de l’hôte Bastion.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Bastion**.

3. Dans les résultats de la recherche, sélectionnez **Bastion**.

4. Sélectionnez **+ Créer**.

5. Dans **Créer un bastion**, entrez ou sélectionnez les informations suivantes.

    | Paramètre | Valeur | 
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement |
    | Resource group | Sélectionnez **Créer nouveau**. </br> Entrez **myResourceGroup** dans **Nom**. </br> Sélectionnez **OK**. |
    | **Détails de l’instance** |  |
    | Nom | Entrez **myBastionHost**. |
    | Région | Sélectionnez **(USA) USA Ouest 2**. |
    | **Configurer un réseau virtuel** |   |
    | Réseau virtuel | Sélectionnez **Créer nouveau**. </br> Entrez **myVNet** dans **Nom**. </br> Conservez l’espace d’adressage par défaut de **10.4.0.0/16**. </br> Conservez le sous-réseau par défaut de **10.4.0.0/24**. </br> Dans la zone de texte sous le sous-réseau **par défaut**, entrez **AzureBastionSubnet**. </br> En guise de plage d’adresses, entrez **10.4.1.0/27**. </br> Sélectionnez **OK**. |
    | Subnet | Sélectionnez **AzureBastionSubnet**. |
    | **Adresse IP publique** |   |
    | Adresse IP publique | Sélectionnez **Utiliser l’existant**. |
    | Choisir une IP publique | Sélectionnez **myStandardPublicIP**. |

6. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer**.

7. Sélectionnez **Create** (Créer).

> [!NOTE]
> Pour plus d’informations sur Azure Bastion, consultez [Présentation d’Azure Bastion](../../bastion/bastion-overview.md)

## <a name="change-or-remove-public-ip-address"></a>Ajouter ou supprimer l’adresse IP publique

Azure Bastion ne prend pas en charge le changement de l’adresse IP publique après la création.

## <a name="more-information"></a>Autres informations

* Il n’existe aucune obligation d’adresse IP publique distincte sur la machine virtuelle au moment de la connexion via Azure Bastion. Le trafic est tout d’abord routé vers l’IP publique de Bastion. Bastion achemine ensuite les connexions RDP ou SSH jusqu’à l’adresse IP privée associée à la machine virtuelle. 

## <a name="caveats"></a>Mises en garde

* Les adresses IPv6 publiques ne sont actuellement pas prises en charge pour Azure Bastion.  

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer une instance Azure Bastion et à utiliser une IP publique existante. 

- Pour plus d’informations sur Azure Bastion, consultez [Présentation d’Azure Bastion](../../bastion/bastion-overview.md).
- Pour en savoir plus sur les adresses IP publiques dans Azure, consultez [Adresses IP publiques](../../virtual-network/public-ip-addresses.md).
