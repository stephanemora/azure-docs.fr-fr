---
title: Gérer les règles pour Azure Load Balancer - Portail Azure
description: Dans cet article, découvrez comment gérer les règles pour Azure Load Balancer à l’aide du portail Azure
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 08/23/2021
ms.custom: template-how-to
ms.openlocfilehash: e94978fa61c144ced0b405717eefc8ffecc47873
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122832328"
---
# <a name="manage-rules-for-azure-load-balancer-using-the-azure-portal"></a>Gérer les règles pour Azure Load Balancer à l’aide du portail Azure

Azure Load Balancer prend en charge les règles de configuration du trafic vers le pool principal.  Dans cet article, vous allez apprendre à gérer les règles d’un Azure Load Balancer.

Il existe quatre types de règles :

* **Règles d’équilibrage de charge** : une règle d’équilibreur de charge sert à définir la manière dont le trafic entrant est distribué à **toutes** les instances du pool principal. Une règle d’équilibrage de charge fait correspondre une configuration d’adresse IP frontale et un port donnés à plusieurs adresses IP frontales et ports. Il peut s’agir, par exemple, d’une règle créée sur le port 80 pour équilibrer la charge du trafic web.

* **Ports à haute disponibilité** : une règle d’équilibreur de charge configurée avec **tous les protocoles** et le **port 0**. Ces règles permettent de fournir une seule règle pour équilibrer la charge de tout le trafic TCP et UDP qui arrive sur tous les ports d’un équilibreur de charge standard interne. Les règles d’équilibrage de charge des ports HA sont utiles dans les scénarios, comme pour la haute disponibilité et la mise à l’échelle d’appliances virtuelles réseau (NVA) dans des réseaux virtuels. La fonctionnalité peut servir quand un grand nombre de ports doit avoir une charge équilibrée.

* **Règle NAT de trafic entrant** : une règle NAT de trafic entrant transfère le trafic entrant envoyé à la combinaison de l’adresse IP frontale et du port. Le trafic est envoyé à une machine virtuelle ou instance **spécifique** dans le pool back-end. Ce réacheminement de port est accompli à l’aide de la même distribution basée sur le hachage que l’équilibrage de charge.

* **Règle de trafic sortant** : une règle de trafic sortant configure la traduction d’adresses réseau (NAT) du trafic sortant pour **toutes** les machines virtuelles ou instances identifiées par le pool principal. Cette règle permet aux instances du back-end de communiquer (vers l’extérieur) sur Internet ou vers d’autres points de terminaison.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Un équilibreur de charge public standard dans votre abonnement. Pour plus d’informations sur la création d’un Azure Load Balancer, consultez [Démarrage rapide : Créer un équilibreur de charge public pour équilibrer la charge de machines virtuelles à l’aide du portail Azure](quickstart-load-balancer-standard-public-portal.md). Le nom de l’équilibreur de charge pour les exemples de cet article est **myLoadBalancer**.

- Un équilibreur de charge interne standard dans votre abonnement. Pour plus d’informations sur la création d’un Azure Load Balancer, consultez [Démarrage rapide : Créer un équilibreur de charge interne pour équilibrer la charge de machines virtuelles à l’aide du portail Azure](quickstart-load-balancer-standard-internal-portal.md). Le nom de l’équilibreur de charge pour les exemples de cet article est **myLoadBalancer**.

## <a name="load-balancing-rules"></a>Règles d’équilibrage de la charge

Dans cette section, vous allez apprendre à ajouter et à supprimer une règle d’équilibrage de charge. Un équilibreur de charge public est utilisé dans les exemples.

### <a name="add-a-load-balancing-rule"></a>Ajouter une règle d’équilibrage de charge

Dans cet exemple, vous allez créer une règle pour équilibrer la charge du port 80.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

3. Sélectionnez **myLoadBalancer** ou votre équilibreur de charge.

4. Dans la page de l’équilibreur de charge, sélectionnez **Règles d’équilibrage de charge** dans **Paramètres**.

