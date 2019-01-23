---
title: Intégrer Azure Functions à un réseau virtuel Azure
description: Tutoriel pas à pas qui vous montre comment connecter une fonction à un réseau virtuel Azure
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 12/03/2018
ms.author: alkarche
ms.openlocfilehash: 1140c23a0aa5344119c35434316ec73cc3918f90
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198369"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>Intégrer une application de fonction à un réseau virtuel Azure
Ce tutoriel pas à pas vous montre comment utiliser Azure Functions pour vous connecter aux ressources d’un réseau virtuel Azure. 

Pour ce tutoriel, nous allons déployer un site WordPress sur une machine virtuelle dans un réseau virtuel privé, non accessible par Internet. Nous allons ensuite déployer une fonction avec accès à Internet et au réseau virtuel. Nous allons utiliser cette fonction pour accéder aux ressources à partir du site WordPress déployé à l’intérieur du réseau virtuel.

Pour plus d’informations sur le fonctionnement de ce système, la résolution des problèmes et la configuration avancée, consultez le document [Intégrer une application à un réseau virtuel Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet). Dans le plan dédié, Azure Functions offre les mêmes fonctionnalités d’hébergement que les applications web. De fait, toutes les fonctionnalités et limitations mentionnées dans ce document s’appliquent également aux fonctions.

## <a name="topology"></a>Topologie
 ![Interface utilisateur de l’intégration au réseau virtuel][1]

## <a name="creating-a-vm-inside-of-a-vnet"></a>Création d’une machine virtuelle à l’intérieur d’un réseau virtuel

Pour commencer, nous allons créer une machine virtuelle préconfigurée exécutant Wordpress sur un réseau virtuel. 

Nous avons choisi d’exécuter WordPress sur une machine virtuelle, car c’est l’une des ressources les moins coûteuses qui peuvent être déployées sur un réseau virtuel. Notez que ce scénario peut également fonctionner avec n’importe quelle ressource de réseau virtuel, y compris les API REST, les autres services Azure, les environnements App Service, etc.

1.  Accédez au portail Azure.
2.  Ajoutez une nouvelle ressource en ouvrant le panneau « Créer une ressource ».
3.  Recherchez « [Wordpress LEMP7 Max Performance on CentOS](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) » et ouvrez son panneau de création. 
4.  Dans le panneau de création, configurez la machine virtuelle avec les informations suivantes :
    1.  Créez un groupe de ressources pour cette machine virtuelle afin de nettoyer les ressources plus facilement à la fin du tutoriel. J’ai nommé mon groupe de ressources « Function-VNET-Tutorial ».
    1.  Attribuez un nom unique à la machine virtuelle. J’ai nommé la mienne « VNET-Wordpress ».
    1.  Sélectionnez la région la plus proche de vous.
    1.  Sélectionnez la taille B1 (1 processeur virtuel, 1 Go de mémoire).
    1.  Pour le compte d’administrateur, choisissez l’authentification par mot de passe et entrez un nom d’utilisateur unique et un mot de passe. Pour ce tutoriel, vous n’aurez pas besoin de vous connecter à la machine virtuelle, sauf si vous devez résoudre des problèmes.
    
        <img src="./media/functions-create-vnet/create-VM-1.png" width="700">

1. Passez à l’onglet Réseau, puis entrez les informations suivantes :
    1.  Créer un réseau virtuel
    1.  Entrez la plage d’adresses privées de votre choix ainsi que le sous-réseau de cette plage. La taille du sous-réseau détermine le nombre de machines virtuelles que vous pouvez utiliser dans le plan App Service. Si la configuration des adresses IP et des sous-réseaux est quelque chose de nouveau pour vous, vous pouvez consulter ce [document qui couvre les notions de base](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). La configuration des adresses IP et des sous-réseaux est importante dans ce scénario. Il est donc recommandé de lire quelques articles et de regarder quelques vidéos en ligne avant de commencer. 
        1. Pour cet exemple, j’ai opté pour le réseau 10.10.0.0/16 avec le sous-réseau 10.10.1.0/24. J’ai choisi de surprovisionner et d’utiliser un sous-réseau /16, car il est facile de calculer les sous-réseaux qui sont disponibles dans le réseau 10.10.0.0/16.
        
        <img src="./media/functions-create-vnet/create-VM-2.png" width="700">

1. De retour à l’onglet Réseau, définissez l’adresse IP publique sur « Aucune ». Cette action déploie la machine virtuelle avec un accès au réseau virtuel uniquement.
       
    <img src="./media/functions-create-vnet/create-VM-2.1.png" width="700">

7. Créez la machine virtuelle. Cette opération va prendre environ 5 minutes.
8. Une fois que la machine virtuelle est créée, accédez à l’onglet Réseau et notez l’adresse IP privée pour plus tard. La machine virtuelle ne doit pas avoir d’adresse IP publique.

    ![14]

Vous disposez maintenant d’un site WordPress déployé entièrement au sein de votre réseau virtuel. Ce site n’est pas accessible par Internet.

## <a name="create-a-dedicated-function-app"></a>Créer une application de fonction dédiée

L’étape suivante consiste à créer une application de fonction dans un plan App Service standard ou supérieur. Notez que les applications de fonction avec plan Consommation ne prennent pas en charge l’intégration au réseau virtuel.

