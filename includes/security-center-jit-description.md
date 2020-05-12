---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 02/24/2020
ms.topic: include
ms.openlocfilehash: 78bf29a170152666d82ec26504ee8f61ed90636a
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82616018"
---
## <a name="attack-scenario"></a>Scénario d’attaque

Les attaques par force brute ciblent généralement les ports de gestion cible pour accéder à une machine virtuelle. S’il réussit, un attaquant peut prendre le contrôle sur la machine virtuelle et créer une brèche dans votre environnement.

Pour réduire l’exposition aux attaques par force brute, vous pouvez limiter la durée d’ouverture d’un port. Les ports de gestion n’ont pas besoin d’être toujours ouverts. Ils doivent uniquement être ouverts lorsque vous êtes connecté à la machine virtuelle, par exemple pour effectuer des tâches de maintenance ou de gestion. Lorsque la fonctionnalité juste-à-temps est activée, Security Center utilise des règles de [groupe de sécurité réseau](../articles/virtual-network/security-overview.md#security-rules) (NSG) et de pare-feu Azure qui limitent l’accès aux ports de gestion, pour qu’ils ne soient pas la cible d’attaquants.

![Scénario juste-à-temps](../articles/security-center/media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Fonctionnement de l’accès JIT

Lorsque la fonctionnalité juste-à-temps est activée, Security Center verrouille le trafic entrant vers vos machines virtuelles Azure en créant une règle de groupe de sécurité réseau (NSG). Vous sélectionnez les ports de la machine virtuelle pour lesquels le trafic entrant sera verrouillé. Ces ports sont contrôlés par la solution juste-à-temps.

Quand un utilisateur demande l’accès à une machine virtuelle, Security Center vérifie que cet utilisateur dispose des autorisations [Contrôle d’accès en fonction du rôle (RBAC)](../articles/role-based-access-control/role-assignments-portal.md) sur cette machine virtuelle. Si la requête est approuvée, Security Center configure automatiquement les groupes de sécurité réseau (NSG) et le pare-feu Azure afin d’autoriser le trafic entrant vers les ports sélectionnés et vers les plages ou adresses IP source demandées, pendant la durée spécifiée. Après expiration du délai, Security Center restaure les groupes de sécurité réseau à leur état précédent. Ces connexions déjà établies ne sont cependant pas interrompues.

 > [!NOTE]
 > Si une demande d’accès juste-à-temps est approuvée pour une machine virtuelle derrière un pare-feu Azure, Security Center modifie automatiquement les règles de stratégie du groupe de sécurité réseau et du pare-feu. Pendant la durée qui a été spécifiée, les règles autorisent le trafic entrant vers les ports sélectionnés et les plages ou adresses IP sources demandées. Une fois la durée écoulée, Security Center restaure les règles du groupe de sécurité réseau et du pare-feu à leur état précédent.


## <a name="roles-that-can-read-jit-policies"></a>Rôles pouvant lire les stratégies JIT

Les rôles **Lecteur** et **Lecteur Sécurité** peuvent tous les deux lire les stratégies.

## <a name="permissions-needed-to-configure-and-use-jit"></a>Autorisations nécessaires pour configurer et utiliser JIT

Si vous voulez créer des rôles personnalisés qui peuvent fonctionner avec JIT, vous devez disposer des informations suivantes :

| Pour permettre à un utilisateur de : | Autorisations à définir|
| --- | --- |
| Configurer ou modifier une stratégie juste-à-temps pour une machine virtuelle | *Attribuez ces actions au rôle :*  <ul><li>Dans le cadre d’un abonnement ou d’un groupe de ressources qui est associé à la machine virtuelle :<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> Dans le cadre d’un abonnement, d’un groupe de ressources de machine virtuelle : <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|Demander l’accès JIT à une machine virtuelle | *Attribuez ces actions à l’utilisateur :*  <ul><li>Dans le cadre d’un abonnement ou d’un groupe de ressources qui est associé à la machine virtuelle :<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>Dans le cadre d’un abonnement ou d’un groupe de ressources qui est associé à la machine virtuelle :<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  Dans le cadre d’un abonnement, d’un groupe de ressources ou d’une machine virtuelle :<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  Dans le cadre d’un abonnement, d’un groupe de ressources ou d’une machine virtuelle :<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
|Lire les stratégies JIT| *Attribuez ces actions à l’utilisateur :*  <ul><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/read`</li><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`</li><li>`Microsoft.Security/policies/read`</li><li>`Microsoft.Compute/virtualMachines/read`</li><li>`Microsoft.Network/*/read`</li>|