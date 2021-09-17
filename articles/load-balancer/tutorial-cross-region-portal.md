---
title: 'Tutoriel : Créer un équilibreur de charge interrégional à l’aide du portail Azure'
titleSuffix: Azure Load Balancer
description: Démarrez avec ce tutoriel pour déployer un équilibreur de charge Azure sur plusieurs régions avec le portail Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 08/02/2021
ms.openlocfilehash: f0004845033493dc7546bb3af467918ea77ebcad
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122201614"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-the-azure-portal"></a>Tutoriel : Créer un équilibreur de charge Azure inter-région à l’aide du portail Azure

Un équilibreur de charge interrégional garantit la disponibilité mondiale du service dans plusieurs régions Azure. En cas de défaillance d'une région, le trafic est acheminé vers l'équilibreur de charge régional sain le plus proche.  

Dans ce tutoriel, vous apprenez à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un équilibreur de charge interrégional.
> * Créer un pool de back-ends contenant deux équilibreurs de charge régionaux.
> * Créez une règle d’équilibreur de charge.
> * Tester l’équilibreur de charge.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!IMPORTANT]
> La réplication Azure Load Balancer entre plusieurs régions est disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure.
- Deux équilibreurs de charge Azure de SKU **standard** avec des pools de back-ends déployés dans deux régions Azure différentes.
    - Pour plus d’informations sur la création d’un équilibreur de charge standard régional et de machines virtuelles pour les pools de back-ends, consultez [Démarrage rapide : Créer un équilibreur de charge public pour équilibrer la charge de machines virtuelles en utilisant le portail Azure](quickstart-load-balancer-standard-public-portal.md).
        - Ajoutez **-R1** et **-R2** au nom des équilibreurs de charge, des machines virtuelles et des autres ressources dans chaque région. 

## <a name="sign-in-to-azure-portal"></a>Se connecter au portail Azure

