---
title: Gérer une adresse IP publique avec une machine virtuelle Azure
titleSuffix: Azure Virtual Network
description: Découvrez les méthodes d’utilisation d’une adresse IP publique avec Machines virtuelles Azure, et le moyen de modifier la configuration.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: a21b734ee1825a86c9276a2e603d04d723bd8d46
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368480"
---
# <a name="manage-a-public-ip-address-with-an-azure-virtual-machine"></a>Gérer une adresse IP publique avec une machine virtuelle Azure

Les adresses IP publiques sont disponibles dans deux SKU : Standard et De base. La sélection de la référence SKU détermine les fonctionnalités de l’adresse IP. La référence SKU fixe les ressources auxquelles l’adresse IP peut être associée.  

Machines virtuelles Azure représente le principal service de calcul dans Azure. les clients peuvent créer des machines virtuelles Linux ou Windows. Une adresse IP publique peut être affectée à une machine virtuelle pour les connexions entrantes de la machine virtuelle. 

Une machine virtuelle n’a pas besoin d’adresse IP publique pour sa configuration.

Dans cet article, vous allez apprendre à créer une machine virtuelle Azure à l’aide d’une adresse IP publique existante dans votre abonnement. Vous allez découvrir comment ajouter une adresse IP publique à une machine virtuelle. Vous changerez également l’adresse IP. Pour terminer, vous découvrirez comment supprimer l’IP publique.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Deux adresses IP publiques de référence SKU standard dans votre abonnement. L’adresse IP ne peut pas être associée à des ressources. Pour plus d’informations sur la création d’une adresse IP publique de référence SKU standard, consultez [Créer une adresse IP publique - Portail Azure](../../virtual-network/create-public-ip-portal.md).
    - Pour les exemples de cet article, nommez les nouvelles adresses IP publiques **myStandardPublicIP-1** et **myStandardPublicIP-2**.
- Une adresse IP publique de SKU standard avec la préférence de routage **Internet** dans votre abonnement. Pour plus d’informations sur la création d’une adresse IP publique avec la préférence de routage **Internet**, consultez [Configurer une préférence de routage pour une adresse IP publique à l’aide du portail Azure](../../virtual-network/routing-preference-portal.md).
    - Pour l’exemple de cet article, nommez la nouvelle adresse IP publique **myStandardPublicIP-3**.
## <a name="create-virtual-machine-existing-public-ip"></a>Créer une machine virtuelle avec une IP publique existante

Dans cette section, vous allez créer une machine virtuelle. Vous allez sélectionner l’adresse IP que vous avez créée dans la section des prérequis en tant qu’IP publique pour la machine virtuelle.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Machine virtuelle**.

3. Dans les résultats de la recherche, sélectionnez **Machines virtuelles**.

4. Sélectionnez **+ Ajouter**, puis **+ Machine virtuelle**.

5. Dans **Créer une machine virtuelle**, entrez ou sélectionnez les informations suivantes.

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **Créer nouveau**. </br> Entrez **myResourceGroupVM**. </br> Sélectionnez **OK**. |
    | **Détails de l’instance** |   |
    | Nom de la machine virtuelle | Entrez **myVM**. |
    | Région | Sélectionnez **(USA) USA Ouest 2**. | 
    | Options de disponibilité | Sélectionnez **Aucune redondance d’infrastructure requise**. |
    | Image | Sélectionnez **Windows Server 2019 Datacenter - Gen1**. |
    | Instance Azure Spot  | Conservez la valeur par défaut (case non cochée). |
    | Taille | Sélectionnez une taille pour la machine virtuelle. |
    | **Compte administrateur** |   |
    | Nom d’utilisateur | Entrez un nom d’utilisateur. |
    | Mot de passe | Entrez un mot de passe. |
    | Confirmer le mot de passe | Confirmez le mot de passe. |
    | **Règles des ports d’entrée** |   |
    | Aucun port d’entrée public | Conservez la valeur par défaut de **Autoriser les ports sélectionnés**. |
    | Sélectionner des ports d’entrée | Conservez la valeur par défaut de **RDP (3389)** . |

6. Sélectionnez l’onglet **Mise en réseau**, ou sélectionnez **Suivant : Disques**, puis **Suivant : Mise en réseau**.

7. Sous l’onglet **Mise en réseau**, entrez ou sélectionnez les informations suivantes.

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Interface réseau** |   |
    | Réseau virtuel | Conservez la valeur par défaut de **(nouveau) myResourceGroupVM-vnet**. |
    | Subnet | Conservez la valeur par défaut de **(nouvelle) valeur par défaut (10.1.0.0/24)** . |
    | Adresse IP publique | Sélectionnez **myStandardPublicIP-1**. |
    | Groupe de sécurité réseau de la carte réseau | Conservez la valeur par défaut **De base**. |
    | Aucun port d’entrée public | Conservez la valeur par défaut de **Autoriser les ports sélectionnés**. |
    | Sélectionner des ports d’entrée | Conservez la valeur par défaut de **RDP (3389)** . |

6. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer**.

7. Sélectionnez **Create** (Créer).

> [!NOTE]
> Il s’agit d’un déploiement simple de Machine virtuelle Azure. Pour la configuration et l’installation avancées, consultez [Démarrage rapide : Créer une machine virtuelle Windows dans le portail Azure](../../virtual-machines/windows/quick-create-portal.md)
>
> Pour plus d’informations sur Machines virtuelles Azure, reportez-vous à [Machines virtuelles Windows dans Azure](../../virtual-machines/windows/overview.md).

