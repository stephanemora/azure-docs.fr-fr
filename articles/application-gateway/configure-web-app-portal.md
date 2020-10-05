---
title: Gérer le trafic vers les applications multilocataires à l’aide du portail
titleSuffix: Azure Application Gateway
description: Cet article fournit de l’aide sur la configuration des applications web Azure App Service en tant que membres du pool de back-ends sur une passerelle d’application nouvelle ou existante.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/09/2020
ms.author: absha
ms.openlocfilehash: dbaad0f6639d65d88da6847886d3aa3d39b93e82
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90563751"
---
# <a name="configure-app-service-with-application-gateway"></a>Configurer App Service avec Application Gateway

Étant donné que le service d’application est un service multi-locataire et non un déploiement dédié, il utilise l’en-tête de l’hôte dans la requête entrante pour résoudre la requête au point de terminaison de service d’application approprié. En règle générale, le nom DNS de l’application, qui à son tour est le nom DNS associé à la passerelle d’application qui expose le service d’application, est différent du nom de domaine du service d’application principal. Par conséquent, l’en-tête d’hôte de la requête d’origine reçu par la passerelle d’application n’est pas le même que le nom d’hôte du service principal. Pour cette raison, sauf si l’en-tête d’hôte de la requête provenant de la passerelle d’application vers le serveur principal est modifié pour porter le nom d’hôte du service principal, les back-ends multilocataires ne sont pas en mesure d’effectuer la résolution. de la requête vers le point de terminaison approprié.

Application Gateway fournit un commutateur appelé `Pick host name from backend address` qui remplace l'en-tête de l'hôte dans la requête par le nom d'hôte du back-end lorsque la requête est acheminée d'Application Gateway vers le back-end. Cette fonctionnalité permet la prise en charge des back-ends multilocataires tels que Azure App Service et la gestion des API. 

Dans cet article, vous apprendrez comment :

- Créer un pool de back-ends et y ajouter un service d’application
- Créer des paramètres HTTP et une sonde personnalisée en activant les commutateurs de choix de nom d’hôte

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
   
   > [!NOTE]
   > Le menu déroulant ne remplira que les services applicatifs figurant dans le même abonnement qu’Application Gateway. Si vous voulez utiliser un service d'application d’un autre abonnement que celui où se trouve Application Gateway, au lieu de choisir **App Services** dans le menu déroulant **Cibles**, choisissez l’option **Adresse IP ou nom d'hôte**, puis entrez le nom d'hôte (example. azurewebsites.net) d’App Service.

## <a name="create-http-settings-for-app-service"></a>Créer les paramètres HTTP du service d’application

1. Sous **Paramètres HTTP**, cliquez sur **Ajouter** pour créer un paramètre HTTP.

2. Entrez un nom pour le paramètre HTTP, puis activez ou désactivez l’affinité basée sur les cookies selon vos besoins.

3. Choisissez le protocole HTTP ou HTTPS selon le cas d’usage. 

   > [!NOTE]
   > Si vous sélectionnez HTTPS, vous n’avez pas besoin de charger un certificat d’authentification ou un certificat racine approuvé pour autoriser le back-end de l’application puisque l’application est un service Azure approuvé.

4. Cochez la case **Utiliser pour App Service**. Notez que les commutateurs `Create a probe with pick host name from backend address` et `Pick host name from backend address` seront automatiquement activés.`Pick host name from backend address` remplacera l'en-tête de l'hôte dans la requête par le nom d'hôte du back-end lorsque la requête est acheminée d'Application Gateway vers le back-end.  

   `Create a probe with pick host name from backend address` créera automatiquement une sonde d’intégrité et l'associera à ce paramètre HTTP. Vous n'avez pas besoin de créer d'autre sonde d’intégrité pour ce paramètre HTTP. Vous pouvez vérifier qu'une nouvelle sonde portant le nom <HTTP Setting name><Unique GUID> a été ajoutée dans la liste des sondes d’intégrité et qu'elle possède déjà le commutateur `Pick host name from backend http settings enabled`.

   Si vous avez déjà un ou plusieurs paramètres HTTP utilisés pour App Service et que ces paramètres HTTP partagent le même protocole que celui que vous créez, alors au lieu du commutateur `Create a probe with pick host name from backend address`, vous obtiendrez un menu déroulant pour sélectionner une des sondes personnalisées. Comme il existe déjà un paramètre HTTP avec App Service, il y aurait donc aussi une sonde d’intégrité avec le commutateur `Pick host name from backend http settings enabled`. Choisissez cette sonde personnalisée dans le menu déroulant.

5. Cliquez sur **OK** pour créer le paramètre HTTP.

   ![Capture d’écran qui montre le volet Ajouter un paramètre HTTP avec Utiliser pour App Service et OK sélectionnés.](./media/configure-web-app-portal/http-setting1.png)

   ![Capture d’écran qui montre une sonde d’intégrité avec Choisir un nom d’hôte à partir des paramètres HTTP du backend sélectionné.](./media/configure-web-app-portal/http-setting2.png)



## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Créer une règle pour lier l’écouteur, le pool de back-ends et le paramètre HTTP

1. Sous **Règles**, cliquez sur **De base** pour créer une règle De base.

2. Indiquez un nom approprié, puis sélectionnez l’écouteur qui doit accepter les requêtes entrantes pour le service d’application.

3. Dans la liste déroulante **Pool de back-ends**, choisissez le pool que vous avez créé.

4. Dans la liste déroulante **Paramètre HTTP**, choisissez le paramètre HTTP que vous avez créé.

5. Cliquez sur **OK** pour enregistrer cette règle.

   ![Capture d’écran qui montre le volet Ajouter une règle de base avec écouteur, pool backend et paramètre HTTP mis en évidence.](./media/configure-web-app-portal/rule.png)

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>Configuration supplémentaire en cas de redirection vers le chemin relatif d’App Service

Quand App Service envoie une réponse de redirection au client pour le rediriger vers son chemin relatif (par exemple, une redirection de contoso.azurewebsites.net/path1 vers contoso.azurewebsites.net/path2), il utilise le même nom d'hôte dans l'en-tête d’emplacement de sa réponse que celui de la requête reçue par la passerelle d’application. Par conséquent, le client adresse directement la requête à contoso.azurewebsites.net/path2 au lieu de passer par la passerelle d’application (contoso.com/path2). Or, il n’est pas souhaitable de contourner la passerelle d’application.

Si dans votre cas d'utilisation, il y a des scénarios où App Service devra envoyer une réponse de redirection au client, exécutez les étapes [supplémentaires pour réécrire l'en-tête d’emplacement](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration).

## <a name="restrict-access"></a>Restriction de l’accès

Les applications web déployées dans ces exemples utilisent des adresses IP publiques qui sont accessibles directement à partir d’Internet. La résolution des problèmes s’en trouve facilitée quand vous découvrez une nouvelle fonctionnalité et essayez de nouvelles choses. Mais si vous prévoyez de déployer une fonctionnalité sur l’environnement de production, l’ajout de restrictions peut vous sembler approprié.

Une façon de restreindre l’accès à vos applications web consiste à utiliser des [restrictions d’adresse IP statique avec Azure App Service](../app-service/app-service-ip-restrictions.md). Par exemple, vous pouvez restreindre l’accès à l’application web au trafic transitant par la passerelle d’application. Utilisez la fonctionnalité de restriction IP avec App Service pour répertorier l’adresse IP virtuelle de la passerelle d’application comme étant la seule adresse bénéficiant d’un accès.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur App Service et sur la prise en charge multilocataire avec Application Gateway, consultez [Prise en charge de service multilocataire avec Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).
