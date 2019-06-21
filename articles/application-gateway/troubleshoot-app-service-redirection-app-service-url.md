---
title: Résoudre les problèmes d’Azure Application Gateway avec App Service – Redirection vers l’URL d’App Service
description: Cet article fournit des informations sur la façon de résoudre les problèmes de redirection lorsque vous utilisez Azure Application Gateway avec Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: f456cfec82a315a2be877a52e4f3f1850b992736
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60715173"
---
# <a name="troubleshoot-application-gateway-with-app-service"></a>Dépanner Application Gateway avec App Service

Découvrez comment diagnostiquer et résoudre les problèmes rencontrés avec App Gateway et App Service en tant que serveur principal.

## <a name="overview"></a>Vue d'ensemble

Dans cet article, vous allez apprendre à résoudre les problèmes suivants :

> [!div class="checklist"]
> * L’URL d’App Service est exposée dans le navigateur en cas de redirection
> * Le domaine du cookie ARRAffinity d’App Service est défini sur nom d’hôte d’App Service (example.azurewebsites.net) au lieu de l’hôte d’origine

Lorsque vous configurez une instance d’App Service publique dans le pool principal d’Application Gateway et que vous avez une redirection configurée dans le code de votre application, vous noterez peut-être que lorsque vous accédez à Application Gateway, vous êtes redirigé par le navigateur directement à l’URL d’App Service.

Cette erreur peut se produire pour les raisons suivantes :

- Vous avez configuré la redirection sur votre App Service. La redirection peut être aussi simple que l’ajout d’une barre oblique à la demande.
- L’authentification Azure AD provoque la redirection.
- Vous avez activé « Choisir le nom d’hôte à partir de l’adresse du serveur principal » dans les paramètres HTTP d’Application Gateway.
- Vous n’avez pas inscrit votre domaine personnalisé avec App Service.

En outre, lorsque vous utilisez App Services derrière App Gateway et que vous utilisez un domaine personnalisé pour accéder à Application Gateway, vous pourriez noter que la valeur de domaine du cookie ARRAffinity définie par App Service contient le nom de domaine « example.azurewebsites.net ». Si vous souhaitez que votre nom d’hôte d’origine soit aussi le domaine du cookie, suivez la solution dans cet article.

## <a name="sample-configuration"></a>Exemple de configuration

- Écouteur HTTP : Base ou de multi-sites
- Pool d’adresses principales : App Service
- Paramètres HTTP : « Choisir le nom d’hôte à partir de l’adresse du serveur principal » activé
- Sonde : « Choisir le nom d’hôte à partir des paramètres HTTP » activé

## <a name="cause"></a>Cause :

Une instance App Service est uniquement accessible avec les noms d’hôte configurés dans les paramètres de domaine personnalisé. Par défaut, il s’agit de « example.azurewebsites.net », et si vous souhaitez accéder à App Service à l’aide d’Application Gateway avec un nom d’hôte qui n’est pas inscrit dans App Service ou le nom de domaine complet d’Application Gateway, vous devez remplacer le nom d’hôte dans la demande d’origine par le nom d’hôte d’App Service.

Pour faire cela avec Application Gateway, nous utilisons l’option « Choisir le nom d’hôte à partir de l’adresse du serveur principal » dans les paramètres HTTP, et pour que la sonde fonctionne, nous utilisons « Choisir le nom d’hôte à partir des paramètres du serveur principal HTTP » dans la configuration de la sonde.

![appservice-1](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Pour cette raison, lorsqu’App Service effectue une redirection, il utilise le nom d’hôte « example.azurewebsites.net » dans l’en-tête Location, au lieu du nom d’hôte d’origine, sauf en cas de configuration différente. Vous pouvez vérifier les exemples d’en-tête de requête et de réponse ci-dessous.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://example.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=example.azurewebsites.net

X-Powered-By: ASP.NET
```
Dans l’exemple ci-dessus, vous pouvez remarquer que l’en-tête de réponse a un code d’état 301 pour la redirection et que l’en-tête Location a le nom d’hôte d’App Service au lieu du nom d’hôte d’origine « www.contoso.com ».

## <a name="solution"></a>Solution

Ce problème peut être résolu en n’utilisant pas de redirection côté application. Toutefois, si cela n’est pas possible, nous devons transmettre le même en-tête hôte que celui qu’App Gateway reçoit à App Service au lieu d’effectuer un remplacement de l’hôte.

Une fois cela fait, App Service effectue la redirection (le cas échéant) sur le même en-tête hôte d’origine qui pointe vers App Gateway et pas lui-même.

Pour cela, vous devez posséder un domaine personnalisé et suivre le processus indiqué ci-dessous.

- Enregistrez le domaine dans la liste de domaines personnalisés d’App Service. Pour ce faire, vous devez disposer d’un enregistrement CNAME dans votre domaine personnalisé qui pointe vers le nom de domaine complet d’App Service. Pour plus d’informations, consultez [Mapper un nom DNS personnalisé existant à Azure App Service](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).

![appservice-2](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Une fois cette opération effectuée, votre instance App Service est prête à accepter le nom d’hôte « www.contoso.com ». Maintenant, modifiez votre entrée CNAME dans le DNS afin qu’elle pointe vers le nom de domaine complet d’Application Gateway. Par exemple, « appgw.eastus.cloudapp.azure.com ».

- Assurez-vous que votre domaine « www.contoso.com » est résolu avec le nom de domaine complet d’Application Gateway lorsque vous effectuez une requête DNS.

- Configurez votre sonde personnalisée pour désactiver « Choisir le nom d’hôte à partir des paramètres HTTP du serveur principal ». Cela peut être effectué à partir du portail en décochant la case à cocher dans les paramètres de sonde et dans PowerShell en n’utilisant pas -PickHostNameFromBackendHttpSettings dans la commande Set-AzApplicationGatewayProbeConfig. Dans le champ de nom d’hôte de la sonde, saisissez le nom de domaine complet d’App Service, « example.azurewebsites.net », car les requêtes de sonde envoyées à partir d’Application Gateway l’incluront dans l’en-tête hôte.

  > [!NOTE]
  > Lors de l’étape suivante, vérifiez que votre sonde personnalisée n’est pas associé aux paramètres HTTP de votre serveur principal, car « Choisir le nom d’hôte à partir de l’adresse du serveur principal » est encore activé dessus à ce stade.

- Configurez les paramètres HTTP d’Application Gateway pour désactiver « Choisir le nom d’hôte à partir de l’adresse du serveur principal ». Cela peut être effectué à partir du portail en décochant la case à cocher dans les paramètres de sonde et dans PowerShell en n’utilisant pas -PickHostNameFromBackendAddress switch dans la commande Set-AzApplicationGatewayBackendHttpSettings.

- Associez à nouveau la sonde personnalisée dans les paramètres HTTP du serveur principal, et vérifiez que l’intégrité du serveur principal est saine.

- Une fois cette opération effectuée, Application Gateway devrait maintenant transférer le même nom d’hôte « www.contoso.com » à App Service, et la redirection se produira sur le même nom d’hôte. Vous pouvez vérifier les exemples d’en-tête de requête et de réponse ci-dessous.

Pour implémenter les étapes mentionnées ci-dessus à l’aide de PowerShell pour une installation existante, suivez l’exemple de script PowerShell ci-dessous. Notez la façon nous n’avons pas utilisé les commutateurs -PickHostname dans la configuration de sonde et des paramètres HTTP.

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>Étapes suivantes

Si les étapes précédentes ne vous permettent pas de résoudre le problème, ouvrez un [ticket d’incident](https://azure.microsoft.com/support/options/).
