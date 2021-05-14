---
title: 'Tutoriel : Ajouter Azure Load Balancer à un groupe de machines virtuelles identiques existant avec le portail Azure'
description: Dans ce tutoriel, découvrez comment ajouter un équilibreur de charge à un groupe de machines virtuelles identiques existant en utilisant le portail Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 4/21/2021
ms.custom: template-tutorial
ms.openlocfilehash: cb87efe8402d49463d10e25aa79c50fc3b9b4c6e
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2021
ms.locfileid: "107931035"
---
# <a name="tutorial-add-azure-load-balancer-to-an-existing-virtual-machine-scale-set-using-the-azure-portal"></a>Tutoriel : Ajouter Azure Load Balancer à un groupe de machines virtuelles identiques existant avec le portail Azure

Ce besoin peut se faire sentir quand Azure Load Balancer n’est pas associé à un groupe de machines virtuelles identiques. 

Vous avez peut-être un groupe de machines virtuelles identiques existant qui est déployé avec Azure Load Balancer et qui nécessite une mise à jour.

Utilisez alors le portail Azure pour ajouter ou mettre à jour le service Azure Load Balancer associé à un groupe de machines virtuelles identiques.  

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créez un réseau virtuel
> * Créer une passerelle NAT pour la connectivité sortante
> * Créer une référence SKU Azure Load Balancer standard
> * Créer un groupe de machines virtuelles identiques sans équilibreur de charge
> * Ajouter Azure Load Balancer à un groupe de machines virtuelles identiques

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Dans cette section, vous allez créer un réseau virtuel pour le groupe identique et les autres ressources utilisées dans le tutoriel.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Réseau virtuel**.

3. Dans les résultats de recherche, sélectionnez **Réseaux virtuels**.

4. Sélectionnez **+ Créer**.

5. Sous l’onglet **Informations de base** de la boîte de dialogue **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ------|
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **Créer nouveau**. </br> Entrez **TutorLBVMSS-rg** dans **Nom**. </br> Sélectionnez **OK**. |
    | **Détails de l’instance** |   |
    | Nom | Entrez **myVNet**. |
    | Région | Sélectionnez **(USA) USA Ouest 2**. |

6. Sélectionnez l’onglet **Adresses IP** ou le bouton **Suivant : Adresses IP** situé en bas de la page.

7. Sous l’onglet **Adresses IP**, sous **Nom du sous-réseau**, sélectionnez **Par défaut**.

8. Dans le volet **Modifier le sous-réseau**, sous **Nom du sous-réseau**, entrez **myBackendSubnet**.

9. Sélectionnez **Enregistrer**.

10. Sélectionnez l’onglet **Sécurité** ou le bouton **Suivant : Sécurité** situé en bas de la page.

11. Sous l’onglet **Sécurité**, dans **BastionHost**, sélectionnez **Activer**.

12. Entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom du bastion | Entrez **MyBastionHost**. |
    | Espace d’adressage AzureBastionSubnet | Entrez **10.1.1.0/27**. |
    | Adresse IP publique | Sélectionnez **Créer nouveau**. </br> Entrez **myBastionIP** dans **Nom**. |

13. Sélectionnez l’onglet **Vérifier + créer** ou le bouton bleu **Vérifier + créer** situé en bas de la page.

14. Sélectionnez **Create** (Créer).

## <a name="create-nat-gateway"></a>Créer une passerelle NAT 

Dans cette section, vous allez créer une passerelle NAT pour la connectivité sortante des machines virtuelles.

1. Dans la zone de recherche située en haut du portail, entrez **Passerelle NAT**.

2. Sélectionnez **Passerelle NAT** dans les résultats de la recherche.

3. Sélectionnez **+ Créer**.

4. Sous l’onglet **Informations de base** de **Créer une passerelle NAT (traduction d’adresses réseau)** , entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Groupe de ressources | Sélectionnez **TutorLBVMSS-rg**. |
    | **Détails de l’instance** |   |
    | Nom de la passerelle NAT | Entrez **myNATgateway**. |
    | Région | Sélectionnez **(USA) USA Ouest 2**. |
    | Zone de disponibilité | Sélectionnez **Aucun**. |
    | Délai d’inactivité (minutes) | Entrez **15**. |

5. Sélectionnez l’onglet **IP sortante**, ou sélectionnez le bouton **Suivant : IP sortante** situé au bas de la page.

6. Sélectionnez **Créer une adresse IP publique** en regard d’**Adresses IP publiques** sous l’onglet **IP sortante**.

7. Entrez **myPublicIP-nat** dans **Nom**.

8. Sélectionnez **OK**.

9. Sélectionnez l’onglet **Sous-réseau**, ou sélectionnez le bouton **Suivant : Sous-réseau** situé au bas de la page.

