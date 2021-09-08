---
title: Gérer une adresse IP publique avec une passerelle NAT
titleSuffix: Azure Virtual Network
description: En savoir plus sur les méthodes d’utilisation d’une adresse IP publique avec une passerelle NAT de réseau virtuel Azure et sur la façon de modifier la configuration.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: e03b6dd46b04d4b162190feb26a6d03fa410a0ee
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114291690"
---
# <a name="manage-a-public-ip-address-with-a-nat-gateway"></a>Gérer une adresse IP publique avec une passerelle NAT

Les ressources de la passerelle NAT Azure autorisent les connexions Internet sortantes à partir de sous-réseaux dans un réseau virtuel. Les ressources déployées dans le sous-réseau du réseau virtuel de la passerelle NAT doivent être la référence SKU standard. Le déploiement d’une passerelle NAT sur un sous-réseau de réseau virtuel avec des ressources SKU de base n’est pas pris en charge. 

La passerelle NAT active les connexions de traduction d’adresses réseau sources (SNAT) à partir des ressources à l’aide de la passerelle NAT. La passerelle NAT prend en charge les adresses IP publiques de référence SKU standard et les préfixes d’adresse IP publique. Toute combinaison est prise en charge, bien que le nombre d’adresses IP fournies ne puisse pas dépasser 16. L’ajout d’adresses IP ou d’un préfixe IP met à l’échelle les connexions SNAT des ressources à l’aide de la passerelle NAT. 

Dans cet article, vous allez apprendre à créer une passerelle NAT à l’aide d’une adresse IP publique existante dans votre abonnement. 

Vous apprendrez à modifier l’adresse IP publique actuelle associée à une passerelle NAT. 

Enfin, vous allez modifier la configuration IP à partir d’une adresse IP publique en préfixe IP.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Deux adresses IP publiques de référence SKU standard dans votre abonnement. Les adresses IP ne peuvent pas être associées à des ressources. Pour plus d’informations sur la création d’une adresse IP publique de référence SKU standard, consultez [Créer une adresse IP publique - Portail Azure](create-public-ip-portal.md).
    - Dans le cadre des exemples de cet article, nommez les adresses IP publiques **myStandardPublicIP-1** et **myStandardPublicIP-2**.
- Préfixe d’adresse IP publique dans votre abonnement. Pour plus d’informations sur la création d’un préfixe d’adresse IP publique, consultez [Créer une adresse IP publique à l’aide du Portail Azure](create-public-ip-prefix-portal.md).
    - Dans le cadre de l’exemple de cet article, nommez le nouveau préfixe d’adresse IP publique **myPublicIPPrefixNAT**.

## <a name="create-nat-gateway-existing-public-ip"></a>Créer une adresse IP publique existante de la passerelle NAT

Dans cette section, vous allez créer une ressource de passerelle NAT. Vous allez sélectionner l’adresse IP que vous avez créée dans la section conditions préalables en tant qu’adresse IP publique de la passerelle NAT.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Passerelle NAT**.

3. Sélectionnez **Passerelles NAT** dans les résultats de la recherche.

4. Sélectionnez **+ Créer**.

5. Dans **Créer une passerelle NAT (traduction d’adresses réseau)** , entrez ou sélectionnez les informations suivantes.

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **Créer nouveau**. </br> Entrez **myResourceGroupNAT**. </br> Sélectionnez **OK**. |
    | **Détails de l’instance** |   |
    | Nom | Entrez **myNATgateway**. |
    | Région | Sélectionnez **(USA) USA Ouest 2**. |
    | Zone de disponibilité | Conservez la valeur par défaut **Aucune**. |
    | Délai d’inactivité | Conservez la valeur par défaut **4**. |

6. Sélectionnez l’onglet **Adresse IP sortante** ou sélectionnez **Suivant : Adresse IP sortante**.

7. Sélectionnez **myStandardPublicIP-1** pour l’**Adresse IP publique** dans l’onglet **Adresse IP sortante**.

6. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer**. 

7. Sélectionnez **Create** (Créer).

> [!NOTE]
> Il s’agit d’un déploiement simple d’une passerelle NAT. Pour la configuration et l’installation avancées, consultez [Didacticiel : créer une passerelle NAT à l’aide du Portail Azure](./nat-gateway/tutorial-create-nat-gateway-portal.md)
>
> Pour plus d’informations sur le service NAT de réseau virtuel Azure, consultez [Qu’est-ce que le service NAT de réseau virtuel Azure ?](./nat-gateway/nat-overview.md)

## <a name="change-or-remove-public-ip-address"></a>Ajouter ou supprimer l’adresse IP publique

Dans cette section, vous allez vous connecter au Portail Azure et modifier l’adresse IP de la passerelle NAT. 

Pour modifier l’adresse IP, vous allez associer une nouvelle adresse IP publique créée précédemment avec la passerelle NAT. Au moins une adresse IP doit être affectée à une passerelle NAT.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Passerelle NAT**.

3. Sélectionnez **Passerelles NAT** dans les résultats de la recherche.

4. Dans **passerelles NAT**, sélectionnez **myNATgateway** ou la passerelle NAT que vous souhaitez modifier.

