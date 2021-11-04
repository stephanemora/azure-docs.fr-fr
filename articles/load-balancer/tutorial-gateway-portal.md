---
title: 'Tutoriel : Créer un équilibreur de charge de passerelle - Portail Azure'
titleSuffix: Azure Load Balancer
description: Utilisez ce tutoriel pour apprendre à créer un équilibreur de charge de passerelle à l’aide du Portail Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: template-tutorial, ignite-fall-2021
ms.openlocfilehash: 918f2da42a57752db2373e65665804d0a49ad31a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097116"
---
# <a name="tutorial-create-a-gateway-load-balancer-using-the-azure-portal"></a>Tutoriel : Créer un équilibreur de charge de passerelle à l'aide du Portail Azure

Azure Load Balancer se compose de SKUs Standard, Basic et Gateway. L'équilibreur de charge de passerelle est utilisé pour l'insertion transparente d'appareils virtuels de réseau (NVA)(appliance virtuelle réseau). Utilisez Load Balancer de passerelle pour les scénarios qui requièrent des performances élevées et une haute évolutivité de Appliances virtuelles réseau.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Inscrire la fonctionnalité de préversion
> * Créez un réseau virtuel.
> * Créer un groupe de sécurité réseau.
> * Créez un équilibreur de charge de passerelle.
> * Chaînez un serveur frontal de l’équilibreur de charge à l’équilibreur de charge de la passerelle.