10. Sélectionnez **myVNet** dans le menu déroulant sous **Réseau virtuel**.

11. Cochez la case en regard de **myBackendSubnet**.

12. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer** situé au bas de la page.

13. Sélectionnez **Create** (Créer).

## <a name="create-load-balancer"></a>Créer un équilibreur de charge

Dans cette section, vous allez créer un équilibreur de charge pour les machines virtuelles.

1. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**.

2. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

3. Sélectionnez **+ Créer**.

4. Sous l’onglet **Informations de base** de **Créer un équilibreur de charge**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Groupe de ressources | Sélectionnez **TutorLBVMSS-rg**. |
    | **Détails de l’instance** |   |
    | Nom | Entrez **myLoadBalancer**. |
    | Région | Sélectionnez **(USA) USA Ouest 2**. |
    | Type | Conservez la valeur par défaut **Public**. |
    | SKU | Conservez la valeur par défaut **Standard**. |
    | Niveau | Conservez la valeur par défaut **Régional**. |
    | **Adresse IP publique** |   |
    | Adresse IP publique | Conservez la valeur par défaut **Créer**. |
    | Nom de l’adresse IP publique | Entrez **myPublicIP-lb**. |
    | Zone de disponibilité | Sélectionnez **Redondant dans une zone**. |
    | Ajouter une adresse IPv6 publique | Conservez la valeur par défaut **Non**. |
    | Préférence de routage | Laissez la valeur par défaut **Microsoft network**. |

5. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer** situé au bas de la page.

6. Sélectionnez **Create** (Créer).

### <a name="configure-load-balancer-settings"></a>Configurer les paramètres d’équilibreur de charge

Dans cette section, vous allez créer un pool de back-ends pour **myLoadBalancer**.

Vous allez créer une sonde d’intégrité pour superviser **HTTP** et le **Port 80**. La sonde d’intégrité supervise l’intégrité des machines virtuelles dans le pool de back-ends. 

Vous allez créer une règle d’équilibrage de charge pour le **Port 80** avec le mode SNAT sortantes désactivé. La passerelle NAT que vous avez créée précédemment traitera la connectivité sortante des machines virtuelles.

1. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**.

2. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

3. Sélectionnez **myLoadBalancer**.

4. Dans **myLoadBalancer**, sélectionnez **Pools de back-end** dans **Paramètres**.

5. Sélectionnez **+ Ajouter** dans **Pools de back-ends**.

6. Dans **Ajouter un pool de back-ends**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez **myBackendPool**. |
    | Réseau virtuel | Sélectionnez **myVNet**. |
    | Configuration d’un pool de back-ends | Conservez la valeur par défaut **Carte réseau**. |
    | Version de l’adresse IP | Conservez la valeur par défaut **IPv4**. |

7. Sélectionnez **Ajouter**.

8. Sélectionnez **Sondes d’intégrité**.

9. Sélectionnez **Ajouter**.

