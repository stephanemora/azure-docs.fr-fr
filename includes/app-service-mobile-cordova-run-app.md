---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 3217383b105c022aef42d8000f3a41cefea542fe
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140904"
---
1. Visitez le [portail Azure].
2. Cliquez sur **App Services** > serveur principal que vous avez créé.
3. Dans les paramètres de l’application mobile, cliquez sur **Démarrage rapide** > **Cordova**.
![Portail Azure avec Démarrage rapide Mobile Apps en surbrillance][quickstart]
4. Sous **Configurer votre application client**, sélectionnez **Création d'une application**, puis cliquez sur **Télécharger**.
2. Décompressez le fichier ZIP téléchargé dans un répertoire sur votre disque dur, accédez au fichier de solution (.sln) et ouvrez-le à l’aide de Visual Studio.
3. Dans Visual Studio, choisissez la plateforme de solution (Windows, iOS ou Android) dans la liste déroulante en regard de la flèche de démarrage. Sélectionnez un périphérique de déploiement spécifique ou un émulateur en cliquant sur le menu déroulant sur la flèche verte. Vous pouvez utiliser la plateforme Android par défaut et l’émulateur Ripple. Certains didacticiels plus avancés (les notifications push, par exemple) vous demanderont de sélectionner un émulateur ou un périphérique pris en charge.
4. Pour générer et exécuter votre application Cordova, appuyez sur F5 ou cliquez sur la flèche verte. Si vous voyez apparaître dans l’émulateur une boîte de dialogue de sécurité demandant l’accès au réseau, acceptez.
5. Après le démarrage de l’application sur le périphérique ou l’émulateur, tapez un texte explicite dans **Entrer un nouveau texte**, tel que *Suivre le didacticiel*, puis cliquez sur le bouton **Ajouter**.

Le serveur principal insère les données de la requête dans la table SQL TodoItem dans la base de données SQL et renvoie des informations sur les éléments récemment stockés à l’application mobile. L’application mobile affiche ces données dans la liste.

Vous pouvez répéter les étapes 3 à 5 pour les autres plateformes.

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png

<!-- URLs -->
[Portail Azure]: https://portal.azure.com/
