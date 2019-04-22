---
title: Fonctionnement d’Azure Application Gateway fonctionne
description: Cet article fournit des informations sur la façon pour le fonctionnement de passerelle d’Application
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: bbaf651233d4cebad3f45e5cf3823bcaf6ce38b6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59783263"
---
# <a name="how-application-gateway-works"></a>Fonctionne de la passerelle d’Application

Cet article explique comment la passerelle d’application accepte les demandes d’entrant et les achemine vers le serveur principal.

![how-application-gateway-works](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-a-request-is-accepted"></a>Comment une demande est acceptée.

Avant un client envoie une demande à votre passerelle d’application, il résout le nom de domaine de la passerelle d’application à l’aide d’un serveur de système DNS (Domain Name). L’entrée DNS est contrôlée par Azure, étant donné que vos passerelles d’application sont dans le domaine azure.com. Le DNS Azure renvoie l’adresse IP au client, qui est la *adresse IP frontale* de la passerelle d’Application. La passerelle d’application accepte le trafic entrant sur un ou plusieurs *écouteurs*. Un écouteur est une entité logique qui vérifie les demandes de connexion. Il est configuré avec un comme façade un adresse IP, le protocole et le numéro de port pour les connexions à partir de clients à la passerelle d’application. Si le pare-feu d’applications Web (WAF) est activé, passerelle d’Application vérifie les en-têtes de demande et le corps (le cas échéant) contre le *règles WAF* pour déterminer si la demande est une demande valide - auquel cas il sera routé vers le principal - ou une menace de sécurité, dans lequel cas la demande sera bloqué.  

Passerelle d’application peut être utilisée comme un équilibreur de charge interne d’application ou un équilibreur de charge accessible sur Internet application. Une passerelle d’application accessible sur Internet a des adresses IP publiques. Le nom DNS d’une passerelle d’application accessible sur Internet est pouvant être résolu publiquement à son adresse IP publique. Par conséquent, les passerelles d’application accessible sur Internet peuvent acheminer les demandes des clients sur Internet. Passerelles d’application interne ont des uniquement les adresse IP privée. Le nom DNS d’une passerelle d’application interne est pouvant être résolu publiquement à son adresse IP privée. Par conséquent, les équilibreurs de charge interne peuvent uniquement acheminer les demandes provenant de clients avec accès au réseau virtuel pour la passerelle d’application. Passerelles d’Application accessible sur Internet et interne acheminer les demandes vers vos serveurs principaux à l’aide d’adresses IP privées. Par conséquent, vos serveurs backend n’avez pas besoin d’adresses IP publiques pour recevoir les demandes à partir d’une commande interne ou une passerelle d’Application accessible sur Internet.

## <a name="how-a-request-is-routed"></a>Comment une requête est routée.

Si la demande n’est pas valide (ou si le pare-feu d’applications Web n’est pas activée), la *règle de routage des demandes* associé à la *écouteur* est évaluée pour déterminer le *pool principal* à c'est-à-dire la demande doit être routé. Les règles sont traitées suivant leur ordre d’affichage dans le portail. Selon le *règle de routage des demandes* configuration, la passerelle d’application détermine s’il faut Router toutes les demandes d’un pool principal spécifique sur l’écouteur ou les acheminer vers les pools principaux différents selon le chemin d’URL ou à *rediriger les demandes* à un autre port ou un site externe

Une fois un *principal* *pool* a été choisie, passerelle d’application envoie la demande à un des serveurs principaux configurés dans le pool est sain (y.y.y.y). L’intégrité du serveur est déterminée par un *sonde d’intégrité*. Si le pool principal contient plusieurs serveurs, passerelle d’application utilise l’algorithme de tourniquet (Round Robin) pour acheminer les demandes entre les serveurs intègres, par conséquent, l’équilibrage de charge les requêtes sur les serveurs.

Une fois un serveur principal a été déterminé, passerelle d’application ouvre une nouvelle session TCP avec le serveur principal basé sur la configuration dans le *paramètres HTTP*. Le *paramètres HTTP* est un composant qui spécifie le protocole, port et autres concernant le routage paramètre qui est requis pour établir une nouvelle session avec le serveur principal. Le port et le protocole utilisé dans les paramètres HTTP déterminent si le trafic entre les serveurs de passerelle et le serveur principal d’applications est chiffré, par conséquent, pour accomplir SSL de bout en bout ou non chiffrés. Lors de l’envoi de la demande d’origine vers le serveur principal, passerelle d’application respecte des configurations personnalisées effectuées dans les paramètres HTTP liés à remplacer le nom d’hôte, le chemin d’accès, le protocole ; le nom d’hôte à partir du serveur principal, etc. de prélèvement affinité basée sur les cookies de session de mise à jour, drainage, de la connexion.

Une passerelle d’Application interne a uniquement les adresse IP privée. Le nom DNS d’une passerelle d’Application interne est en interne peut être résolu à son adresse IP privée. Par conséquent, les équilibreurs de charge interne peuvent uniquement acheminer des demandes des clients ayant accès au réseau virtuel pour la passerelle d’Application.

Si votre pool principal contient un nom de domaine complet peut être résolu en interne ou une adresse IP privée, Application Gateway achemine la demande vers le serveur principal à l’aide de ses adresses IP privées d’instance. Si votre pool principal contient un point de terminaison externe ou un nom de domaine complet peut être résolu en externe, Application Gateway achemine la demande vers le serveur principal à l’aide de son adresse IP publique de serveur frontal. La résolution DNS est basée sur une zone DNS privée ou d’un serveur DNS personnalisé si configuré ou qu’il prend la valeur par défaut fourni par Azure DNS. Si vous n’avez pas configuré une adresse IP publique de serveur frontal, un est affecté pour la connectivité externe sortante.

### <a name="modifications-to-the-request"></a>Modifications apportées à la demande

Passerelle d’application insère les en-têtes supplémentaires 4 pour toutes les demandes avant qu’il transfère les demandes au serveur principal. Ces en-têtes sont X-forwarded-for, X-forwarded-proto, X-forwarded-port et d’origine-X-host. Le format d’en-tête x-forwarded-for est une liste séparée par des virgules des IP : port. Les valeurs valides pour x-forwarded-proto sont HTTP ou HTTPS. X-forwarded-port spécifie le port atteint par la requête au niveau de la passerelle d’application. En-tête X-original-hôte contient l’en-tête d’hôte d’origine avec lequel la demande est parvenue. Cet en-tête est utile dans des scénarios comme l’intégration de sites web Azure, dans lesquels l’en-tête de l’hôte entrant est modifié avant que le trafic ne soit acheminé vers le serveur principal. Si vous le souhaitez, si l’affinité de session est activée, un cookie d’affinité gérés de passerelle est inséré. 

Vous pouvez en outre configurer passerelle d’application pour modifier les en-têtes à l’aide [en-têtes HTTP réécrire](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) ou modifier le chemin d’accès de l’URI à l’aide de la configuration de remplacement de chemin d’accès. Mais ne soient configurés pour ce faire, toutes les demandes entrantes sont traitées en l’état au serveur principal.


## <a name="next-steps"></a>Étapes suivantes

Après avoir découvert le fonctionne de la passerelle d’application, consultez [composants de passerelle d’Application](application-gateway-components.md) pour comprendre ses composants plus en détail.