## <a name="change-public-ip-address"></a>Modifier l’adresse IP publique

Dans cette section, vous allez modifier l’adresse IP publique associée à la configuration d’IP publique par défaut de la machine virtuelle.

1. Dans la zone de recherche située en haut du portail, entrez **Machine virtuelle**.

2. Dans les résultats de la recherche, sélectionnez **Machines virtuelles**.

3. Sélectionnez **myVM** dans **Machines virtuelles**.

4. Sélectionnez **Mise en réseau** dans les **Paramètres** de **myVM**.

5. Dans **Mise en réseau**, sélectionnez l’**Interface réseau** de la machine virtuelle. Le nom de la carte d’interface réseau commencera par le nom de la machine virtuelle et se terminera par un nombre aléatoire.  Dans cet exemple, il s’agit de **myvm793**.

    :::image type="content" source="./media/configure-public-ip-vm/network-interface.png" alt-text="Sélection de l’interface réseau." border="true":::

6. Dans les **Paramètres** de l’interface réseau, sélectionnez **Configurations IP**.

7. Sélectionnez **ipconfig1** dans **Configurations IP**.  

    :::image type="content" source="./media/configure-public-ip-vm/change-ipconfig.png" alt-text="Sélection de la configuration ipconfig pour modifier l’adresse IP." border="true":::

1. Sélectionnez **myStandardPublicIP-2** dans **Adresse IP publique** de **ipconfig1**.

7. Sélectionnez **Enregistrer**.

## <a name="add-public-ip-configuration"></a>Ajouter une configuration d’adresse IP publique

Dans cette section, vous allez ajouter une configuration d’IP publique à la machine virtuelle. 

Pour plus d’informations sur l’ajout de plusieurs adresses IP, consultez [Attribuer plusieurs adresses IP à des machines virtuelles à l’aide du portail Azure](../../virtual-network/virtual-network-multiple-ip-addresses-portal.md). 

Pour plus d’informations sur l’utilisation des deux types de préférences de routage, consultez [Configurer les options de préférence de routage pour une machine virtuelle](../../virtual-network/routing-preference-mixed-network-adapter-portal.md).

1. Dans la zone de recherche située en haut du portail, entrez **Machine virtuelle**.

2. Dans les résultats de la recherche, sélectionnez **Machines virtuelles**.

3. Sélectionnez **myVM** dans **Machines virtuelles**.

4. Sélectionnez **Mise en réseau** dans les **Paramètres** de **myVM**.

5. Dans **Mise en réseau**, sélectionnez l’**Interface réseau** de la machine virtuelle. Le nom de la carte d’interface réseau commencera par le nom de la machine virtuelle et se terminera par un nombre aléatoire.  Dans cet exemple, il s’agit de **myvm793**.

    :::image type="content" source="./media/configure-public-ip-vm/network-interface.png" alt-text="Sélection de l’interface réseau." border="true":::

6. Dans les **Paramètres** de l’interface réseau, sélectionnez **Configurations IP**.

7. Dans **Configurations IP**, sélectionnez **+ Ajouter**.

8. Entrez **ipconfig2** dans **Nom**.

9. Dans **Adresse IP publique**, sélectionnez **Associer**.

10. Sélectionnez **myStandardPublicIP-3** dans **Adresse IP publique**.

11. Sélectionnez **OK**.

## <a name="remove-public-ip-address-association"></a>Supprimer l’association de l’adresse IP publique

Dans cette section, vous allez supprimer l’adresse IP publique de l’interface réseau. Après ce processus, la machine virtuelle ne sera pas disponible pour les connexions externes.

1. Dans la zone de recherche située en haut du portail, entrez **Machine virtuelle**.

2. Dans les résultats de la recherche, sélectionnez **Machines virtuelles**.

3. Sélectionnez **myVM** dans **Machines virtuelles**.

4. Sélectionnez **Mise en réseau** dans les **Paramètres** de **myVM**.

5. Dans **Mise en réseau**, sélectionnez l’**Interface réseau** de la machine virtuelle. Le nom de la carte d’interface réseau commencera par le nom de la machine virtuelle et se terminera par un nombre aléatoire.  Dans cet exemple, il s’agit de **myvm793**.

    :::image type="content" source="./media/configure-public-ip-vm/network-interface.png" alt-text="Sélection de l’interface réseau." border="true":::

6. Dans les **Paramètres** de l’interface réseau, sélectionnez **Configurations IP**.

7. Sélectionnez **ipconfig1** dans **Configurations IP**.  

    :::image type="content" source="./media/configure-public-ip-vm/change-ipconfig.png" alt-text="Sélection de la configuration ipconfig pour modifier l’adresse IP." border="true":::

8. Sélectionnez **Dissocier** dans **Paramètres d’adresse IP publique**.

9. Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer une machine virtuelle et à utiliser une IP publique existante. Vous avez modifié l’adresse IP publique de la configuration IP par défaut. Vous avez finalement ajouté une configuration d’IP publique au pare-feu à l’aide de la préférence de routage Internet.

- Pour en savoir plus sur les adresses IP publiques dans Azure, consultez [Adresses IP publiques](../../virtual-network/public-ip-addresses.md).
