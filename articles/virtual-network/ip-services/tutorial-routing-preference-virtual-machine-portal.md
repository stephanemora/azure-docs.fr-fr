---
title: 'Tutoriel : Configurer la préférence de routage pour une machine virtuelle – Portail Azure'
description: Dans ce tutoriel, apprenez à créer une machine virtuelle avec une adresse IP publique dotée d’un choix de préférence de routage, à l’aide du portail Azure.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 6a06ec95b6970e4c3f8d4bde4cc180bf832f3a6a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369091"
---
# <a name="tutorial-configure-routing-preference-for-a-vm-using-the-azure-portal"></a>Tutoriel : Configurer la préférence de routage pour une machine virtuelle à l’aide du portail Azure 

Ce tutoriel explique comment configurer la préférence de routage pour une machine virtuelle. Le trafic lié à Internet à partir de la machine virtuelle est routé via le réseau du fournisseur de services Internet lorsque vous choisissez l’option de préférence de routage **Internet**. Le routage par défaut passe par le réseau Microsoft mondial.

Dans ce tutoriel, vous apprenez à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une machine virtuelle avec une adresse IP publique configurée pour la préférence de routage **Internet**.
> * Vérifier que la préférence de routage de l’adresse IP publique est définie sur **Internet**.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-virtual-machine"></a>Créer une machine virtuelle

Dans cette section, vous allez créer une machine virtuelle et une adresse IP publique. Au cours de la configuration de l’adresse IP publique, vous devez sélectionner **Internet** en guise de préférence de routage.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche du portail, entrez **Machine virtuelle**. Dans les résultats de la recherche, sélectionnez **Machines virtuelles**.

3. Dans **Machines virtuelles**, sélectionnez **+ Créer**, puis **+ Machine virtuelle**.

4. Sous l’onglet **Informations de base** de la page **Créer une machine virtuelle**, entrez ou sélectionnez les informations suivantes.

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **Créer nouveau**. </br> Entrez **TutorVMRoutePref-rg**. Sélectionnez **OK**. |
    | **Détails de l’instance** |   |
    | Nom de la machine virtuelle | Entrez **myVM**. |
    | Région | Sélectionnez **(USA) USA Ouest 2**. |
    | Options de disponibilité | Sélectionnez **Aucune redondance d’infrastructure requise**. |
    | Image | Sélectionnez **Windows Server 2019 Datacenter – Gen2**. |
    | Instance Azure Spot | Conservez la valeur par défaut (case non cochée). |
    | Taille | Sélectionnez une taille. |
    | **Compte administrateur** |   |
    | Nom d’utilisateur | Entrez un nom d’utilisateur. |
    | Mot de passe | Entrez un mot de passe. |
    | Confirmer le mot de passe | Retapez le mot de passe. |
    | **Règles des ports d’entrée** |
    | Aucun port d’entrée public | Sélectionnez **Autoriser les ports sélectionnés**. |
    | Sélectionner des ports d’entrée | Conservez la valeur par défaut de **RDP (3389)** . </br> _**Il est déconseillé d’ouvrir le port 3389 à partir d’Internet pour les charges de travail de production**_. |

    :::image type="content" source="./media/tutorial-routing-preference-virtual-machine-portal/create-virtual-machine.png" alt-text="Capture d’écran de la création d’une machine virtuelle.":::

5. Sélectionnez **Suivant : Disques**, puis **Suivant : Mise en réseau**, ou sélectionnez l’onglet **Mise en réseau**.

6. Sous l’onglet Mise en réseau, entrez ou sélectionnez les informations suivantes.

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Interface réseau** |   |
    | Réseau virtuel | Conservez la valeur par défaut de **(nouveau) TutorVMRoutePref-rg-vnet**. |
    | Subnet | Conservez la valeur par défaut de **(nouvelle) valeur par défaut (10.1.0.0/24)** . |
    | Adresse IP publique | Sélectionnez **Créer nouveau**. </br> Dans **Nom**, entrez **myPublicIP**. </br> Sélectionnez **Standard** dans **SKU**. </br> Dans **Préférence de routage**, sélectionnez **Internet**. </br> Sélectionnez **OK**. |

    :::image type="content" source="./media/tutorial-routing-preference-virtual-machine-portal/create-public-ip.png" alt-text="Capture d’écran de la création d’une adresse IP publique.":::

7. Sélectionnez **Revoir + créer**.

8. Sélectionnez **Create** (Créer).

## <a name="verify-internet-routing-preference"></a>Vérifier la préférence de routage Internet

Dans cette section, vous allez rechercher l’adresse IP publique créée précédemment et vérifier la préférence de routage Internet.

1. Dans la zone de recherche du portail, entrez **Adresse IP publique**. Dans les résultats de la recherche, sélectionnez **Adresse IP publique**.

2. Dans **Adresses IP publiques**, sélectionnez **myPublicIP**.

3. Sélectionnez **Propriétés** dans **Paramètres**.

4. Vérifiez que l’option **Internet** s’affiche dans **Préférence de routage**. 

    :::image type="content" source="./media/tutorial-routing-preference-virtual-machine-portal/verify-routing-preference.png" alt-text="Capture d’écran de la vérification de la préférence de routage Internet.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne comptez pas continuer à utiliser cette application, supprimez l’adresse IP publique en suivant ces étapes :

1. Dans la zone de recherche située en haut du portail, entrez **Groupe de ressources**.

2. Dans les résultats de la recherche, sélectionnez **Groupes de ressources**.

3. Sélectionnez **TutorVMRoutePref-rg**.

4. Sélectionnez **Supprimer le groupe de ressources**.

5. Entrez **myResourceGroup** dans **TAPER LE NOM DU GROUPE DE RESSOURCES** puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour apprendre à créer une machine virtuelle avec une préférence de routage mixte :
> [!div class="nextstepaction"]
> [Configurer les deux options de préférence de routage pour une machine virtuelle](routing-preference-mixed-network-adapter-portal.md)