5. Dans les paramètres **myNATgateway**, sélectionnez **Adresse IP sortante**.

6. Sélectionnez **Modifier** à côté des **Adresses IP publiques** dans **Adresse IP sortante**.

7. Vous pouvez choisir de remplacer l’adresse IP actuelle ou d’ajouter l’adresse existante. Dans **Gérer les adresses IP publiques et les préfixes** à côté des **Adresses IP publiques**, sélectionnez **myStandardPublicIP-2**.

8. Sélectionnez **OK**.

9. Vérifiez que **myStandardPublicIP-2** soit ajouté aux Adresses IP publiques. Vous pouvez supprimer l’adresse IP déjà affectée en sélectionnant la corbeille, si nécessaire.

10. Sélectionnez **Enregistrer**.

## <a name="add-public-ip-prefix"></a>Ajouter le préfixe d’adresse IP publique

Les préfixes d’adresse IP publique étendent l’extensibilité de SNAT pour les connexions sortantes à partir de la passerelle NAT. Un préfixe d’adresse IP publique évite l’épuisement du port SNAT. Chaque adresse IP fournit 64 000 ports éphémères pouvant être utilisés.

> [!NOTE] 
> Lors de l’affectation d’un préfixe d’adresse IP publique à une passerelle NAT, toute la plage est utilisée. 

Dans cette section, vous allez modifier la configuration IP sortante pour utiliser un préfixe d’adresse IP publique que vous avez créé précédemment.

> [!NOTE]
> Vous pouvez choisir de supprimer l’adresse IP unique associée à la passerelle NAT et de la réutiliser, ou de la conserver associée à la passerelle NAT pour augmenter les ports SNAT sortants. La passerelle NAT prend en charge une combinaison d’adresses IP publiques et de préfixes dans la configuration IP sortante. Si vous avez créé un préfixe d’adresse IP publique avec 16 adresses, supprimez l’adresse IP publique unique et enregistrez la configuration avant d’ajouter le préfixe. Le nombre d’adresses IP allouées ne peut pas dépasser 16.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Passerelle NAT**.

3. Sélectionnez **Passerelles NAT** dans les résultats de la recherche.

4. Dans **passerelles NAT**, sélectionnez **myNATgateway** ou la passerelle NAT que vous souhaitez modifier.

5. Dans les paramètres **myNATgateway**, sélectionnez **Adresse IP sortante**.

6. Sélectionnez **Modifier** à côté des **Préfixes d’adresse IP publique** dans **Adresse IP sortante**.

7. Sélectionnez **myPublicIPPrefixNAT** ou votre préfixe.

8. Sélectionnez **OK**.

9. Vérifiez que **myPublicIPPrefixNAT** soit ajouté aux préfixes d’adresse IP publique.

10. Sélectionnez **Enregistrer**.

## <a name="more-information"></a>Autres informations

* Lors du déploiement de machines virtuelles dans un réseau virtuel avec une passerelle NAT, tout le trafic entrant envoyé à la passerelle NAT ressort via la passerelle NAT. Lors de l’utilisation d’une passerelle NAT avec un équilibreur de charge public standard, tout le trafic entrant envoyé à l’adresse IP ou aux adresses IP publiques de la passerelle NAT ressortira via la passerelle NAT. 

    > [!NOTE] 
    > Toute configuration de trafic sortant à partir d’une règle d’équilibrage de charge ou de règles de trafic sortant est remplacée par la passerelle NAT. Les membres du pool principal de l’équilibreur de charge pourront également utiliser la passerelle NAT pour les connexions sortantes. Pour en savoir plus, consultez [Conception de réseaux virtuels avec des ressources de passerelle NAT](./nat-gateway/nat-gateway-resource.md).

* Les passerelles NAT et les adresses IP publiques peuvent avoir une valeur de délai d’expiration TCP assignée pour la durée pendant laquelle une connexion doit être ouverte avant les écoutes continues.  Si une adresse IP publique est affectée à une passerelle NAT, la valeur du délai d’expiration sur l’adresse IP est prioritaire.  Pour en savoir plus, consultez [Conception de réseaux virtuels avec des ressources de passerelle NAT](./nat-gateway/nat-gateway-resource.md#timers).

## <a name="caveats"></a>Mises en garde

* L’adresse IPv6 publique et les préfixes IPv6 publics ne sont pas pris en charge sur les passerelles NAT pour l’instant. Les passerelles NAT ne peuvent pas être déployées sur un sous-réseau de réseau virtuel avec un préfixe IPv6. Pour plus d’informations, consultez [Résoudre les problèmes de connectivité du réseau virtuel Azure](./nat-gateway/troubleshoot-nat.md).
## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer une passerelle NAT de chargement et à utiliser une adresse IP publique existante. Vous avez remplacé l’adresse IP dans une configuration IP sortante de passerelle NAT. Enfin, vous avez modifié une configuration IP sortante pour utiliser un préfixe d’adresse IP publique.

- Pour plus d’informations sur le service NAT de réseau virtuel Azure, consultez [Qu’est-ce que le service NAT de réseau virtuel ?](./nat-gateway/nat-overview.md)
- Pour en savoir plus sur les adresses IP publiques dans Azure, consultez [Adresses IP publiques](public-ip-addresses.md).