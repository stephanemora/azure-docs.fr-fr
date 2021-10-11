---
title: 'Tutoriel : Configurer les deux options de préférence de routage pour une machine virtuelle – Portail Azure'
titlesuffix: Azure Virtual Network
description: Utilisez ce tutoriel pour apprendre à configurer les deux options de préférence de routage pour une machine virtuelle, à l’aide du portail Azure.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: d11e77725dfb99060c96f62233833330867f1c0d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368934"
---
# <a name="tutorial-configure-both-routing-preference-options-for-a-virtual-machine-using-the-azure-portal"></a>Tutoriel : Configurer les deux options de préférence de routage pour une machine virtuelle à l’aide du portail Azure

Cet article explique comment configurer les deux options de [préférence de routage](routing-preference-overview.md) (Internet et le réseau Microsoft mondial) pour une machine virtuelle. Cette configuration est obtenue à l’aide de deux interfaces de réseau virtuel. Une interface de réseau est configurée avec une IP publique routée via le réseau Microsoft mondial. L’autre interface de réseau est configurée avec une IP publique routée via le réseau d’un fournisseur de services Internet.

Dans ce tutoriel, vous apprenez à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une machine virtuelle avec une adresse IP publique dotée de la préférence de routage **Réseau Microsoft**.
> * Créer une adresse IP publique dotée de la préférence de routage **Internet**.
> * Créer une interface de réseau secondaire pour la machine virtuelle.
> * Associer l’IP publique dotée de la préférence de routage **Internet** à l’interface de réseau secondaire de la machine virtuelle.
> * Attacher une interface de réseau secondaire à la machine virtuelle.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-the-virtual-machine"></a>Créer la machine virtuelle

Dans cette section, vous allez créer une machine virtuelle et une adresse IP publique. Lors de la configuration de l’adresse IP publique, vous devez sélectionner **Réseau Microsoft**  comme préférence de routage.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche du portail, entrez **Machine virtuelle**. Dans les résultats de la recherche, sélectionnez **Machines virtuelles**.

3. Dans **Machines virtuelles**, sélectionnez **+ Créer**, puis **+ Machine virtuelle**.

4. Sous l’onglet **Informations de base** de la page **Créer une machine virtuelle**, entrez ou sélectionnez les informations suivantes.

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **Créer nouveau**. </br> Entrez **TutorVMMixRoutePref-rg**. Sélectionnez **OK**. |
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

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-virtual-machine.png" alt-text="Capture d’écran de la création d’une machine virtuelle.":::

5. Sélectionnez **Suivant : Disques**, puis **Suivant : Mise en réseau**, ou sélectionnez l’onglet **Mise en réseau**.

6. Sous l’onglet Mise en réseau, entrez ou sélectionnez les informations suivantes.

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Interface réseau** |   |
    | Réseau virtuel | Conservez la valeur par défaut de **(nouveau) TutorVMMixRoutePref-rg-vnet**. |
    | Subnet | Conservez la valeur par défaut de **(nouvelle) valeur par défaut (10.1.0.0/24)** . |
    | Adresse IP publique | Sélectionnez **Créer nouveau**. </br> Dans **Nom**, entrez **myPublicIP**. </br> Sélectionnez **Standard** dans **SKU**. </br> Dans **Préférence de routage**, sélectionnez **Réseau Microsoft**. </br> Sélectionnez **OK**. |

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-public-ip-ms-rp.png" alt-text="Capture d’écran de la création d’une adresse IP publique dotée de la préférence de routage Microsoft.":::

7. Sélectionnez **Revoir + créer**.

8. Sélectionnez **Create** (Créer).

## <a name="create-the-public-ip-address"></a>Créer une adresse IP publique

Dans cette section, vous allez créer une adresse IP publique dotée de la préférence de routage **Internet**.

1. Dans la zone de recherche du portail, entrez **Adresse IP publique**. Dans les résultats de la recherche, sélectionnez **Adresse IP publique**.

2. Sélectionnez **+ Créer**.

3. Dans **Créer une adresse IP publique**, tapez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Version de l’adresse IP | Conservez la valeur par défaut **IPv4**. |
    | SKU | Conservez la valeur par défaut **Standard**. |
    | Niveau | Conservez la valeur par défaut **Régional**. |
    | **Configuration d’adresse IP IPv4** |   |
    | Name | Entrez **myPublicIP-lR**. |
    | Préférence de routage | Sélectionnez **Internet**. |
    | Abonnement | Sélectionnez votre abonnement. |
    | Groupe de ressources | Sélectionnez **TutorVMMixRoutePref-rg**. |
    | Emplacement | Sélectionnez **USA Ouest 2**. |

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-public-ip-internet-rp.png" alt-text="Capture d’écran de la création d’une adresse IP publique dotée de la préférence de routage Internet.":::

