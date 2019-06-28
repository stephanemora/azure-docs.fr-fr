---
title: Gérer le trafic vers les applications multilocataires comme les applications web App Service avec Azure Application Gateway - Portail
description: Cet article fournit de l’aide sur la configuration des applications web Azure App Service en tant que membres du pool de back-ends sur une passerelle d’application nouvelle ou existante.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/11/2019
ms.author: absha
ms.openlocfilehash: 4dae04c14f9132c54dcc0575ccb2841a4742a626
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60831187"
---
# <a name="configure-app-service-with-application-gateway"></a>Configurer App Service avec Application Gateway

Application Gateway vous permet d’avoir une application web Azure App Service ou tout autre service multilocataire en tant que membre du pool de back-ends. 

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
>
> - Créer un pool de back-ends et y ajouter un service d’application
> - Créer des paramètres HTTP et une sonde personnalisée en activant les commutateurs de choix de nom d’hôte

## <a name="prerequisites"></a>Prérequis

- Passerelle d’application : Si vous n’avez pas de passerelle d’application, consultez les informations permettant de [créer une passerelle d’application](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- Service d’application : Si vous n’avez pas encore de service d’application, consultez la [documentation relative à App Service](https://docs.microsoft.com/azure/app-service/).

## <a name="add-app-service-as-backend-pool"></a>Ajouter un service d’application comme pool de back-ends

1. Dans le portail Azure, ouvrez la vue de configuration de votre passerelle d’application.

2. Sous **Pools principaux**, cliquez sur **Ajouter** pour créer un pool de back-ends.

3. Indiquez un nom approprié pour le pool de back-ends. 

4. Sous **Cibles**, cliquez sur la liste déroulante, puis choisissez l’option **App Services**.

5. Un menu déroulant situé immédiatement sous le menu déroulant **Cibles** s’affiche avec une liste de vos services d’application. Dans cette liste déroulante, choisissez le service d’application à ajouter comme membre du pool de back-ends, puis cliquez sur Ajouter.

   ![Service d’application comme back-end](./media/configure-web-app-portal/backendpool.png)

## <a name="create-http-settings-for-app-service"></a>Créer les paramètres HTTP du service d’application

1. Sous **Paramètres HTTP**, cliquez sur **Ajouter** pour créer un paramètre HTTP.

2. Entrez un nom pour le paramètre HTTP, puis activez ou désactivez l’affinité basée sur les cookies selon vos besoins.

3. Choisissez le protocole HTTP ou HTTPS selon le cas d’usage. 

4. Cochez la case **Utiliser pour App Service** pour activer les options **Créer une sonde avec un nom d’hôte choisi à partir d’une adresse backend** et **Choisir un nom d’hôte à partir d’une adresse backend**. Cette option permet également de créer automatiquement une sonde avec le commutateur activé, et de l’associer à ce paramètre HTTP.

5. Cliquez sur **OK** pour créer le paramètre HTTP.

   ![Paramètre-HTTP1](./media/configure-web-app-portal/http-setting1.png)

   ![Paramètre-HTTP2](./media/configure-web-app-portal/http-setting2.png)

## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Créer une règle pour lier l’écouteur, le pool de back-ends et le paramètre HTTP

1. Sous **Règles**, cliquez sur **De base** pour créer une règle De base.

2. Indiquez un nom approprié, puis sélectionnez l’écouteur qui doit accepter les requêtes entrantes pour le service d’application.

3. Dans la liste déroulante **Pool de back-ends**, choisissez le pool que vous avez créé.

4. Dans la liste déroulante **Paramètre HTTP**, choisissez le paramètre HTTP que vous avez créé.

5. Cliquez sur **OK** pour enregistrer cette règle.

   ![Règle](./media/configure-web-app-portal/rule.png)

## <a name="restrict-access"></a>Restriction de l’accès

Les applications web déployées dans ces exemples utilisent des adresses IP publiques qui sont accessibles directement à partir d’Internet. La résolution des problèmes s’en trouve facilitée quand vous découvrez une nouvelle fonctionnalité et essayez de nouvelles choses. Mais si vous prévoyez de déployer une fonctionnalité sur l’environnement de production, l’ajout de restrictions peut vous sembler approprié.

Une façon de restreindre l’accès à vos applications web consiste à utiliser des [restrictions d’adresse IP statique avec Azure App Service](../app-service/app-service-ip-restrictions.md). Par exemple, vous pouvez restreindre l’accès à l’application web au trafic transitant par la passerelle d’application. Utilisez la fonctionnalité de restriction IP avec App Service pour répertorier l’adresse IP virtuelle de la passerelle d’application comme étant la seule adresse bénéficiant d’un accès.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur App Service et sur la prise en charge multilocataire avec Application Gateway, consultez [Prise en charge de service multilocataire avec Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

Si la réponse de votre service d’application est redirigée vers l’URL du service d’application, consultez les informations relatives à la [résolution des problèmes de redirection vers l’URL du service d’application](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).
