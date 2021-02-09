---
title: 'Scénario : Isolation personnalisée des réseaux virtuels et des branches'
titleSuffix: Azure Virtual WAN
description: 'Scénarios de routage : empêcher certains réseaux virtuels et certaines branches de pouvoir s’atteindre mutuellement'
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: wellee
ms.openlocfilehash: e8e5a5a1b9325f40fdd51133155a0daffaa55a7b
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99396238"
---
# <a name="scenario-custom-isolation-for-virtual-networks-and-branches"></a>Scénario : Isolation personnalisée des réseaux virtuels et des branches

Lorsque vous travaillez avec le routage de hub virtuel Virtual WAN, il existe un certain nombre de scénarios disponibles. Dans un scénario d’isolation personnalisé pour les réseaux virtuels et les branches, l’objectif est d’empêcher un ensemble spécifique de réseaux virtuels d’atteindre un autre ensemble de réseaux virtuels. De même, les branches (VPN/ER/User VPN) sont uniquement autorisées à atteindre certains ensembles de réseaux virtuels.

Nous présentons également la condition supplémentaire selon laquelle Pare-feu Azure doit inspecter le trafic de la branche au réseau virtuel et du réseau virtuel à la branche, mais **pas** le trafic de réseau virtuel à réseau virtuel.  

Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Conception

Pour déterminer le nombre de tables de routage nécessaires, vous pouvez créer une matrice de connectivité. Pour ce scénario, celle-ci se présente comme suit, où chaque cellule indique si une source (ligne) peut communiquer avec une destination (colonne) :

| Du | Par :| *Réseaux virtuels bleus* | *Réseaux virtuels rouges* | *Branches rouges*| *Branches bleues*| 
|---|---|---|---|---|---|
| **Réseaux virtuels bleus** |   &#8594;|   Direct     |           |   |  AzFW|
| **Réseaux virtuels rouges**  |   &#8594;|              |   Direct  |  AzFW  | 
| **Branches rouges**   |   &#8594;|   |   AzFW  |  Direct | Direct
| **Branches bleues**| &#8594;| AzFW  |   |Direct   | Direct

Chacune des cellules du tableau précédent indique si une connexion Virtual WAN (côté « De » du flux, les en-têtes de lignes) communique avec une destination (côté « À » du flux, en-têtes de colonne en italique). **Direct** implique que le trafic transite directement via Virtual WAN, tandis que **AzFW** implique que le trafic est inspecté par Pare-feu Azure avant d’être transféré vers la destination. Une entrée vide signifie que le flux est bloqué dans la configuration.

Dans ce cas, les deux tables de routage pour les réseaux virtuels sont nécessaires pour atteindre l’objectif d’isolation du réseau virtuel sans Pare-feu Azure dans le chemin. Nous appellerons ces tables de routage **RT_BLUE** et **RT_RED**.

En outre, les branches doivent toujours être associées à la table de routage **par défaut**. Pour garantir que le trafic depuis et vers les branches est inspecté par Pare-feu Azure, nous ajoutons des itinéraires statiques dans les tables de routage **Par défaut**, **RT_RED** et **RT_BLUE** qui pointent le trafic vers Pare-feu Azure et définissent des règles de réseau pour autoriser le trafic souhaité. Nous nous assurons également que les branches ne se propagent **pas** à **RT_BLUE** et **RT_RED**.

Voici donc la conception finale :

* Réseaux virtuels bleus :
  * Table de routage associée : **RT_BLUE**
  * Propagation aux tables de routage : **RT_BLUE**
* Réseaux virtuels rouges :
  * Table de routage associée : **RT_RED**
  * Propagation aux tables de routage : **RT_RED** 
* Branches :
  * Table de routage associée : **Par défaut**
  * Propagation aux tables de routage : **Par défaut**
* Itinéraires statiques :
    * **Table de routage par défaut** : Espaces d’adressage du réseau virtuel avec Pare-feu Azure du tronçon suivant
    * **RT_RED** : 0.0.0.0/0 avec Pare-feu Azure du tronçon suivant
    * **RT_BLUE** : 0.0.0.0/0 avec Pare-feu Azure du tronçon suivant
* Règles de réseau de pare-feu :
    * **RÈGLE ALLOW** **Préfixe de la source** : Préfixes d’adresse de la branche bleue **Préfixe de la destination** : Préfixes du réseau virtuel bleu 
    * **RÈGLE ALLOW** **Préfixe de la source** : Préfixes d’adresse de la branche rouge **Préfixe de la destination** : Préfixes du réseau virtuel rouge

