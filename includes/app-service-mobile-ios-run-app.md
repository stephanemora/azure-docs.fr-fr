---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: f4ba467b6d80c9ccafba0a91c1f04152b92cf869
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58115477"
---
1. Sur votre Mac, accédez au [Portail Azure]. Cliquez sur **Tous les services** > **App Services** > le serveur principal que vous venez de créer. Dans les paramètres de l’application mobile, choisissez votre langue par défaut :

   - Objective-C &ndash; **Démarrage rapide** > **iOS (Objective-C)**
   - Swift &ndash; **Démarrage rapide** > **iOS (Swift)**

     Sous **3. Configurer votre application cliente**, cliquez sur **Télécharger**. Cette opération télécharge un projet Xcode complet préconfiguré pour se connecter à votre serveur principal. Ouvrez le projet à l’aide de Xcode.

1. Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l’application dans le simulateur iOS.

1. Dans l’application, cliquez sur l’icône du signe plus (**+**), tapez un texte explicite comme *Suivre le tutoriel*, puis cliquez sur le bouton Enregistrer. Cette action envoie une requête POST au serveur principal Azure déployé précédemment. Le backend insère les données de la requête dans la table SQL TodoItem et renvoie des informations sur les éléments récemment stockés à l’application mobile. L’application mobile affiche ces données dans la liste.

   ![Application de démarrage rapide en cours d’exécution sur iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Portail Azure]: https://portal.azure.com/
