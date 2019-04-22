---
title: Réécrire des en-têtes HTTP dans Azure Application Gateway | Microsoft Docs
description: Cet article fournit présente la possibilité de réécrire des en-têtes HTTP dans Azure Application Gateway.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/11/2019
ms.author: absha
ms.openlocfilehash: 20c484779e7ffe74ae01e33472b4cf8761d81b66
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682678"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Réécrire les en-têtes HTTP avec Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Les en-têtes HTTP permettent au client et au serveur de passer des informations supplémentaires avec la requête ou la réponse. Réécriture de ces en-têtes HTTP vous permet d’accomplir plusieurs scénarios importants tels que l’ajout de champs d’en-tête liées à la sécurité comme HSTS / X-XSS-Protection, en-tête de réponse de suppression des champs qui peut révéler des informations sensibles, suppression des informations de port à partir de En-têtes X-Forwarded-For, etc. Passerelle d’application prend en charge la possibilité d’ajouter, supprimer ou mettre à jour des en-têtes de demande et de réponse HTTP lors de la demande et les paquets de réponse se déplacent entre les pools de client et serveur principal. Il vous permet d’ajouter des conditions pour vous assurer que les en-têtes spécifiés sont réécrites uniquement lorsque certaines conditions sont remplies. La fonctionnalité prend également en charge plusieurs [variables serveur](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) qui contribuent à stocker des informations supplémentaires sur les demandes et réponses, ce qui permet de rendre les règles de réécriture puissantes.
> [!NOTE]
>
> La prise en charge de la réécriture d’en-tête HTTP n’est disponible que pour la [nouvelle référence SKU [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

![Réécriture des en-têtes](media/rewrite-http-headers/rewrite-headers.png)

## <a name="headers-supported-for-rewrite"></a>Réécriture des en-têtes pris en charge pour

La fonctionnalité vous permet de réécrire tous les en-têtes de demande et de réponse à l’exception des en-têtes d’hôte, la connexion et mise à niveau. Vous pouvez également utiliser la passerelle d’application pour créer des en-têtes personnalisés et les ajouter à la demande et les réponses qui est acheminées vers lui. 

## <a name="rewrite-conditions"></a>Conditions de réécriture

À l’aide de la réécriture de conditions, vous pouvez évaluer le contenu des requêtes HTTP (S) et des réponses et effectuer un en-tête réécrire uniquement si une ou plusieurs conditions sont remplies. Les 3 types de variables suivants sont utilisés par la passerelle d’application pour évaluer le contenu des requêtes HTTP (S) et des réponses :

- En-têtes HTTP dans la demande
- En-têtes HTTP dans la réponse
- Variables de serveur de passerelle application

Une condition peut être utilisée pour déterminer si la variable spécifiée est présente, si la variable spécifiée correspond exactement à une valeur spécifique, ou si la variable spécifiée correspond exactement à un modèle spécifique. [Bibliothèque d’Expressions régulières Compatible (PCRE) Perl](https://www.pcre.org/) est utilisé pour implémenter le modèle d’expression régulière dans les conditions de correspondance. Pour en savoir plus sur la syntaxe d’expression régulière, consultez le [page de manuel des expressions régulières Perl](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Actions de réécriture

Réécriture d’actions sont utilisées pour spécifier les en-têtes de demande et de réponse que vous souhaitez réécrire et la nouvelle valeur que les en-têtes d’origine doivent être réécrites pour. Vous pouvez créer un nouvel en-tête, modifiez la valeur d’un en-tête existant ou supprimer un en-tête existant. La valeur d’un en-tête existant ou d’un nouvel en-tête peut être définie pour les types de valeurs suivants :

- Texte 
- En-tête de demande : Pour spécifier un en-tête de requête, vous devez utiliser la syntaxe {http_req_*headerName*}
- En-tête de réponse : Pour spécifier un en-tête de réponse, vous devez utiliser la syntaxe {http_resp_*headerName*}
- Variable de serveur : Pour spécifier une variable de serveur, vous devez utiliser la syntaxe {var_*serverVariable*}
- Combinaison de texte, en-tête de requête, en-tête de réponse et une variable de serveur.

## <a name="server-variables"></a>Variables de serveur

Application gateway utilise les variables de serveur pour stocker des informations utiles sur le serveur, la connexion avec le client et la demande en cours sur la connexion, telles que l’adresse IP du client ou le type de navigateur web. Ces variables sont modifiées dynamiquement, telles que lorsqu’une nouvelle page est chargée, ou un formulaire est publié. Vous pouvez utiliser ces variables de serveur pour évaluer les conditions de réécriture et réécrire les en-têtes. 

Passerelle d’application prend en charge les variables de serveur suivantes :

| Variables de serveur prises en charge | Description                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Contient le « X-Forwarded-For » client demande champ d’en-tête avec le `client_ip` (procédure expliquée dans le tableau ci-dessous) variable est ajoutée dans le format (IP1, IP2, IP3,...). Si le champ « X-Forwarded-For » n’est pas présent dans l’en-tête de demande client, le `add_x_forwarded_for_proxy` variable est égale à la `$client_ip` variable. Cette variable est particulièrement utile dans les scénarios où les clients envisagez de réécrire l’en-tête X-Forwarded-For définie par la passerelle d’Application, telles que l’en-tête contient uniquement l’adresse IP sans les informations de port. |
| ciphers_supported          | retourne la liste de chiffrements pris en charge par le client          |
| ciphers_used               | retourne la chaîne de chiffrements utilisés pour une connexion SSL établie |
| client_ip                  | Adresse IP du client à partir de laquelle la passerelle d’application a reçu la demande. S’il est un proxy inverse avant de la passerelle d’application et le client d’origine, puis *client_ip* retournera l’adresse IP du proxy inverse. |
| client_port                | port client                                                  |
| client_tcp_rtt             | informations sur la connexion TCP client ; disponibles sur les systèmes qui prennent en charge l’option de socket TCP_INFO |
| client_user                | lorsque vous utilisez une authentification HTTP, nom d’utilisateur fourni pour l’authentification |
| host                       | dans cet ordre de priorité : nom d’hôte de la ligne de demande, ou nom d’hôte du champ d’en-tête de demande « Host », ou nom de serveur correspondant à une demande |
| cookie_*nom*              | le *nom* cookie                                            |
| http_method                | méthode utilisée pour établir la demande d’URL. Par exemple GET, POST, etc. |
| HTTP_STATUS                | état de session, par exemple : 200, 400, 403, etc.                       |
| http_version               | protocole de demande, généralement « HTTP/1.0 », « HTTP/1.1 » ou « HTTP/2.0 » |
| query_string               | liste de paires variable-valeur qui suivent le « ? » dans l’URL demandée. |
| received_bytes             | longueur de la demande (incluant la ligne de demande, l’en-tête et le corps de la demande) |
| request_query              | arguments dans la ligne de demande                                |
| request_scheme             | schéma de demande, « http » ou « https »                            |
| request_uri                | URI complète de la demande d’origine (avec arguments)                   |
| sent_bytes                 | nombre d’octets envoyés à un client                             |
| server_port                | port du serveur, qui a accepté une demande                 |
| ssl_connection_protocol    | retourne le protocole d’une connexion SSL établie        |
| ssl_enabled                | « on » si la connexion opère en mode SSL, ou chaîne vide dans le cas contraire |

## <a name="rewrite-configuration"></a>Configuration de réécriture

Pour configurer la réécriture d’en-tête HTTP, vous devez :

1. Créer les nouveaux objets requis pour réécrire les en-têtes HTTP :

   - **Réécriture d’Action**: permet de spécifier la demande et les champs d’en-tête de demande que vous avez l’intention de réécrire et la nouvelle valeur que les en-têtes d’origine doivent être réécrites pour. Vous pouvez choisir d’associer à un ou plus réécriture condition avec une action de réécriture.

   - **Réécrivez la Condition**: Il est une configuration facultative. Si une condition de la réécriture est ajoutée, il est évalué le contenu des requêtes HTTP (S) et des réponses. La décision d’exécuter l’action de réécriture associée à la condition de réécriture reposera si la demande de HTTP (S) ou la réponse mise en correspondance avec la condition de réécriture. 

     Si plusieurs conditions sont associé à une action, puis l’action sera exécutée uniquement lorsque toutes les conditions sont remplies, par exemple, une opération AND logique sera effectuée.

   - **Règle de réécriture**: règle de réécriture contient plusieurs réécriture action - réécrire les combinaisons de condition.

   - **Séquence de règle**: permet de déterminer l’ordre dans lequel les différentes règles de réécriture sont exécutées. Cela est utile lorsqu’il existe plusieurs règles de réécriture dans un ensemble de réécriture. La règle de réécriture avec la valeur de séquence de règle inférieure obtient exécutée en premier. Si vous fournissez la même séquence de règle à deux règles de réécriture, l’ordre d’exécution sera non déterministe.

   - **Réécrire ensemble**: contient plusieurs règles de réécriture qui seront associés à une règle de routage de demande.

2. Vous serez invité à joindre le jeu de réécriture (*rewriteRuleSet*) avec une règle de routage. Il s’agit, car la configuration de la réécriture est attachée à l’écouteur source via la règle de routage. Lorsque vous utilisez une règle d’acheminement de base, la configuration de réécriture de l’en-tête est associée à un écouteur de la source et est une réécriture de l’en-tête global. Lorsqu’une règle d’acheminement basée sur le chemin d’accès est utilisée, la configuration de réécriture de l’en-tête est définie sur le mappage de chemin d’accès d’URL. Elle s’applique donc exclusivement à la zone de chemin d’accès spécifique d’un site.

Vous pouvez créer plusieurs jeux de réécriture d’en-tête http et chaque jeu de réécriture peut être appliqué à plusieurs écouteurs. Toutefois, vous pouvez appliquer uniquement un ensemble à un port d’écoute spécifique de réécriture.

## <a name="common-scenarios"></a>Scénarios courants

Certains des scénarios courants qui nécessitent la réécriture de l’en-tête sont indiquées ci-dessous.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Supprimer les informations de port de l’en-tête X-Forwarded-For

Passerelle d’application insère l’en-tête X-Forwarded-For à toutes les demandes avant qu’il transfère les demandes au serveur principal. Le format de cet en-tête est une liste séparée par des virgules des IP : port. Toutefois, il est peut-être les scénarios où les serveurs principaux requièrent l’en-tête à contenir uniquement des adresses IP. Pour accomplir ces scénarios, réécriture de l’en-tête peut être utilisé pour supprimer les informations de port de l’en-tête X-Forwarded-For. Pour ce faire consiste à définir l’en-tête sur la variable de serveur add_x_forwarded_for_proxy. 

![Supprimer le port](media/rewrite-http-headers/remove-port.png)

### <a name="modify-the-redirection-url"></a>Modifier l’URL de redirection

Lorsqu’une application de serveur principal envoie une réponse de redirection, vous souhaiterez rediriger le client vers une URL différente de celle spécifiée par l’application principale. Un tel scénario est lorsqu’un service d’application est hébergé derrière une passerelle d’application et, le client doit effectuer une redirection vers son chemin d’accès relatif (redirection à partir de contoso.azurewebsites.net/path1 vers contoso.azurewebsites.net/path2). 

Dans la mesure où app service est un service mutualisé, il utilise l’en-tête d’hôte dans la demande pour router vers le point de terminaison correct. Services d’application ont le nom de domaine par défaut *. azurewebsites.net (par exemple, contoso.azurewebsites.net) qui est différent du nom de domaine de la passerelle d’application (par exemple, contoso.com). Dans la mesure où la demande d’origine à partir du client a un nom de domaine de contoso.com la passerelle d’application comme nom d’hôte, la passerelle d’application modifie le nom d’hôte pour contoso.azurewebsites.net, afin que le service de l’application peut router vers le point de terminaison correct. Lorsque le service d’application envoie une réponse de redirection, il utilise le même nom d’hôte dans l’en-tête location de sa réponse que celui de la demande qu’il reçoit à partir de la passerelle d’application. Par conséquent, le client effectuer la demande directement à contoso.azurewebsites.net/path2, au lieu de passer par la passerelle d’application (contoso.com/path2). En ignorant la passerelle d’application n’est pas souhaitable. 

Ce problème peut être résolu en définissant le nom d’hôte dans l’en-tête d’emplacement au nom de domaine de la passerelle d’application. Pour ce faire, vous pouvez créer une règle de réécriture avec une condition qui évalue si l’en-tête d’emplacement dans la réponse contient azurewebsites.net en entrant `(https?):\/\/.*azurewebsites\.net(.*)$` comme modèle et exécute une action pour la réécriture de l’en-tête d’emplacement pour que la passerelle d’application nom d’hôte en entrant `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` en tant que la valeur d’en-tête.

![Modifier l’en-tête d’emplacement](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implémenter des en-têtes de sécurité HTTP pour éviter les vulnérabilités

Plusieurs vulnérabilités de sécurité peuvent être résolues en implémentant les en-têtes nécessaires dans la réponse de l’application. Certaines de ces en-têtes de sécurité sont X-XSS-Protection, Strict-Transport-Security, Content-Security-Policy, etc. Vous pouvez utiliser la passerelle d’application pour définir ces en-têtes pour toutes les réponses.

![En-tête de sécurité](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Supprimer des en-têtes indésirables

Voulez-vous supprimer ces en-têtes de la réponse HTTP qui révèlent des informations sensibles telles que le nom du serveur principal, système d’exploitation, les détails de bibliothèque, etc. Vous pouvez utiliser la passerelle d’application pour les supprimer.

![La suppression d’en-tête](media/rewrite-http-headers/remove-headers.png)

### <a name="check-presence-of-a-header"></a>Vérifiez la présence d’un en-tête

Vous pouvez évaluer l’en-tête de demande ou réponse HTTP pour la présence d’une variable de l’en-tête ou le serveur. Cela est utile lorsque vous souhaitez effectuer une réécriture de l’en-tête uniquement quand un en-tête spécifique est présent.

![Vérification de la présence d’un en-tête](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Limites

- Réécrire les en-têtes de connexion, de mise à niveau et d’hôte n'est pas encore pris en charge.

- Les noms d’en-tête peuvent contenir tout caractère alphanumérique et symbole spécifique définis dans la norme [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Toutefois, nous avons ne prenons pas en charge actuellement le caractère spécial « soulignement » (\_) dans le nom d’en-tête. 

## <a name="need-help"></a>Vous avez besoin d’aide ?

Contactez-nous à l’adresse [AGHeaderRewriteHelp@microsoft.com](mailto:AGHeaderRewriteHelp@microsoft.com) au cas où vous avez besoin d’aide avec cette fonctionnalité.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment réécrire les en-têtes HTTP, consultez :

-  [Réécrire les en-têtes HTTP à l’aide du portail Azure](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
-  [Réécrire les en-têtes HTTP à l’aide d’Azure PowerShell](add-http-header-rewrite-rule-powershell.md)
