---
title: Activation du protocole SSL de bout en bout sur Azure Application Gateway
description: Cet article fournit une vue d’ensemble de la prise en charge SSL de bout en bout pour Application Gateway.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 92799019d13de71d911767d8e400598513587667
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60715218"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>Vue d’ensemble de la terminaison SSL et SSL de bout en bout avec Application Gateway

Couche de Sockets sécurisée (SSL) est la technologie de sécurité standard pour établir une liaison chiffrée entre un serveur web et un navigateur. Ce lien permet de s’assurer que toutes les données transmises entre le serveur web et les navigateurs restent privés et chiffrés. Passerelle d’application prend en charge les deux un arrêt SSL à la passerelle, ainsi que le chiffrement SSL de bout en bout.

## <a name="ssl-termination"></a>Arrêt SSL

Application Gateway prend en charge l’arrêt SSL au niveau de la passerelle, après lequel le trafic transite généralement de façon non chiffrée vers les serveurs backend. Il existe un certain nombre d’avantages de faire la terminaison SSL au niveau de la passerelle d’application :

- **Amélioration des performances** – la plus importante dégradation des performances lors effectuant le déchiffrement SSL sont la négociation initiale. Pour améliorer les performances, le serveur effectue le déchiffrement met en cache les ID de session SSL et gère les tickets de session TLS. Si cela est fait à la passerelle d’application, toutes les demandes provenant du même client peuvent utiliser les valeurs mises en cache. Si elle est effectuée sur les serveurs principaux, chaque fois que les demandes du client Accédez à un autre serveur, le client doit re‑authenticate. L’utilisation des tickets TLS peut aider à atténuer ce problème, mais ils ne sont pas pris en charge par tous les clients et peuvent être difficiles à configurer et gérer.
- **Meilleure utilisation des serveurs principaux** : traitement de SSL/TLS est beaucoup de ressources processeur et devient plus performante que l’augmentent des tailles de clé. Suppression de ce travail à partir de serveurs principaux leur permet de se concentrer sur ce qu’ils sont plus efficaces à la diffusion de contenu.
- **Le routage intelligent** – en déchiffrant le trafic, la passerelle d’application a accès au contenu de la demande, tels que les en-têtes, URI et ainsi de suite et peut utiliser ces données pour acheminer les demandes.
- **Gestion des certificats** – certificats doivent uniquement être achetées et installées sur la passerelle d’application et pas tous les serveurs principaux. Cela permet d’économiser temps et argent.

Pour configurer une terminaison SSL, un certificat SSL doit être ajouté à l’écouteur pour permettre à la passerelle d’application dériver une clé symétrique selon la spécification du protocole SSL. La clé symétrique est ensuite utilisée pour chiffrer et déchiffrer le trafic envoyé à la passerelle. Le certificat SSL doit être au format PFX Personal Information Exchange (). Ce format de fichier permet d’exporter la clé privée requise par la passerelle d’application pour effectuer le chiffrement et le déchiffrement du trafic.

> [!NOTE] 
>
> Passerelle d’application ne fournit pas de fonction pour créer un nouveau certificat ou envoyer une demande de certificat à une autorité de certification.

Pour la connexion SSL fonctionne, vous devez vous assurer que le certificat SSL remplit les conditions suivantes :

- Que la date et heure actuelles est dans la plage de date de « Valid à » sur le certificat et le « Valide à partir de ».
- Que le « nom commun du certificat » (CN) correspond à l’en-tête d’hôte dans la demande. Par exemple, si le client effectue une demande à `https://www.contoso.com/`, le nom commun doit être `www.contoso.com`.

### <a name="certificates-supported-for-ssl-termination"></a>Certificats pris en charge pour un arrêt SSL

Passerelle d’application prend en charge les types de certificats suivants :