5. Sélectionnez **+ Ajouter** dans **Règles d’équilibrage de charge** pour ajouter une règle.

    :::image type="content" source="./media/manage-rules-how-to/load-balancing-rules.png" alt-text="Capture d’écran de la page des règles d’équilibrage de charge dans un équilibreur de charge standard." border="true":::

6. Entrez ou sélectionnez les informations suivantes dans **Ajouter une règle d’équilibrage de charge**.

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | **myHTTPRule** |
    | Version de l’adresse IP | Sélectionnez **IPv4** ou **IPv6**. |
    | Adresse IP du serveur frontal | Sélectionnez l’adresse IP frontale de l’équilibreur de charge. <br> Dans cet exemple, il s’agit de **myFrontendIP**. |
    | Protocol | Conservez la valeur par défaut **TCP**. |
    | Port | Entrez **80**. |
    | Port principal | Entrez **80**. |
    | Pool principal | Sélectionnez le pool principal de l’équilibreur de charge. </br> Dans cet exemple, il s’agit de **myBackendPool**. |
    | Sonde d’intégrité | Sélectionnez **Créer nouveau**. </br> Dans **Nom**, entrez **myHealthProbe**. </br> Sélectionnez **HTTP** dans **Protocole**. </br> Conservez les autres valeurs par défaut ou adaptez-les à vos besoins. </br> Sélectionnez **OK**. |
    | Persistance de session | Sélectionnez **Aucun** ou la persistance requise. </br> Pour plus d’informations sur les modes de distribution, consultez [Modes de distribution d’Azure Load Balancer](load-balancer-distribution-mode.md). | 
    | Délai d’inactivité (minutes) | Conservez la valeur par défaut **4** ou déplacez le curseur vers le délai d’inactivité requis. |
    | Réinitialisation du protocole TCP | Sélectionnez **Enabled**. </br> Pour plus d’informations sur la réinitialisation TCP, consultez [Réinitialisation TCP de Load Balancer et délai d’inactivité](load-balancer-tcp-reset.md). |
    | IP flottante | Conservez la valeur par défaut **Désactivé** ou activez si votre déploiement requiert une adresse IP flottante. </br> Pour plus d’informations sur l’adresse IP flottante, consultez [Configuration de l’adresse IP flottante d’Azure Load Balancer](load-balancer-floating-ip.md). |
    | Traduction d’adresses réseau (SNAT) sources sortante | Conservez la valeur par défaut **(Recommandé) Utiliser des règles de trafic sortant pour fournir aux membres du pool de back-ends l’accès à Internet**. </br> Pour plus d’informations sur les règles de trafic sortant et SNAT, consultez [Règles de trafic d’Azure Load Balancer](outbound-rules.md) et [Utilisation de SNAT (Source Network Address Translation) pour les connexions sortantes](load-balancer-outbound-connections.md).|

7. Sélectionnez **Ajouter**.

    :::image type="content" source="./media/manage-rules-how-to/add-load-balancing-rule.png" alt-text="Capture d’écran de la page Ajouter une règle d’équilibrage de charge." border="true":::

### <a name="remove-a-load-balancing-rule"></a>Supprimer une règle d’équilibrage de charge

Dans cet exemple, vous allez supprimer une règle d’équilibrage de charge.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

3. Sélectionnez **myLoadBalancer** ou votre équilibreur de charge.

4. Dans la page de l’équilibreur de charge, sélectionnez **Règles d’équilibrage de charge** dans **Paramètres**.

5. Sélectionnez les trois points en regard de la règle que vous souhaitez supprimer.

6. Sélectionnez **Supprimer**.

    :::image type="content" source="./media/manage-rules-how-to/remove-load-balancing-rule.png" alt-text="Capture d’écran de la suppression d’une règle d’équilibrage de charge." border="true":::

## <a name="high-availability-ports"></a>Ports à haute disponibilité

