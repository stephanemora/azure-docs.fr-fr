---
title: Réécrire des en-têtes HTTP dans Azure Application Gateway | Microsoft Docs
description: Cet article fournit présente la possibilité de réécrire des en-têtes HTTP dans Azure Application Gateway.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 6750276cf31d0c804b38cdf3ea6e41a4505c93f1
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971816"
---
# <a name="rewrite-http-headers-with-application-gateway-public-preview"></a>Réécrire des en-têtes HTTP dans Azure Application Gateway (préversion publique)

Les en-têtes HTTP permettent au client et au serveur de passer des informations supplémentaires avec la requête ou la réponse. La réécriture de ces en-têtes HTTP permet de prendre en charge plusieurs scénarios importants, notamment l’ajout de champs d’en-tête liés à la sécurité comme HSTS/ X-XSS-Protection ou la suppression de champs d’en-tête de réponse pouvant révéler des informations sensibles comme le nom du serveur principal.

Application Gateway permet désormais de réécrire des en-têtes de requêtes HTTP entrantes et de réponses HTTP sortantes. Vous pouvez ajouter, supprimer ou mettre à jour les en-têtes de requête et de réponse HTTP pendant le déplacement des paquets de requête/réponse entre les pools client et principal. Vous pouvez réécrire les champs d’en-tête standard (définis dans la [RFC 2616](https://www.ietf.org/rfc/rfc2616.txt)) et non standard.

> [!NOTE] 
>
> La prise en charge de la réécriture d’en-tête HTTP n’est disponible que pour la [nouvelle référence SKU [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

La prise en charge de la réécriture d’en-tête Application Gateway offre ce qui suit :

- **Réécriture d’en-tête global** : Vous pouvez réécrire des en-têtes spécifiques pour toutes les demandes et réponses en lien avec le site.
- **Réécriture d’en-tête basé sur un chemin d’accès** : ce type de réécriture permet de réécrire un en-tête uniquement pour les demandes et réponses qui ont trait uniquement à une zone de site spécifique, par exemple, une zone de panier d’achat indiquée par /cart/*.

Avec cette modification, vous devez :

1. Créer les nouveaux objets requis pour réécrire les en-têtes HTTP : 
   - **RequestHeaderConfiguration** : cet objet est utilisé pour spécifier les champs d’en-tête de requête que vous souhaitez réécrire et la nouvelle valeur de réécriture des en-têtes d’origine.
   - **ResponseHeaderConfiguration** : cet objet est utilisé pour spécifier les champs d’en-tête de réponse que vous souhaitez réécrire et la nouvelle valeur de réécriture des en-têtes d’origine.
   - **ActionSet** : cet objet contient les configurations des en-têtes de requête et de réponse spécifiés ci-dessus. 
   - **RewriteRule** : cet objet contient tous les *actionSets* spécifiés ci-dessus. 
   - **RewriteRuleSet** : cet objet contient toutes les *rewriteRules* et devra être attaché à une règle d’acheminement des requêtes, qu’elles soient basiques ou basées sur un chemin d’accès.
2. Vous devez ensuite associer l’ensemble de règles de réécriture à une règle d’acheminement. Une fois créée, cette configuration de réécriture est attachée à l’écouteur source via la règle d’acheminement. Lorsque vous utilisez une règle d’acheminement de base, la configuration de réécriture de l’en-tête est associée à un écouteur de la source et est une réécriture de l’en-tête global. Lorsqu’une règle d’acheminement basée sur le chemin d’accès est utilisée, la configuration de réécriture de l’en-tête est définie sur le mappage de chemin d’accès d’URL. Elle s’applique donc exclusivement à la zone de chemin d’accès spécifique d’un site.

Vous pouvez créer plusieurs ensembles de règles de réécriture d’en-tête HTTP, et chaque ensemble de règles de réécriture peut être appliqué à plusieurs écouteurs. Cependant, vous ne pouvez appliquer qu’un seul ensemble de règles de réécriture HTTP à un écouteur spécifique.

Vous pouvez réécrire la valeur dans les en-têtes pour :

- Valeur de texte. 

  *Exemple :* 

  ```azurepowershell-interactive
  $responseHeaderConfiguration = New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -  HeaderValue "max-age=31536000")
  ```

- Valeur d’un autre en-tête. 

  *Exemple 1 :* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration= New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-RequestHeader" -HeaderValue {http_req_oldHeader}
  ```

  > [!Note] 
  > Pour spécifier un en-tête de requête, vous devez utiliser la syntaxe : {http_req_headerName}

  *Exemple 2 :*

  ```azurepowershell-interactive
  $responseHeaderConfiguration= New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-ResponseHeader" -HeaderValue {http_resp_oldHeader}
  ```

  > [!Note] 
  > Pour spécifier un en-tête de réponse, vous devez utiliser la syntaxe : {http_resp_headerName}

- Valeur de variables de serveur prises en charge.

  *Exemple :* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration = New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Ciphers-Used" -HeaderValue "{var_ciphers_used}"
  ```

  > [!Note] 
  > Pour spécifier une variable de serveur, vous devez utiliser la syntaxe : {var_serverVariable}

- Combinaison des éléments ci-dessus.

Les variables de serveur mentionnées ci-dessus sont les variables qui fournissent des informations sur le serveur, la connexion avec le client et la requête en cours sur la connexion. Cette fonctionnalité prend en charge la réécriture d’en-têtes dans les variables de serveur suivantes :

| Variables de serveur prises en charge | Description                                                  |
| -------------------------- | :----------------------------------------------------------- |
| ciphers_supported          | retourne la liste de chiffrements pris en charge par le client          |
| ciphers_used               | retourne la chaîne de chiffrements utilisés pour une connexion SSL établie |
| client_latitude            | pour déterminer le pays, région et la ville en fonction de l’adresse IP du client |
| client_longitude           | pour déterminer le pays, région et la ville en fonction de l’adresse IP du client |
| client_port                | port client                                                  |
| client_tcp_rtt             | informations sur la connexion TCP client ; disponibles sur les systèmes qui prennent en charge l’option de socket TCP_INFO |
| client_user                | lorsque vous utilisez une authentification HTTP, nom d’utilisateur fourni pour l’authentification |
| host                       | dans cet ordre de priorité : nom d’hôte de la ligne de demande, ou nom d’hôte du champ d’en-tête de demande « Host », ou nom de serveur correspondant à une demande |
| http_method                | méthode utilisée pour établir la demande d’URL. Par exemple GET, POST, etc. |
| HTTP_STATUS                | état de session, par exemple : 200, 400, 403, etc.                       |
| http_version               | protocole de demande, généralement « HTTP/1.0 », « HTTP/1.1 » ou « HTTP/2.0 » |
| query_string               | liste de paires variable-valeur qui suivent le « ? » dans l’URL demandée. |
| received_byte              | longueur de la demande (incluant la ligne de demande, l’en-tête et le corps de la demande) |
| request_query              | arguments dans la ligne de demande                                |
| request_scheme             | schéma de demande, « http » ou « https »                            |
| request_uri                | URI complète de la demande d’origine (avec arguments)                   |
| sent_bytes                 | nombre d’octets envoyés à un client                             |
| server_port                | port du serveur, qui a accepté une demande                 |
| ssl_connection_protocol    | retourne le protocole d’une connexion SSL établie        |
| ssl_enabled                | « on » si la connexion opère en mode SSL, ou chaîne vide dans le cas contraire |

## <a name="limitations"></a>Limites

- Cette fonctionnalité permettant de réécrire des en-têtes HTTP est actuellement disponible uniquement via Azure PowerShell, API Azure et SDK Azure. Une prise en charge via le portail et Azure CLI sera bientôt disponible.

- Lorsque vous appliquez une réécriture à votre Application Gateway, vous ne devez pas utiliser le portail pour apporter des modifications ultérieures à cette Application Gateway aussi longtemps que la fonctionnalité est prise en charge sur le portail. Si vous utilisez le portail pour apporter des modifications à l’Application Gateway après application d’une règle de réécriture, la règle de réécriture d’en-tête. Vous pouvez continuer à apporter des modifications à l’aide d’Azure PowerShell, d’API Azure ou de SDK Azure.

- La prise en charge de la réécriture de l’en-tête HTTP est disponible uniquement sur la nouvelle référence SKU [Standard_V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant). La fonctionnalité ne sera pas prise en charge sur l’ancienne référence SKU.

- La réécriture des en-têtes Connect, Upgrade et Host n’est pas encore prise en charge.

- Deux variables de serveur importantes, client_ip (adresse IP du client effectuant la requête) et cookie_*name* (cookie *name*), ne pas sont encore prises en charge. La variable de serveur client_ip est particulièrement utile dans les scénarios dans lesquels des clients envisagent de réécrire l’en-tête x-forwarded-for défini par Application Gateway, de sorte que l’en-tête contient uniquement l’adresse IP du client et pas les informations de port.

  Ces deux variables de serveur seront bientôt être prises en charge.

- La fonctionnalité permettant de réécrire les en-têtes HTTP de manière conditionnelle sera bientôt disponible.

- Les noms d’en-tête peuvent contenir tout caractère alphanumérique et symbole spécifique définis dans la norme [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Toutefois, nous avons ne prenons pas en charge actuellement le caractère spécial « soulignement » (\_) dans le nom d’en-tête. 

## <a name="need-help"></a>Vous avez besoin d’aide ?

Contactez-nous à l’adresse [AGHeaderRewriteHelp@microsoft.com](mailto:AGHeaderRewriteHelp@microsoft.com) au cas où vous avez besoin d’aide avec cette fonctionnalité.

## <a name="next-steps"></a>Étapes suivantes

Après avoir découvert la fonctionnalité permettant de réécrire des en-têtes HTTP, voir [Créer une passerelle d’application redondante interzone et avec mise à l’échelle automatique qui réécrit des en-têtes HTTP](tutorial-http-header-rewrite-powershell.md) ou [Réécrire des en-têtes HTTP dans une passerelle d’application redondante interzone et avec mise à l’échelle automatique existante](add-http-header-rewrite-rule-powershell.md)
