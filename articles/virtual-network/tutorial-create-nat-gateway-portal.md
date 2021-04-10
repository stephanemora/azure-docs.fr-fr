---
title: 'Tutoriel : Créer une passerelle NAT - Portail Azure'
titlesuffix: Azure Virtual Network NAT
description: Dans ce tutoriel, découvrez comment créer et valider une passerelle NAT à l’aide du portail Azure.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 82b5892b027627871e5492e3c6cd3776a923632b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102553440"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal"></a>Tutoriel : Créer une passerelle NAT avec le portail Azure

Ce tutoriel vous montre comment utiliser le service NAT de réseau virtuel Azure. Vous allez créer une passerelle NAT pour fournir une connectivité sortante à une machine virtuelle dans Azure. 

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créez un réseau virtuel.
> * Création d’une machine virtuelle
> * Créer une passerelle NAT et l’associer au réseau virtuel.
> * Vous connecter à la machine virtuelle et vérifier l’adresse IP NAT.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="virtual-network"></a>Réseau virtuel

Avant de déployer une machine virtuelle et de pouvoir utiliser votre passerelle NAT, il nous faut créer le groupe de ressources et le réseau virtuel.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. En haut à gauche de l’écran, sélectionnez **Créer une ressource > Réseau > Réseau virtuel**, ou recherchez **Réseau virtuel** à partir de la zone de recherche.

3. Sélectionnez **Create** (Créer).

4. Dans **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes sous l’onglet **Général** :

    | **Paramètre**          | **Valeur**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Détails du projet**  |                                                                 |
    | Abonnement     | Sélectionner votre abonnement Azure                                  |
    | Groupe de ressources   | Sélectionnez **Créer nouveau**. </br> Entrez **myResourceGroupNAT**. </br> Sélectionnez **OK**. |
    | **Détails de l’instance** |                                                                 |
    | Nom             | Entrez **myVNet**                                    |
    | Région           | Sélectionnez **(Europe) Europe Ouest** |

5. Sélectionnez l’onglet **Adresses IP**, ou sélectionnez le bouton **Suivant : Adresses IP** au bas de la page.

6. Sous l’onglet **Adresses IP**, entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Espace d’adressage IPv4 | Entrez **10.1.0.0/16** |

7. Sélectionnez **+ Ajouter un sous-réseau**. 

8. Dans **Modifier le sous-réseau**, entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Nom du sous-réseau | Entrez **mySubnet** |
    | Plage d’adresses de sous-réseau | Entrez **10.1.0.0/24** |

9. Sélectionnez **Ajouter**.

10. Sélectionnez l'onglet **Sécurité** .

11. Sous **BastionHost**, sélectionnez **Activer**. Entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Nom du bastion | Entrez **myBastionHost** |
    | Espace d’adressage AzureBastionSubnet | Entrez **10.1.1.0/24** |
    | Adresse IP publique | Sélectionnez **Créer nouveau**. </br> Pour **Nom**, entrez **myBastionIP**. </br> Sélectionnez **OK**. |

12. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton **Vérifier + créer**.

13. Sélectionnez **Create** (Créer).

## <a name="nat-gateway"></a>Passerelle NAT

Vous pouvez utiliser une ou plusieurs ressources d’adresse IP publique, des préfixes d’adresse IP publique, ou ces deux options à la fois. Nous allons ajouter une ressource d’IP publique et une ressource de passerelle NAT.

1. En haut à gauche de l’écran, sélectionnez **Créer une ressource > Réseau > Passerelle NAT**, ou recherchez **Passerelle NAT** dans la zone de recherche.

2. Sélectionnez **Create** (Créer). 

3. Dans **Créer une passerelle NAT (traduction d’adresses réseau)** , entrez ou sélectionnez les informations suivantes sous l’onglet **Informations de base** :

    | **Paramètre**          | **Valeur**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Détails du projet**  |                                                                 |
    | Abonnement     | Sélectionnez votre abonnement Azure.                                  |
    | Groupe de ressources   | Sélectionnez **myResourceGroupNAT**. |
    | **Détails de l’instance** |                                                                 |
    | Nom             | Entrez **myNATgateway**                                    |
    | Région           | Sélectionnez **(Europe) Europe Ouest**  |
    | Zone de disponibilité | Sélectionnez **Aucun**. |
    | Délai d’inactivité (minutes) | Entrez **10**. |

4. Sélectionnez l’onglet **IP sortante**, ou sélectionnez le bouton **Suivant : IP sortante** situé au bas de la page.

5. Sous l’onglet **IP sortante**, entrez ou sélectionnez les informations suivantes :

    | **Paramètre** | **Valeur** |
    | ----------- | --------- |
    | Adresses IP publiques | Sélectionnez **Créer une adresse IP publique**. </br> Dans **Nom**, entrez **myPublicIP**. </br> Sélectionnez **OK**. |

