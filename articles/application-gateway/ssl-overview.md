---
title: Activation du protocole SSL de bout en bout sur Azure Application Gateway
description: Cet article fournit une vue d’ensemble de la prise en charge SSL de bout en bout pour Application Gateway.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 64b90afd598b96604fc9c3ddc4bc10586e714363
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75657979"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>Présentation de la terminaison SSL et du chiffrement SSL de bout en bout sur la passerelle Application Gateway

Secure Sockets Layer (SSL) est la technologie de sécurité standard pour établir une liaison chiffrée entre un serveur web et un navigateur. Cette liaison garantit que toutes les données transmises entre le serveur web et les navigateurs restent privées et chiffrées. Application Gateway prend en charge la terminaison SSL sur la passerelle ainsi que le chiffrement SSL de bout en bout.

## <a name="ssl-termination"></a>Arrêt SSL

Application Gateway prend en charge l’arrêt SSL au niveau de la passerelle, après lequel le trafic transite généralement de façon non chiffrée vers les serveurs backend. La terminaison SSL établie au niveau de la passerelle offre un certain nombre d’avantages :

- **Amélioration des performances** – Lors du déchiffrement SSL, la négociation initiale est l’opération qui dégrade le plus les performances. Pour améliorer les performances, le serveur qui effectue le déchiffrement met en cache les ID de session SSL et gère les tickets de session TLS. Si cela est fait au niveau de la passerelle d’application, toutes les requêtes d’un même client peuvent utiliser les valeurs mises en cache. Si cela est fait sur les serveurs principaux, le client doit s’authentifier chaque fois que les requêtes du client sont adressées à un serveur différent. L’utilisation de tickets TLS peut aider à atténuer ce problème, mais ces tickets ne sont pas pris en charge par tous les clients et peuvent être difficiles à configurer et à gérer.
- **Meilleure utilisation des serveurs principaux**  - Le traitement SSL/TLS nécessite une grande quantité de ressources processeur et, à mesure qu’il s’intensifie, la taille des clés augmente. Ce travail n’est plus dévolu aux serveurs principaux si bien qu’ils peuvent se concentrer sur ce qu’ils savent faire le mieux : diffuser du contenu.
- **Routage intelligent** – En déchiffrant le trafic, la passerelle d’application a accès au contenu de la requête, comme les en-têtes, l’URI, etc. et peut utiliser ces données pour acheminer les requêtes.
- **Gestion des certificats** - Il suffit d’acheter et d’installer les certificats sur la passerelle d’application et non sur tous les serveurs principaux. Cela permet d’économiser du temps et de l’argent.

Pour configurer l’arrêt SSL, un certificat SSL doit être ajouté à l’écouteur pour permettre à la passerelle d’application de dériver une clé symétrique conformément aux spécifications du protocole SSL. La clé symétrique est ensuite utilisée pour chiffrer et déchiffrer le trafic envoyé à la passerelle. Le certificat SSL doit être partagé au format Personal Information Exchange (PFX). Ce format de fichier permet d’exporter la clé privée requise par la passerelle d’application pour effectuer le chiffrement et le déchiffrement du trafic.

> [!NOTE] 
>
> La passerelle d’application ne permet pas de créer un certificat numérique, ni d’envoyer une demande de certificat numérique à une autorité de certification.

Pour que la connexion SSL fonctionne, assurez-vous que le certificat SSL remplit les conditions suivantes :

- La date/heure actuelle est dans la plage de dates « Valide à partir du » et « Valide jusqu’au » sur le certificat.
- Le « nom commun » (CN) du certificat correspond à l’en-tête de l’hôte dans la requête. Par exemple, si le client adresse une requête à `https://www.contoso.com/`, le nom commun doit être `www.contoso.com`.

### <a name="certificates-supported-for-ssl-termination"></a>Certificats pris en charge pour un arrêt SSL

Application Gateway prend en charge les types de certificat suivants :

- Certificat délivré par une autorité de certification (CA) : Un certificat CA est un certificat numérique émis par une autorité de certification (CA).
- Certificat EV (à validation étendue) : Un certificat EV est un certificat numérique répondant aux directives standards du secteur. La barre de recherche du navigateur devient verte et le nom de la société s’affiche.
- Certificat générique : Ce certificat prend en charge un nombre quelconque de sous-domaines basés sur *.site.com, adresse dans laquelle l’astérisque correspond au sous-domaine. Cependant, il ne prend pas en charge l’adresse site.com ; ainsi, le certificat numérique ne gère pas le cas où un utilisateur accède à votre site web sans avoir saisi « www » au début de l’adresse.
- Certificats auto-signés : Les navigateurs clients ne font pas confiance à ces certificats et avertiront l’utilisateur que le certificat du service virtuel ne fait pas partie d’une chaîne d’approbation. Les certificats auto-signés conviennent pour les tests ou les environnements dans lesquels les administrateurs contrôlent les clients et peuvent ignorer en toute sécurité les alertes de sécurité du navigateur. Les charges de travail de production ne doivent jamais utiliser de certificats auto-signés.

