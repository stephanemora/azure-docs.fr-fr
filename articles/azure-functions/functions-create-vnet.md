---
title: Intégrer Azure Functions avec un réseau virtuel Azure
description: Un didacticiel pas à pas qui vous montre comment connecter une fonction à un réseau virtuel Azure
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 96ab479d3373eb6e575a00898f7007a4df252e39
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125665"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>Intégrer une application de fonction à un réseau virtuel Azure

Ce didacticiel vous montre comment utiliser Azure Functions pour se connecter aux ressources dans un réseau virtuel Azure.

Pour ce didacticiel, nous allons déployer un site WordPress sur une machine virtuelle dans un réseau virtuel qui n’est pas accessible à partir d’internet. Nous allons ensuite déployer une fonction avec un accès à internet et le réseau virtuel. Nous allons utiliser cette fonction pour accéder aux ressources à partir du site WordPress déployé à l’intérieur du réseau virtuel.

Pour plus d’informations sur le fonctionnement du système, la résolution des problèmes et la configuration avancée, consultez [intégrer votre application à un réseau virtuel Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet). Azure functions dans le plan Premium ont les mêmes fonctionnalités d’hébergement en tant qu’applications web, donc toutes les fonctionnalités et limitations de cet article s’appliquent aux fonctions.

## <a name="topology"></a>Topologie

 ![Interface utilisateur pour l’intégration de réseau virtuel][1]

## <a name="create-a-vm-inside-a-virtual-network"></a>Créer une machine virtuelle à l’intérieur d’un réseau virtuel

Pour commencer, nous allons créer une machine virtuelle préconfigurée qui s’exécute de WordPress à l’intérieur d’un réseau virtuel. 

Nous avons choisi WordPress sur une machine virtuelle, car il est une des ressources moins coûteuses qui peuvent être déployés à l’intérieur d’un réseau virtuel. Notez que ce scénario peut également fonctionner avec n’importe quelle ressource dans un réseau virtuel, telles que les API REST, les environnements App Service et les autres services Azure.

1. Accédez au portail Azure.
2. Ajoutez une nouvelle ressource en ouvrant le **créer une ressource** panneau.
3. Recherchez «[WordPress LEMP7 Max Performance sur CentOS](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)» et ouvrir son panneau de création. 
4. Sur le **notions de base** , configurez la machine virtuelle avec les informations suivantes :
    1. Créez un groupe de ressources pour cette machine virtuelle afin de nettoyer les ressources plus facilement à la fin du tutoriel. Ici, nous utilisons « Fonction-réseau virtuel-Tutorial » comme exemple.
    1. Attribuez un nom unique à la machine virtuelle. Nous utilisons « Réseau virtuel-Wordpress » comme exemple.
    1. Sélectionnez la région la plus proche pour vous.
    1. Sélectionnez la taille en tant que B1s (1 processeur virtuel, 1 Go de mémoire).
    1. Pour le compte d’administrateur, choisissez l’authentification par mot de passe et entrez un nom d’utilisateur unique et un mot de passe. Pour ce didacticiel, vous ne devez vous connecter à la machine virtuelle, sauf si vous avez besoin résoudre les problèmes.
    
        ![Onglet de base pour la création d’une machine virtuelle](./media/functions-create-vnet/create-vm-1.png)

