---
title: Gérer une adresse IP publique avec Pare-feu Azure
titleSuffix: Azure Virtual Network
description: En savoir plus sur les méthodes d’utilisation d’une adresse IP publique avec Pare-feu Azure et sur la façon de modifier la configuration.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: f520b992e018b41a8adf99b87202975a4a2edd38
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368392"
---
# <a name="manage-a-public-ip-address-with-azure-firewall"></a>Gérer une adresse IP publique avec Pare-feu Azure

Pare-feu Azure est un service de sécurité réseau cloud qui protège vos ressources de réseau virtuel Azure. Pare-feu Azure requiert la configuration d’au moins une adresse IP statique publique. Cette adresse IP ou cet ensemble d’adresses IP est utilisé comme point de connexion externe au pare-feu. Pare-feu Azure prend en charge les adresses IP publiques de référence SKU Standard. Les adresses IP publiques de référence SKU De base et les préfixes d’adresses IP publiques ne sont pas pris en charge. 

Dans cet article, vous allez apprendre à créer un Pare-feu Azure à l’aide d’une adresse IP publique existante dans votre abonnement. Vous allez modifier la configuration IP du pare-feu. Enfin, vous allez ajouter une configuration IP au pare-feu.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Trois adresses IP publiques de référence SKU Standard dans votre abonnement. L’adresse IP ne peut pas être associée à des ressources. Pour plus d’informations sur la création d’une adresse IP publique de référence SKU Standard, consultez [Créer une adresse IP publique - portail Azure](../../virtual-network/create-public-ip-portal.md).
    - Dans le cadre des exemples de cet article, nommez les nouvelles adresses IP publiques **myStandardPublicIP-1**, **myStandardPublicIP-2** et **myStandardPublicIP-3**.

## <a name="create-azure-firewall-existing-public-ip"></a>Créer une adresse IP publique existante Pare-feu Azure

Dans cette section, vous allez créer un Pare-feu Azure. Vous allez sélectionner l’adresse IP que vous avez créée dans la section des prérequis en tant qu’adresse IP publique du pare-feu.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Pare-feu**.

3. Dans les résultats de la recherche, sélectionner **Pare-feux**

4. Sélectionnez **+ Créer**.

5. Dans **Créer un pare-feu**, entrez ou sélectionnez les informations suivantes.

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **Créer nouveau**. </br> Entrez **myResourceGroupFW**. </br> Sélectionnez **OK**. |
    | **Détails de l’instance** |   |
    | Nom | Entrez **myFirewall**. |
    | Région | Sélectionnez **USA Ouest 2**. |
    | Zone de disponibilité | Conservez la valeur par défaut **Aucune**. |
    | Niveau de pare-feu  | Conservez la valeur par défaut **Standard**. |
    | Gestion de pare-feu | Conservez la valeur par défaut de l’option **Utiliser une stratégie de pare-feu pour gérer ce pare-feu**.|
    | Stratégie de pare-feu | Sélectionnez **Ajouter**. </br> Entrez **myFirewallPolicy** dans **Nom de la stratégie**. </br> Dans **Région**, sélectionnez **USA Ouest 2**. </br> Sélectionnez **Oui**. |
    | Nom du réseau virtuel | Entrez **myVNet**. |
    | Espace d’adressage | Entrez **10.0.0.0/16**. |
    | Espace d’adressage du sous-réseau | Entrez **10.0.0.0/26**. |
    | Adresse IP publique | Sélectionnez **myStandardPublicIP-1** ou votre adresse IP publique. |
    | Tunneling forcé | Conservez la valeur par défaut **Désactivé**. |
    
 
6. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer**.

7. Sélectionnez **Create** (Créer).

> [!NOTE]
> Il s’agit d’un déploiement simple de Pare-feu Azure. Pour une configuration avancée, consultez [Tutoriel : Déployer et configurer le Pare-feu Azure et une stratégie en utilisant le portail Azure](../../firewall/tutorial-firewall-deploy-portal-policy.md).
>
> Pour plus d’informations sur le Pare-feu Azure, consultez [Qu’est-ce que le Pare-feu Azure ?](../../firewall/overview.md).

