---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 1d1d593b7305e0cd9899f4ec388cb441ced90b10
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133823"
---
1. Sur votre Mac, accédez au [Portail Azure]. Cliquez sur **Tous les services** > **App Services** > le serveur principal que vous venez de créer. Dans les paramètres de l’application mobile, choisissez votre langue par défaut :

    - Objective-C &ndash; **Démarrage rapide** > **iOS (Objective-C)**
    - Swift &ndash; **Démarrage rapide** > **iOS (Swift)**

    Sous **3. Configurer votre application cliente**, cliquez sur **Télécharger**. Cette opération télécharge un projet Xcode complet préconfiguré pour se connecter à votre serveur principal. Ouvrez le projet à l’aide de Xcode.

1. Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l’application dans le simulateur iOS.

1. Dans l’application, tapez un texte explicite, comme *Suivre le didacticiel* , puis cliquez sur l’icône plus (**+**). Cette action envoie une requête POST au serveur principal Azure déployé précédemment. Le backend insère les données de la requête dans la table SQL TodoItem et renvoie des informations sur les éléments récemment stockés à l’application mobile. L’application mobile affiche ces données dans la liste.

   ![Application de démarrage rapide en cours d’exécution sur iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Portail Azure]: https://portal.azure.com/
