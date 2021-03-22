---
title: 'Démarrage rapide : Créer un profil Azure Front Door Standard/Premium – Portail Azure'
description: Ce démarrage rapide montre comment utiliser Azure Front Door Standard/Premium Service pour votre application web globale hautement disponible et très performante à l’aide du portail Azure.
services: frontdoor
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 18956948a32a79c1435bf4fc1554b09480c9010c
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100667"
---
# <a name="quickstart-create-an-azure-front-door-standardpremium-profile---azure-portal"></a>Démarrage rapide : Créer un profil Azure Front Door Standard/Premium – Portail Azure

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Consultez la [documentation Azure Front Door](../front-door-overview.md).

Dans ce démarrage rapide, vous allez apprendre à créer un profil Azure Front Door Standard/Premium à l’aide du portail Azure. Vous pouvez créer le profil Azure Front Door Standard/Premium via *Création rapide* avec des configurations de base ou via *Création personnalisée* avec des configurations plus avancées. Avec *Création personnalisée*, vous déployez deux applications web. Ensuite, vous créez le profil Azure Front Door Standard/Premium en utilisant les deux applications web comme origin. Vous vérifierez ensuite la connectivité à vos applications web à l’aide du nom d’hôte frontal d’Azure Front Door Standard/Premium.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-front-door-profile---quick-create"></a>Créer un profil Front Door – Création rapide

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans la page d’accueil ou le menu Azure, sélectionnez  **+ Créer une ressource**. Recherchez  *Front Door Standard/Premium (préversion)* . Sélectionnez ensuite **Créer**.

1. Sur la page **Comparer les offres**, sélectionnez **Création rapide**. Sélectionnez ensuite **Continuer pour créer une instance Front Door**.

   :::image type="content" source="../media/create-front-door-portal/front-door-quick-create.png" alt-text="Capture d’écran de la comparaison des offres.":::

1. Dans la page **Créer un profil Front Door**, entrez ou sélectionnez les paramètres suivants.

    :::image type="content" source="../media/create-front-door-portal/front-door-quick-create-2.png" alt-text="Capture d’écran de la page de création rapide de Front Door.":::    

    | Paramètres | Valeur |
    | --- | --- |
    | **Abonnement**  | Sélectionnez votre abonnement. |
    | **Groupe de ressources**  | Sélectionnez **Créer nouveau** et entrez *contoso-appservice* dans la zone de texte.|
    | **Nom** | Donnez un nom à votre profil. Cet exemple utilise **contoso-afd-quickcreate**. |
    | **Niveau** | Sélectionnez le niveau tarifaire Standard ou Premium. Le niveau tarifaire Standard est optimisé pour la livraison de contenu. Le niveau tarifaire Premium s’appuie sur le niveau tarifaire Standard et est axé sur la sécurité. Voir [Comparaison des niveaux](tier-comparison.md).  |
    | **Nom du point de terminaison** | Entrez un nom unique au monde pour votre point de terminaison. |
    | **Type d’origine** | Sélectionnez le type de ressource pour votre origin. Dans cet exemple, nous sélectionnons un service d’application comme origin pour laquelle Private Link est activé. |
    | **Nom d’hôte de l’origin** | Entrez le nom d’hôte de votre origin. |
    | **Activer Azure Private Link** | Si vous souhaitez disposer d’une connexion privée entre Azure Front Door et votre origin. Pour plus d’informations, reportez-vous à l’[aide relative à Private Link](concept-private-link.md) et à [Activer Private Link](how-to-enable-private-link.md).
    | **Mise en cache** | Cochez la case si vous souhaitez mettre en cache le contenu plus près des utilisateurs à l’échelle mondiale en utilisant les POP de périphérie d’Azure Front Door et le réseau Microsoft. |
    | **Stratégie WAF** | Sélectionnez **Créer nouveau** ou sélectionnez une stratégie WAF existante dans la liste déroulante si vous souhaitez activer cette fonctionnalité. |

    > [!NOTE]
    > Lors de la création d’un profil Azure Front Door Standard/Premium, vous devez sélectionner une origin dans le même abonnement que celui dans lequel l’instance de Front Door est créée.

1. Sélectionnez **Vérifier + créer** pour mettre en service votre profil Front Door.

   > [!NOTE]
   > Quelques minutes peuvent être nécessaires pour que les configurations soient propagées à tous les POP de périphérie.

1. Cliquez ensuite sur **Créer** pour déployer et mettre en service votre profil Front Door.