- Certificat d’autorité de certification (autorité de certification) : Un certificat d’autorité de certification est un certificat numérique émis par une autorité de certification (CA)
- Certificat de validation étendue (Extended Validation) : Un certificat EV est un secteur des directives de certificat standard. Cela vert de la barre de recherche du navigateur et publier le nom de la société.
- Certificat générique : Ce certificat prend en charge un nombre quelconque de sous-domaines basés sur *. site.com, où votre sous-domaine remplacerait la *. Il n’est, toutefois, prend en charge les site.com, donc dans le cas où les utilisateurs accèdent à votre site Web sans avoir à saisir le caractère de début « www », le certificat générique ne traite pas de qui.
- Les certificats auto-signés : Les navigateurs clients ne faites pas confiance à ces certificats et avertissement l’utilisateur que le certificat du service virtuel n’est pas partie d’une chaîne d’approbation. Les certificats auto-signés conviennent pour le test ou les environnements où les administrateurs de contrôlent les clients et peuvent ignorer en toute sécurité les alertes de sécurité du navigateur. Charges de travail de production ne doivent jamais utiliser des certificats auto-signés.

Pour plus d’informations, consultez [configurer une terminaison SSL avec application gateway](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

## <a name="end-to-end-ssl-encryption"></a>Le chiffrement SSL de bout en bout

Certains clients ne peuvent pas souhaitent communication non chiffrée vers les serveurs principaux. Cela peut être dû à des d’exigences de sécurité et de conformité, ou au fait que l’application n’accepte qu’une connexion sécurisée. Pour de telles applications, la passerelle Application Gateway prend désormais en charge le chiffrement SSL de bout en bout.

Le chiffrement SSL de bout en bout vous permet de transmettre en toute sécurité des données sensibles au serveur back-end, tout en continuant de bénéficier des avantages des fonctionnalités d’équilibrage de charge de couche 7 fournies par la passerelle Application Gateway. Il s’agit notamment de la fonctionnalité d’affinité basée sur les cookies, du routage basé sur l’URL, de la prise en charge du routage basé sur des sites ou de la possibilité d’injecter des * en-têtes X-Forwarded.

Lorsqu’elle est configurée avec un mode de communication SSL de bout en bout, la passerelle Application Gateway ferme les sessions SSL au niveau de la passerelle et déchiffre le trafic de l’utilisateur. Il applique ensuite les règles configurées pour sélectionner une instance de pool principal appropriée vers laquelle acheminer le trafic. La passerelle Application Gateway établit ensuite une nouvelle connexion SSL vers le serveur back-end, puis chiffre à nouveau les données à l’aide du certificat de clé publique de ce serveur avant de transmettre la requête au back-end. Toute réponse du serveur web passe par le même processus vers l’utilisateur final. SSL de bout en bout est activé en définissant le paramètre de protocole dans [paramètre HTTP principal de](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) vers HTTPS, qui est ensuite appliqué à un pool principal.

La stratégie SSL s’applique au trafic frontend et backend. Sur le serveur frontal, Application Gateway joue le rôle du serveur et applique la stratégie. Sur le serveur principal, Application Gateway joue le rôle du client et envoie les informations de protocole/chiffrement en tant que la préférence pendant la négociation SSL.

Passerelle d’application communique uniquement avec des instances de serveur principal qui ont soit dans la liste verte leur certificat avec la passerelle d’application ou dont les certificats sont signés par des autorités d’autorité de certification connues où le nom commun du certificat correspond au nom hôte dans le protocole HTTP paramètres du serveur principal. Citons notamment les services Azure approuvés tels que les applications Azure App service web et de gestion des API Azure.

Si les certificats des membres du pool principal ne sont pas signés par des autorités de certification connues, chaque instance dans le pool principal avec SSL de bout en bout est activé doit être configuré avec un certificat pour permettre la communication sécurisée. L’ajout du certificat permet à la passerelle d’application de communiquer uniquement avec des instances de serveur principal connues. Il sécurise la communication de bout en bout.

> [!NOTE] 
>
> Le programme d’installation de l’authentification par certificat n’est pas nécessaire pour les services Azure approuvés tels que les applications Azure App service web et de gestion des API Azure.

> [!NOTE] 
>
> Le certificat ajouté au **paramètre HTTP principal de** serveurs pour authentifier le serveur principal peuvent être le même que le certificat ajouté à la **écouteur** pour la terminaison SSL au niveau de passerelle d’application ou différents pour sécurité renforcée.

![scénario ssl de bout en bout][1]

Dans cet exemple, les demandes utilisant TLS1.2 sont acheminées vers les serveurs principaux dans Pool1 à l’aide du chiffrement SSL de bout en bout.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Chiffrement SSL de bout en bout et liste blanche de certificats

La passerelle Application Gateway communique uniquement avec les instances de serveur principal identifiées dont le certificat figure sur la liste approuvée par la passerelle Application Gateway. Pour approuver des certificats, vous devez télécharger la clé publique des certificats du serveur principal pour la passerelle Application Gateway (non le certificat racine). Seules les connexions aux serveurs principaux connus et sur liste blanche sont alors autorisées. Les autres serveurs principaux renvoient une erreur de passerelle. Les certificats auto-signés sont uniquement destinés à des fins de test et ne sont pas recommandés pour les charges de travail de production. Ces certificats doivent figurer sur la liste approuvée par la passerelle d’application comme décrit dans les étapes précédentes avant de pouvoir être utilisés.

> [!NOTE]
> Il n’est pas nécessaire de configurer les certificats d’authentification pour les services Azure approuvés comme Azure App Service.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>SSL de bout en bout avec la référence SKU v2

Les certificats d’authentification ont été déconseillés et remplacés par des certificats racines approuvés dans la référence SKU v2 d’Application Gateway. Ils fonctionnent comme les certificats d’authentification, avec quelques différences importantes :

- Les certificats signés par des autorités de certification connues dont le nom commun correspond au nom de l’hôte dans les paramètres HTTP du serveur principal ne nécessitent pas d’étape supplémentaire pour que le protocole SSL de bout en bout fonctionne. 

   Par exemple, si les certificats de serveur principal sont émis par une autorité de certification connue et a un nom commun contoso.com et que le de paramètres HTTP du serveur principal est également défini sur contoso.com, aucune étape supplémentaire n’est nécessaire. Vous pouvez définir le paramètre du protocole HTTP du serveur principal sur HTTPS et le contrôle d’intégrité et le chemin des données seront de ce fait compatibles SSL. Si vous utilisez Azure App Service ou autres services web Azure en tant que votre serveur principal, puis ils sont également implicitement fiables et aucune étape n’est requise pour le protocole SSL de bout en bout.
- Si le certificat est auto-signé ou signé par des intermédiaires inconnus, un certificat racine approuvé doit être défini pour pouvoir activer le SSL de bout en bout dans la référence SKU v2. Application Gateway communique uniquement avec les serveurs dont le certificat racine du certificat de serveur correspond à un présent dans la liste des certificats racines de confiance dans le paramètre HTTP principal associé au pool de serveurs.
- En plus de la correspondance du certificat racine, Application Gateway vérifie également si le paramètre d’hôte spécifié dans le paramètre HTTP du serveur principal d’hôte correspond à celle du nom commun (CN) présenté par le certificat SSL du serveur principal. Lorsque vous tentez d’établir une connexion SSL au serveur principal, Application Gateway définit l’extension SNI d’indication de nom de serveur à l’hôte spécifié dans le paramètre HTTP du serveur principal.
- Si vous choisissez l’option **Choisir le nom d’hôte à partir de l’adresse du serveur principal** au lieu du champ Hôte dans le paramètre HTTP du serveur principal, l’en-tête SNI est toujours définie sur le nom de domaine complet du pool principal et le nom commun sur le certificat SSL du serveur principal doit correspondre à son nom de domaine complet. Membres du pool principal avec des adresses IP ne sont pas pris en charge dans ce scénario.
- Le certificat racine est un certificat de racine codé en base64 parmi les certificats de serveur principal.

## <a name="next-steps"></a>Étapes suivantes

Après avoir découvert le chiffrement SSL de bout en bout, accédez à [Configurer le SSL de bout en bout avec Application Gateway avec PowerShell](application-gateway-end-to-end-ssl-powershell.md) pour créer une passerelle d’application à l’aide du chiffrement SSL de bout en bout.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
