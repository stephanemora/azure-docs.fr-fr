---
title: Qu’est-ce qu’Azure Application Gateway
description: Découvrez comment vous pouvez utiliser une passerelle d’application Azure pour gérer le trafic web vers votre application.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.workload: infrastructure-services
ms.date: 4/9/2018
ms.author: victorh
ms.openlocfilehash: aa31bb599f665e8d3a407531d399701a5be35e41
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364115"
---
# <a name="what-is-azure-application-gateway"></a>Qu’est-ce qu’Azure Application Gateway ?

Azure Application Gateway est un équilibreur de charge du trafic web qui vous permet de gérer le trafic vers vos applications web. 

Les équilibreurs de charge traditionnels fonctionnent au niveau de la couche de transport (couche OSI 4 - TCP et UDP) et acheminent le trafic en fonction de l’adresse IP et du port sources, vers une adresse IP et un port de destination. Mais Application Gateway vous permet d’être plus précis. Par exemple, vous pouvez acheminer le trafic en fonction de l’URL entrante. Par conséquent, si `/images` est dans l’URL entrante, vous pouvez acheminer le trafic vers un ensemble spécifique de serveurs (considérés comme un pool) configurés pour les images. Si `/video` est dans l’URL, ce trafic est acheminé vers un autre pool optimisé pour les vidéos.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Ce type de routage est connu comme l’équilibrage de charge de la couche d’application (couche OSI 7). Azure Application Gateway permet d’effectuer un routage basé sur une URL et bien plus encore. Les fonctionnalités suivantes sont incluses dans Azure Application Gateway : 

## <a name="url-based-routing"></a>Routage basé sur des URL

Le routage basé sur le chemin d’accès de l’URL vous permet d’acheminer le trafic vers des pools de serveurs principaux en fonction des chemins d’accès de l’URL de la demande. L’un des scénarios consiste à acheminer les requêtes pour différents types de contenu vers un pool différent.

Par exemple, les requêtes adressées à `http://contoso.com/video/*` sont acheminées vers VideoServerPool et les requêtes adressées à `http://contoso.com/images/*` sont acheminées vers ImageServerPool. DefaultServerPool est sélectionné si aucun des modèles de chemin d’accès ne correspond.

## <a name="redirection"></a>Redirection

Un scénario courant pour de nombreuses applications web consiste à prendre en charge la redirection automatique de HTTP vers HTTPS pour vous assurer que toutes les communications entre l’application et ses utilisateurs auront lieu via un chemin d’accès chiffré. 

Dans le passé, vous avez peut-être utilisé des techniques telles que la création d’un pool dédié dont le seul objectif consistait à rediriger les requêtes qu’il recevait sur HTTP vers HTTPS. Application Gateway prend en charge la possibilité de rediriger le trafic. Cela simplifie la configuration de l’application, optimise l’utilisation des ressources et prend en charge de nouveaux scénarios de redirection, notamment la redirection globale et basée sur le chemin d’accès. La prise en charge de la redirection Application Gateway n’est pas limitée à la seule redirection HTTP vers HTTPS. Il s’agit d’un mécanisme de redirection générique, vous pouvez ainsi rediriger depuis et vers n’importe quel port défini à l’aide de règles. Il prend également en charge la redirection vers un site externe.

La prise en charge de la redirection Application Gateway offre les fonctionnalités suivantes :

- Redirection globale à partir d’un port vers un autre port sur la passerelle d’application. Cela permet la redirection HTTP vers HTTPS sur un site.
- Redirection basée sur un chemin d’accès. Ce type de redirection permet la redirection HTTP vers HTTPS uniquement sur une zone de site spécifique, par exemple une zone de panier d’achat indiquée par `/cart/*`.
- Procédez à la redirection vers un site externe.

## <a name="multiple-site-hosting"></a>Hébergement de plusieurs sites

L’hébergement de plusieurs sites vous permet de configurer plusieurs sites web sur la même instance de passerelle d’application. Cette fonctionnalité vous permet de configurer une topologie plus efficace pour vos déploiements en ajoutant jusqu’à 20 sites web à une passerelle d’application. Chaque site web peut être dirigé vers son propre pool. Par exemple, la passerelle d’application peut traiter le trafic pour `contoso.com` et `fabrikam.com` à partir de deux pools de serveurs appelés ContosoServerPool et FabrikamServerPool.

Les requêtes adressées à `http://contoso.com` sont acheminées vers ContosoServerPool, tandis que les requêtes adressées à `http://fabrikam.com` sont acheminées vers FabrikamServerPool.

