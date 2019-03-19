---
title: 'Résolution des problèmes de passerelle Azure Application Gateway avec App Service : la Redirection vers l’URL du Service de l’application'
description: Cet article fournit des informations sur la façon de résoudre le problème de redirection Azure Application Gateway est utilisé avec Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: 359d75f10f95b0e41ccd9a869d49247355f0d5d0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58123179"
---
# <a name="troubleshoot-application-gateway-with-app-service--redirection-to-app-services-url"></a>Résoudre les problèmes d’Application Gateway avec App Service – Redirection vers l’URL d’App Service

 Découvrez comment diagnostiquer et résoudre les problèmes de redirection avec la passerelle d’Application où l’URL du Service de l’application est bien exposé.

## <a name="overview"></a>Présentation

Lorsque vous configurez un Service d’application dans le pool back-end de passerelle d’Application publiques et si vous avez une redirection configurée dans votre code d’Application, vous verrez peut-être lorsque vous accédez à la passerelle d’Application, vous serez redirigé par le navigateur directement à l’application URL du service.

Ce problème peut se produire en raison des raisons principales suivantes :

- Vous avez configurée sur votre Service d’application de la redirection. La redirection peut être aussi simple que l’ajout d’une barre oblique à la demande.
- Vous avez l’authentification Azure AD qui provoque la redirection.
- Vous avez activé le commutateur « Choisir hôte nom d’adresse du serveur principal » dans les paramètres HTTP de passerelle d’Application.
- Vous n’avez pas votre domaine personnalisé enregistré avec votre Service d’application.

## <a name="sample-configuration"></a>Exemple de configuration

- Écouteur HTTP : Base ou de plusieurs sites
- Pool d’adresses principales : App Service
- Paramètres HTTP : « Pick nom d’hôte à partir de l’adresse du serveur principal » activé
- Sonde : « Pick nom d’hôte à partir des paramètres HTTP » activé

## <a name="cause"></a>Cause :

Un Service d’application est uniquement accessible avec les noms d’hôte configuré dans les paramètres de domaine personnalisé, par défaut, il est « example.azurewebsites.net » et si vous souhaitez accéder à votre Service d’application à l’aide de la passerelle d’Application avec un nom d’hôte ne pas inscrit dans App Service ou avec La passerelle d’application nom de domaine complet, vous devez remplacer le nom d’hôte dans la demande d’origine au nom d’hôte du Service de l’application.

Pour ce faire, avec la passerelle d’Application, nous utilisons le commutateur « Choisir nom d’hôte à partir de back-end Address » dans les paramètres HTTP et de la sonde fonctionne, nous utilisons « Sélectionner des nom d’hôte à partir de paramètres Backend HTTP » dans la configuration de sonde.

![appservice-1](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Pour cette raison, lorsque le Service de l’application effectue une redirection, il utilise le nom d’hôte « example.azurewebsites.net » dans l’en-tête Location, au lieu du nom d’hôte d’origine sauf configuration différente. Vous pouvez vérifier les en-têtes exemple demande et de réponse ci-dessous.
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
Dans l’exemple ci-dessus, vous pouvez remarquer que l’en-tête de réponse a un code d’état de 301 pour la redirection et l’en-tête location a de nom d’hôte du Service de l’application au lieu du nom d’hôte d’origine « www.contoso.com ».

## <a name="solution"></a>Solution

Ce problème peut être résolu en évitant une redirection côté Application, toutefois, si cela n’est pas possible, que nous devons transmettre l’en-tête d’hôte qui reçoit de la passerelle d’Application sur App Service ainsi au lieu d’effectuer un remplacement de l’hôte.

Une fois que nous le faire, App Service effectue la redirection (le cas échéant) sur le même en-tête d’hôte d’origine qui pointe vers la passerelle d’Application et non pas son propre.

Pour ce faire, vous devez posséder un domaine personnalisé et suivez le processus indiqué ci-dessous.

- Enregistrez le domaine à la liste de domaine personnalisé d’App Service. Pour ce faire, vous devez disposer d’un enregistrement CNAME dans votre domaine personnalisé qui pointe vers le nom de domaine complet du Service de l’application. Pour plus d’informations, consultez [mapper un nom DNS personnalisé existant à Azure App Service](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).

![appservice-2](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Une fois cette opération effectuée, votre App Service est prêt à accepter le nom d’hôte « www.contoso.com ». Maintenant modifier votre entrée CNAME dans DNS afin qu’il pointe vers FQDN de la passerelle d’Application. Par exemple, « appgw.eastus.cloudapp.azure.com ».

- Assurez-vous que votre domaine « www.contoso.com » est résolu en nom de domaine complet de passerelle d’Application lorsque vous effectuez une requête DNS.

- Définissez votre sonde personnalisée pour désactiver les « Choisir des nom d’hôte à partir de paramètres serveur principal HTTP ». Cela peut être effectuée à partir du portail en décochant la case à cocher dans les paramètres de sonde et dans PowerShell en n’utilisant ne pas le PickHostNameFromBackendHttpSettings - basculer dans la commande Set-AzApplicationGatewayProbeConfig. Dans le champ nom d’hôte de la sonde, entrez votre nom de domaine complet du Service d’application « example.azurewebsites.net » comme les demandes de sonde envoyées à partir de la passerelle d’Application contiendra dans l’en-tête d’hôte.

  > [!NOTE]
  > Lors de l’étape suivante, vérifiez que votre sonde personnalisée est associé aux paramètres de votre serveur principal HTTP, car vos encore de paramètres HTTP du commutateur « Choisir nom d’hôte de serveur principal Address » activé à ce stade.

- Définir des paramètres HTTP de votre passerelle d’Application pour désactiver « Choisir nom d’hôte à partir de back-end Address ». Cela est possible à partir du portail en décochant la case à cocher et dans PowerShell en n’utilisant ne pas les PickHostNameFromBackendAddress - basculer dans la commande Set-AzApplicationGatewayBackendHttpSettings.

- Associer la sonde personnalisée vers les paramètres de serveur principal HTTP et vérifiez l’intégrité du serveur principal s’il est sain.

- Une fois cette opération est effectuée, Application Gateway doit transférer maintenant le même nom d’hôte « www.contoso.com » pour le Service d’application et la redirection se produira sur le même nom d’hôte. Vous pouvez vérifier les en-têtes exemple demande et de réponse ci-dessous.
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