> [!NOTE]
> Étant donné que toutes les branches doivent être associées à la table de routage Par défaut et se propager vers le même ensemble de tables de routage, toutes les branches ont le même profil de connectivité. Autrement dit, le concept Rouge/Bleu des réseaux virtuels ne peut pas être appliqué aux branches. Toutefois, pour obtenir un routage personnalisé pour les branches, nous pouvons transférer le trafic des branches vers Pare-feu Azure.

> [!NOTE]
> Par défaut, Pare-feu Azure refuse le trafic dans un modèle « zero-trust » (confiance nulle). S’il n’existe aucune règle **ALLOW** explicite qui correspond au paquet inspecté, Pare-feu Azure supprime le paquet.

Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).



## <a name="workflow"></a><a name="architecture"></a>Flux de travail

Dans la **figure 1**, il y a des réseaux virtuels bleus et rouges, ainsi que des branches qui peuvent accéder à des réseaux virtuels bleus ou rouges.

* Les réseaux virtuels connectés en bleu peuvent s’atteindre entre eux et peuvent atteindre toutes les connexions de branches bleues (VPN/ER/P2S). Dans le diagramme, la branche bleue est le site VPN site à site.
* Les réseaux virtuels connectés en rouge peuvent s’atteindre entre eux et peuvent atteindre toutes les connexions de branches rouges (VPN/ER/P2S). Dans le diagramme, la branche rouge correspond aux utilisateurs VPN point à site.

Suivez les étapes ci-dessous lors de la configuration du routage.

1. Créez deux tables de routage personnalisées dans le portail Azure, **RT_BLUE** et **RT_RED**, afin de personnaliser le trafic vers ces réseaux virtuels.
2. Pour la table de routage **RT_BLUE**, appliquez les paramètres suivants pour vous assurer que les réseaux virtuels bleus apprennent les préfixes d’adresse de tous les autres réseaux virtuels bleus :
   * **Association** : Sélectionnez tous les réseaux virtuels bleus.
   * **Propagation** : Sélectionnez tous les réseaux virtuels bleus.
3. Répétez les mêmes étapes pour la table de routage **RT_RED** pour les réseaux virtuels rouges.
4. Approvisionnez un pare-feu Azure dans Virtual WAN. Pour plus d’informations sur Pare-feu Azure dans le hub Virtual WAN, consultez [Configurer Pare-feu Azure dans un hub de réseau étendu virtuel](howto-firewall.md).
5. Ajoutez un itinéraire statique à la table de routage **Par défaut** du hub virtuel qui dirige tout le trafic destiné aux espaces d’adressage de réseau virtuel (bleu et rouge) vers Pare-feu Azure. Cette étape garantit que tous les paquets provenant de vos branches seront envoyés à Pare-feu Azure pour inspection.
    * Exemple : **Préfixe de destination** :  10.0.0.0/24 **Tronçon suivant** : Pare-feu Azure
    >[!NOTE]
    > Vous pouvez également effectuer cette étape via Firewall Manager en sélectionnant l’option « Trafic privé sécurisé ». Cette opération ajoute un itinéraire pour toutes les adresses IP privées RFC1918 applicables aux réseaux virtuels et aux branches. Vous devrez ajouter manuellement les branches ou les réseaux virtuels qui ne sont pas conformes à la norme RFC1918. 

6. Ajoutez un itinéraire statique à **RT_RED** et **RT_BLUE** redirigeant tout le trafic vers Pare-feu Azure. Cette étape garantit que les réseaux virtuels ne pourront pas accéder directement aux branches. Cette étape ne peut pas être effectuée via Firewall Manager, car ces réseaux virtuels ne sont pas associés à la table de routage par défaut.
    * Exemple : **Préfixe de destination** : 0.0.0.0/0 **Tronçon suivant** : Pare-feu Azure

    > [!NOTE]
    > Le routage s’effectue à l’aide de la correspondance de préfixe la plus longue. Par conséquent, les itinéraires statiques 0.0.0.0/0 ne seront **PAS** préférés aux préfixes exacts qui existent dans **BLUE_RT** et **RED_RT**. Par conséquent, le trafic intraréseau virtuel ne sera pas inspecté par Pare-feu Azure.

Cela entraîne des modifications de configuration de routage, comme illustré dans la figure ci-dessous.

**Figure 1**
[ ![Figure 1](./media/routing-scenarios/custom-branch-vnet/custom-branch.png) ](./media/routing-scenarios/custom-branch-vnet/custom-branch.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur Virtual WAN, consultez la [FAQ](virtual-wan-faq.md).
* Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).