De même, deux sous-domaines du même domaine parent peuvent également être hébergés sur le même déploiement de passerelle d’application. Par exemple, les sous-domaines `http://blog.contoso.com` et `http://app.contoso.com` peuvent être hébergés sur un déploiement de passerelle d’application unique.

## <a name="session-affinity"></a>Affinité de session

La fonctionnalité d’affinité de session basée sur les cookies est utile lorsque vous souhaitez conserver une session utilisateur sur le même serveur. En utilisant des cookies gérés de passerelle, la passerelle Application Gateway peut diriger le trafic pour traitement à partir d’une session utilisateur vers le même serveur. Ceci est important lorsque l’état de la session est enregistré localement sur le serveur pour une session utilisateur.

## <a name="secure-sockets-layer-ssl-termination"></a>Terminaison SSL (Secure Sockets Layer)

La passerelle Application Gateway prend en charge la terminaison SSL au niveau de la passerelle, après laquelle le trafic transite généralement de façon non chiffrée vers les serveurs principaux. Grâce à cette fonctionnalité, les serveurs web ne sont plus chargés des opérations coûteuses de chiffrement et de déchiffrement. Toutefois, une communication non chiffrée vers les serveurs n’est parfois pas une option acceptable. Cela peut être dû à des d’exigences de sécurité et de conformité, ou au fait que l’application n’accepte qu’une connexion sécurisée. Pour de telles applications, la passerelle Application Gateway prend désormais en charge le chiffrement SSL de bout en bout.

## <a name="web-application-firewall"></a>Pare-feu d’application web

Le pare-feu d’applications Web (WAF) est une fonctionnalité de passerelle d’application qui protège vos applications web de manière centralisée contre les vulnérabilités et exploits courants. WAF suit les règles des [Ensembles de règles de base OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9. 

Les applications Web sont de plus en plus la cible d’attaques malveillantes qui exploitent des vulnérabilités connues. Les types d’attaques les plus courantes sont l’injection de code SQL et les attaques de script site à site (XSS). Empêcher ces attaques dans le code d’application peut se révéler difficile et nécessiter une maintenance rigoureuse, des mises à jour correctives ainsi que la surveillance au niveau d’un grand nombre de couches de la topologie de l’application. Un pare-feu d’applications web centralisé facilite grandement la gestion de la sécurité et offre une meilleure garantie de protection aux administrateurs de l’application contre les menaces ou les intrusions. Une solution WAF peut également réagir plus rapidement à une menace de sécurité en exécutant la mise à jour corrective d’une vulnérabilité connue dans un emplacement central plutôt que de sécuriser individuellement chaque déploiement d’application web. Les passerelles d’application existantes peuvent être facilement converties en une passerelle d’application avec un pare-feu d’applications web.

## <a name="websocket-and-http2-traffic"></a>Trafic WebSocket et HTTP/2

Application Gateway prend en charge les protocoles WebSocket et HTTP/2 de manière native. Il n’existe aucun paramètre configurable par l’utilisateur permettant d’activer ou de désactiver de manière sélective la prise en charge de WebSocket. La prise en charge de HTTP/2 peut être activée à l’aide d’Azure PowerShell.
 
Les protocoles WebSocket et HTTP/2 permettent une communication en duplex intégral entre le serveur et le client via une connexion TCP de longue durée. Cela assure une communication plus interactive entre le serveur web et le client, qui peut être bidirectionnelle sans nécessiter d’interrogations, comme c’est le cas pour les implémentations basées sur le protocole HTTP. Ces protocoles engendrent une faible surcharge et peuvent réutiliser la même connexion TCP pour plusieurs demandes/réponses, ce qui entraîne une utilisation plus efficace des ressources. Ces protocoles sont conçus pour fonctionner sur les ports HTTP traditionnels (80 et 443).



## <a name="next-steps"></a>Étapes suivantes

Selon vos besoins et l’environnement, vous pouvez créer une passerelle Application Gateway test à l’aide du portail Azure, de Azure PowerShell ou de Azure CLI :

- [Démarrage rapide : Diriger le trafic web avec Azure Application Gateway - Portail Azure](quick-create-portal.md).
- [Démarrage rapide : Diriger le trafic web avec Azure Application Gateway - Azure PowerShell](quick-create-powershell.md)
- [Démarrage rapide : Diriger le trafic web avec Azure Application Gateway - Azure CLI](quick-create-cli.md)
