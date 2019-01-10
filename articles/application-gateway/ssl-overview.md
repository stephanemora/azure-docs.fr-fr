---
title: Activation du protocole SSL de bout en bout sur Azure Application Gateway
description: Cet article fournit une vue d’ensemble de la prise en charge SSL de bout en bout pour Application Gateway.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2018
ms.author: amsriva
ms.openlocfilehash: fcb49f532d5dfcd340baf017bd55c69d4e81e0e6
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53630680"
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
> Il n’est pas nécessaire de configurer les certificats d’authentification pour les services Azure approuvés comme Azure App Service.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>SSL de bout en bout avec la référence SKU v2

Les certificats d’authentification ont été déconseillés et remplacés par des certificats racines approuvés dans la référence SKU v2 d’Application Gateway. Ils fonctionnent comme les certificats d’authentification, avec quelques différences importantes :

- Les certificats signés par des autorités de certification connues dont le nom commun correspond au nom de l’hôte dans les paramètres HTTP du serveur principal ne nécessitent pas d’étape supplémentaire pour que le protocole SSL de bout en bout fonctionne. 

   Par exemple, si les certificats de serveur principal sont émis par une autorité de certification connue et a un nom commun contoso.com et que le de paramètres HTTP du serveur principal est également défini sur contoso.com, aucune étape supplémentaire n’est nécessaire. Vous pouvez définir le paramètre du protocole HTTP du serveur principal sur HTTPS et le contrôle d’intégrité et le chemin des données seront de ce fait compatibles SSL. Si vous utilisez Azure App Service ou d’autres services web Azure en tant que back-end, ceux-ci sont également implicitement approuvés et aucune étape supplémentaire n’est nécessaire pour le protocole SSL de bout en bout.
- Si le certificat est auto-signé ou signé par des intermédiaires inconnus, un certificat racine approuvé doit être défini pour pouvoir activer le SSL de bout en bout dans la référence SKU v2. Application Gateway communique uniquement avec les serveurs dont le certificat racine du certificat de serveur correspond à un présent dans la liste des certificats racines de confiance dans le paramètre HTTP principal associé au pool de serveurs.
- En plus de la correspondance du certificat racine, Application Gateway vérifie également si le paramètre d’hôte spécifié dans le paramètre HTTP du serveur principal d’hôte correspond à celle du nom commun (CN) présenté par le certificat SSL du serveur principal. Lorsque vous tentez d’établir une connexion SSL au serveur principal, Application Gateway définit l’extension SNI d’indication de nom de serveur à l’hôte spécifié dans le paramètre HTTP du serveur principal.
- Si vous choisissez l’option **Choisir le nom d’hôte à partir de l’adresse du serveur principal** au lieu du champ Hôte dans le paramètre HTTP du serveur principal, l’en-tête SNI est toujours définie sur le nom de domaine complet du pool principal et le nom commun sur le certificat SSL du serveur principal doit correspondre à son nom de domaine complet. Les membres du pool principal avec des adresses IP ne sont pas pris en charge dans ce scénario.
- Le certificat racine est un certificat de racine codé en base64 parmi les certificats de serveur principal.

## <a name="next-steps"></a>Étapes suivantes

Après avoir découvert le chiffrement SSL de bout en bout, accédez à [Configurer le SSL de bout en bout avec Application Gateway avec PowerShell](application-gateway-end-to-end-ssl-powershell.md) pour créer une passerelle d’application à l’aide du chiffrement SSL de bout en bout.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