## <a name="change-public-ip-address"></a>Modifier l’adresse IP publique

Dans cette section, vous allez modifier l’adresse IP publique associée au pare-feu. Un pare-feu doit avoir au moins une adresse IP publique associée à sa configuration. 

1. Dans la zone de recherche située en haut du portail, entrez **Pare-feu**.

2. Dans les résultats de la recherche, sélectionner **Pare-feux**

3. Sélectionnez **myFirewall** dans **Pare-feux**.

4. Sélectionnez **Configuration d’adresse IP publique** dans **Paramètres** dans **myFirewall**.

5. Dans **Configuration d’adresse IP publique**, sélectionnez **myStandardPublicIP-1** ou votre adresse IP.

6. Sélectionnez **myStandardPublicIP-2** dans **Adresse IP publique** sous **Modifier la configuration d’adresse IP publique**.

7. Sélectionnez **Enregistrer**.

## <a name="add-public-ip-configuration"></a>Ajouter une configuration d’adresse IP publique

Dans cette section, vous allez ajouter une configuration d’adresse IP publique au Pare-feu Azure. Pour plus d’informations sur les adresses IP multiples, consultez [Adresses IP publiques multiples](../../firewall/features.md#multiple-public-ip-addresses).  

1. Dans la zone de recherche située en haut du portail, entrez **Pare-feu**.

2. Dans les résultats de la recherche, sélectionner **Pare-feux**

3. Sélectionnez **myFirewall** dans **Pare-feux**.

4. Sélectionnez **Configuration d’adresse IP publique** dans **Paramètres** dans **myFirewall**.

5. Sélectionnez **+ Ajouter une configuration d’adresse IP publique**.

6. Entrez **myNewPublicIPconfig** dans **Nom**.

7. Sélectionnez **myStandardPublicIP-3** dans **Adresse IP publique**.

8. Sélectionnez **Ajouter**.

## <a name="more-information"></a>Autres informations

* Un Pare-feu Azure peut être intégré à un équilibreur de charge de référence SKU Standard pour protéger les ressources du pool de back-ends.  Si vous associez le pare-feu à un équilibreur de charge public, configurez le trafic entrant pour qu’il soit dirigé vers l’adresse IP publique du pare-feu. Configurez la sortie via un itinéraire défini par l’utilisateur vers l’adresse IP publique du pare-feu.  Pour plus d’informations et des instructions de configuration, consultez [Intégrer un pare-feu Azure avec Azure Standard Load Balancer](../../firewall/integrate-lb.md). 

* Un Pare-feu Azure peut également être associé à une passerelle NAT pour étendre l’extensibilité de la traduction d’adresses réseau source (SNAT). Une passerelle NAT évite que les configurations autorisent le trafic à partir d’un grand nombre d’adresses IP publiques associées au pare-feu. Avec cette configuration, tout le trafic entrant utilise l’adresse IP publique ou les adresses de la passerelle NAT. Le trafic sortant s’effectue via l’adresse ou les adresses IP publique(s) du Pare-feu Azure.  Pour plus d’informations, consultez [Mettre à l’échelle les ports SNAT avec Azure NAT Gateway](../../firewall/integrate-with-nat-gateway.md).

## <a name="caveats"></a>Mises en garde

* Le pare-feu Azure utilise l’équilibrage de charge de référence SKU Standard. Les protocoles autres que TCP et UDP dans les règles de filtre réseau ne sont pas pris en charge pour SNAT sur l’adresse IP publique du pare-feu. 
## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer un Pare-feu Azure et à utiliser une adresse IP publique existante. Vous avez modifié l’adresse IP publique de la configuration IP par défaut. Enfin, vous avez ajouté une configuration d’adresse IP publique au pare-feu.

- Pour en savoir plus sur les adresses IP publiques dans Azure, consultez [Adresses IP publiques](../../virtual-network/public-ip-addresses.md).
- Pour en savoir plus sur le Pare-feu Azure, consultez [Qu’est-ce que le Pare-feu Azure ?](../../firewall/overview.md).