10. Dans **Ajouter une sonde d’intégrité**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez **myHTTPProbe**. |
    | Protocol | Sélectionnez **HTTP**. |
    | Port | Conservez la valeur par défaut **80**. |
    | Chemin d’accès | Conservez la valeur par défaut **/** . |
    | Intervalle | Conservez la valeur par défaut de **5** secondes. |
    | Seuil de défaillance sur le plan de l’intégrité | Conservez la valeur par défaut de **2** échecs consécutifs. |

11. Sélectionnez **Ajouter**.

12. Sélectionnez **Règles d’équilibrage de charge**. 

13. Sélectionnez **Ajouter**.

14. Entrez ou sélectionnez les informations suivantes dans **Ajouter une règle d’équilibrage de charge** :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez **MyHTTPRule**. |
    | Version de l’adresse IP | Conservez la valeur par défaut **IPv4**. |
    | Adresse IP du serveur frontal | Sélectionnez **LoadBalancerFrontEnd**. |
    | Protocole | Sélectionnez la valeur par défaut **TCP**. |
    | Port | Entrez **80**. |
    | Port principal | Entrez **80**. |
    | Pool principal | Sélectionnez **MyBackendPool**. |
    | Sonde d’intégrité | Sélectionnez **myHTTPProbe**. |
    | Persistance de session | Conservez la valeur par défaut **Aucune**. |
    | Délai d’inactivité (minutes) | Déplacez le curseur sur **15**. |
    | Réinitialisation du protocole TCP | Sélectionnez **Enabled**. |
    | IP flottante | Conservez la valeur par défaut **Désactivé**. |
    | Traduction d’adresses réseau (SNAT) sources sortante | Conservez la valeur par défaut **(Recommandé) Utiliser des règles de trafic sortant pour fournir aux membres du pool de back-ends l’accès à Internet**. |

5. Sélectionnez **Ajouter**.

## <a name="create-virtual-machine-scale-set"></a>Créer un groupe de machines virtuelles identiques

Dans cette section, vous allez créer un groupe de machines virtuelles identiques sans équilibreur de charge. Plus tard, vous allez ajouter un équilibreur de charge à ce groupe identique dans le portail Azure.

1. Dans la zone de recherche située en haut du portail, entrez **Groupe de machines virtuelles**.

2. Dans les résultats de la recherche, sélectionnez **Groupes de machines virtuelles identiques**.

3. Sélectionnez **Ajouter**.

4. Sous l’onglet **Informations de base** de la page **Créer un groupe de machines virtuelles identiques**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Groupe de ressources | Sélectionnez **TutorLBVMSS-rg**. |
    | **Détails du groupe identique** |   |
    | Nom du groupe de machines virtuelles identiques | Entrez **myVMScaleSet**. |
    | Région | Sélectionnez **(USA) USA Ouest 2**. |
    | Zone de disponibilité | Conservez la valeur par défaut **Aucune**. |
    | **Orchestration** |   |
    | Mode d’orchestration | Laissez la valeur par défaut **Uniforme : optimisé pour les charges de travail sans état à grande échelle avec des instances identiques**. |
    | **Détails de l’instance** |   |
    | Image | Sélectionnez **Windows Server 2019 Datacenter - Gen1**. |
    | Instance Azure Spot | Laissez la case décochée par défaut. |
    | Taille | Sélectionnez une taille. |
    | **Compte administrateur** |
    | Nom d’utilisateur | Entrez un nom d’utilisateur. |
    | Mot de passe | Entrez un mot de passe. |
    | Confirmer le mot de passe | Confirmez le mot de passe. |

5. Sélectionnez l’onglet **Réseau**.

6. Entrez ou sélectionnez les informations suivantes sous l’onglet **Réseau** :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Configuration du réseau virtuel** |   |
    | Réseau virtuel | Sélectionnez **myVNet**. |

7. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer** situé au bas de la page.

8. Sélectionnez **Create** (Créer).

## <a name="add-load-balancer-to-scale-set"></a>Ajouter un équilibreur de charge à un groupe identique

Dans cette section, vous allez accéder au groupe identique dans le portail Azure pour y ajouter un équilibreur de charge.

1. Dans la zone de recherche située en haut du portail, entrez **Groupe de machines virtuelles**.

2. Dans les résultats de la recherche, sélectionnez **Groupes de machines virtuelles identiques**.

3. Sélectionnez **myVMScaleSet**.

4. Dans la section **Paramètres** de **myVMScaleSet**, sélectionnez **Réseau**.

5. Sélectionnez l’onglet **Équilibrage de charge** dans la page **Vue d’ensemble** des paramètres **Réseau** de **myVMScaleSet**.

    :::image type="content" source="./media/tutorial-add-lb-existing-scale-set-portal/load-balancing-tab.png" alt-text="Sélectionnez l’onglet Équilibrage de charge dans Réseau." border="true":::

6. Sélectionnez le bouton bleu **Ajouter l’équilibrage de charge**.

7. Dans **Ajouter l’équilibrage de charge**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Options d’équilibrage de charge | Sélectionnez **Équilibreur de charge Azure**. |
    | Sélectionnez un équilibreur de charge | Sélectionnez **myLoadBalancer**. |
    | Pool principal | Sélectionnez **Utiliser l’existant**. |
    | Sélectionnez un pool principal | Sélectionnez **MyBackendPool**. |

8. Sélectionnez **Enregistrer**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne comptez pas continuer à utiliser cette application, supprimez l’équilibreur de charge et les ressources de prise en charge en effectuant les étapes suivantes :

1. Dans la zone de recherche située en haut du portail, entrez **Groupe de ressources**.

2. Sélectionnez **Groupes de ressources** dans les résultats de la recherche.

3. Sélectionnez **TutorLBVMSS-rg**.

4. Dans la page de présentation de **TutorLBVMSS-rg**, sélectionnez **Supprimer le groupe de ressources**.

5. Entrez **TutorLBVMSS-rg** dans **ENTREZ LE NOM DU GROUPE DE RESSOURCES**.

6. Sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous :

* Avez créé un réseau virtuel et un hôte Azure Bastion.
* Avez créé un équilibreur de charge Azure standard.
* Avez créé un groupe de machines virtuelles identiques.
* Avez ajouté un équilibreur de charge à un groupe de machines virtuelles identiques.

Passez à l’article suivant pour découvrir comment créer un équilibreur de charge Azure interrégional :
> [!div class="nextstepaction"]
> [Créer un équilibreur de charge interrégional](tutorial-cross-region-portal.md)