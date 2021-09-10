---
title: Gérer une IP publique avec un équilibreur de charge
titleSuffix: Azure Virtual Network
description: En savoir plus sur les méthodes d’utilisation d’une IP publique avec un équilibreur de charge Azure et sur la façon de modifier la configuration.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 88e67711b71ad80b48a7d5c19377847d0dadd296
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531542"
---
# <a name="manage-a-public-ip-address-with-a-load-balancer"></a>Gérer une IP publique avec un équilibreur de charge

Un équilibreur de charge public est une solution de couche 4 permettant de distribuer le trafic TCP et UDP à un pool principal. L’équilibreur de charge est disponible sous les niveaux tarifaires De base et Standard. 

Ces niveaux tarifaires correspondent aux niveaux tarifaires De base et Standard d’une IP publique.

Une IP publique associée à un équilibreur de charge fait office de configuration d’adresse IP frontale accessible sur Internet. Le serveur frontal est utilisé pour accéder aux ressources du pool principal. L’adresse IP du serveur frontal peut être utilisée pour les membres du pool principal en sortie vers Internet. 

Un équilibreur de charge Azure du niveau tarifaire De base est limité dans ses options de disponibilité et ses ensembles de fonctionnalités. La combinaison d’un équilibreur de charge et d’adresses IP du niveau tarifaire Standard est le déploiement recommandé pour les charges de travail de production. Les adresses IP du niveau tarifaire Standard prennent en charge les zones de disponibilité dans les régions prises en charge. 

Dans cet article, vous allez apprendre à créer un équilibreur de charge avec une IP publique existante dans votre abonnement. 

Vous apprendrez à modifier l’IP publique actuelle associée à un équilibreur de charge. 

Vous allez apprendre à modifier la configuration frontale d’un pool principal sortant en un préfixe d’IP publique.  

Enfin, l’article passe en revue les aspects uniques de l’utilisation des IP publiques et des préfixes d’IP publiques avec un équilibreur de charge. 

> [!NOTE]
> Un équilibreur de charge et une IP publique du niveau tarifaire Standard sont utilisés pour les exemples de cet article. Pour les équilibreurs de charge du niveau tarifaire De base, les procédures sont les mêmes, à l’exception de la sélection du niveau tarifaire lors de la création de l’équilibreur de charge et de la ressource d’IP publique. Les équilibreurs de charge De base ne prennent pas en charge les règles de trafic sortant ni les préfixes d’IP publiques. 

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Deux IP publiques du niveau tarifaire Standard dans votre abonnement. Les adresses IP ne peuvent pas être associées à des ressources. Pour plus d’informations sur la création d’une adresse IP publique de référence SKU Standard, consultez [Créer une adresse IP publique - portail Azure](create-public-ip-portal.md).
    - Pour les besoins des exemples de cet article, nommez les nouvelles IP publiques **myStandardPublicIP-1** et **myStandardPublicIP-2**.
- Un préfixe d’IP publique dans votre abonnement. Pour plus d’informations sur la création d’un préfixe d’IP publique, consultez [Créer un préfixe d’IP publique à l’aide du portail Azure](create-public-ip-prefix-portal.md).
    - Pour les besoins de l’exemple de cet article, nommez le nouveau préfixe d’IP publique **myPublicIPPrefixOutbound**.

## <a name="create-load-balancer-existing-public-ip"></a>Créer un équilibreur de charge pour l’IP publique existante

Dans cette section, vous allez créer un équilibreur de charge au niveau tarifaire Standard. Vous allez sélectionner l’adresse IP que vous avez créée dans la section Prérequis comme adresse IP frontale de l’équilibreur de charge.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**.

3. Dans les résultats de la recherche, sélectionnez **Équilibreurs de charge**.

4. Sélectionnez **+ Créer**.

5. Dans **Créer un équilibreur de charge**, entrez ou sélectionnez les informations suivantes.

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **Créer nouveau**. </br> Entrez **myResourceGroupIP**. </br> Sélectionnez **OK**. |
    | **Détails de l’instance** |   |
    | Nom | Entrez **myLoadBalancer**. |
    | Région | Sélectionnez **(USA) USA Ouest 2**. |
    | Type | Conservez la valeur par défaut **Public**. |
    | SKU | Conservez la valeur par défaut **Standard**. |
    | Niveau | Conservez la valeur par défaut **Régional**. |
    | **Adresse IP publique** |   |
    | Adresse IP publique | Sélectionnez **Utiliser l’existant**. |
    | Choisir une IP publique | Sélectionnez **myStandardPublicIP-1**. |
    | Ajouter une adresse IPv6 publique | Conservez la valeur par défaut **Non**. |

6. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer**. 

7. Sélectionnez **Create** (Créer).

> [!NOTE]
> Il s’agit d’un déploiement simple d’un équilibreur de charge. Pour une configuration et une installation avancées, consultez [Démarrage rapide : Créer un équilibreur de charge public pour équilibrer la charge de machines virtuelles en utilisant le portail Azure](../load-balancer/quickstart-load-balancer-standard-public-portal.md).
>
> Pour plus d’informations sur Azure Load Balancer, consultez [Qu’est-ce qu’Azure Load Balancer ?](../load-balancer/load-balancer-overview.md)

## <a name="change-or-remove-public-ip-address"></a>Modifier ou supprimer l’IP publique

Dans cette section, vous allez vous connecter au portail Azure et modifier l’adresse IP de l’équilibreur de charge. 

Un équilibreur de charge Azure doit avoir une adresse IP associée à un serveur frontal. Une IP publique distincte peut être utilisée en tant que serveur frontal pour le trafic d’entrée et de sortie. 

Pour modifier l’adresse IP, vous allez associer une nouvelle IP publique créée précédemment à l’aide du serveur frontal de l’équilibreur de charge.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**.

3. Dans les résultats de la recherche, sélectionnez **Équilibreurs de charge**.

4. Dans **Équilibreurs de charge**, sélectionnez **myLoadBalancer** ou l’équilibreur de charge que vous souhaitez modifier.

5. Dans les paramètres de **myLoadBalancer**, sélectionnez **Configuration d’adresses IP frontales**.

6. Dans **Configuration d’adresses IP frontales**, sélectionnez **LoadBalancerFrontend** ou le serveur frontal de votre équilibreur de charge.

7. Dans la configuration du serveur frontal de l’équilibreur de charge, sélectionnez **myStandardPublicIP-2** dans **IP publique**.

8. Sélectionnez **Enregistrer**.

9. Vérifiez que le serveur frontal de l’équilibreur de charge affiche la nouvelle adresse IP nommée **myStandardPublicIP-2**.

    > [!NOTE]
    > Ces procédures sont applicables à un équilibreur de charge inter-région. Pour plus d’informations sur l’équilibreur de charge inter-région, consultez **[Équilibreur de charge inter-région](../load-balancer/cross-region-overview.md)** .


## <a name="add-public-ip-prefix"></a>Ajouter le préfixe d’IP publique

L’équilibreur de charge Standard prend en charge les règles de trafic sortant pour la traduction d’adresses réseau sources (SNAT). La SNAT autorise la sortie vers Internet pour les membres du pool principal. Les préfixes d’IP publiques étendent l’extensibilité de la SNAT en autorisant plusieurs adresses IP pour les connexions sortantes. 

Plusieurs adresses IP évitent l’épuisement des ports SNAT. Chaque adresse IP frontale fournit 64 000 ports éphémères que l’équilibreur de charge peut utiliser. Pour plus d’informations, consultez [Règles de trafic sortant](../load-balancer/outbound-rules.md).

Dans cette section, vous allez modifier la configuration de serveur frontal utilisée pour les connexions sortantes afin d’utiliser un préfixe d’IP publique.

> [!IMPORTANT]
> Pour terminer cette section, vous devez disposer d’un équilibreur de charge avec une configuration sortante du serveur frontal et des règles de trafic sortant déployées.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**.

3. Dans les résultats de la recherche, sélectionnez **Équilibreurs de charge**.

4. Dans **Équilibreurs de charge**, sélectionnez **myLoadBalancer** ou l’équilibreur de charge que vous souhaitez modifier.

5. Dans les paramètres de **myLoadBalancer**, sélectionnez **Configuration d’adresses IP frontales**.

6. Dans **Configuration d’adresses IP frontales**, sélectionnez **LoadBalancerFrontendOutbound** ou le serveur frontal de sortie de votre équilibreur de charge.

7. Sous **Type d’adresse IP**, sélectionnez **Préfixe d’IP publique**.