6. Sélectionnez l’onglet **Sous-réseau**, ou sélectionnez le bouton **Suivant : Sous-réseau** situé au bas de la page.

7. Sous l’onglet **Sous-réseau**, sélectionnez **myVNet** dans la zone de liste déroulante **Réseau virtuel**.

8. Cochez la case en regard de **mySubnet**.

9. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer** situé au bas de la page.

10. Sélectionnez **Create** (Créer).
    
## <a name="virtual-machine"></a>Machine virtuelle

Dans cette section, vous allez créer une machine virtuelle pour tester la passerelle NAT et vérifier l’adresse IP publique de la connexion sortante.

1. En haut à gauche du portail, sélectionnez **Créer une ressource** > **Calcul** > **Machine virtuelle**. 

2. Dans la page **Créer une machine virtuelle** de l’onglet **Informations de base**, entrez ou sélectionnez les informations suivantes :

    | **Paramètre** | **Valeur** |
    | ----------- | --------- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroupNAT**. |
    | **Détails de l’instance** |   |
    | Nom de la machine virtuelle | Entrez **myVM**. |
    | Région | Sélectionnez **(Europe) Europe Ouest**. |
    | Options de disponibilité | Conservez la valeur par défaut indiquant qu’aucune redondance n’est nécessaire. |
    | Image | Sélectionnez **Windows Server 2019 Datacenter - Gen1**. |
    | Taille | Sélectionnez **Standard_DS1_v2**. |
    | **Compte administrateur** |   |
    | Nom d’utilisateur | Entrez un nom d’utilisateur pour la machine virtuelle. |
    | Mot de passe | Entrez un mot de passe. |
    | Confirmer le mot de passe | Confirmez le mot de passe. |
    | **Règles des ports d’entrée** |    |
    | Aucun port d’entrée public | Sélectionnez **Aucun**. |

3. Sélectionnez l’onglet **Disques**, ou sélectionnez le bouton **Suivant : Disques** situé au bas de la page.

4. Conservez la valeur par défaut sous l’onglet **Disques**.

5. Sélectionnez l’onglet **Réseau**, ou sélectionnez le bouton **Suivant : Réseau** situé au bas de la page.

6. Sous l’onglet **Mise en réseau**, entrez ou sélectionnez les informations suivantes :

    | **Paramètre** | **Valeur** |
    | ----------- | --------- |
    | **Interface réseau** |   |
    | Réseau virtuel | Sélectionnez **myVNet**. |
    | Subnet | Sélectionnez **mySubnet**. |
    | Adresse IP publique | Sélectionnez **Aucun**. |
    | Groupe de sécurité réseau de la carte réseau | Sélectionnez **De base**. |
    | Aucun port d’entrée public | Sélectionnez **Aucun**. |

7. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer** situé au bas de la page.

8. Sélectionnez **Create** (Créer).

## <a name="test-nat-gateway"></a>Tester la passerelle NAT

Dans cette section, nous allons tester la passerelle NAT. Nous allons d’abord découvrir l’IP publique de la passerelle NAT. Nous allons ensuite nous connecter à la machine virtuelle de test et vérifier la connexion sortante via la passerelle NAT.
    
1. Recherchez l’adresse IP publique de la passerelle NAT sur l’écran **Vue d’ensemble**. Sélectionnez **Tous les services** dans le menu de gauche, sélectionnez **Toutes les ressources**, puis sélectionnez **myPublicIP**.

2. Prenez note de l’adresse IP publique :

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Découvrir l’adresse IP publique de la passerelle NAT" border="true":::

3. Sélectionnez **Tous les services** dans le menu de gauche, sélectionnez **Toutes les ressources**, puis dans la liste de ressources, sélectionnez **myVM**, qui se trouve dans le groupe de ressources **myResourceGroupNAT**.

4. Dans la page **Vue d’ensemble**, sélectionnez **Se connecter**, puis **Bastion**.

5. Sélectionnez le bouton bleu **Utiliser le bastion**.

6. Entrez le nom d’utilisateur et le mot de passe saisis pendant la création de la machine virtuelle.

7. Ouvrez **Internet Explorer** sur **myTestVM**.

8. Entrez **https://whatsmyip.com** dans la barre d’adresse.

9. Vérifiez que l’adresse IP affichée correspond à l’adresse de la passerelle NAT que vous avez notée à l’étape précédente :

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer affichant une IP sortante externe" border="true":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne comptez pas continuer à utiliser cette application, supprimez le réseau virtuel, la machine virtuelle et la passerelle NAT en effectuant les étapes suivantes :

1. Dans le menu de gauche, sélectionnez **Groupes de ressources**.

2. Sélectionnez le groupe de ressources **myResourceGroupNAT**.

3. Sélectionnez **Supprimer le groupe de ressources**.

4. Entrez **myResourceGroupNAT**, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le NAT de réseau virtuel Azure, consultez :
> [!div class="nextstepaction"]
> [Vue d’ensemble de NAT de réseau virtuel](nat-overview.md)