4. Sélectionnez **Create** (Créer).

## <a name="create-the-secondary-nic"></a>Créer la carte d’interface réseau secondaire

Dans cette section, vous allez créer une interface réseau secondaire pour la machine virtuelle que vous avez créée précédemment.

1. Dans la zone de recherche du portail, entrez **Interface réseau**. Dans les résultats de la recherche, sélectionnez **Interfaces réseau**.

2. Sélectionnez **+ Créer**.

3. Dans **Créer une interface réseau**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Groupe de ressources | Sélectionnez **TutorVMMixRoutePref-rg**. |
    | Name | Entrez **myVMNic2**. |
    | Région | Sélectionnez **USA Ouest 2**. |
    | Réseau virtuel | Sélectionnez **TutorVMMixRoutePref-rg-vnet**. |
    | Sous-réseau | Sélectionnez **TutorVMMixRoutePref-rg-vnet/default (10.1.0.0/24)** . |
    | Groupe de sécurité réseau | Sélectionnez **myVM-nsg**. |

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-network-interface.png" alt-text="Capture d’écran de la création d’une interface réseau secondaire.":::

4. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton **Vérifier + créer** situé au bas de la page.

5. Sélectionnez **Create** (Créer).

## <a name="associate-the-public-ip-address-with-secondary-nic"></a>Associer l’adresse IP publique à la carte d’interface réseau secondaire

Dans cette section, vous allez associer l’adresse IP publique, dotée de la préférence de routage **Internet**, que vous avez créée précédemment, à l’interface réseau que vous avez créée à la section précédente.

1. Dans la zone de recherche du portail, entrez **Adresse IP publique**. Dans les résultats de la recherche, sélectionnez **Adresse IP publique**.

2. Sélectionnez **myPublicIP-IR**.

3. Dans la page **Vue d’ensemble** de **myPublic-IR**, sélectionnez **Associer**.

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/associate-public-ip.png" alt-text="Capture d’écran de la page de présentation de myPublicIP-IR avec le bouton d’association.":::

4. Dans **Associer l’adresse IP publique**, sélectionnez **Interface réseau** dans la zone déroulante **Type de ressource**.

5. Sélectionnez **myVMNic2** dans la zone déroulante **Interface réseau**.

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/select-ip-association-resource.png" alt-text="Capture d’écran de la sélection de la ressource à associer à l’adresse IP publique.":::

6. Sélectionnez **OK**.

## <a name="attach-secondary-network-interface-to-virtual-machine"></a>Attacher l’interface réseau secondaire à la machine virtuelle

Dans cette section, vous allez attacher à la machine virtuelle l’interface de réseau secondaire que vous avez créée précédemment.

1. Dans la zone de recherche du portail, entrez **Machine virtuelle**. Dans les résultats de la recherche, sélectionnez **Machines virtuelles**.

2. Sélectionnez **myVM**.

3. Arrêtez **myVM** si cette instance est en cours d’exécution, sinon passez à l’étape suivante.

4. Dans **myVM**, sélectionnez **Mise en réseau** dans **Paramètres**.

5. Dans la page **Mise en réseau** de **myVM**, sélectionnez **Attacher l’interface réseau**.

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/attach-nic-01.png" alt-text="Capture d’écran de la page de présentation de la mise en réseau de myVM.":::

6. Dans **Attacher l’interface réseau**, sélectionnez **myVMNic2** à partir de la zone déroulante.

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/attach-nic-02.png" alt-text="Capture d’écran de l’option Attacher l’interface réseau.":::

7. Sélectionnez **OK**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne comptez pas continuer à utiliser cette application, supprimez les adresses IP publiques et la machine virtuelle en suivant ces étapes :

1. Dans la zone de recherche située en haut du portail, entrez **Groupe de ressources**.

2. Dans les résultats de la recherche, sélectionnez **Groupes de ressources**.

3. Sélectionnez **TutorVMMixRoutePref-rg**.

4. Sélectionnez **Supprimer le groupe de ressources**.

5. Entrez **myResourceGroup** dans **TAPER LE NOM DU GROUPE DE RESSOURCES** puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour apprendre à créer un préfixe d’adresse IP publique :
> [!div class="nextstepaction"]
> [Configurer la préférence de routage d’un cluster Kubernetes à l’aide d’Azure CLI](routing-preference-azure-kubernetes-service-cli.md)