1. Si vous avez activé Private Link, accédez à votre origin (service d’application dans cet exemple). Sélectionnez **Mise en réseau** > **Configurer Private Link**. Sélectionnez ensuite la demande en attente dans Azure Front Door, puis cliquez sur Approuver. Après quelques secondes, votre application sera accessible par Azure Front Door de manière sécurisée.

## <a name="create-front-door-profile---custom-create"></a>Créer un profil Front Door – Création personnalisée

### <a name="create-a-web-app-with-two-instances-as-the-origin"></a>Créer une application web avec deux instances comme origin

Si vous avez déjà configuré une origin ou un groupe d’origins, passez à la section Créer une instance Front Door Standard/Premium (préversion) pour votre application.

Dans cet exemple, nous créons une application web avec deux instances qui s’exécutent dans des régions Azure différentes. Les deux instances de l’application web s’exécutent en mode *Actif/Actif*, donc l’une ou l’autre peut s’occuper du trafic. Cette configuration diffère d’une configuration *Active/Veille* où l’une sert de basculement.

Si vous n’avez pas encore d’application web, utilisez les étapes suivantes pour configurer un exemple d’application web.

1. Connectez-vous au portail Azure sur https://portal.azure.com.

1. En haut à gauche de l’écran, sélectionnez **Créer une ressource** > **Application web**.

1. Sous l’onglet **Informations de base** de la page **Application web**, entrez ou sélectionnez les informations suivantes.

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | **Abonnement**               | Sélectionnez votre abonnement. |
    | **Groupe de ressources**       | Sélectionnez **Créer**, puis entrez *FrontDoorQS_rg1* dans la zone de texte.|
    | **Nom**                   | Attribuez un **Nom** unique à votre application web. Cet exemple utilise *WebAppContoso-001*. |
    | **Publier** | Sélectionnez **Code**. |
    | **Pile d’exécution**         | Sélectionnez **.NET Core 2.1 (LTS)** . |
    | **Système d’exploitation**          | Sélectionnez **Windows**. |
    | **Région**           | Sélectionnez **USA Centre**. |
    | **Plan Windows** | Sélectionnez **Créer** et entrez *myAppServicePlanCentralUS* dans la zone de texte. |
    | **Référence et taille** | Sélectionnez **Standard S1 100 ACU au total, 1,75 Go de mémoire**. |

     :::image type="content" source="../media/create-front-door-portal/create-web-app.png" alt-text="Création rapide d’une instance Front Door au niveau tarifaire Premium dans le portail Azure":::

1. Sélectionnez **Vérifier + Créer**, vérifiez le récapitulatif, puis sélectionnez **Créer**. Le déploiement peut prendre plusieurs minutes.

Une fois le déploiement terminé, créez une seconde application web. Utilisez les mêmes paramètres que ci-dessus, à l’exception des paramètres suivants :

| Paramètre          | Valeur     |
| ---              | ---  |
| **Groupe de ressources**   | Sélectionnez **Créer nouveau** et entrez *FrontDoorQS_rg2*. |
| **Nom**             | Entrez un nom unique pour votre application web, dans cet exemple, *WebAppContoso-002*.  |
| **Région**           | Une autre région, dans cet exemple, *USA Centre Sud* |
| **Plan App Service** > **Plan Windows**         | Sélectionnez **Nouveau** et entrez *myAppServicePlanSouthCentralUS*, puis sélectionnez **OK**. |

### <a name="create-a-front-door-standardpremium-preview-for-your-application"></a>Créer une instance Front Door Standard/Premium (préversion) pour votre application

Configurez Azure Front Door Standard/Premium (préversion) pour diriger le trafic utilisateur en fonction de la latence la plus faible entre les serveurs des deux applications web. Sécurisez également votre instance Front Door à l’aide de Web Application Firewall. 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
 
1. Dans la page d’accueil ou le menu Azure, sélectionnez  **+ Créer une ressource**. Recherchez  *Front Door Standard/Premium (préversion)* . Sélectionnez ensuite **Créer**.

1. Sur la page **Comparer les offres**, sélectionnez **Création personnalisée**. Sélectionnez ensuite **Continuer pour créer une instance Front Door**.