Pour plus d’informations, consultez [Configurer l’arrêt SSL avec Application Gateway](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Taille du certificat
Consultez la section [Limites d’Application Gateway](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) pour connaître la taille maximale des certificats SSL pris en charge.

## <a name="end-to-end-ssl-encryption"></a>Chiffrement SSL de bout en bout

Certains clients n’acceptent pas une communication non chiffrée vers les serveurs principaux. Cela peut être dû à des d’exigences de sécurité et de conformité, ou au fait que l’application n’accepte qu’une connexion sécurisée. Pour de telles applications, la passerelle Application Gateway prend désormais en charge le chiffrement SSL de bout en bout.

Le chiffrement SSL de bout en bout vous permet de transmettre en toute sécurité des données sensibles au serveur back-end, tout en continuant de bénéficier des avantages des fonctionnalités d’équilibrage de charge de couche 7 fournies par la passerelle Application Gateway. Il s’agit notamment de la fonctionnalité d’affinité basée sur les cookies, du routage basé sur l’URL, de la prise en charge du routage basé sur des sites ou de la possibilité d’injecter des * en-têtes X-Forwarded.

Lorsqu’elle est configurée avec un mode de communication SSL de bout en bout, la passerelle Application Gateway ferme les sessions SSL au niveau de la passerelle et déchiffre le trafic de l’utilisateur. Il applique ensuite les règles configurées pour sélectionner une instance de pool principal appropriée vers laquelle acheminer le trafic. La passerelle Application Gateway établit ensuite une nouvelle connexion SSL vers le serveur back-end, puis chiffre à nouveau les données à l’aide du certificat de clé publique de ce serveur avant de transmettre la requête au back-end. Toute réponse du serveur web passe par le même processus vers l’utilisateur final. Pour activer le chiffrement SSL de bout en bout, définissez le [Paramètre HTTP du serveur principal](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) sur HTTPS, qui est ensuite appliqué à un pool de serveurs principaux.

La stratégie SSL s’applique à la fois au trafic frontal et au trafic des serveurs principaux. Au niveau frontal, Application Gateway agit en tant que serveur et applique la stratégie. Sur le serveur principal, Application Gateway agit en tant que client et envoie les informations de protocole/chiffrement en tant que préférence pendant la négociation SSL.

La passerelle d’application communique uniquement avec ces instances de back-end qui ont placé leur certificat en liste verte avec la passerelle d’application, ou dont les certificats sont signés par des autorités de certification connues lorsque le nom commun du certificat correspond au nom de l’hôte dans les paramètres HTTP du serveur principal. Cela inclut les services Azure approuvés, tels que les applications web Azure App Service et la Gestion des API Azure.

Si les certificats des membres dans le pool principal ne sont pas signés par des autorités de certification connues, chaque instance du pool principal pour lequel un chiffrement SSL de bout en bout a été activé doit être configuré avec un certificat afin de permettre une communication sécurisée. L’ajout du certificat permet à la passerelle d’application de communiquer uniquement avec des instances de serveur principal connues. Il sécurise la communication de bout en bout.

> [!NOTE] 
>
> Il n’est pas nécessaire de configurer les certificats d’authentification pour les services Azure approuvés comme les applications web Azure App Service et Gestion des API Azure.

> [!NOTE] 
>
> Le certificat ajouté au **Paramètre HTTP du serveur principal** pour authentifier les serveurs principaux peut être le même que le certificat ajouté à **l’écouteur** pour la terminaison SSL au niveau de la passerelle d’application ou être différent pour une sécurité accrue.

![scénario ssl de bout en bout][1]

Dans cet exemple, les demandes utilisant TLS1.2 sont acheminées vers les serveurs principaux dans Pool1 à l’aide du chiffrement SSL de bout en bout.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Chiffrement SSL de bout en bout et liste verte de certificats

La passerelle Application Gateway communique uniquement avec les instances de serveur principal identifiées dont le certificat figure sur la liste approuvée par la passerelle Application Gateway. Pour approuver des certificats, vous devez télécharger la clé publique des certificats du serveur principal pour la passerelle Application Gateway (non le certificat racine). Seules les connexions aux serveurs principaux connus et sur liste verte sont alors autorisées. Les autres serveurs principaux renvoient une erreur de passerelle. Les certificats auto-signés sont uniquement destinés à des fins de test et ne sont pas recommandés pour les charges de travail de production. Ces certificats doivent figurer sur la liste approuvée par la passerelle d’application comme décrit dans les étapes précédentes avant de pouvoir être utilisés.

> [!NOTE]
> Il n’est pas nécessaire de configurer les certificats d’authentification pour les services Azure approuvés comme Azure App Service.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>SSL de bout en bout avec la référence SKU v2

Les certificats d’authentification ont été déconseillés et remplacés par des certificats racines approuvés dans la référence SKU v2 d’Application Gateway. Ils fonctionnent comme les certificats d’authentification, avec quelques différences importantes :

- Les certificats signés par des autorités de certification connues dont le nom commun correspond au nom de l’hôte dans les paramètres HTTP du serveur principal ne nécessitent pas d’étape supplémentaire pour que le protocole SSL de bout en bout fonctionne. 

   Par exemple, si les certificats de serveur principal sont émis par une autorité de certification connue et a un nom commun contoso.com et que le de paramètres HTTP du serveur principal est également défini sur contoso.com, aucune étape supplémentaire n’est nécessaire. Vous pouvez définir le paramètre du protocole HTTP du serveur principal sur HTTPS et le contrôle d’intégrité et le chemin des données seront de ce fait compatibles SSL. Si vous utilisez Azure App Service ou d’autres services web Azure en tant que back-end, ceux-ci sont également implicitement approuvés et aucune étape supplémentaire n’est nécessaire pour le protocole SSL de bout en bout.
   
> [!NOTE] 
>
> Pour qu’un certificat SSL soit approuvé, ce certificat du serveur principal doit avoir été émis par une autorité de certification qui est incluse dans le magasin approuvé Application Gateway. Si le certificat n’a pas été émis par une autorité de certification approuvée, Application Gateway vérifiera si le certificat de l’autorité de certification émettrice a été émis par une autorité de certification approuvée, et ainsi de suite jusqu’à ce qu’une autorité de certification approuvée soit détectée (à ce stade, une connexion sécurisée sera établie) ou qu’aucune autorité de certification approuvée ne soit trouvée (dans ce cas, Application Gateway marquera le serveur principal comme étant non sain). Par conséquent, il est recommandé que le certificat de serveur principal contienne à la fois les autorités de certification racines et intermédiaires.

- Si le certificat est auto-signé ou signé par des intermédiaires inconnus, un certificat racine approuvé doit être défini pour pouvoir activer le SSL de bout en bout dans la référence SKU v2. Application Gateway communique uniquement avec les serveurs dont le certificat racine du certificat de serveur correspond à un présent dans la liste des certificats racines de confiance dans le paramètre HTTP principal associé au pool de serveurs.

> [!NOTE] 
>
> Le certificat auto-signé doit faire partie d’une chaîne de certificats. Un seul certificat auto-signé sans chaîne n’est pas pris en charge dans la référence SKU v2.

- En plus de la correspondance du certificat racine, Application Gateway vérifie également si le paramètre d’hôte spécifié dans le paramètre HTTP du serveur principal d’hôte correspond à celle du nom commun (CN) présenté par le certificat SSL du serveur principal. Lorsque vous tentez d’établir une connexion SSL au serveur principal, Application Gateway définit l’extension SNI d’indication de nom de serveur à l’hôte spécifié dans le paramètre HTTP du serveur principal.
- Si vous choisissez l’option **Choisir le nom d’hôte à partir de l’adresse du serveur principal** au lieu du champ Hôte dans le paramètre HTTP du serveur principal, l’en-tête SNI est toujours définie sur le nom de domaine complet du pool principal et le nom commun sur le certificat SSL du serveur principal doit correspondre à son nom de domaine complet. Les membres du pool principal avec des adresses IP ne sont pas pris en charge dans ce scénario.
- Le certificat racine est un certificat de racine codé en base64 parmi les certificats de serveur principal.

## <a name="next-steps"></a>Étapes suivantes

Après avoir découvert le chiffrement SSL de bout en bout, accédez à [Configurer le SSL de bout en bout avec Application Gateway avec PowerShell](application-gateway-end-to-end-ssl-powershell.md) pour créer une passerelle d’application à l’aide du chiffrement SSL de bout en bout.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