> [!IMPORTANT]
> Gateway Azure Load Balancer est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Une référence SKU standard publique existante Azure Load Balancer. Pour plus d'informations sur la création d'un équilibreur de charge, voir **[Créer un équilibreur de charge public à l'aide du Portail Azure](quickstart-load-balancer-standard-public-portal.md)** .
    - Dans le cadre de ce tutoriel, l’équilibreur de charge dans les exemples est nommé **myLoadBalancer**.

## <a name="register-preview-feature"></a>Inscrire la fonctionnalité d’évaluation

Dans le cadre de la préversion publique, le fournisseur doit être inscrit dans votre abonnement Azure. Utilisez les exemples PowerShell ou Azure CLI suivants pour activer votre abonnement.

### <a name="powershell"></a>PowerShell

Utilisez [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) pour enregistrer la fonctionnalité du fournisseur **AllowGatewayLoadBalancer** :

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowGatewayLoadBalancer

```

Utilisez [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) pour enregistrer le fournisseur de ressources **Microsoft.Network** :

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Network

```

### <a name="azure-cli"></a>Azure CLI

Utilisez [az feature register](/cli/azure/feature#az_feature_register) pour enregistrer la fonctionnalité du fournisseur **AllowGatewayLoadBalancer** :

```azurecli-interactive
  az feature register \
    --name AllowGatewayLoadBalancer \
    --namespace Microsoft.Network
```

Utilisez [Register-AzResourceProvider](/cli/azure/provider#az_provider_register) pour enregistrer le fournisseur de ressources **Microsoft.Network** :

```azurecli-interactive
  az provider register \
    --namespace Microsoft.Network
```

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://preview.portal.azure.com](https://preview.portal.azure.com).

## <a name="create-virtual-network"></a>Création d’un réseau virtuel

Un réseau virtuel est nécessaire pour les ressources qui se trouvent dans le pool backend de l'équilibreur de charge de la passerelle. 

1. Dans la zone de recherche située en haut du portail, entrez **Réseau virtuel**. Dans les résultats de la recherche, sélectionnez **Réseaux virtuels**.

2. Dans **Réseaux virtuels**, sélectionnez **+ Créer**.

3. Dans **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes sous l’onglet **Général** :

    | **Paramètre**          | **Valeur**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Détails du projet**  |                                                                 |
    | Abonnement     | Sélectionner votre abonnement Azure                                  |
    | Groupe de ressources   | Sélectionnez **Créer nouveau**. </br> Dans **Nom**, entrez **TutorialLBIP-rg**. </br> Sélectionnez **OK**. |
    | **Détails de l’instance** |                                                                 |
    | Nom             | Entrez **myVNet**                                    |
    | Région           | Sélectionnez **USA Est**. |

4. Sélectionnez l’onglet **Adresses IP**, ou sélectionnez le bouton **Suivant : Adresses IP** au bas de la page.

5. Sous l’onglet **Adresses IP**, entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Espace d’adressage IPv4 | Entrez **10.1.0.0/16** |

6. Sous **Nom de sous-réseau**, sélectionnez le mot **par défaut**.

7. Dans **Modifier le sous-réseau**, entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Nom du sous-réseau | Entrez **myBackendSubnet** |
    | Plage d’adresses de sous-réseau | Entrez **10.1.0.0/24** |

8. Sélectionnez **Enregistrer**.

9. Sélectionnez l'onglet **Sécurité** .

10. Sous **BastionHost**, sélectionnez **Activer**. Entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Nom du bastion | Entrez **myBastionHost** |
    | Espace d’adressage AzureBastionSubnet | Entrer **10.1.1.0/27** |
    | Adresse IP publique | Sélectionnez **Créer nouveau**. </br> Pour **Nom**, entrez **myBastionIP**. </br> Sélectionnez **OK**. |


11. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton **Vérifier + créer**.

12. Sélectionnez **Create** (Créer).

## <a name="create-nsg"></a>Création d’un groupe de sécurité réseau

L’exemple suivant permet de créer un groupe de sécurité réseau nommé : Vous allez configurer les règles NSG nécessaires pour le trafic réseau dans le réseau virtuel créé précédemment.

1. Dans la zone de recherche située en haut du portail, entrez **Sécurité réseau**. Dans les résultats de la recherche, sélectionnez **Groupe de sécurité réseau**.

2. Sélectionnez **+ Créer**.

3. Dans **Créer un groupe de sécurité réseau**, entrez ou sélectionnez les informations suivantes sous l’onglet **Informations de base** :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionner **TutorGwLB-RG** |
    | **Détails de l’instance** |   |
    | Nom | Entrez **myNSG**. |
    | Région | Sélectionnez **USA Est**. |

4. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton **Vérifier + créer**.

5. Sélectionnez **Create** (Créer).

6. Dans la zone de recherche située en haut du portail, entrez **Sécurité réseau**. Dans les résultats de la recherche, sélectionnez **Groupe de sécurité réseau**.

7. Sélectionnez **myNSG**.

8. Dans **Paramètres** de **myNSG**, sélectionnez **Règles de sécurité de trafic entrant**.

9. Sélectionnez **Ajouter**.

10. Dans **Ajouter une règle de sécurité de trafic entrant**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Source | Conservez la valeur par défaut **Tous**. |
    | Source port ranges | Conservez la valeur par défaut  **'*'** . |
    | Destination | Conservez la valeur par défaut **Tous**. |
    | Service | Conservez la valeur par défaut **Personnalisé**. |
    | Plages de ports de destination | Entrez **'*'** |
    | Protocole | sélectionnez **N'importe laquelle**. |
    | Action | Conservez la valeur par défaut **Autoriser**. |
    | Priority | Entrez **100**. | 
    | Nom | Entrer **myNSGRule-AllowAll-All** |

11. Sélectionnez **Ajouter**.

12. Sous **Paramètres**, sélectionnez **Règles de sécurité de trafic sortant**.

13. Sélectionnez **Ajouter**.

14. Dans **Ajouter une règle de sécurité de trafic entrant**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Source | Conservez la valeur par défaut **Tous**. |
    | Source port ranges | Conservez la valeur par défaut  **'*'** . |
    | Destination | Conservez la valeur par défaut **Tous**. |
    | Service | Conservez la valeur par défaut **Personnalisé**. |
    | Plages de ports de destination | Entrez **'*'** |
    | Protocol | Sélectionnez **TCP**. |
    | Action | Conservez la valeur par défaut **Autoriser**. |
    | Priority | Entrez **100**. | 
    | Nom | Entrer **myNSGRule-AllowAll-TCP-Out** |

15. Sélectionnez **Ajouter**.

Sélectionnez ce groupe de sécurité réseau lors de la création du Appliances virtuelles réseau pour votre déploiement.

## <a name="create-gateway-load-balancer"></a>Créez un équilibreur de charge de passerelle.

Dans cette section, vous allez créer la configuration et déployer l’équilibrage de charge de la passerelle. 

1. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

2. Dans la page **Équilibreur de charge**, sélectionnez **Créer**.

3. Dans l’onglet **Fonctions de base** de la page **Créer un équilibreur de charge**, entrez ou sélectionnez les informations suivantes : 

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | **Détails du projet** |   |
    | Abonnement               | Sélectionnez votre abonnement.    |    
    | Resource group         | Sélectionner **TutorGwLB-RG** |
    | **Détails de l’instance** |   |
    | Nom                   | Enter **myLoadBalancer-gw**                                   |
    | Région         | Sélectionnez **(États-Unis) USA Est**.                                        |
    | Type          | sélectionnez **Interne**.                                        |
    | SKU           | Sélectionner **Passerelle** |

    :::image type="content" source="./media/tutorial-gateway-portal/create-load-balancer.png" alt-text="Capture d’écran de l’onglet Créer des informations de base de l’équilibreur de charge standard." border="true":::

4. Sélectionnez **Suivant : configuration de l’adresse IP front-end** au bas de la page.

5. Dans **Configuration de l’adresse IP front-end**, sélectionnez **+ Ajouter une adresse IP front-end**.

6. Entrez **MyFrontEnd** dans **Nom**.

7. Sélectionnez **myBackendSubnet** dans **Sous-réseau**.

8. Sélectionnez **Dynamique** comme **Attribution**.

9. Sélectionnez **Ajouter**.

10. Sélectionnez **Suivant : Pools de back-end** au bas de la page.

11. Sous l’onglet **Pools de back-end**, sélectionnez **+ Ajouter un pool de back-end**.

12. Dans **Ajouter un pool de back-ends**, entrez ou sélectionnez les informations suivantes.

    | Paramètre | Value |
    | ------- | ----- |
    | Nom | Entrez **myBackendPool**. |
    | Configuration d’un pool de back-ends | Sélectionner **Carte réseau**. |
    | Version de l’adresse IP | Sélectionnez **IPv4**. |
    | **Configuration d’Azure Load Balancer** |   |
    | Type | Sélectionnez **Interne et Externe** |
    | Port interne | Conservez la valeur par défaut **10800**. |
    | Identificateur interne | Conservez la valeur par défaut **800**. |
    | Port externe | Conservez la valeur par défaut **10801**. |
    | Identificateur externe | Conservez la valeur par défaut **801**. |

13. Sélectionnez **Ajouter**.

14. Sélectionnez le bouton **Suivant : Règles de trafic entrant** au bas de la page.

15. Dans **Règle d’équilibrage de la charge** sous l’onglet **Règles de trafic entrant**, sélectionnez **+ Ajouter une règle d’équilibrage de charge**.

16. Dans **Ajouter une règle d’équilibrage de charge**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez **myLBRule** |
    | Version de l’adresse IP | Sélectionnez **IPv4** ou **IPv6** en fonction de vos besoins. |
    | Adresse IP du serveur frontal | Sélectionnez **myFrontEnd**. |
    | Pool principal | Sélectionnez **MyBackendPool**. |
    | Sonde d’intégrité | Sélectionnez **Créer nouveau**. </br> Dans **Nom**, entrez **myHealthProbe**. </br> Sélectionnez **HTTP** dans **Protocole**. </br> Laissez les autres valeurs par défaut et sélectionnez **OK**. |
    | Persistance de session | Sélectionnez **Aucun**. |

    :::image type="content" source="./media/tutorial-gateway-portal/add-load-balancing-rule.png" alt-text="Capture d’écran de l’ajout d’une règle d’équilibrage de charge." border="true":::

17. Sélectionnez **Ajouter**.

18. Sélectionnez le bouton bleu **Vérifier + créer** au bas de la page.

19. Sélectionnez **Create** (Créer).

## <a name="add-network-virtual-appliances-to-the-gateway-load-balancer-backend-pool"></a>Ajouter des appliances virtuelles réseau à la passerelle Load Balancer pool backend
Déployez Appliances virtuelles réseau via la place de marché Azure. Une fois le déploiement terminé, ajoutez les machines virtuelles NVA au pool principal en accédant à l’onglet Pools principaux de votre Load Balancer de passerelle.

## <a name="chain-load-balancer-frontend-to-gateway-load-balancer"></a>Chaînez un serveur frontal de l’équilibreur de charge à l’équilibreur de charge de la passerelle

Dans cet exemple, vous allez chaîner le serveur frontal d’un équilibreur de charge standard à l’équilibreur de charge de la passerelle. 

Vous allez ajouter le serveur frontal à l’adresse IP frontale d’un équilibreur de charge existant dans votre abonnement.

1. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**. Dans les résultats de la recherche, sélectionnez **Équilibreurs de charge**.

2. Dans **Équilibreur de charge**, sélectionnez **myLoadBalancer** ou votre équilibreur de charge existant

3. Sur la page de l'équilibreur de charge, sélectionnez **Configuration de l'IP du frontal** dans **Paramètres**.

4. Sélectionnez l’adresse IP frontale de l’équilibreur de charge. Dans cet exemple, le nom du serveur frontal est **myFrontendIP**.

    :::image type="content" source="./media/tutorial-gateway-portal/frontend-ip.png" alt-text="Capture d’écran de l’ajout d’une configuration d’adresse IP frontale." border="true":::

5. Sélectionnez **myFrontendIP (10.1.0.4)** dans la liste déroulante à côté de l’**équilibrage de charge de la passerelle**.

6. Sélectionnez **Enregistrer**.

    :::image type="content" source="./media/tutorial-gateway-portal/select-gateway-load-balancer.png" alt-text="Capture d’écran de l’ajout de l’équilibrage de charge de la passerelle à l’adresse IP frontale." border="true":::


## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, l’équilibreur de charge et toutes les ressources associées. Pour ce faire, sélectionnez le groupe de ressources **TutorGwLB-rg** qui contient les ressources, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Créer des appliances virtuelles réseau dans Azure. 

Lorsque vous créez le Appliances virtuelles réseau, choisissez les ressources créées dans ce tutoriel :

* Réseau virtuel

* Subnet

* Groupe de sécurité réseau

* Équilibreur de charge de passerelle

Passez à l’article suivant pour découvrir comment créer un équilibreur de charge Azure interrégional :
> [!div class="nextstepaction"]
> [Équilibreur de charge inter-région](tutorial-cross-region-powershell.md)