Dans cette section, vous allez apprendre à ajouter et à supprimer une règle de ports à haute disponibilité. Dans cet exemple, vous allez utiliser un équilibreur de charge interne. 

Les règles de ports HA sont prises en charge sur un équilibreur de charge interne standard.

### <a name="add-high-availability-ports-rule"></a>Ajouter une règle de ports à haute disponibilité

Dans cet exemple, vous allez créer une règle de ports à haute disponibilité.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

3. Sélectionnez **myLoadBalancer** ou votre équilibreur de charge.

4. Dans la page de l’équilibreur de charge, sélectionnez **Règles d’équilibrage de charge** dans **Paramètres**.

5. Sélectionnez **+ Ajouter** dans **Règles d’équilibrage de charge** pour ajouter une règle.

    :::image type="content" source="./media/manage-rules-how-to/load-balancing-rules.png" alt-text="Capture d’écran de la page des règles d’équilibrage de charge dans un équilibreur de charge standard." border="true":::

6. Entrez ou sélectionnez les informations suivantes dans **Ajouter une règle d’équilibrage de charge**.

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez **myHARule**. |
    | Version de l’adresse IP | Sélectionnez **IPv4** ou **IPv6**. |
    | Adresse IP du serveur frontal | Sélectionnez l’adresse IP frontale de l’équilibreur de charge. <br> Dans cet exemple, il s’agit de **myFrontendIP**. </br> Activez la case à cocher en regard de **Ports HA**. |
    | Pool principal | Sélectionnez le pool principal de l’équilibreur de charge. </br> Dans cet exemple, il s’agit de **myBackendPool**. |
    | Sonde d’intégrité | Sélectionnez **Créer nouveau**. </br> Dans **Nom**, entrez **myHealthProbe**. </br> Sélectionnez **TCP** dans **Protocole**. </br> Entrez un port TCP dans **Port**. Dans cet exemple, il s’agit du port **80**. Entrez un port qui répond à vos besoins. </br> Conservez les autres valeurs par défaut ou adaptez-les à vos besoins. </br> Sélectionnez **OK**. |
    | Persistance de session | Sélectionnez **Aucun** ou la persistance requise. </br> Pour plus d’informations sur les modes de distribution, consultez [Modes de distribution d’Azure Load Balancer](load-balancer-distribution-mode.md). | 
    | Délai d’inactivité (minutes) | Conservez la valeur par défaut **4** ou déplacez le curseur vers le délai d’inactivité requis. |
    | Réinitialisation du protocole TCP | Sélectionnez **Enabled**. </br> Pour plus d’informations sur la réinitialisation TCP, consultez [Réinitialisation TCP de Load Balancer et délai d’inactivité](load-balancer-tcp-reset.md). |
    | IP flottante | Conservez la valeur par défaut **Désactivé** ou activez si votre déploiement requiert une adresse IP flottante. </br> Pour plus d’informations sur l’adresse IP flottante, consultez [Configuration de l’adresse IP flottante d’Azure Load Balancer](load-balancer-floating-ip.md). |

    Pour plus d’informations sur la configuration d’une règle de ports HA, consultez **[Présentation des ports à haute disponibilité](load-balancer-ha-ports-overview.md)** .

7. Sélectionnez **Ajouter**.

    :::image type="content" source="./media/manage-rules-how-to/add-ha-ports-load-balancing-rule.png" alt-text="Capture d’écran de la page Ajouter une règle de ports HA." border="true":::

### <a name="remove-a-high-availability-ports-rule"></a>Supprimer une règle de ports à haute disponibilité

Dans cet exemple, vous allez supprimer une règle d’équilibrage de charge.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

3. Sélectionnez **myLoadBalancer** ou votre équilibreur de charge.

4. Dans la page de l’équilibreur de charge, sélectionnez **Règles d’équilibrage de charge** dans **Paramètres**.

5. Sélectionnez les trois points en regard de la règle que vous souhaitez supprimer.