[Connectez-vous](https://portal.azure.com) au portail Azure.

## <a name="create-cross-region-load-balancer"></a>Créer un équilibreur de charge interrégional

Dans cette section, vous allez créer un 

* Équilibreur de charge inter-région
* front-end avec une adresse IP publique globale
* pool de back-end avec deux équilibreurs de charge régionaux

> [!IMPORTANT]
> Pour effectuer ces étapes, vérifiez que deux équilibreurs de charge régionaux avec des pools de back-ends ont été déployés dans votre abonnement.  Pour plus d’informations, consultez **[Démarrage rapide : Créer un équilibreur de charge public pour équilibrer la charge de machines virtuelles en utilisant le portail Azure](quickstart-load-balancer-standard-public-portal.md)** .

1. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**. Sélectionnez **Équilibreur de charge** dans les résultats de recherche.

2. Dans la page **Équilibreur de charge**, sélectionnez **Créer**.

3. Dans l’onglet **Fonctions de base** de la page **Créer un équilibreur de charge**, entrez ou sélectionnez les informations suivantes : 

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | **Détails du projet** |    |
    | Abonnement               | Sélectionnez votre abonnement.    |    
    | Resource group         | Sélectionnez **Créer**, puis entrez **CreateCRLBTutorial-rg** dans la zone de texte. |
    | **Détails de l’instance** |   |
    | Nom                   | Entrez **myLoadBalancer-CR**.                                   |
    | Région         | Sélectionnez **(USA) USA Ouest**.                                        |
    | Type          | Sélectionnez **Public**.                                        |
    | SKU           | Conservez la valeur par défaut **Standard**. |
    | Niveau           | Sélectionnez **Global**. |

    :::image type="content" source="./media/tutorial-cross-region-portal/create-cross-region.png" alt-text="Créer un équilibreur de charge interrégional" border="true":::
  
4. Sélectionnez **Suivant : configuration de l’adresse IP front-end** au bas de la page.

5. Dans **Configuration de l’adresse IP front-end**, sélectionnez **+ Ajouter une adresse IP front-end**.

6. Entrez **LoadBalancerFrontend** dans **Nom** dans **Ajouter une adresse IP front-end**.

7. Sélectionnez **IPv4** ou **IPv6** pour la **Version IP**.

8. Dans **Adresse IP publique**, sélectionnez **Créer un nouveau**. Entrez **myPublicIP-cr** dans **Nom**.  Sélectionnez **OK**.

9. Sélectionnez **Ajouter**.

10. Sélectionnez **Suivant : Pools de back-end** au bas de la page.

11. Dans **Pools de back-end**, sélectionnez **+ Ajouter un pool de back-end**.

12. Entrez **myBackendPool-cr** dans **Nom** dans **Ajouter un pool de back-end**.

13. Dans **Équilibreurs de charge**, sélectionnez **myLoadBalancer-r1** ou votre premier équilibreur de charge régional dans la boîte de liste déroulante **Équilibreur de charge** . Vérifiez que la **Configuration IP front-end** et l'**adresse IP** correspondent à **myLoadBalancer-r1**.

14. Sélectionnez **myLoadBalancer-r2** ou votre deuxième équilibreur de charge régional dans la boîte de liste déroulante **Équilibreur de charge**. Vérifiez que la **Configuration IP front-end** et l'**adresse IP** correspondent à **myLoadBalancer-r2**.

15. Sélectionnez **Ajouter**.

16. Sélectionnez **Suivant : règles de trafic entrant** au bas de la page.

17. Dans **Règles de trafic entrant**, sélectionnez **+ Ajouter une règle d’équilibrage de charge**.

18. Dans **Ajouter une règle d’équilibrage de charge**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez **MyHTTPRule-cr**. |
    | Version de l’adresse IP | Sélectionnez **IPv4** ou **IPv6** pour la **Version IP**. |
    | Adresse IP du serveur frontal | Sélectionnez **LoadBalancerFrontend**. |
    | Protocol | Sélectionnez **TCP**. |
    | Port | Entrez **80**. |
    | Pool principal | Sélectionnez **myBackendPool-cr**. |
    | Persistance de session | Sélectionnez **Aucun**. |
    | Délai d’inactivité (minutes) | Entrez ou déplacez le curseur sur **15**. |
    | Réinitialisation du protocole TCP | Sélectionnez **Enabled**. |
    | IP flottante | Conservez la valeur par défaut **Désactivé**. |

19. Sélectionnez **Ajouter**.

20. Au bas de la page, sélectionnez **Examiner et créer**.

21. Sélectionnez **Créer** sous l’onglet **Vérifier + créer**.

    > [!NOTE]
    > L’équilibreur de charge interrégional peut uniquement être déployé dans les régions d’accueil suivantes : **USA Est 2, USA Ouest, Europe Ouest, Asie Sud-Est, USA Centre, Europe Nord, Asie Est**. Pour plus d’informations, consultez **https://aka.ms/homeregionforglb**.

## <a name="test-the-load-balancer"></a>Tester l’équilibreur de charge

Dans cette section, vous allez tester l’équilibreur de charge interrégional. Vous allez vous connecter à l’adresse IP publique dans un navigateur web.  Vous allez arrêter les machines virtuelles dans l’un des pools de back-ends de l’équilibreur de charge régional et observer le basculement.

1. Recherchez l’adresse IP publique de l’équilibreur de charge sur l’écran **Vue d’ensemble**. Sélectionnez **Tous les services** dans le menu de gauche, sélectionnez **Toutes les ressources**, puis **myPublicIP-CR**.

2. Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur. La page par défaut du serveur Web IIS s’affiche sur le navigateur.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-1.png" alt-text="Tester l’équilibreur de charge" border="true":::

3. Arrêtez les machines virtuelles dans le pool de back-ends de l’un des équilibreurs de charge régionaux.

4. Actualisez le navigateur web et observez le basculement de la connexion vers l’autre équilibreur de charge régional.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-2.png" alt-text="Tester l’équilibreur de charge après le basculement" border="true":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, l’équilibreur de charge et toutes les ressources associées. 

Pour ce faire, sélectionnez le groupe de ressources **CreateCRLBTutorial-rg** qui contient les ressources, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous allez :

* Vous avez créé un équilibreur de charge interrégional.
* Vous avez ajouté des équilibreurs de charge régionaux au pool de back-ends de l’équilibreur de charge interrégional.
* Vous avez créé une règle d’équilibrage de charge.
* Vous avez testé l’équilibreur de charge.

Pour plus d’informations sur l’équilibreur de charge interrégional, consultez :
> [!div class="nextstepaction"]
> [Équilibreur de charge inter-région (préversion)](cross-region-overview.md)
