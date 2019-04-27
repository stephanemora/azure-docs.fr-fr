---
title: Fonctionne d’une passerelle d’application
description: Cet article fournit des informations sur le fonctionne d’une passerelle d’application
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 2f27105aed940f0411abaa534cb09adf0be34bfe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60830317"
---
# <a name="how-an-application-gateway-works"></a>Fonctionne d’une passerelle d’application

Cet article explique comment une passerelle d’application accepte les demandes entrantes et les dirige vers le serveur principal.

![Comment une passerelle d’application accepte une demande](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Comment une passerelle d’application accepte une demande

1. Avant un client envoie une demande à une passerelle d’application, il résout le nom de domaine de la passerelle d’application à l’aide d’un serveur de système DNS (Domain Name). Azure contrôle l’entrée DNS, car toutes les passerelles d’application sont dans le domaine azure.com.

2. Le DNS Azure renvoie l’adresse IP au client, qui est l’adresse IP de serveur frontal de la passerelle d’application.

3. La passerelle d’application accepte le trafic entrant sur un ou plusieurs écouteurs. Un écouteur est une entité logique qui vérifie les demandes de connexion. Il est configuré avec un comme façade un adresse IP, le protocole et le numéro de port pour les connexions à partir de clients à la passerelle d’application.

4. Si un pare-feu d’applications web (WAF) est en cours d’utilisation, la passerelle d’application vérifie les en-têtes de demande et le corps, le cas échéant, par rapport aux règles de pare-feu d’applications Web. Cette action détermine si la demande est une demande valide ou une menace de sécurité. Si la demande est valide, il est routé vers le serveur principal. Si la demande n’est pas valide, il est bloqué comme une menace de sécurité.

Azure Application Gateway peut être utilisé sous la forme d’un équilibreur de charge interne d’application ou un équilibreur de charge d’application accessible sur internet. Une passerelle d’application orienté internet utilise des adresses IP publiques. Le nom DNS d’une passerelle d’application accessible sur internet est pouvant être résolu publiquement à son adresse IP publique. Par conséquent, les passerelles d’application accessible sur internet peuvent acheminer les demandes de client à internet.

Passerelles d’application interne utilisent uniquement des adresses IP privées. Le nom DNS d’une passerelle d’application interne est pouvant être résolu publiquement à son adresse IP privée. Par conséquent, les équilibreurs de charge internes peuvent uniquement acheminer les demandes provenant de clients avec accès à un réseau virtuel pour la passerelle d’application.

Les passerelles accessibles sur internet et interne à l’application acheminer les demandes vers les serveurs principaux à l’aide d’adresses IP privées. Serveurs principaux n’avez pas besoin des adresses IP publiques pour recevoir des demandes à partir d’une commande interne ou une passerelle d’application accessible sur internet.

## <a name="how-an-application-gateway-routes-a-request"></a>Comment une passerelle d’application achemine une requête

Si une demande est valide, ou un pare-feu WAF n’est pas en cours d’utilisation, la passerelle d’application évalue la règle de routage de demande qui est associé à l’écouteur. Cette action détermine quel pool back-end pour acheminer la demande.

Les règles sont traitées dans l’ordre que qui sont répertoriées dans le portail. Selon la règle de routage de demande, la passerelle d’application détermine s’il faut Router toutes les demandes d’un pool principal spécifique, sur l’écouteur itinéraire demande aux pools principaux différents selon le chemin d’URL, ou rediriger les demandes vers un autre port ou externes site.

Lorsque la passerelle d’application sélectionne le pool principal, il envoie la demande à un des serveurs principaux intègres dans le pool (y.y.y.y). L’intégrité du serveur est déterminée par une sonde d’intégrité. Si le pool principal contient plusieurs serveurs, la passerelle d’application utilise un algorithme de tourniquet (round-robin) pour acheminer les demandes entre les serveurs sains. Cette charge équilibre les demandes sur les serveurs.

Une fois que la passerelle d’application détermine le serveur principal, il s’ouvre une nouvelle session TCP avec le serveur principal en fonction des paramètres de HTTP. Paramètres HTTP spécifient le protocole, port et autres paramètres liés au routage sont requises pour établir une nouvelle session avec le serveur principal.

Le port et le protocole utilisé dans les paramètres HTTP déterminent si le trafic entre les serveurs de passerelle et le serveur principal d’application est chiffré (par conséquent, pour accomplir SSL de bout en bout) ou qu’il n’est pas chiffré.

Lorsqu’une passerelle d’application envoie la demande d’origine vers le serveur principal, il respecte des configurations personnalisées effectuées dans les paramètres HTTP liés à remplacer le nom d’hôte, le chemin d’accès et le protocole. Cette action conserve l’affinité de session basée sur les cookies, la sélection de drainage, nom d’hôte de connexion à partir du serveur principal et ainsi de suite.

Une passerelle d’application interne utilise uniquement les adresses IP privées. Le nom DNS d’une passerelle d’application interne ne peut être résolu à son adresse IP privée. Par conséquent, les équilibreurs de charge internes peuvent uniquement acheminer les demandes provenant de clients avec accès au réseau virtuel pour la passerelle d’application.

 >[!NOTE]
 >Les deux passerelles d’application internet-internes et acheminement les demandes vers les serveurs principaux à l’aide des adresses IP privées. Cette action se produit lorsque votre ressource de pool principal contient une adresse IP privée, configuration de VM NIC ou une adresse peut être résolue en interne. Si le pool principal :
> - **Est un point de terminaison public**, la passerelle d’application utilise son adresse IP publique de serveur frontal d’atteindre le serveur. S’il n’est pas une adresse IP publique de serveur frontal, un est affecté pour la connectivité externe sortante.
> - **Contient un nom de domaine complet peut être résolu en interne ou une adresse IP privée**, la passerelle d’application achemine la demande vers le serveur principal à l’aide de ses adresses IP privées d’instance.
> - **Contient un point de terminaison externe ou un nom de domaine complet peut être résolu en externe**, la passerelle d’application achemine la demande vers le serveur principal à l’aide de son adresse IP publique de serveur frontal. La résolution DNS est basée sur une zone DNS privée ou d’un serveur DNS personnalisé, si configuré, ou il utilise la valeur par défaut fournie par Azure DNS. S’il n’est pas une adresse IP publique de serveur frontal, un est affecté pour la connectivité externe sortante.

### <a name="modifications-to-the-request"></a>Modifications apportées à la demande

Une passerelle d’application insère quatre en-têtes supplémentaires à toutes les demandes avant qu’il transfère les demandes au serveur principal. Ces en-têtes sont x-forwarded-for, x-forwarded-proto, x-forwarded-port et x-original-host. Le format d’en-tête x-forwarded-for est une liste séparée par des virgules des IP : port.

Les valeurs valides pour x-forwarded-proto sont HTTP ou HTTPS. X-forwarded-port Spécifie le port où la demande atteint la passerelle d’application. En-tête X-original-hôte contient l’en-tête d’hôte d’origine avec lequel la demande est parvenue. Cet en-tête est utile dans l’intégration de site Web Azure, où l’en-tête d’hôte entrant est modifié avant que le trafic est acheminé vers le serveur principal. Si l’affinité de session est activée en tant qu’option, puis il ajoute un cookie d’affinité géré par passerelle.

Vous pouvez configurer la passerelle d’application afin de modifier les en-têtes à l’aide de [en-têtes HTTP réécrire](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) ou pour modifier le chemin d’accès URI à l’aide d’un paramètre de chemin d’accès-override. Toutefois, ne soient configurés pour ce faire, toutes les demandes entrantes sont traitées sur le serveur principal.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur les composants de passerelle d’application](application-gateway-components.md)