1. Dans l’onglet **Informations de base** , entrez ou sélectionnez les informations suivantes, puis sélectionnez **Suivant : Secret**. 

    | Paramètre | Valeur |
    | --- | --- |
    | **Abonnement** | Sélectionnez votre abonnement. |
    | **Groupe de ressources** | Sélectionnez **Créer**, puis entrez *FrontDoorQS_rg0* dans la zone de texte. |
    | **Emplacement du groupe de ressources** | Sélectionnez **USA Est**. |
    | **Nom de profil** | Entrez un nom unique, dans cet abonnement, **Webapp-Contoso-AFD**. |
    | **Niveau** | Sélectionnez **Premium**. |
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-2.png" alt-text="Créer un profil Front Door":::

1. *Facultatif* : **Secrets**. Si vous envisagez d’utiliser des certificats gérés, cette étape est facultative. Si vous avez un coffre de clés dans Azure que vous prévoyez d’utiliser pour apporter votre propre certificat pour un domaine personnalisé, sélectionnez **Ajouter un certificat**. Vous pouvez également ajouter un certificat dans l’expérience de gestion après la création.

    > [!NOTE]
    > Vous devez disposer de l’autorisation appropriée pour ajouter le certificat à partir d’Azure Key Vault en tant qu’utilisateur. 

    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-secret.png" alt-text="Capture d’écran de l’ajout d’un secret dans la création personnalisée.":::

1. Sous l’onglet **Point de terminaison**, sélectionnez **Ajouter un point de terminaison** et donnez un nom unique au monde à votre point de terminaison. Vous pouvez créer plusieurs points de terminaison dans votre profil Azure Front Door Standard/Premium une fois l’expérience de création terminée. Cet exemple utilise *contoso-frontend*. Conservez les valeurs par défaut pour les paramètres Temps de réponse de l’origin (en secondes) et État. Sélectionnez **Ajouter** pour ajouter le point de terminaison.
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-endpoint.png" alt-text="Capture d’écran de la page Ajouter un point de terminaison.":::

1. Ensuite, ajoutez un groupe d’origins qui contient vos deux applications web. Sélectionnez **+ Ajouter** pour ouvrir la page **Ajouter un groupe d’origins**. Pour Nom, entrez *myOrignGroup*, puis sélectionnez **+ Ajouter une origin**.
 
     :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group.png" alt-text="Capture d’écran de la page Ajouter un groupe d’origins.":::

1. Dans la page **Ajouter une origin** , entrez ou sélectionnez les informations ci-dessous. Sélectionnez ensuite **Ajouter**.

    | Paramètre | Valeur |
    | --- | --- |
    | **Nom** | Entrez **webapp1** |
    | **Type d’origine** | Sélectionner **App services** |
    | **Nom d’hôte** | Sélectionnez `WebAppContoso-001.azurewebsites.net`. |
    | **En-tête de l’hôte d’origine** | Sélectionnez `WebAppContoso-001.azurewebsites.net`. |
    | **Autres champs** | Laissez tous les autres champs sur leurs valeurs par défaut |

    > [!NOTE]
    > Lorsque vous créez un profil Azure Front Door Standard/Premium, vous devez sélectionner une origin dans le même abonnement que celui dans lequel l’instance d’Azure Front Door Standard/Premium est créée.
   
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-1.png" alt-text="Capture d’écran de l’ajout d’origins supplémentaires.":::

1. Répétez l’étape 8 pour ajouter la deuxième origin webapp002. Sélectionnez `webappcontoso-002.azurewebsite.net` comme **Nom d’hôte de l’origin** et **En-tête de l’hôte de l’origin**.

1. Dans la page **Ajouter un groupe d’origins**, vous verrez deux origins ajoutées. Laissez tous les autres champs par défaut.
  
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group-2.png" alt-text="Capture d’écran de la page Ajouter un groupe d’origins.":::

1. Ensuite, ajoutez un itinéraire pour mapper votre point de terminaison frontal au groupe d’origins. Cet itinéraire transfère les demandes du point de terminaison à myOriginGroup. Sélectionnez **+ Ajouter** sur l’itinéraire pour configurer un itinéraire.  

