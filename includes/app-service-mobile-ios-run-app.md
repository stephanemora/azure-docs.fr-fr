---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240271"
---
1. Ouvrez le projet client téléchargé à l’aide de Xcode.

2. Accédez à la [Azure portal](https://portal.azure.com/) et accédez à l’application mobile que vous avez créé. Sur le `Overview` panneau, recherchez l’URL qui est le point de terminaison public pour votre application mobile. Exemple - sitename pour mon nom de l’application « test123 » sera https://test123.azurewebsites.net.

3. Ouvrez le fichier projet Swift `ToDoTableViewController.swift` dans ce dossier - ZUMOAPPNAME/ZUMOAPPNAME/ToDoTableViewController.swift. Le nom de l’application est `ZUMOAPPNAME`.

4. Dans `viewDidLoad()` (méthode), remplacez `ZUMOAPPURL` paramètre avec le point de terminaison public ci-dessus.

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    devient
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. Ouvrez le fichier projet Objective-C `QSTodoService.m` dans ce dossier - ZUMOAPPNAME/ZUMOAPPNAME. Le nom de l’application est `ZUMOAPPNAME`.

6. Dans `init` (méthode), remplacez `ZUMOAPPURL` paramètre avec le point de terminaison public ci-dessus.

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    devient
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l’application dans le simulateur iOS.

8. Dans l’application, cliquez sur l’icône du signe plus ( **+** ), tapez un texte explicite comme *Suivre le tutoriel*, puis cliquez sur le bouton Enregistrer. Cette action envoie une requête POST au serveur principal Azure déployé précédemment. Le backend insère les données de la requête dans la table SQL TodoItem et renvoie des informations sur les éléments récemment stockés à l’application mobile. L’application mobile affiche ces données dans la liste.

   ![Application de démarrage rapide en cours d’exécution sur iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
