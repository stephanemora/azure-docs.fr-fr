---
title: Vue d’ensemble des serveurs principaux multilocataires, tels que Azure App service, avec Azure Application Gateway
description: Cette page présente la prise en charge des serveurs principaux multilocataires par Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: victorh
ms.openlocfilehash: 8434340bb7ed95cc36115c05048b2b67682b5796
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224496"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Fin de prise en charge d’application Gateway pour la sauvegarde de l’architecture mutualisée comme App service

Dans l’architecture mutualisées des conceptions architecturales dans les serveurs web, plusieurs sites Web sont en cours d’exécution sur la même instance de serveur web. Noms d’hôtes sont utilisés pour faire la distinction entre les différentes applications qui sont hébergées. Par défaut, la passerelle d’application ne modifie pas l’en-tête d’hôte HTTP entrant à partir du client et envoie l’en-tête non modifié au serveur principal. Cette fonctionne bien pour les membres du pool principal telles que des NIC, machines virtuelles identiques, les adresses IP publiques, internes des adresses IP et nom de domaine complet comme ils ne reposent pas sur un en-tête d’hôte spécifique ou d’une extension SNI pour résoudre au point de terminaison correct. Toutefois, il existe de nombreux services tels que les applications Azure App service web et de gestion des API Azure qui sont multilocataires par nature et s’appuient sur un en-tête d’hôte spécifique ou d’une extension SNI pour résoudre au point de terminaison correct. En règle générale, le nom DNS de l’application, qui à son tour est le nom DNS associé à la passerelle d’application, est différent du nom de domaine du service principal. Par conséquent, l’en-tête d’hôte dans la demande d’origine est reçue par la passerelle d’application n’est pas le même que le nom d’hôte du service principal. Pour cette raison, sauf si l’en-tête d’hôte dans la demande à partir de la passerelle d’application vers le serveur principal est modifié pour le nom d’hôte du service principal, les serveurs principaux d’architecture mutualisée ne sont pas en mesure de résoudre la demande au point de terminaison correct. 

Application gateway fournit une fonctionnalité qui permet aux utilisateurs de remplacer l’en-tête d’hôte HTTP dans la demande en fonction du nom d’hôte de back-end. Cette fonctionnalité permet la prise en charge des serveurs principaux multilocataires tels que les applications Azure App service web et de gestion des API. Cette fonctionnalité est disponible pour la v1 et v2 standard et références (SKU) WAF. 

