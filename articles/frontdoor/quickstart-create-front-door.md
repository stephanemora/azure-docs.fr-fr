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
ms.openlocfilehash: 67a4f9eb3290ba09a2c19325464cf7ad224856e7
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184512"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Démarrage rapide : Créer une porte d’entrée pour une application web globale hautement disponible

Ce guide de démarrage rapide décrit comment créer un profil Front Door qui assure une haute disponibilité et de hautes performances pour votre application web globale. 

Le scénario décrit dans ce démarrage rapide inclut deux instances d’une application web en cours d’exécution dans différentes régions Azure. Nous créons une configuration Front Door basée sur des [backends de même poids et de même priorité](front-door-routing-methods.md) pour diriger le trafic utilisateur sur l’ensemble le plus proche de backends de site exécutant l’application. Front Door supervise en continu l’application web et fournit un basculement automatique vers le backend disponible suivant si le site le plus proche n’est pas disponible.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="sign-in-to-azure"></a>Connexion à Azure 
Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="prerequisites"></a>Prérequis
Pour suivre ce guide de démarrage rapide, vous devez avoir déployé deux instances d’une application web s’exécutant dans des régions Azure différentes (*USA Est* et *Europe Ouest*). Les instances d’application web s’exécutent toutes deux en mode actif/actif, autrement dit, l’une des deux peut accepter du trafic à tout moment, contrairement à une configuration actif/passif où l’une sert de basculement.

1. Dans l’angle supérieur gauche de l’écran, sélectionnez **Créer une ressource** > **Web** > **Application web** > **Créer**.
2. Dans **Application Web**, entrez ou sélectionnez les informations suivantes et entrez les paramètres par défaut lorsqu’aucun n’est spécifié :

     | Paramètre         | Valeur     |
     | ---              | ---  |
     | Nom           | Entrez un nom unique pour votre application web  |
     | Resource group          | Sélectionnez **Nouveau**, puis tapez *myResourceGroupFD1* |
     | Plan/lieu App Service         | Sélectionnez **Nouveau**.  Dans le plan App Service, entrez *myAppServicePlanEastUS*, puis sélectionnez **OK**. 
     |      Location  |   USA Est        |
    |||

3. Sélectionnez **Create** (Créer).
4. Un site Web par défaut est créé lorsque l’application web est déployée avec succès.
5. Répétez les étapes 1 à 3 pour créer un deuxième site Web dans une autre région Azure avec les paramètres suivants :

     | Paramètre         | Valeur     |
     | ---              | ---  |
     | Nom           | Entrez un nom unique pour votre application web  |
     | Resource group          | Sélectionnez **Nouveau**, puis tapez *myResourceGroupFD2* |
     | Plan/lieu App Service         | Sélectionnez **Nouveau**.  Dans le plan App Service, entrez *myAppServicePlanEastUS*, puis sélectionnez **OK**. 
     |      Location  |   Europe Ouest      |
    |||


## <a name="create-a-front-door-for-your-application"></a>Créer une porte d’entrée pour votre application
### <a name="a-add-a-frontend-host-for-front-door"></a>R. Ajouter un hôte frontend pour la porte d’entrée
Créez une configuration Front Door qui dirige le trafic utilisateur selon la plus petite latence de deux backends.

1. En haut à gauche de l’écran, sélectionnez **Créer une ressource** > **Mise en réseau** > **Porte d’entrée** > **Créer**.
2. Dans **Créer une porte d’entrée**, commencez par ajouter les informations de base et indiquer un abonnement où configurer la porte d’entrée. De même, tout comme les autres ressources Azure, vous devez indiquer un groupe de ressources et une région de groupe de ressources si vous en créez un. Enfin, vous devez indiquer un nom pour votre porte d’entrée.
3. Une fois les informations de base renseignées, la première étape est de définir **l’hôte frontend** pour la configuration. Le résultat doit être un nom de domaine valide de type `myappfrontend.azurefd.net`. Ce nom d’hôte doit être globalement unique et validé par Front Door. 

### <a name="b-add-application-backend-and-backend-pools"></a>B. Ajouter un backend d’application et des pools de backends

Ensuite, vous devez configurer votre ou vos backends d’application dans un pool de backends pour indiquer à Front Door où se trouve votre application. 

1. Cliquez sur l’icône « + » pour ajouter un pool de backends, puis donnez-lui un **nom**, par exemple, `myBackendPool`.
2. Ensuite, cliquez sur Ajouter des backends pour ajouter les sites web que vous avez créés précédemment.
3. Sélectionnez « App Service » comme **Type d’hôte cible**, sélectionnez l’abonnement dans lequel vous avez créé le site web, puis choisissez le premier site web à partir du **nom d’hôte cible**, autrement dit, *myAppServicePlanEastUS.azurewebsites.net*.
4. Laissez les champs restants tels quels pour l’instant et cliquez sur **Ajouter**.
5. Répétez les étapes 2 à 4 pour ajouter l’autre site web, autrement dit, *myAppServicePlanWestEurope.azurewebsites.net*
6. Vous pouvez aussi choisir de mettre à jour les paramètres de sonde d’intégrité et d’équilibrage de charge du pool de backends, mais les valeurs par défaut doivent également fonctionner. Cliquez sur **Add**.


### <a name="c-add-a-routing-rule"></a>C. Ajouter une règle de routage
Enfin, cliquez sur l’icône « + » d’une règle de routage pour la configurer. Vous devez le faire pour mapper votre hôte backend au pool de backends, ce qui consiste en fait à définir que si une demande arrive sur `myappfrontend.azurefd.net`, il faut la transférer au pool de backends `myBackendPool`. Cliquez sur **Ajouter** pour ajouter la règle de routage de votre porte d’entrée. Vous devez maintenant être prêt à créer la porte d’entrée. Cliquez sur **Vérifier et créer**.

>[!WARNING]
> Vous **devez** vérifier que chaque hôte frontend dans votre porte d’entrée a une règle de routage avec un chemin par défaut (/\*) associé. Autrement dit, dans toutes vos règles de routage, il doit y en avoir au moins une pour chaque hôte frontend défini dans le chemin par défaut (/\*). Dans le cas contraire, votre trafic d’utilisateur final risque de ne pas être routé correctement.

## <a name="view-front-door-in-action"></a>Voir Front Door en action
Une fois la porte d’entrée créée, le déploiement global de la configuration prend quelques minutes. Ensuite, accédez à l’hôte frontend que vous avez créé : ouvrez un navigateur web et entrez l’URL `myappfrontend.azurefd.net`. Votre demande est automatiquement routée vers le frontend le plus proche de vous à partir des backends spécifiés dans le pool de backends. 

### <a name="view-front-door-handle-application-failover"></a>Voir comment Front Door gère le basculement d’application
Si vous voulez tester un basculement instantané global effectué par Front Door, vous pouvez accéder à l’un des sites web que vous avez créés et l’arrêter. Selon le paramètre de sonde d’intégrité défini pour le pool de backends, nous basculons instantanément le trafic vers l’autre déploiement de site web. Vous pouvez aussi tester le comportement en désactivant le backend dans la configuration du pool de backends pour votre porte d’entrée. 

## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, les application web et toutes les ressources associées.

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez créé une porte d’entrée qui dirige le trafic utilisateur pour des applications web qui nécessitent une haute disponibilité et performances maximales. Pour en savoir plus sur le routage du trafic, consultez les [Méthodes de routage](front-door-routing-methods.md) utilisées par Front Door.