6. Sélectionnez **Supprimer**.

    :::image type="content" source="./media/manage-rules-how-to/remove-ha-ports-load-balancing-rule.png" alt-text="Capture d’écran de la suppression d’une règle d’équilibrage de charge de ports HA." border="true":::

## <a name="inbound-nat-rule"></a>Règle NAT entrante

Les règles NAT de trafic entrant sont utilisées pour acheminer les connexions vers une machine virtuelle spécifique dans le pool principal. Pour plus d’informations et pour obtenir un tutoriel détaillé sur la configuration et le test de règles NAT de trafic entrant, consultez [Tutoriel : Configurer la redirection de port dans Azure Load Balancer à l’aide du portail](tutorial-load-balancer-port-forwarding-portal.md).

## <a name="outbound-rule"></a>Règle de trafic sortant

Vous allez apprendre à ajouter et à supprimer une règle de trafic sortant dans cette section. Dans cet exemple, vous allez utiliser un équilibreur de charge public. 

Les règles de trafic sortant sont prises en charge sur les équilibreurs de charge publics standard.

### <a name="add-outbound-rule"></a>Ajouter une règle de trafic sortant

Dans cet exemple, vous allez créer une règle de trafic sortant.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

3. Sélectionnez **myLoadBalancer** ou votre équilibreur de charge.

4. Dans la page de l’équilibreur de charge, sélectionnez **Règles de trafic sortant** dans **Paramètres**.

5. Sélectionnez **+ Ajouter** dans **Règles de trafic sortant** pour ajouter une règle.

    :::image type="content" source="./media/manage-rules-how-to/outbound-rules.png" alt-text="Capture d’écran de la page des règles de trafic sortant dans un équilibreur de charge standard." border="true":::

6. Entrez ou sélectionnez les informations suivantes dans **Ajouter une règle de trafic sortant**.

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez **myOutboundRule**. |
    | Adresse IP du serveur frontal | Sélectionnez l’adresse IP frontale de l’équilibreur de charge. <br> Dans cet exemple, il s’agit de **myFrontendIP**. | 
    | Protocol | Conservez la valeur par défaut **Tous**. |
    | Délai d’inactivité (minutes) | Conservez la valeur par défaut **4** ou déplacez le curseur en fonction de vos besoins. |
    | Réinitialisation du protocole TCP | Conservez la valeur par défaut **Activé**. |
    | Pool principal | Sélectionnez le pool principal de l’équilibreur de charge. </br> Dans cet exemple, il s’agit de **myBackendPool**. |
    | **Allocation de ports** |   |
    | Allocation de ports | Sélectionnez **Choisir manuellement le nombre de ports de sortie**. |
    | **Ports de sortie** |  |
    | Choisir par | Sélectionnez **Ports par instance**. |
    | Ports par instance | Entrez **10000**. |

7. Sélectionnez **Ajouter**.

    :::image type="content" source="./media/manage-rules-how-to/add-outbound-rule.png" alt-text="Capture d’écran de la page Ajouter une règle de trafic sortant." border="true":::

### <a name="remove-an-outbound-rule"></a>Supprimer une règle de trafic sortant

Dans cet exemple, vous allez supprimer une règle de trafic sortant.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

3. Sélectionnez **myLoadBalancer** ou votre équilibreur de charge.

4. Dans la page de l’équilibreur de charge, sélectionnez **Règles de trafic sortant** dans **Paramètres**.

5. Sélectionnez les trois points en regard de la règle que vous souhaitez supprimer.

6. Sélectionnez **Supprimer**.

    :::image type="content" source="./media/manage-rules-how-to/remove-outbound-rule.png" alt-text="Capture d’écran de la suppression d’une règle de trafic sortant." border="true":::

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment gérer les règles d’équilibrage de charge pour un Azure Load Balancer.

Pour plus d’informations sur Azure Load Balancer, consultez :
- [Qu’est-ce qu’Azure Load Balancer ?](load-balancer-overview.md)
- [Forum aux questions (FAQ) - Azure Load Balancer](load-balancer-faqs.yml)
