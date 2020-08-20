---
title: Fonctionnement de l’accès aux machines virtuelles juste-à-temps dans Azure Security Center
description: Ce document explique comment l’accès aux machines virtuelles juste-à-temps dans Azure Security Center peut vous aider à contrôler l’accès à vos machines virtuelles Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 9c77ed2bf0d764fbbbe24770cc70b3fbeec7f678
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87833451"
---
# <a name="understanding-just-in-time-jit-vm-access"></a>Fonctionnement de l’accès aux machines virtuelles juste-à-temps (JAT)

Cette page explique les principes qui sous-tendent la fonctionnalité d’accès aux machines virtuelles juste-à-temps (JAT) d’Azure Security Center et la logique qui sous-tend la recommandation.

Pour savoir comment appliquer l’accès JAT à vos machines virtuelles à l’aide du portail Azure (Security Center ou Machines virtuelles Microsoft Azure) ou par programmation, consultez [Comment sécuriser vos ports de gestion à l’aide de l’accès JAT](security-center-just-in-time.md).


## <a name="the-risk-of-open-management-ports-on-a-virtual-machine"></a>Risque inhérent à des ports de gestion ouverts sur une machine virtuelle

Les acteurs des menaces repèrent activement les machines accessibles avec des ports de gestion ouverts, par exemple pour les protocoles RDP ou SSH. Toutes vos machines virtuelles sont des cibles potentielles pour une attaque. Lorsqu’une machine virtuelle est compromise, elle est utilisée comme point d’entrée pour attaquer d’autres ressources au sein de votre environnement.



## <a name="why-jit-vm-access-is-the-solution"></a>Raison pour laquelle l’accès aux machines virtuelles JAT est la solution 

Comme pour toutes les techniques de prévention en cybersécurité, votre objectif doit être de réduire la surface d’attaque. Dans ce cas, cela signifie avoir moins de ports ouverts, en particulier des ports de gestion.

Vos utilisateurs légitimes utilisent également ces ports, il n’est donc pas pratique de les garder fermés.

Pour résoudre ce dilemme, Azure Security Center propose l’accès JAT. Grâce à l’accès JAT, vous pouvez verrouiller le trafic entrant sur vos machines virtuelles, ce qui réduit l’exposition aux attaques tout en facilitant la connexion aux machines virtuelles le cas échéant.



## <a name="how-jit-operates-with-network-security-groups-and-azure-firewall"></a>Fonctionnement de l’accès JAT avec des groupes de sécurité réseau et Pare-feu Azure

Lorsque vous activez l’accès aux machines virtuelles juste-à-temps, vous pouvez sélectionner les ports de la machine virtuelle vers lesquels le trafic entrant sera bloqué. Security Center garantit que des règles de « refus de tout le trafic entrant » existent pour les ports sélectionnés dans les règles du [groupe de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) (NSG) et de [Pare-feu Azure](https://docs.microsoft.com/azure/firewall/rule-processing). Ces règles restreignent l’accès aux ports de gestion de vos machines virtuelles Azure et les protègent contre les attaques. 

Si d’autres règles existent déjà pour les ports sélectionnés, celles-ci sont prioritaires sur les nouvelles règles de « refus de tout le trafic entrant ». S’il n’existe aucune règle sur les ports sélectionnés, alors les nouvelles règles sont prioritaires dans le groupe de sécurité réseau et Pare-feu Azure.

Quand un utilisateur demande l’accès à une machine virtuelle, Security Center vérifie que cet utilisateur dispose des autorisations de [contrôle d’accès en fonction du rôle Azure (Azure RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) sur cette machine virtuelle. Si la requête est approuvée, Security Center configure les NSG et Pare-feu Azure afin d’autoriser le trafic entrant vers les ports sélectionnés à partir de l’adresse IP (ou de la plage d’adresses IP) correspondante, pendant la durée spécifiée. Après expiration du délai, Security Center restaure les groupes de sécurité réseau à leur état précédent. Les connexions déjà établies ne sont pas interrompues.

> [!NOTE]
> L’accès JAT ne prend pas en charge les machines virtuelles protégées par des pare-feu Azure contrôlés par [Azure Firewall Manager](https://docs.microsoft.com/azure/firewall-manager/overview).




## <a name="how-security-center-identifies-which-vms-should-have-jit-applied"></a>Méthode utilisée par Security Center pour identifier les machines virtuelles auxquelles l’accès JAT doit être appliqué

Le diagramme ci-dessous montre la logique que Security Center applique lorsqu’il décide de la manière de classer les machines virtuelles prises en charge : 

[![Just-in-time (JIT) virtual machine (VM) logic flow](media/just-in-time-explained/jit-logic-flow.png)](media/just-in-time-explained/jit-logic-flow.png#lightbox)

Lorsque Security Center trouve une machine qui peut tirer parti de l’accès JAT, il l’ajoute à l’onglet **Ressources non saines** de la recommandation. 

![Recommandation d’accès aux machines virtuelles juste-à-temps (JAT)](./media/just-in-time-explained/unhealthy-resources.png)


## <a name="faq---questions-about-just-in-time-virtual-machine-access"></a>FAQ : questions relatives à l’accès aux machines virtuelles juste-à-temps

### <a name="what-permissions-are-needed-to-configure-and-use-jit"></a>Quelles sont les autorisations nécessaires pour configurer et utiliser l’accès JAT ?

Si vous voulez créer des rôles personnalisés qui peuvent fonctionner avec l’accès JAT, vous aurez besoin des détails du tableau ci-dessous.

> [!TIP]
> Pour créer un rôle de moindre privilège pour les utilisateurs qui doivent demander un accès JAT à une machine virtuelle et n’exécuter aucune autre opération JAT, utilisez le script [Set-JitLeastPrivilegedRole](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role) à partir des pages de la communauté GitHub Security Center.

| Pour permettre à un utilisateur de : | Autorisations à définir|
| --- | --- |
| Configurer ou modifier une stratégie juste-à-temps pour une machine virtuelle | *Attribuez ces actions au rôle :*  <ul><li>Dans le cadre d’un abonnement ou d’un groupe de ressources qui est associé à la machine virtuelle :<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> Dans le cadre d’un abonnement, d’un groupe de ressources de machine virtuelle : <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|Demander l’accès JIT à une machine virtuelle | *Attribuez ces actions à l’utilisateur :*  <ul><li>Dans le cadre d’un abonnement ou d’un groupe de ressources qui est associé à la machine virtuelle :<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>Dans le cadre d’un abonnement ou d’un groupe de ressources qui est associé à la machine virtuelle :<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  Dans le cadre d’un abonnement, d’un groupe de ressources ou d’une machine virtuelle :<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  Dans le cadre d’un abonnement, d’un groupe de ressources ou d’une machine virtuelle :<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
|Lire les stratégies JIT| *Attribuez ces actions à l’utilisateur :*  <ul><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/read`</li><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`</li><li>`Microsoft.Security/policies/read`</li><li>`Microsoft.Compute/virtualMachines/read`</li><li>`Microsoft.Network/*/read`</li>|
|||





## <a name="next-steps"></a>Étapes suivantes

Cette page explique _pourquoi_ l’accès aux machines virtuelles juste-à-temps (JAT) doit être utilisé. 

Passez à l’article pratique pour en savoir plus sur l’activation de l’accès JAT et la demande d’accès à vos machines virtuelles prenant en charge l’accès JAT :

> [!div class="nextstepaction"]
> [Comment sécuriser vos ports de gestion avec l’accès JAT](security-center-just-in-time.md)