![remplacement de l’hôte](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Cela n’est pas applicable à Azure App service environment (ASE) dans la mesure où les ASE est une ressource dédiée à la différence d’Azure App service, qui est une ressource de l’architecture mutualisée.

## <a name="override-host-header-in-the-request"></a>Remplacer l’en-tête d’hôte dans la demande

La possibilité de spécifier un remplacement de l’hôte est définie dans le [paramètres HTTP](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) et peuvent être appliquées à n’importe quel pool back-end lors de la création de règle. Deux façons de remplacer l’en-tête d’hôte et l’extension SNI pour les serveurs principaux multilocataires est pris en charge :

- La possibilité de définir le nom d’hôte sur une valeur fixe est explicitement entrée dans les paramètres HTTP. Cette fonctionnalité garantit que l’en-tête d’hôte est remplacé par cette valeur pour tout le trafic vers le pool back-end où les paramètres HTTP particuliers sont appliquées. Lorsque vous utilisez le protocole SSL de bout en bout, ce nom d’hôte remplacé est utilisé dans l’extension SNI. Cette fonctionnalité permet de mettre en œuvre des scénarios dans lesquels une batterie de pools de serveurs principaux attend un en-tête d’hôte différent de l’en-tête d’hôte client entrant.

- La possibilité de dériver le nom d’hôte de l’adresse IP ou du nom de domaine complet des membres du pool de serveurs principaux. Paramètres HTTP fournissent également une option permettant de choisir dynamiquement le nom d’hôte à partir du nom de domaine complet d’un membre de pool back-end si configuré avec l’option permettant de dériver le nom d’hôte à partir d’un membre individuel pool back-end. Lorsque vous utilisez le protocole SSL de bout en bout, ce nom d’hôte est dérivé du nom de domaine complet et utilisé dans l’extension SNI. Cette fonctionnalité permet de mettre en œuvre des scénarios dans lesquels un pool de serveurs principaux peut avoir deux ou plusieurs services PaaS multilocataires tels que des applications web Azure et l’en-tête de l’hôte de la demande vers chaque membre contient le nom d’hôte dérivé de son nom de domaine complet. Pour implémenter ce scénario, nous utilisons un commutateur dans les paramètres HTTP appelé [choisir le nom d’hôte à partir de l’adresse du serveur principal](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-backend-address) qui remplacera dynamiquement l’en-tête d’hôte dans la demande d’origine à celui mentionné dans le pool principal.  Par exemple, si votre nom de domaine complet du pool principal contient « contoso11.azurewebsites.net » et « contoso22.azurewebsites.net », en-tête de l’hôte de la demande d’origine qui est contoso.com substitution contoso11.azurewebsites.net ou contoso22.azurewebsites.net Lorsque la demande est envoyée au serveur principal approprié. 

  ![scénario d’application web](./media/application-gateway-web-app-overview/scenario.png)

Avec cette fonctionnalité, les clients spécifient les options dans les paramètres HTTP et les sondes personnalisées pour la configuration appropriée. Ce paramètre est ensuite lié à un écouteur et à un pool de serveurs principaux à l’aide d’une règle.

## <a name="special-considerations"></a>Considérations spéciales

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>Un arrêt SSL et SSL de bout en bout avec les services de l’architecture mutualisées

Un arrêt SSL et le chiffrement SSL de bout en bout est pris en charge avec les services de l’architecture mutualisées. Pour un arrêt SSL au niveau de la passerelle d’application, certificat SSL continue d’être doivent être ajoutées à l’écouteur de passerelle d’application. Toutefois, en cas de SSL de bout en bout, Azure approuvé services, tels que les applications Azure App service web ne nécessitent pas de mise en liste verte les serveurs principaux dans la passerelle d’application. Par conséquent, il n’est pas nécessaire d’ajouter le certificat d’authentification. 

![SSL de bout en bout](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Notez que dans l’illustration ci-dessus, il n’est pas nécessaire pour ajouter des certificats d’authentification lors de l’App service est sélectionné en tant que serveur principal.

### <a name="health-probe"></a>Sonde d’intégrité

Substitution de l’en-tête d’hôte dans le **paramètres HTTP** affecte uniquement la demande et son routage. Il n’affecte pas le comportement de sonde d’intégrité. Pour que la fonctionnalité de bout en bout fonctionne, la sonde et les paramètres HTTP doivent être modifiés pour refléter la configuration correcte. En plus de fournir la possibilité de spécifier un en-tête d’hôte dans la configuration de sonde, les sondes personnalisées prennent également en charge la possibilité de dériver l’en-tête d’hôte à partir des paramètres HTTP configurés actuellement. Cette configuration peut être spécifiée à l’aide du paramètre `PickHostNameFromBackendHttpSettings` dans la configuration de la sonde.

### <a name="redirection-to-app-services-url-scenario"></a>Redirection vers le scénario d’URL du Service de l’application

Il peut y avoir des scénarios où le nom d’hôte dans la réponse à partir du service d’application peut diriger le navigateur de l’utilisateur final à la *. azurewebsites.net les nom d’hôte plutôt que le domaine associé à la passerelle d’Application. Ce problème peut se produire lorsque :

- Vous avez configurée sur votre Service d’application de la redirection. La redirection peut être aussi simple que l’ajout d’une barre oblique à la demande.
- Vous avez l’authentification Azure AD qui provoque la redirection.

Pour résoudre de tels cas, consultez [résoudre les problèmes de la redirection vers le problème d’URL du service de l’application](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment configurer une passerelle d’application avec une application mutualisée comme application web Azure App service en tant que membre du pool back-end en vous rendant sur [configurer App Service web apps avec Application Gateway](https://docs.microsoft.com/azure/application-gateway/create-web-app)
