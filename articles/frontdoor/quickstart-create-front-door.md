---
title: 'Démarrage rapide : Créer un profil Porte d’entrée pour la haute disponibilité des applications'
description: Cet article de démarrage rapide décrit comment créer une porte d’entrée pour votre application web globale hautement disponible et très performante.
services: front-door
documentationcenter: ''
author: sharad4u
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2018
ms.author: sharadag
ms.openlocfilehash: 2b44c0cdbe2d955efe20a5f9473a29bc9f500a07
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521450"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Démarrage rapide : Créer une porte d’entrée pour une application web globale hautement disponible

Ce guide de démarrage rapide décrit comment créer un profil Front Door qui assure une haute disponibilité et de hautes performances pour votre application web globale. 

Le scénario décrit dans ce démarrage rapide inclut deux instances d’une application web en cours d’exécution dans différentes régions Azure. Nous créons une configuration Front Door basée sur des [backends de même poids et de même priorité](front-door-routing-methods.md) pour diriger le trafic utilisateur sur l’ensemble le plus proche de backends de site exécutant l’application. Front Door supervise en continu l’application web et fournit un basculement automatique vers le backend disponible suivant si le site le plus proche n’est pas disponible.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="sign-in-to-azure"></a>Connexion à Azure 
Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="prerequisites"></a>Prérequis
Pour suivre ce guide de démarrage rapide, vous devez avoir déployé deux instances d’une application web s’exécutant dans des régions Azure différentes (*USA Est* et *Europe Ouest*). Les instances d’application web s’exécutent toutes deux en mode actif/actif, autrement dit, l’une des deux peut accepter du trafic à tout moment, contrairement à une configuration actif/passif où l’une sert de basculement.

1. Dans l’angle supérieur gauche de l’écran, sélectionnez **Créer une ressource** > **Web** > **Application web**.
2. Dans **Application Web**, entrez ou sélectionnez les informations suivantes et entrez les paramètres par défaut lorsqu’aucun n’est spécifié :

     | Paramètre         | Valeur     |
     | ---              | ---  |
     | Resource group          | Sélectionnez **Nouveau**, puis tapez *myResourceGroupFD1* |
     | Nom           | Entrez un nom unique pour votre application web  |
     | Pile d’exécution          | Sélectionnez une pile de runtime pour votre application |
     |      Région  |   USA Ouest        |
     | Plan/lieu App Service         | Sélectionnez **Nouveau**.  Dans le plan App Service, entrez *myAppServicePlanEastUS*, puis sélectionnez **OK**.| 
     |SKU et taille  | Sélectionnez **Modifier la taille** Sélectionnez **Standard S1 100 ACU au total, 1,75 Go de mémoire** |
     
3. Sélectionnez **Vérifier + créer**.
4. Passez en revue les informations récapitulatives de l’application web. Sélectionnez **Create** (Créer).
5. Lorsque l’application web est déployée correctement, un site web par défaut est créé au bout de 5 minutes environ.
6. Répétez les étapes 1 à 3 pour créer un deuxième site Web dans une autre région Azure avec les paramètres suivants :

     | Paramètre         | Valeur     |
     | ---              | ---  |
     | Resource group          | Sélectionnez **Nouveau**, puis tapez *myResourceGroupFD2* |
     | Nom           | Entrez un nom unique pour votre application web  |
     | Pile d’exécution          | Sélectionnez une pile de runtime pour votre application |
     |      Région  |   Europe Ouest      |
     | Plan/lieu App Service         | Sélectionnez **Nouveau**.  Dans le plan App Service, entrez *myAppServicePlanEastUS*, puis sélectionnez **OK**.|   
     |SKU et taille  | Sélectionnez **Modifier la taille** Sélectionnez **Standard S1 100 ACU au total, 1,75 Go de mémoire** |
    
## <a name="create-a-front-door-for-your-application"></a>Créer une porte d’entrée pour votre application
### <a name="a-add-a-frontend-host-for-front-door"></a>R. Ajouter un hôte frontend pour la porte d’entrée
Créez une configuration Front Door qui dirige le trafic utilisateur selon la plus petite latence de deux backends.

1. Dans l’angle supérieur gauche de l’écran, sélectionnez **Créer une ressource** > **Mise en réseau** > **Front Door**.
2. Dans **Créer une instance Front Door**, entrez ou sélectionnez les informations suivantes ; entrez les paramètres par défaut lorsqu’aucune n’est spécifiée :

     | Paramètre         | Valeur     |
     | ---              | ---  |
     |Abonnement  | Sélectionnez l’abonnement dans lequel vous souhaitez que Front Door soit créé.|
     | Resource group          | Sélectionnez **Nouveau**, puis tapez *myResourceGroupFD0* |
     | Emplacement du groupe de ressources  |   USA Centre        |
     
     > [!NOTE]
     > Il n’est pas nécessaire de créer un nouveau groupe de ressources dans lequel déployer Front Door.  Si vous pouvez, sélectionnez un groupe de ressources existant.
     