1. Dans la page **Ajouter un itinéraire**, entrez ou sélectionnez les informations ci-dessous. Sélectionnez ensuite **Ajouter**.
  
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-route-without-caching.png" alt-text="Ajouter un itinéraire sans mise en cache":::

    | Paramètre | Valeur |
    | --- | --- |
    | **Nom** | Entrez **MyRoute**. |    
    | **Domaine** | Sélectionnez `contoso-frontend.z01.azurefd.net`. | 
    | **Nom d’hôte** | Sélectionnez `WebAppContoso-001.azurewebsites.net`. |
    | **Modèles à mettre en correspondance** | Laissez la valeur par défaut. |
    | **Protocoles acceptés** | Laissez la valeur par défaut. | 
    | **Rediriger** | Laissez la valeur par défaut pour **Rediriger tout le trafic pour utiliser le protocole HTTPS**. | 
    | **Groupe d’origins** | Sélectionnez **MyOriginGroup**. | 
    | **Chemin de l’origine** | Laissez la valeur par défaut. | 
    | **Protocole de transfert** | Sélectionnez **Mettre en correspondance la demande entrante**. | 
    | **Mise en cache** | Ne cochez pas la case dans ce démarrage rapide. Si vous souhaitez que votre contenu soit mis en cache en périphérie, cochez la case **Activer la mise en cache**. |
    | **Règles** | Laissez la valeur par défaut. Après avoir créé votre profil Front Door, vous pouvez créer des règles personnalisées et les appliquer à des itinéraires. |  
       
    >[!WARNING]
    > **Assurez-vous** qu’il existe un itinéraire pour chaque point de terminaison. L’absence d’un itinéraire peut entraîner l’échec d’un point de terminaison.

1. Ensuite, sélectionnez **+ Ajouter** sur Sécurité pour ajouter une stratégie WAF. Sélectionnez **Créer nouveau** et attribuez un nom unique à votre stratégie. Cochez la case **Ajouter une protection bot**. Sélectionnez le point de terminaison dans **Domaines**, puis sélectionnez **Ajouter**.

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-waf-policy-2.png" alt-text="Ajouter une stratégie WAF":::

1. Sélectionnez **Vérifier + créer**, puis **Créer**. Quelques minutes peuvent être nécessaires pour que les configurations soient propagées à tous les POP de périphérie. Vous disposez à présent de votre premier profil et point de terminaison Front Door.

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-review.png" alt-text="Vérifier la création personnalisée":::

## <a name="verify-azure-front-door"></a>Vérifier Azure Front Door

Une fois le profil Azure Front Door Standard/Premium créé, il faut quelques minutes pour que la configuration soit déployée dans le monde entier. Une fois l’opération terminée, vous pouvez accéder à l’hôte frontal que vous avez créé. Dans un navigateur, accédez à `contoso-frontend.z01.azurefd.net`. Votre demande est automatiquement acheminée vers le serveur le plus proche parmi les serveurs spécifiés dans le groupe d’origins.

Si vous avez créé ces applications dans ce guide de démarrage rapide, vous verrez une page d’informations.

Pour tester le basculement global instantané, nous allons utiliser les étapes suivantes :

1. Ouvrez un navigateur, comme décrit ci-dessus, puis accédez à l’adresse front-end : `contoso-frontend.azurefd.net`.

1. Dans le portail Azure, recherchez et sélectionnez *App Services*. Faites défiler la liste jusqu’à l’une de vos applications web, **WebAppContoso-001** dans cet exemple.

1. Sélectionnez votre application web, puis **Arrêter** et **Oui** pour vérifier.

1. Actualisez votre navigateur. Vous devriez voir la même page d’informations.

   >[!TIP]
   >Il y a un peu de retard pour ces actions. Vous devrez peut-être actualiser une nouvelle fois.

1. Recherchez l’autre application web, puis arrêtez-la également.

1. Actualisez votre navigateur. Cette fois, vous devriez voir un message d’erreur.

    :::image type="content" source="../media/create-front-door-portal/web-app-stopped-message.png" alt-text="Les deux instances de l’application web arrêtées":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Une fois que vous avez terminé, vous pouvez supprimer tous les éléments que vous avez créés. La suppression d’un groupe de ressources supprime également son contenu. Si vous n’envisagez pas d’utiliser cette porte d’entrée, vous devez supprimer les ressources pour éviter des frais inutiles.

1. Dans le portail Azure, sélectionnez et recherchez **Groupes de ressources** ou sélectionnez **Groupes de ressources** dans le menu du portail Azure.

1. Filtrez ou faites défiler la liste pour rechercher un groupe de ressources, par exemple **FrontDoorQS_rg0**.

1. Sélectionnez le groupe de ressources, puis **Supprimer le groupe de ressources**.

   >[!WARNING]
   >Cette action est irréversible.

1. Tapez le nom du groupe de ressources pour vérifier, puis sélectionnez **Supprimer**.

Répétez la procédure pour les deux autres groupes.

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour apprendre à ajouter un domaine personnalisé à votre porte d’entrée.
> [!div class="nextstepaction"]
> [Ajouter un domaine personnalisé](how-to-add-custom-domain.md)
