---
title: 'Démarrage rapide : Créer un préfixe d’adresse IP publique – Portail Azure'
titlesuffix: Azure Virtual Network
description: Découvrez comment créer un préfixe d’adresse IP publique statique à l’aide du portail Azure.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: 0d792a6e2fc83c16575fab59a52010b29d3d1282
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369039"
---
# <a name="quickstart-create-a-public-ip-address-prefix-using-the-azure-portal"></a>Démarrage rapide : Créer un préfixe d’adresse IP publique à l’aide du portail Azure

Découvrez les préfixes d’adresse IP publique et comment en créer, changer et supprimer un. Un préfixe d’adresse IP publique est une plage contiguë d’adresses IP publiques à référence SKU standard. 

Lorsque vous créez une ressource d’adresse IP publique, vous pouvez attribuer une adresse IP publique statique à partir du préfixe et l’associer à des machines virtuelles, à des équilibreurs de charge ou à d’autres ressources. Pour plus d’informations, consultez [Vue d’ensemble du préfixe d’adresse IP publique](public-ip-address-prefix.md).

## <a name="prerequisites"></a>Configuration requise

- Si vous n’avez pas encore de compte, inscrivez-vous pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/free).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-a-public-ip-address-prefix"></a>Créer un préfixe d’adresse IP publique

Dans cette section, vous allez créer un préfixe d’adresse IP publique en utilisant le portail Azure.

1. Dans la zone de recherche située en haut du portail, entrez **IP publique**.

2. Dans les résultats de recherche, sélectionnez **Préfixes d’adresses IP publiques**.

3. Sélectionnez **+ Créer**.

4. Dans **Créer un préfixe d’adresse IP publique**, entrez ou sélectionnez les informations suivantes sous l’onglet **Fonctions de base** :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement |
    | Resource group | Sélectionnez **Créer nouveau**. </br> Entrez **QuickStartCreateIPPrefix-rg**. </br> Sélectionnez **OK**. |
    | **Détails de l’instance** |   |
    | Nom | Entrez **myPublicIPPrefix**. |
    | Région | Sélectionnez **(USA) USA Ouest 2**. |
    | Version de l’adresse IP | Conservez la valeur par défaut **IPv4**. |
    | Taille de préfixe | Sélectionnez la taille de votre préfixe. |

    :::image type="content" source="./media/create-public-ip-prefix-portal/create-public-ip-prefix.png" alt-text="Capture d’écran de la création d’un préfixe d’adresse IP publique dans le portail Azure":::
    
    > [!NOTE]
    >Pour créer un préfixe IPv6, choisissez **IPv6** comme **Version IP**.

     :::image type="content" source="./media/create-public-ip-prefix-portal/create-public-ipv6-prefix.png" alt-text="Capture d’écran de la création d’un préfixe d’adresse IPv6 publique dans le portail Azure":::

5. Sélectionnez l’onglet **Vérifier + créer** ou le bouton bleu **Vérifier + créer** situé en bas de la page.

6. Sélectionnez **Create** (Créer).

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Créer une adresse IP publique statique à partir d’un préfixe

Une fois que vous avez créé un préfixe, vous devez créer des adresses IP statiques à partir du préfixe. Dans cette section, vous allez créer une adresse IP statique à partir du préfixe que vous avez créé précédemment.

1. Dans la zone de recherche située en haut du portail, entrez **IP publique**.

2. Dans les résultats de recherche, sélectionnez **Préfixes d’adresses IP publiques**.

3. Dans **Préfixes d’adresses IP publiques**, sélectionnez **myPublicIPPrefix**.

4. Dans **Vue d’ensemble** pour **myPublicIPPrefix**, sélectionnez **+ Ajouter une adresse IP**.

    :::image type="content" source="./media/create-public-ip-prefix-portal/add-ip-address.png" alt-text="Capture d’écran de la création d’une d’adresse IP publique à partir d’un préfixe dans le portail Azure":::

5. Entrez **myPublicIP** sous **Nom**. 

6. Conservez les valeurs par défaut des autres sélections.

7. Sélectionnez **Ajouter**.

    >[!NOTE]
    >Seules les adresses IP publiques statiques créées avec la référence SKU standard peuvent être attribuées à partir de la plage du préfixe. Pour en savoir plus sur les références (SKU) d’adresses IP publiques, consultez [Adresse IP publique](./public-ip-addresses.md#public-ip-addresses).

8. Sélectionnez **Adresses IP publiques** sous **Paramètres** pour voir l’adresse IP créée.

## <a name="delete-a-prefix"></a>Supprimer un préfixe

Dans cette section, vous allez apprendre à afficher ou à supprimer un préfixe.

1. Dans la zone de recherche située en haut du portail, entrez **IP publique**.

2. Dans les résultats de recherche, sélectionnez **Préfixes d’adresses IP publiques**.

3. Dans **Préfixes d’adresses IP publiques**, sélectionnez **myPublicIPPrefix**.

4. Sélectionnez **Supprimer** dans la section **Vue d’ensemble**.

    >[!NOTE]
    >Si les adresses dans le préfixe sont associées à des ressources d’adresse IP publique, vous devez d’abord supprimer les ressources d’adresse IP publique. Consultez [Supprimer une adresse IP publique](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Dans cet article, vous avez créé un préfixe d’adresse IP publique et une adresse IP publique à partir de ce préfixe. 

Lorsque vous en avez terminé avec le préfixe d’adresse IP publique, supprimez le groupe de ressources et toutes les ressources qu’il contient :

1. Recherchez et sélectionnez **QuickStartCreateIPPrefix-rg**.

2. Sélectionnez **Supprimer le groupe de ressources**.

3. Indiquez **QuickStartCreateIPPrefix-rg** dans **ENTREZ LE NOM DU GROUPE DE RESSOURCES**, puis sélectionnez **Supprimer**.
## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour apprendre à créer une adresse IP publique :
> [!div class="nextstepaction"]
> [Créer une adresse IP publique à l’aide du portail Azure](create-public-ip-portal.md)
