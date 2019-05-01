---
title: Réécrire les en-têtes HTTP avec Azure Application Gateway | Microsoft Docs
description: Cet article fournit une vue d’ensemble de la réécriture des en-têtes HTTP dans Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 04/29/2019
ms.author: absha
ms.openlocfilehash: 89df3a981ba3710e848f834c303772e94e10b139
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947175"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Réécrire les en-têtes HTTP avec Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

En-têtes HTTP permettent à un client et le serveur pour passer des informations supplémentaires avec une demande ou réponse. En réécrivant ces en-têtes, vous pouvez effectuer des tâches importantes, telles que l’ajout de champs d’en-tête liées à la sécurité comme HSTS / X-XSS-Protection, en supprimant des champs d’en-tête de réponse qui peuvent révéler des informations sensibles et en supprimant les informations de port à partir de En-têtes X-Forwarded-For.

Application Gateway vous permet à ajouter, supprimer ou mettre à jour des en-têtes de demande et de réponse HTTP lors de la demande et déplacent des paquets de réponse entre le client et les pools principaux. Et il vous permet d’ajouter les conditions pour vous assurer que les en-têtes spécifiés sont réécrites uniquement lorsque certaines conditions sont remplies.

Application Gateway prend également en charge plusieurs [variables serveur](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) qui vous permettent de stocker des informations supplémentaires concernant les demandes et réponses. Cela rend plus facile de créer des règles de réécriture puissantes.

> [!NOTE]
>
> La prise en charge de la réécriture de l’en-tête HTTP est disponible uniquement pour le [Standard_V2 et WAF_v2 référence (SKU)](application-gateway-autoscaling-zone-redundant.md).

