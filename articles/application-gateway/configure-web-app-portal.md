---
title: Gérer le trafic vers les applications mutualisées tels que App service web apps avec Azure Application Gateway - portail
description: Cet article fournit des conseils sur la configuration des applications Azure App service web en tant que membres dans le pool principal sur une passerelle d’application nouvelle ou existante.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/11/2019
ms.author: absha
ms.openlocfilehash: 4dae04c14f9132c54dcc0575ccb2841a4742a626
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176206"
---
# <a name="configure-app-service-with-application-gateway"></a>Configurer App Service avec Application Gateway

Application gateway vous permet d’avoir une application de service Azure application Web ou d’autres services de l’architecture mutualisées comme un membre du pool back-end. 

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
>
> - Créer un pool principal et lui ajouter un Service d’application
> - Créer des paramètres HTTP et la sonde personnalisée avec des commutateurs « Hostname choisir » activés

## <a name="prerequisites"></a>Conditions préalables

- Passerelle d’application : Si vous n’avez pas une passerelle d’application existante, consultez Comment [créer une passerelle d’application](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- Service d’application : Si vous n’avez pas un service d’application existant, consultez [documentation du service d’application](https://docs.microsoft.com/azure/app-service/).

## <a name="add-app-service-as-backend-pool"></a>Ajouter un service d’application en tant que pool principal

1. Dans le portail Azure, ouvrez la vue de la configuration de passerelle d’application pour vous.

2. Sous **pools principaux**, cliquez sur **ajouter** pour créer un nouveau pool principal.

3. Indiquez un nom approprié au pool principal. 

4. Sous **cibles**, cliquez sur la liste déroulante et choisissez **App Services** comme option.

5. Une liste déroulante ci-dessous immédiatement la **cibles** liste déroulante s’affiche, qui contient une liste de vos Services d’application. Dans cette liste déroulante, choisissez le Service d’application que vous souhaitez ajouter en tant que membre du pool principal et cliquez sur Ajouter.

   ![Principal de service d’application](./media/configure-web-app-portal/backendpool.png)

## <a name="create-http-settings-for-app-service"></a>Créer des paramètres HTTP pour App service

1. Sous **paramètres HTTP**, cliquez sur **ajouter** pour créer un nouveau paramètre HTTP.

2. Entrez un nom pour le paramètre HTTP et que vous pouvez activer ou désactiver l’affinité des cookies en fonction selon vos besoins.

3. Choisissez le protocole HTTP ou HTTPS, conformément à votre cas d’utilisation. 

4. Cochez la case **utilisation pour App Service** et Active le **créer une sonde avec le nom d’hôte de choix à partir de l’adresse du serveur principal** et **nom d’hôte de choix à partir de l’adresse du serveur principal** options. Cette option sera également créer une sonde automatiquement avec le commutateur est activé et l’associer à ce paramètre HTTP.

5. Cliquez sur **OK** pour créer le paramètre HTTP.

   ![HTTP-setting1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP-setting2](./media/configure-web-app-portal/http-setting2.png)

## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Créer la règle pour lier l’écouteur, le Pool principal et le paramètre HTTP

1. Sous **règles**, cliquez sur **base** pour créer une nouvelle règle de base.

2. Indiquez un nom approprié et sélectionnez le port d’écoute qui acceptera les demandes entrantes pour le service d’application.

3. Dans le **pool principal** liste déroulante, choisissez le serveur principal du pool que vous avez créé ci-dessus.

4. Dans le **paramètre HTTP** liste déroulante, choisissez la configuration que vous avez créé ci-dessus de HTTP.

5. Cliquez sur **OK** pour enregistrer cette règle.

   ![Règle](./media/configure-web-app-portal/rule.png)

## <a name="restrict-access"></a>Restriction de l’accès

Les applications web déployées dans ces exemples utilisent des adresses IP publiques qui sont accessibles directement à partir d’Internet. La résolution des problèmes s’en trouve facilitée quand vous découvrez une nouvelle fonctionnalité et essayez de nouvelles choses. Mais si vous prévoyez de déployer une fonctionnalité sur l’environnement de production, l’ajout de restrictions peut vous sembler approprié.

Une façon de restreindre l’accès à vos applications web consiste à utiliser des [restrictions d’adresse IP statique avec Azure App Service](../app-service/app-service-ip-restrictions.md). Par exemple, vous pouvez restreindre l’accès à l’application web au trafic transitant par la passerelle d’application. Utilisez la fonctionnalité de restriction IP avec App Service pour répertorier l’adresse IP virtuelle de la passerelle d’application comme étant la seule adresse bénéficiant d’un accès.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le service d’application et d’autres prises en charge de l’architecture mutualisée avec application gateway, consultez [prise en charge de service mutualisé avec application gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

Dans le cas où la réponse à partir de votre service d’application redirige vers l’URL du service de l’application, consultez Comment [résoudre les problèmes de la redirection vers le problème d’URL du service de l’application](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).