1. Accédez au portail Azure.
2. Ajoutez une nouvelle ressource en ouvrant le panneau « Créer une ressource ».
3. Sélectionnez « Application de fonction serverless ».
4. Entrez toutes les informations habituelles dans le panneau de création, à une exception près :
    1. Sélectionnez un plan App Service standard ou supérieur.

        <img src="./media/functions-create-vnet/Create-App-Service-Plan.PNG" width="300">
    
1. Une fois terminé, mon panneau de création ressemble à ce qui suit.

    <img src="./media/functions-create-vnet/Create-Function-App.png" width="300">


## <a name="connect-your-function-app-to-your-vnet"></a>Connecter votre application de fonction à votre réseau virtuel

Maintenant que nous avons un site WordPress qui héberge de nombreux fichiers dans un réseau virtuel, nous devons connecter l’application de fonction à ce réseau virtuel.

1.  Dans le portail de l’application de fonction de l’étape précédente, sélectionnez **Fonctionnalités de la plateforme**, puis **Réseau**.
1.  Sous Intégration de réseau virtuel, sélectionnez **Cliquez ici pour configurer**.

    <img src="./media/functions-create-vnet/Networking-1.png" width="450">

1. Dans la page Intégration de réseau virtuel, sélectionnez **Ajouter un réseau virtuel (préversion)**.

    <img src="./media/functions-create-vnet/Networking-2.png" width="600"> 
    
1.  Créez un sous-réseau pour la fonction et le plan App Service à utiliser. Notez que la taille du sous-réseau limite le nombre total de machines virtuelles que vous pouvez ajouter à votre plan App Service. Votre réseau virtuel route automatiquement le trafic entre les sous-réseaux de votre réseau virtuel. Cela n’a donc pas d’importance si votre fonction se trouve sur un sous-réseau différent de celui de votre machine virtuelle. 
    
    <img src="./media/functions-create-vnet/Networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-vnet"></a>Créer une fonction qui accède à une ressource de votre réseau virtuel

L’application de fonction peut désormais accéder au réseau virtuel avec notre site WordPress. Nous allons donc utiliser la fonction pour accéder à ce fichier et l’afficher pour l’utilisateur. Pour cet exemple, nous utiliserons un site WordPress comme API, et un proxy de fonction comme fonction d’appel, car ils sont faciles à configurer et à visualiser. Vous pouvez tout aussi facilement utiliser une autre API déployée sur un réseau virtuel, ainsi qu’une autre fonction avec du code qui appelle l’API qui est déployée sur votre réseau virtuel. Un serveur SQL déployé sur votre réseau virtuel en est un parfait exemple.

1. Dans le portail, ouvrez l’application de fonction de l’étape précédente.
1. Créez un proxy en sélectionnant **Proxys** > **+**

    <img src="./media/functions-create-vnet/New-Proxy.png" width="250">

1. Configurez le nom et la route du proxy. J’ai choisi la route /plant.
1. Indiquez l’adresse IP de votre site WordPress et définissez l’URL back-end sur `http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg`.
    
    <img src="./media/functions-create-vnet/Create-Proxy.png" width="900">

Maintenant, si vous tentez d’accéder directement à votre URL back-end en la collant dans un nouvel onglet de navigateur, la page doit expirer. Ceci est normal, car votre site WordPress est connecté uniquement à votre réseau virtuel et pas à Internet. Si vous collez votre URL de proxy dans le navigateur, vous devez voir une jolie image de plante, tirée (pull) du site Wordpress de votre réseau virtuel. 

Votre application de fonction est connectée à la fois à Internet et à votre réseau virtuel. Le proxy reçoit une requête via l’Internet public, puis agit comme un proxy HTTP simple pour transférer cette requête dans le réseau virtuel. Le proxy vous relaie ensuite la réponse via l’Internet public. 

<img src="./media/functions-create-vnet/Plant.png" width="900">

## <a name="next-steps"></a>Étapes suivantes

Les fonctions Azure qui sont exécutées dans le cadre des plans App Service sont exécutées sur le même service que les applications web. Par conséquent, toute la documentation relative aux applications web s'applique également aux fonctions dédiées.

1. [En savoir plus sur l’intégration au réseau virtuel avec App Service/Functions](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
1. [En savoir plus sur les réseaux virtuels dans Azure](https://azure.microsoft.com/documentation/articles/virtual-networks-overview/)
1. [Activer les fonctionnalités et le contrôle de réseau avec Azure App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro)
1. [Se connecter à des ressources locales sans modifier le pare-feu à l’aide des connexions hybrides](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)
1. [En savoir plus sur Functions Proxies](https://review.docs.microsoft.com/azure/azure-functions/functions-proxies)

<!--Image references-->
[1]: ./media/functions-create-vnet/topology.png
[2]: ./media/functions-create-vnet/Create-Function-App.png
[3]: ./media/functions-create-vnet/Create-App-Service-Plan.PNG
[4]: ./media/functions-create-vnet/configure-VNET.png
[5]: ./media/functions-create-vnet/create-VM-1.png
[6]: ./media/functions-create-vnet/create-VM-2.png
[7]: ./media/functions-create-vnet/create-VM-2.1.png
[8]: ./media/functions-create-vnet/Networking-1.png
[9]: ./media/functions-create-vnet/Networking-2.png
[10]: ./media/functions-create-vnet/Networking-3.png
[11]: ./media/functions-create-vnet/New-Proxy.png
[12]: ./media/functions-create-vnet/Create-Proxy.png
[14]: ./media/functions-create-vnet/VM-Networking.png