8. Dans **Préfixe d’IP publique**, sélectionnez le préfixe d’IP publique que vous avez créé précédemment : **myPublicIPPrefixOutbound**.

9. Sélectionnez **Enregistrer**.

10. Dans **Configuration d’adresses IP frontales**, confirmez que le préfixe d’adresse IP a été ajouté à la configuration du serveur frontal de sortie.

## <a name="more-information"></a>Autres informations

* Les équilibreurs de charge inter-régions sont un type spécial d’équilibreur de charge public Standard qui peut couvrir plusieurs régions. Le serveur frontal d’un équilibreur de charge inter-région peut uniquement être utilisé avec l’option de niveau global des IP publiques du niveau tarifaire Standard. Le trafic envoyé à l’adresse IP frontale d’un équilibreur de charge inter-région est distribué entre les équilibreurs de charge publics régionaux. Les adresses IP frontales régionales sont contenues dans le pool principal de l’équilibreur de charge inter-région. Pour plus d’informations, consultez [Équilibreur de charge inter-région](../load-balancer/cross-region-overview.md).

* Par défaut, un équilibreur de charge public ne vous permet pas d’utiliser plusieurs règles d’équilibrage de charge avec le même port principal. Si une configuration à plusieurs règles sur le même port principal est nécessaire, activez l’option d’IP flottante pour une règle d’équilibrage de charge. Ce paramètre remplace l’adresse IP de destination du trafic envoyé au pool principal. Si l’IP flottante n’est pas activée, la destination sera l’adresse IP privée du pool principal. Si l’IP flottante est activée, l’adresse IP de destination sera l’IP publique du serveur frontal de l’équilibreur de charge. Cette IP publique doit être configurée dans la configuration réseau de l’instance principale pour recevoir correctement ce trafic. Interface de bouclage avec l’adresse IP de serveur frontal configurée dans l’instance. Pour plus d’informations, consultez [Configuration de l’IP flottante pour Azure Load Balancer](../load-balancer/load-balancer-floating-ip.md).

* Avec une configuration d’équilibreur de charge, les membres du pool principal peuvent souvent se voir attribuer des IP publiques au niveau de l’instance. Si cette architecture est utilisée, l’envoi de trafic directement vers ces adresses IP contourne l’équilibreur de charge. 

* Une valeur de délai d’expiration TCP peut être attribuée à la fois aux équilibreurs de charge publics standard et aux IP publiques pour déterminer combien de temps une connexion doit rester ouverte avant d’entendre des keepalives. Si une IP publique est attribuée en tant que serveur frontal de l’équilibreur de charge, la valeur du délai d’expiration sur l’adresse IP est prioritaire. Notez que ce paramètre s’applique uniquement aux connexions entrantes vers l’équilibreur de charge. Pour plus d’informations, consultez [Réinitialisation TCP de Load balancer](../load-balancer/load-balancer-tcp-reset.md).

## <a name="caveats"></a>Mises en garde

* Les équilibreurs de charge publics Standard peuvent utiliser des adresses IPv6 comme IP publiques frontales ou préfixes d’IP publiques.  Chaque déploiement doit être à double pile avec des serveurs frontaux IPv4 et IPv6. La traduction NAT64 n’est pas disponible. Pour plus d’informations, consultez [Déployer une application double pile IPv6 dans Azure – PowerShell](./virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).

* Lorsque plusieurs serveurs frontaux sont attribués à un équilibreur de charge public, aucune méthode ne permet d’attribuer des flux d’instances de serveur principal particulières à la sortie sur une adresse IP spécifique.  Pour plus d’informations, consultez [Serveurs frontaux multiples dans Azure Load Balancer](../load-balancer/load-balancer-multivip-overview.md).
## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer un équilibreur de charge et à utiliser une IP publique existante. 

Vous avez remplacé l’adresse IP dans une configuration de serveur frontal d’équilibreur de charge. 

Enfin, vous avez modifié une configuration de serveur frontal sortant pour utiliser un préfixe d’IP publique.

- Pour plus d’informations sur Azure Load Balancer, consultez [Qu’est-ce qu’Azure Load Balancer ?](../load-balancer/load-balancer-overview.md).
- Pour en savoir plus sur les adresses IP publiques dans Azure, consultez [Adresses IP publiques](public-ip-addresses.md).
