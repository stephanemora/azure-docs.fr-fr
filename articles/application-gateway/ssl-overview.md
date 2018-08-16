---
title: Activation du protocole SSL de bout en bout sur Azure Application Gateway
description: Cet article fournit une vue d’ensemble de la prise en charge SSL de bout en bout pour Application Gateway.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 8/6/2018
ms.author: amsriva
ms.openlocfilehash: 4575bed18697a5661d58dc350c24a9497f7c46ff
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578811"
---
# <a name="overview-of-end-to-end-ssl-with-application-gateway"></a>Présentation du chiffrement SSL de bout en bout sur la passerelle Application Gateway

Application Gateway prend en charge l’arrêt SSL au niveau de la passerelle, après lequel le trafic transite généralement de façon non chiffrée vers les serveurs backend. Grâce à cette fonctionnalité, les serveurs web ne sont plus chargés des opérations coûteuses de chiffrement et de déchiffrement. Mais pour certains clients, une communication non chiffrée vers les serveurs principaux n’est pas une option acceptable. Cette communication non chiffrée peut être due à des exigences de sécurité ou de conformité, ou au fait que l’application n’accepte qu’une connexion sécurisée. Pour de telles applications, la passerelle Application Gateway prend désormais en charge le chiffrement SSL de bout en bout.

Le chiffrement SSL de bout en bout vous permet de transmettre en toute sécurité des données sensibles au serveur back-end, tout en continuant de bénéficier des avantages des fonctionnalités d’équilibrage de charge de couche 7 fournies par la passerelle Application Gateway. Il s’agit notamment de la fonctionnalité d’affinité basée sur les cookies, du routage basé sur l’URL, de la prise en charge du routage basé sur des sites ou de la possibilité d’injecter des * en-têtes X-Forwarded.

Lorsqu’elle est configurée avec un mode de communication SSL de bout en bout, la passerelle Application Gateway ferme les sessions SSL au niveau de la passerelle et déchiffre le trafic de l’utilisateur. Il applique ensuite les règles configurées pour sélectionner une instance de pool principal appropriée vers laquelle acheminer le trafic. La passerelle Application Gateway établit ensuite une nouvelle connexion SSL vers le serveur back-end, puis chiffre à nouveau les données à l’aide du certificat de clé publique de ce serveur avant de transmettre la requête au back-end. Pour activer le chiffrement SSL de bout en bout, définissez le paramètre de protocole **BackendHTTPSetting** sur HTTPS, qui est ensuite appliqué à un pool back-end. Chaque serveur principal du pool principal pour lequel un chiffrement SSL de bout en bout a été activé doit être configuré avec un certificat afin de permettre une communication sécurisée.

![scénario ssl de bout en bout][1]

Dans cet exemple, les demandes utilisant TLS1.2 sont acheminées vers les serveurs principaux dans Pool1 à l’aide du chiffrement SSL de bout en bout.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Chiffrement SSL de bout en bout et liste blanche de certificats

La passerelle Application Gateway communique uniquement avec les instances de serveur principal identifiées dont le certificat figure sur la liste approuvée par la passerelle Application Gateway. Pour approuver des certificats, vous devez télécharger la clé publique des certificats du serveur principal pour la passerelle Application Gateway (non le certificat racine). Seules les connexions aux serveurs principaux connus et sur liste blanche sont alors autorisées. Les autres serveurs principaux renvoient une erreur de passerelle. Les certificats auto-signés sont uniquement destinés à des fins de test et ne sont pas recommandés pour les charges de travail de production. Ces certificats doivent figurer sur la liste approuvée par la passerelle d’application comme décrit dans les étapes précédentes avant de pouvoir être utilisés.

> [!NOTE]
> Il n’est pas nécessaire de configurer les certificats d’authentification pour les services Azure approuvés comme Azure Web Apps.

## <a name="next-steps"></a>Étapes suivantes

Après avoir découvert le chiffrement SSL de bout en bout, accédez à [Configurer une passerelle d’application avec arrêt SSL à l’aide du portail Azure](create-ssl-portal.md) pour créer une passerelle d’application à l’aide du chiffrement SSL de bout en bout.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
