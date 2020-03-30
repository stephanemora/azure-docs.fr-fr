---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240271"
---
1. Ouvrez le projet client téléchargé à l’aide de Xcode.

2. Ouvrez le [Portail Microsoft Azure](https://portal.azure.com/), puis accédez à l’application mobile que vous avez créée. Sur le panneau `Overview`, recherchez l’URL qui correspond au point de terminaison public de votre application mobile. Exemple : la valeur sitename relative au nom de l’application « test123 » est https://test123.azurewebsites.net.

3. Dans le cas d’un projet Swift, ouvrez le fichier `ToDoTableViewController.swift` dans le dossier ZUMOAPPNAME/ZUMOAPPNAME/ToDoTableViewController.swift. Le nom de l’application est `ZUMOAPPNAME`.

4. Dans la méthode `viewDidLoad()`, remplacez le paramètre `ZUMOAPPURL` par le point de terminaison public ci-dessus.

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    devient
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. Dans le cas d’un projet Objective-C, ouvrez le fichier `QSTodoService.m` dans le dossier ZUMOAPPNAME/ZUMOAPPNAME. Le nom de l’application est `ZUMOAPPNAME`.

6. Dans la méthode `init`, remplacez le paramètre `ZUMOAPPURL` par le point de terminaison public ci-dessus.

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    devient
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l’application dans le simulateur iOS.

8. Dans l’application, cliquez sur l’icône du signe plus ( **+** ), tapez un texte explicite comme *Suivre le tutoriel*, puis cliquez sur le bouton Enregistrer. Cette action envoie une requête POST au serveur principal Azure déployé précédemment. Le backend insère les données de la requête dans la table SQL TodoItem et renvoie des informations sur les éléments récemment stockés à l’application mobile. L’application mobile affiche ces données dans la liste.

   ![Application de démarrage rapide en cours d’exécution sur iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