3. Cliquez sur **Suivant : Configuration**.
4. Cliquez sur l’icône « + » dans la carte des front-ends/domaines.  En guise de **Nom d’hôte** entrez `<Your Initials>frontend`. Ce nom d’hôte doit être globalement unique, Front Door s’occupera de la validation.
5. Cliquez sur **Add**.

### <a name="b-add-application-backend-and-backend-pools"></a>B. Ajouter un backend d’application et des pools de backends

Ensuite, vous devez configurer vos front-ends/domaines et votre ou vos back-ends d’application dans un pool de back-ends pour indiquer à Front Door où se trouve votre application. 

1. Cliquez sur l’icône « + » dans la carte des pools de back-ends afin d’ajouter un pool de back-ends pour **Nom** ; entrez `myBackendPool` pour votre pool de back-ends.
2. Ensuite, cliquez sur **Ajouter un back-end** pour ajouter les sites web que vous avez créés précédemment.
3. Sélectionnez « App Service » comme **Type de l’hôte back-end**, sélectionnez l’abonnement dans lequel vous avez créé le site web, puis choisissez le premier site web dans la liste déroulante **Nom de l’hôte back-end**.
4. Laissez les champs restants tels quels pour l’instant et cliquez sur **Ajouter**.
5. Sélectionnez « App Service » comme **Type de l’hôte back-end**, sélectionnez l’abonnement dans lequel vous avez créé le site web, puis choisissez le **deuxième** site web dans la liste déroulante **Nom de l’hôte back-end**.
6. Laissez les champs restants tels quels pour l’instant et cliquez sur **Ajouter**.
7. Vous pouvez éventuellement choisir de mettre à jour les paramètres de sonde d’intégrité et d’équilibrage de charge du pool de back-ends. Toutefois, les valeurs par défaut doivent également fonctionner. Dans les deux cas, cliquez sur **Ajouter**.


### <a name="c-add-a-routing-rule"></a>C. Ajouter une règle de routage
1. Enfin, cliquez sur l’icône « + » d’une carte de règles de routage pour configurer une règle. Vous devez le faire pour mapper votre hôte backend au pool de backends, ce qui consiste en fait à définir que si une demande arrive sur `myappfrontend.azurefd.net`, il faut la transférer au pool de backends `myBackendPool`. 
2. Pour **Nom**, entrez « LocationRule ».
3. Cliquez sur **Ajouter** pour ajouter la règle de routage de votre porte d’entrée. 
4. Cliquez sur **Vérifier et créer**.
5. Passez en revue les paramètres de la création de Front Door. Cliquez sur **Créer**

>[!WARNING]
> Vous **devez** vérifier que chaque hôte frontend dans votre porte d’entrée a une règle de routage avec un chemin par défaut (/\*) associé. En d’autres termes, parmi toutes vos règles d’acheminement, il doit exister au moins une règle d’acheminement pour chacun des hôtes frontend définis dans le chemin par défaut (« /\* »). Sinon, le trafic de l’utilisateur final risque de ne pas être routé correctement.

## <a name="view-front-door-in-action"></a>Voir Front Door en action
Une fois la porte d’entrée créée, le déploiement global de la configuration prend quelques minutes. Ensuite, accédez à l’hôte frontend que vous avez créé : ouvrez un navigateur web et entrez l’URL `myappfrontend.azurefd.net`. Votre demande est automatiquement routée vers le frontend le plus proche de vous à partir des backends spécifiés dans le pool de backends. 

### <a name="view-front-door-handle-application-failover"></a>Voir comment Front Door gère le basculement d’application
Si vous voulez tester un basculement instantané global effectué par Front Door, vous pouvez accéder à l’un des sites web que vous avez créés et l’arrêter. Selon le paramètre de sonde d’intégrité défini pour le pool de backends, nous basculons instantanément le trafic vers l’autre déploiement de site web. Vous pouvez aussi tester le comportement en désactivant le backend dans la configuration du pool de backends pour votre porte d’entrée. 

## <a name="clean-up-resources"></a>Nettoyer les ressources
Lorsque vous n’en avez plus besoin, supprimez les groupes de ressources **myResourceGroupFD1**, **myResourceGroupFD2** et **myResourceGroupFD0** :

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez créé une porte d’entrée qui dirige le trafic utilisateur pour des applications web qui nécessitent une haute disponibilité et performances maximales. Pour en savoir plus sur le routage du trafic, consultez les [Méthodes de routage](front-door-routing-methods.md) utilisées par Front Door.
