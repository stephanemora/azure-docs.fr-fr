---
ms.openlocfilehash: 93c88f34e32e2057efd3eae25b1f41f58b948575
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682208"
---
1. Démarrez une session de débogage en appuyant sur la touche F5. La fenêtre **TERMINAL** affiche des messages.
1. Le code *operations.json* appelle les méthodes directes `GraphTopologyList` et `GraphInstanceList`. Si vous avez nettoyé les ressources après les guides de démarrage rapide précédents, ce processus retourne des listes vides, puis s’interrompt. Sélectionnez la touche Entrée.
    
    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
      "@apiVersion": "1.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
      "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
  
  La fenêtre **TERMINAL** affiche le jeu d’appels de méthode directe suivant :  
  
  * Un appel à `GraphTopologySet` qui utilise `topologyUrl` 
  * Un appel à `GraphInstanceSet` qui utilise le corps suivant :
  
  ```
  {
    "@apiVersion": "1.0",
    "name": "Sample-Graph",
    "properties": {
      "topologyName": "EVRToFilesOnMotionDetection",
      "description": "Sample graph description",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/lots_015.mkv"
        },
        {
          "name": "rtspUserName",
          "value": "testuser"
        },
        {
          "name": "rtspPassword",
          "value": "testpassword"
        }
      ]
    }
  }
  ```
    
  * Un appel à `GraphInstanceActivate` qui démarre l’instance de graphe et le flux vidéo.
  * Un deuxième appel à `GraphInstanceList` qui indique que l’instance de graphe est dans l’état En cours d’exécution.
1. La sortie affichée dans la fenêtre **TERMINAL** fait une pause au niveau de `Press Enter to continue`. Ne sélectionnez pas encore Entrée. Faites défiler vers le haut pour voir les charges utiles de réponse JSON pour les méthodes directes que vous avez appelées.
1. Basculez vers la fenêtre **SORTIE** de Visual Studio Code. Les messages indiquant que le module Live Video Analytics sur IoT Edge effectue des envois à IoT Hub s’affichent. La section suivante de ce guide de démarrage rapide décrit ces messages.
1. Le graphe multimédia continue à s’exécuter et à afficher les résultats. Le simulateur RTSP continue de boucler la vidéo source. Pour arrêter le graphe multimédia, retournez dans la fenêtre **TERMINAL**, puis sélectionnez Entrée. 

    La série d’appels suivante nettoie les ressources :

    * Un appel à `GraphInstanceDeactivate` désactive l’instance de graphe.
    * Un appel à `GraphInstanceDelete` supprime l’instance.
    * Un appel à `GraphTopologyDelete` supprime la topologie.
    * Un dernier appel à `GraphTopologyList` indique que la liste est maintenant vide.