![Réécriture des en-têtes](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>En-têtes pris en charge

Vous pouvez réécrire tous les en-têtes de demandes et réponses, sauf pour les en-têtes d’hôte, de connexion et de mise à niveau. Vous pouvez également utiliser la passerelle d’application pour créer des en-têtes personnalisés et les ajouter pour les demandes et réponses est acheminées vers lui.

## <a name="rewrite-conditions"></a>Conditions de réécriture

Vous pouvez utiliser des conditions de réécriture pour évaluer le contenu des requêtes HTTP (S) et des réponses et de procéder à une réécriture de l’en-tête uniquement lorsqu’une ou plusieurs conditions sont remplies. La passerelle d’application utilise ces types de variables à évaluer le contenu des requêtes HTTP (S) et des réponses :

- En-têtes HTTP dans la demande.
- En-têtes HTTP dans la réponse.
- Variables de serveur de passerelle d’application

Vous pouvez utiliser une condition pour déterminer si une variable spécifiée est présente, si une variable spécifiée correspond à une valeur spécifique, ou si une variable spécifiée correspond à un modèle spécifique. Vous utilisez le [bibliothèque d’Expressions régulières Compatible (PCRE) Perl](https://www.pcre.org/) pour configurer le modèle d’expression régulière dans les conditions de correspondance. Pour en savoir plus sur la syntaxe d’expression régulière, consultez le [page principale d’expressions régulières Perl](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Actions de réécriture

Vous utilisez des actions de réécriture pour spécifier les en-têtes de demande et de réponse que vous souhaitez réécrire et la nouvelle valeur pour les en-têtes. Vous pouvez créer un nouvel en-tête, modifiez la valeur d’un en-tête existant ou supprimer un en-tête existant. La valeur d’un en-tête existant ou d’un nouvel en-tête peut être définie pour ces types de valeurs :

- Texte.
- En-tête de demande. Pour spécifier un en-tête de requête, vous devez utiliser la syntaxe {http_req_*headerName*}.
- En-tête de réponse. Pour spécifier un en-tête de réponse, vous devez utiliser la syntaxe {http_resp_*headerName*}.
- Variable de serveur. Pour spécifier une variable de serveur, vous devez utiliser la syntaxe {var_*serverVariable*}.
- Une combinaison de texte, un en-tête de requête, un en-tête de réponse et une variable de serveur.

## <a name="server-variables"></a>Variables de serveur

Application Gateway utilise les variables de serveur pour stocker des informations utiles sur le serveur, la connexion avec le client et la demande en cours sur la connexion. Adresse IP du client et le type de navigateur web sont des exemples d’informations stockées. Variables de serveur modifier dynamiquement, par exemple, lorsqu’une nouvelle page est chargée ou lorsqu’un formulaire est publié. Vous pouvez utiliser ces variables à évaluer les conditions de réécriture et de réécrire les en-têtes.

Passerelle d’application prend en charge ces variables de serveur :

| Nom de la variable | Description                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Le champ d’en-tête de requête X-Forwarded-For client avec le `client_ip` variable (voir l’explication plus loin dans ce tableau) est ajoutée dans le format IP1, IP2, IP3 et ainsi de suite. Si le champ X-Forwarded-For n’est pas dans l’en-tête de demande client, le `add_x_forwarded_for_proxy` variable est égale à la `$client_ip` variable. Cette variable est particulièrement utile lorsque vous souhaitez réécrire l’en-tête X-Forwarded-For définie par la passerelle d’Application afin que l’en-tête contient uniquement l’adresse IP sans les informations de port. |
| ciphers_supported          | Une liste des chiffrements pris en charge par le client.          |
| ciphers_used               | La chaîne de chiffrements utilisé pour une connexion SSL établie. |
| client_ip                  | L’adresse IP du client à partir de laquelle la passerelle d’application a reçu la demande. S’il existe un proxy inverse avant de la passerelle d’application et le client d’origine, *client_ip* retournera l’adresse IP du proxy inverse. |
| client_port                | Le port du client.                                                  |
| client_tcp_rtt             | Informations sur le client connexion TCP. Disponible sur les systèmes qui prennent en charge l’option de socket TCP_INFO. |
| client_user                | Lorsque l’authentification HTTP est utilisée, le nom d’utilisateur fournies pour l’authentification. |
| host                       | Dans cet ordre de priorité : le nom d’hôte à partir de la ligne de requête, le nom d’hôte à partir du champ d’en-tête de demande hôte ou le nom du serveur correspondant à une demande. |
| cookie_*nom*              | Le *nom* cookie.                                            |
| http_method                | la méthode utilisée pour effectuer la demande d’URL. Par exemple, GET ou POST. |
| HTTP_STATUS                | L’état de session. Par exemple, 200, 400 ou 403.                       |
| http_version               | Le protocole de la demande. Généralement HTTP/1.0, HTTP/1.1 ou HTTP/2.0. |
| query_string               | La liste de paires variable/valeur qui suit le « ? » dans l’URL demandée. |
| received_bytes             | La longueur de la demande (y compris la ligne de requête, un en-tête et un corps de la demande). |
| request_query              | Les arguments dans la ligne de demande.                                |
| request_scheme             | Le schéma de demande : http ou https.                            |
| request_uri                | La demande URI complet d’origine (avec des arguments).                   |
| sent_bytes                 | Le nombre d’octets envoyés à un client.                             |
| server_port                | Le port du serveur qui a accepté une demande.                 |
| ssl_connection_protocol    | Le protocole de la connexion SSL établie.        |
| ssl_enabled                | « On » si la connexion fonctionne en mode SSL. Sinon, une chaîne vide. |

## <a name="rewrite-configuration"></a>Configuration de réécriture

Pour configurer la réécriture d’en-tête HTTP, vous devez suivre ces étapes.

1. Créer les objets qui sont requis pour la réécriture d’en-tête HTTP :

   - **Réécriture d’action**: Permet de spécifier la demande et de champs d’en-tête de demande que vous voulez éviter de réécrire et de la nouvelle valeur pour les en-têtes. Vous pouvez associer un ou plusieurs conditions avec une action de réécriture de réécriture.

   - **Réécrivez la condition**: Une configuration facultative. Conditions de réécriture évaluent le contenu des requêtes HTTP (S) et des réponses. L’action de réécriture se produira si la demande de HTTP (S) ou la réponse correspond à la condition de réécriture.

     Si vous associez plusieurs conditions à une action, l’action se produit uniquement lorsque toutes les conditions sont remplies. En d’autres termes, l’opération est une opération AND logique.

   - **Règle de réécriture**: Contient plusieurs réécriture action / réécrire les combinaisons de condition.

   - **Séquence de règle**: Permet de déterminer l’ordre dans lequel les règles de réécriture s’exécutent. Cette configuration est utile lorsque vous avez plusieurs règles de réécriture dans un ensemble de réécriture. Une règle de réécriture qui a une valeur de séquence de règle inférieure s’exécute en premier. Si vous affectez la même séquence de règle à deux règles de réécriture, l’ordre d’exécution est non déterministe.

   - **Réécrire ensemble**: Contient plusieurs règles de réécriture qui seront associés à une règle de routage de demande.

2. Attacher le jeu de réécriture (*rewriteRuleSet*) à une règle de routage. La configuration de la réécriture est attachée à l’écouteur source via la règle de routage. Lorsque vous utilisez une règle de routage de base, la configuration de réécriture de l’en-tête est associée à un écouteur de la source et est une réécriture de l’en-tête global. Lorsque vous utilisez une règle de routage basée sur le chemin d’accès, la configuration de réécriture de l’en-tête est définie sur le mappage de chemin d’accès d’URL. Dans ce cas, il s’applique uniquement à la zone de chemin d’accès spécifique d’un site.

Vous pouvez créer plusieurs jeux de réécriture d’en-tête HTTP et appliquer chaque réécriture définie sur plusieurs écouteurs. Mais vous pouvez appliquer uniquement un ensemble à un port d’écoute spécifique de réécriture.

## <a name="common-scenarios"></a>Scénarios courants

Voici quelques scénarios courants pour l’utilisation de réécriture de l’en-tête.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Supprimer les informations de port de l’en-tête X-Forwarded-For

Application Gateway insère un en-tête X-Forwarded-For dans toutes les demandes avant qu’il transfère les demandes au serveur principal. Cet en-tête est une liste séparée par des virgules des ports IP. Il peut y avoir des scénarios dans lesquels les serveurs principaux peut-être uniquement les en-têtes pour contenir les adresses IP. Vous pouvez utiliser la réécriture de l’en-tête à supprimer les informations de port de l’en-tête X-Forwarded-For. Pour ce faire consiste à définir l’en-tête à la variable de serveur add_x_forwarded_for_proxy :

![Supprimer le port](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Modifier une URL de redirection

Lorsqu’une application de serveur principal envoie une réponse de redirection, vous souhaiterez peut-être rediriger le client vers une URL différente de celle spécifiée par l’application back-end. Par exemple, vous pouvez souhaiter procéder lorsqu’un service d’application est hébergé derrière une passerelle d’application et requiert que le client effectuer une redirection vers son chemin d’accès relatif. (Par exemple, une redirection de contoso.azurewebsites.net/path1 à contoso.azurewebsites.net/path2.)

App Service étant un service partagé, il utilise l’en-tête d’hôte dans la demande pour acheminer la demande vers le point de terminaison correct. Services d’application ont le nom de domaine par défaut *. azurewebsites.net (par exemple, contoso.azurewebsites.net) qui est différent du nom de domaine de la passerelle d’application (par exemple, contoso.com). Étant donné que la demande d’origine à partir du client a le nom de domaine de la passerelle d’application (contoso.com) en tant que le nom d’hôte, la passerelle d’application modifie le nom d’hôte pour contoso.azurewebsites.net. Il apporte cette modification afin que le service d’application peut acheminer la demande vers le point de terminaison correct.

Lorsque le service d’application envoie une réponse de redirection, il utilise le même nom d’hôte dans l’en-tête location de sa réponse que celui de la demande qu’il reçoit à partir de la passerelle d’application. Par conséquent, le client fera la demande directement à contoso.azurewebsites.net/path2 au lieu de passer par la passerelle d’application (contoso.com/path2). En ignorant la passerelle d’application n’est pas souhaitable.

Vous pouvez résoudre ce problème en définissant le nom d’hôte dans l’en-tête d’emplacement au nom de domaine de la passerelle d’application.

Voici les étapes permettant de remplacer le nom d’hôte :

1. Créer une règle de réécriture avec une condition qui évalue si l’en-tête d’emplacement dans la réponse contient azurewebsites.net. Entrez le modèle `(https?):\/\/.*azurewebsites\.net(.*)$`.
1. Effectuer une action pour réécrire l’en-tête d’emplacement afin qu’il dispose de nom d’hôte de la passerelle d’application. Ce faire, entrez `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` en tant que la valeur d’en-tête.

![Modifier l’en-tête d’emplacement](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implémenter des en-têtes de sécurité HTTP pour éviter les vulnérabilités

Vous pouvez corriger plusieurs vulnérabilités de sécurité en implémentant les en-têtes nécessaires dans la réponse de l’application. Ces en-têtes de sécurité incluent X-XSS-Protection Strict-Transport-Security et la stratégie de sécurité de contenu. Vous pouvez utiliser la passerelle d’Application pour définir ces en-têtes pour toutes les réponses.

![En-tête de sécurité](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Supprimer des en-têtes indésirables

Vous souhaiterez peut-être supprimer les en-têtes qui révèlent des informations sensibles à partir d’une réponse HTTP. Par exemple, vous souhaiterez peut-être supprimer des informations telles que le nom de serveur back-end, système d’exploitation ou détails de la bibliothèque. Vous pouvez utiliser la passerelle d’application pour supprimer ces en-têtes :

![La suppression d’en-tête](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Vérifier la présence d’un en-tête

Vous pouvez évaluer un en-tête de demande ou réponse HTTP pour la présence d’une variable de l’en-tête ou le serveur. Cette version d’évaluation est utile lorsque vous souhaitez procéder à une réécriture de l’en-tête uniquement quand un en-tête spécifique est présent.

![Vérification de la présence d’un en-tête](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Limites

- Réécrire les en-têtes de connexion, de mise à niveau et d’hôte n’est pas actuellement pris en charge.

- Les noms d’en-tête peuvent contenir des caractères alphanumériques et des symboles spécifiques tel que défini dans [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Nous ne prennent actuellement en charge le caractère de soulignement (\_) caractère spécial dans les noms d’en-tête.

## <a name="need-help"></a>Vous avez besoin d’aide ?

Contactez-nous à l’adresse [ AGHeaderRewriteHelp@microsoft.com ](mailto:AGHeaderRewriteHelp@microsoft.com) si vous avez besoin d’aide avec cette fonctionnalité.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment réécrire les en-têtes HTTP, consultez :

- [Réécrire les en-têtes HTTP à l’aide du portail Azure](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [Réécrire les en-têtes HTTP à l’aide d’Azure PowerShell](add-http-header-rewrite-rule-powershell.md)
