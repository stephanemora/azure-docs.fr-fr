---
title: Résoudre les problèmes d’Azure Application Gateway avec App Service – Redirection vers l’URL d’App Service
description: Cet article fournit des informations sur la façon de résoudre les problèmes de redirection lorsque vous utilisez Azure Application Gateway avec Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 07/19/2019
ms.author: absha
ms.openlocfilehash: 7baadfb549b19bb12757c82d723578202b5cf8ad
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347880"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Résoudre les problèmes d’App Service dans Application Gateway

Découvrez comment diagnostiquer et résoudre les problèmes lorsque serveur d’applications est utilisé comme cible de back-end avec Application Gateway

## <a name="overview"></a>Vue d'ensemble

Dans cet article, vous allez apprendre à résoudre les problèmes suivants :

> [!div class="checklist"]
> * L’URL d’App Service est exposée dans le navigateur en cas de redirection
> * Le domaine du cookie ARRAffinity d’App Service est défini sur nom d’hôte d’App Service (example.azurewebsites.net) au lieu de l’hôte d’origine

Quand une application back-end envoie une réponse de redirection, vous pouvez souhaiter rediriger le client vers une URL différente de celle spécifiée par l’application back-end. Par exemple, cela peut être le cas quand un service d’application est hébergé derrière une passerelle d’application et exige que le client effectue une redirection vers son chemin relatif. (Par exemple, une redirection de contoso.azurewebsites.net/path1 vers contoso.azurewebsites.net/path2.) Quand le service d’application envoie une réponse de redirection, il utilise le nom d’hôte qui figure dans l’en-tête d’emplacement de sa réponse, qui est identique à celui situé dans la requête qu’il reçoit de la passerelle d’application. Par conséquent, le client adresse directement la requête à contoso.azurewebsites.net/path2 au lieu de passer par la passerelle d’application (contoso.com/path2). Or, il n’est pas souhaitable de contourner la passerelle d’application.

Cette erreur peut se produire pour les raisons suivantes :

- Vous avez configuré la redirection sur votre App Service. La redirection peut être aussi simple que l’ajout d’une barre oblique à la demande.
- L’authentification Azure AD provoque la redirection.

De même, lorsque vous utilisez App Services derrière Application Gateway, le nom de domaine associé à Application Gateway (example.com) est différent du nom de domaine d’App Service (par exemple, example.azurewebsites.net). Vous pourrez constater que la valeur de domaine pour le cookie ARRAffinity défini par App Service contiendra le nom de domaine « example.azurewebsites.net », ce qui n’est pas souhaitable. Le nom d’hôte d’origine (example.com) doit être la valeur du nom de domaine dans le cookie.

## <a name="sample-configuration"></a>Exemple de configuration

- Écouteur HTTP : Base ou de multi-sites
- Pool d’adresses principales : App Service
- Paramètres HTTP : « Choisir le nom d’hôte à partir de l’adresse du serveur principal » activé
- Sonde : « Choisir le nom d’hôte à partir des paramètres HTTP » activé

## <a name="cause"></a>Cause :

App Service étant un service multilocataire, il utilise l’en-tête d’hôte de la requête pour router celle-ci vers le point de terminaison approprié. Cependant, le nom de domaine par défaut des services d’applications, *.azurewebsites.net (par exemple, contoso.azurewebsites.net), est différent du nom de domaine de la passerelle d’application (par exemple, contoso.com). Étant donné que la requête d’origine du client a le nom de domaine de la passerelle d’application (contoso.com) comme nom d’hôte, vous devez configurer la passerelle d’application pour faire du nom d’hôte dans la requête d’origine le nom d’hôte des services d’applications lorsque la requête est acheminée vers le back-end du service d’applications.  Pour cela, utilisez l’option « Choisir le nom d’hôte à partir de l’adresse du serveur principal » dans la configuration des paramètres HTTP d’Application Gateway et l’option « Choisir le nom d’hôte à partir des paramètres du serveur principal HTTP » dans la configuration de la sonde d’intégrité.



![appservice-1](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Pour cette raison, lorsqu’App Service effectue une redirection, il utilise le nom d’hôte écrasé « contoso.azurewebsites.net » dans l’en-tête Location, au lieu du nom d’hôte d’origine « contoso.com », sauf en cas de configuration différente. Vous pouvez vérifier les exemples d’en-tête de requête et de réponse ci-dessous.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
Dans l’exemple ci-dessus, vous pouvez remarquer que l’en-tête de réponse a un code d’état 301 pour la redirection et que l’en-tête Location a le nom d’hôte d’App Service au lieu du nom d’hôte d’origine « www.contoso.com ».

## <a name="solution-rewrite-the-location-header"></a>Solution : Réécrire l’en-tête d’emplacement

Vous devez définir le nom d’hôte de l’en-tête d’emplacement sur le nom de domaine de la passerelle d’application. Pour cela, créez une [règle de réécriture](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) avec une condition qui évalue si l’en-tête d’emplacement dans la réponse contient azurewebsites.net et exécute une action de réécriture de l’en-tête d’emplacement avec le nom d’hôte de la passerelle d’application.  Consultez les instructions de [réécriture de l'en-tête d’emplacement](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url).

> [!NOTE]
> La prise en charge de la réécriture d’en-tête HTTP n’est disponible que pour [Standard_V2 et WAF_v2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) dans Application Gateway. Si vous utilisez la référence SKU V1, nous vous recommandons vivement de [migrer de V1 vers V2](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2) afin de pouvoir utiliser la réécriture et d’autres [fonctionnalités avancées](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) disponibles avec la référence SKU V2.

## <a name="alternate-solution-use-app-services-custom-domain-instead-of-default-domain-name"></a>Autre solution : Utilisez le domaine personnalisé du service d’applications au lieu du nom de domaine par défaut

Si vous utilisez la référence SKU V1, vous ne pourrez pas réécrire l’en-tête d’emplacement, car cette fonctionnalité est disponible uniquement pour la référence SKU V2. Pour résoudre le problème de redirection, vous devrez donc transmettre le nom d’hôte reçu par Application Gateway à App Service au lieu d’écraser l’hôte.

Une fois c’est fait, App Service effectue la redirection (le cas échéant) sur le même en-tête hôte d’origine qui pointe vers App Gateway et pas lui-même.

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
