---
title: Solution d’accès Azure VMware par CloudSimple - portail
description: Décrit comment accéder à des solutions de VMware à portail CloudSimple à partir du portail Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 30882899e5be4101ae3d77f9840d8bdef567e53f
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676982"
---
# <a name="accessing-the-vmware-solution-by-cloudsimple-portal-from-azure-portal"></a>L’accès à la VMware Solution par portail CloudSimple à partir du portail Azure

L’authentification unique est prise en charge pour l’accès au portail CloudSimple. Après que vous être connecté au portail Azure, vous pouvez accéder au portail de CloudSimple sans vous connecter à nouveau. La première fois que vous accédez au portail de CloudSimple que vous êtes invité à autoriser la [autorisation du Service CloudSimple](#consent-to-cloudsimple-service-authorization-application) application.  L’autorisation est qu’une seule fois.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Accéder au portail CloudSimple

1. Sélectionnez **Tous les services**.

2. Recherchez **CloudSimple Services**.

3. Sélectionnez le service CloudSimple sur lequel vous souhaitez créer votre Cloud privé.

4. Sur le **vue d’ensemble** , cliquez sur **accéder au portail CloudSimple**.  Si vous accédez au portail de CloudSimple à partir du portail Azure pour la première fois, vous allez être invité à autoriser la [autorisation du Service CloudSimple](#consent-to-cloudsimple-service-authorization-application) application. 

    ![Lancez CloudSimple portail](media/launch-cloudsimple-portal.png)

> [!TIP]
> Si vous sélectionnez une opération de Cloud privé (par exemple, la création ou le développement d’un Cloud privé) directement depuis le portail Azure, le portail CloudSimple s’ouvre à la page indiquée.

Dans le portail CloudSimple, sélectionnez **accueil** dans le menu latéral pour afficher des informations résumées concernant vos Clouds privés. Les ressources et la capacité de vos Clouds privés sont affichés, ainsi que les alertes et les tâches qui nécessitent votre attention. Pour les tâches courantes, cliquez sur les icônes nommées en haut de la page.

![Page d’accueil](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Consentement pour une application de l’autorisation du Service CloudSimple

Lancement du portail CloudSimple à partir du portail Azure pour la première fois nécessite votre consentement pour l’application de l’autorisation du Service CloudSimple.  Sélectionnez **Accept** pour accorder les autorisations demandées et accéder au portail CloudSimple. 

![Donner son consentement pour l’autorisation du Service CloudSimple - les administrateurs](media/cloudsimple-azure-consent.png)

Si vous disposez des privilèges d’administrateur global, vous pouvez donner son consentement pour votre organisation.  Sélectionnez **donner son consentement au nom de votre organisation**.

![Donner son consentement pour l’autorisation du Service CloudSimple - administrateur général](media/cloudsimple-azure-consent-global-admin.png)

Si vos autorisations ne pas autoriser l’accès au portail CloudSimple, contactez l’administrateur général de votre client pour accorder des autorisations requises.  Un administrateur global peut donner son consentement au nom de votre organisation.

![Donner son consentement à l’autorisation de Service CloudSimple : nécessite que les administrateurs](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer un cloud privé](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Découvrez comment [configurer un environnement de cloud privé](quickstart-create-private-cloud.md)