1. Déplacer vers le **mise en réseau** onglet et entrez les informations suivantes :
    1.  Créer un réseau virtuel.
    1.  Entrez une plage d’adresses privées et un sous-réseau au sein de cette plage d’adresses. La taille du sous-réseau détermine le nombre de machines virtuelles que vous pouvez utiliser dans le plan App Service. Si l’adressage IP et au sous-réseau est nouveaux pour vous, il existe un [document qui couvre les notions de base](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Adressage IP et sous-réseaux sont importantes dans ce scénario, nous vous recommandons de lire quelques articles et de regarder des vidéos en ligne jusqu'à ce qu’il est judicieux. 
    
        Pour cet exemple, nous allons qui optent pour utiliser le réseau de 10.10.0.0/16 avec un sous-réseau de 10.10.1.0/24. Nous allons Surprovisionnement et à l’aide d’un /16 sous-réseau, car il est facile de calculer les sous-réseaux sont disponibles dans le réseau 10.10.0.0/16.
        
        <img src="./media/functions-create-vnet/create-vm-2.png" width="700">

1. Sur le **mise en réseau** onglet, définissez l’adresse IP publique **aucun**. Cette étape déploiera la machine virtuelle avec un accès au réseau virtuel uniquement.
       
    <img src="./media/functions-create-vnet/create-vm-2-1.png" width="700">

7. Créez la machine virtuelle. Le processus prendra environ 5 minutes.
8. Une fois la machine virtuelle est créée, accédez à son **mise en réseau** onglet et notez l’adresse IP privée pour une utilisation ultérieure. La machine virtuelle ne doit pas avoir d’adresse IP publique.

    ![14]

Vous disposez maintenant d’un site WordPress déployé entièrement au sein de votre réseau virtuel. Ce site n’est pas accessible par Internet.

## <a name="create-a-function-app-in-a-premium-plan"></a>Créer une function app dans un plan Premium

L’étape suivante consiste à créer une function app dans un plan Premium. Un plan Premium offre de mise à l’échelle sans serveur avec tous les avantages d’un plan App Service dédié. Applications de fonction créées via le plan de consommation ne prennent pas en charge l’intégration de réseau virtuel.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-your-virtual-network"></a>Connecter votre application de fonction à votre réseau virtuel

Avec un site WordPress hébergeant les fichiers à partir de votre réseau virtuel, vous pouvez désormais connecter l’application de fonction au réseau virtuel.

1.  Dans le portail pour l’application de fonction à partir de l’étape précédente, sélectionnez **fonctionnalités de la plateforme**. Puis sélectionnez **mise en réseau**.

    <img src="./media/functions-create-vnet/networking-0.png" width="850">

1.  Sélectionnez **cliquez ici pour configurer** sous **intégration au réseau virtuel**.

    ![État de configuration d’une fonctionnalité de réseau](./media/functions-create-vnet/Networking-1.png)

1. Dans la page d’intégration de réseau virtuel, sélectionnez **ajouter réseau virtuel (version préliminaire)**.

    <img src="./media/functions-create-vnet/networking-2.png" width="600"> 
    
1.  Créer un sous-réseau pour votre fonction et le plan App Service à utiliser. Notez que la taille du sous-réseau limitera le nombre total de machines virtuelles que vous pouvez ajouter à votre plan App Service. Votre réseau virtuel acheminera automatiquement le trafic entre les sous-réseaux de votre réseau virtuel, donc il n’a pas d’importance que votre fonction se trouve dans un sous-réseau différent de celui de votre machine virtuelle. 
    
    <img src="./media/functions-create-vnet/networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-virtual-network"></a>Créer une fonction qui accède à une ressource dans votre réseau virtuel

L’application de fonction peut accéder maintenant le réseau virtuel avec notre site WordPress. Par conséquent, nous allons utiliser la fonction pour accéder à ce fichier et les envoie à l’utilisateur. Pour cet exemple, nous allons utiliser un site WordPress en tant que l’API et un proxy en tant que la fonction appelante, car elles correspondent à la fois facile à configurer et à visualiser. 

Vous pouvez tout aussi facilement utiliser toute autre API déployé au sein d’un réseau virtuel. Vous pouvez également utiliser une autre fonction avec le code qui effectue des appels d’API à l’API déployée au sein de votre réseau virtuel. Une instance de SQL Server déployée au sein de votre réseau virtuel est un parfait exemple.

1. Dans le portail, ouvrez l’application de fonction à partir de l’étape précédente.
1. Créer un proxy en sélectionnant **proxys** > **+**.

    <img src="./media/functions-create-vnet/new-proxy.png" width="250">

1. Configurer le nom de proxy et le routage. Cet exemple utilise « / planter » comme un itinéraire.
1. Remplir dans adresse IP de votre site WordPress précédemment et définissez **URL principal** à `http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg`
    
    <img src="./media/functions-create-vnet/create-proxy.png" width="900">

Maintenant, si vous essayez d’accéder à votre URL principal directement en le collant dans un nouvel onglet de navigateur, la page doit expirer. Il s’agit, car votre site WordPress est connecté à uniquement à votre réseau virtuel et non sur internet. Si vous collez votre URL de proxy dans le navigateur, vous devez voir une image d’usine (extraite à partir de votre site WordPress) à l’intérieur de votre réseau virtuel. 

Votre application de fonction est connectée à internet et votre réseau virtuel. Le proxy reçoit une requête via l’Internet public, puis agit comme un proxy HTTP simple pour transférer cette requête dans le réseau virtuel. Le proxy vous relaie ensuite la réponse via l’Internet public. 

<img src="./media/functions-create-vnet/plant.png" width="900">

## <a name="next-steps"></a>Étapes suivantes

Fonctions exécutées dans un plan Premium partagent la même infrastructure App Service sous-jacent en tant qu’applications web sur les plans de PremiumV2. Toute la documentation pour les applications web s’applique à vos fonctions de plan Premium.

* [En savoir plus sur les options de mise en réseau dans les fonctions](./functions-networking-options.md)
* [Lire les Forum aux questions sur la mise en réseau de fonctions](./functions-networking-faq.md)
* [En savoir plus sur les réseaux virtuels dans Azure](../virtual-network/virtual-networks-overview.md)
* [Activer plusieurs fonctionnalités mise en réseau et le contrôle avec les environnements App Service](../app-service/environment/intro.md)
* [Se connecter à des ressources locales sans modifications de pare-feu à l’aide de connexions hybrides](../app-service/app-service-hybrid-connections.md)
* [En savoir plus sur Functions Proxies](./functions-proxies.md)

<!--Image references -->
[1]: ./media/functions-create-vnet/topology.png
[2]: ./media/functions-create-vnet/create-function-app.png
[3]: ./media/functions-create-vnet/create-app-service-plan.png
[4]: ./media/functions-create-vnet/configure-vnet.png
[5]: ./media/functions-create-vnet/create-vm-1.png
[6]: ./media/functions-create-vnet/create-vm-2.png
[7]: ./media/functions-create-vnet/create-vm-2-1.png
[8]: ./media/functions-create-vnet/networking-1.png
[9]: ./media/functions-create-vnet/networking-2.png
[10]: ./media/functions-create-vnet/networking-3.png
[11]: ./media/functions-create-vnet/new-proxy.png
[12]: ./media/functions-create-vnet/create-proxy.png
[14]: ./media/functions-create-vnet/vm-networking.png
