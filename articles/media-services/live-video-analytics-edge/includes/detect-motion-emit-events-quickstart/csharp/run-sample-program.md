---
ms.openlocfilehash: 7b173a5fe639ff21cc8a475edd16d0e3885dacef
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113279875"
---
Pour exécuter l’exemple de code, effectuez les étapes suivantes :

1. Dans Visual Studio Code, ouvrez l’onglet **Extensions** (ou appuyez sur Ctrl+Maj+X) et recherchez Azure IoT Hub.
1. Cliquez avec le bouton droit et sélectionnez **Paramètres d’extension**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Paramètres d’extension":::
1. Recherchez et activez « Afficher le message détaillé ».

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Afficher le message détaillé":::
1. Dans Visual Studio Code, accédez à *src/cloud-to-device-console-app/operations.json*.
1. Sur le nœud **GraphTopologySet**, vérifiez que vous voyez la valeur suivante :

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/2.0/topology.json"`
1. Sur les nœuds **GraphInstanceSet** et **GraphTopologyDelete**, vérifiez que la valeur de `topologyName` correspond à celle de la propriété `name` dans la topologie de graphe :

    `"topologyName" : "MotionDetection"`
    
1. Démarrez une session de débogage en appuyant sur la touche F5. La fenêtre **TERMINAL** affiche des messages.

    Si vous démarrez une session de débogage avec la touche F5, vous êtes invité à entrer le type d’environnement et le projet la première fois. Il s’agit de créer et configurer le fichier launch.json dans votre dossier. Pour les besoins de cette démonstration, utilisez ce qui suit :
    * Environnement - .NET Core
    * Projet - c2d-console-app
    
    Modifiez le fichier launch.json une fois qu’il a été créé. Modifiez la configuration de la console sur « integratedTeminal ».
    
    `"console": "integratedTerminal"`
1. Le fichier *operations.json* commence par appeler `GraphTopologyList` et `GraphInstanceList`. Si vous avez nettoyé les ressources après avoir suivi les guides de démarrage rapide précédents, ce processus retourne des listes vides, puis s’interrompt. Pour continuer, sélectionnez la touche Entrée.

    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
        "@apiVersion": "2.0"
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
     * Un appel à `GraphTopologySet` qui utilise le `topologyUrl` précédent
     * Un appel à `GraphInstanceSet` qui utilise le corps suivant :
         
    ```
    {
      "@apiVersion": "2.0",
      "name": "Sample-Graph",
      "properties": {
        "topologyName": "MotionDetection",
        "description": "Sample graph description",
        "parameters": [
          {
            "name": "rtspUrl",
            "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
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
1. La sortie affichée dans la fenêtre **TERMINAL** fait une pause au niveau de `Press Enter to continue`. Ne sélectionnez pas encore Entrée. Faites défiler vers le haut pour voir les charges utiles de réponse JSON pour les méthodes directes que vous avez invoquées.
1. Basculez vers la fenêtre **SORTIE** de Visual Studio Code. Les messages indiquant que le module Live Video Analytics sur IoT Edge effectue des envois à IoT Hub s’affichent. La section suivante de ce guide de démarrage rapide décrit ces messages.
1. Le graphe multimédia continue à s’exécuter et à afficher les résultats. Le simulateur RTSP continue de boucler la vidéo source. Pour arrêter le graphe multimédia, retournez dans la fenêtre **TERMINAL**, puis sélectionnez Entrée. 

    La série d’appels suivante nettoie les ressources :

    * Un appel à `GraphInstanceDeactivate` désactive l’instance de graphe.
    * Un appel à `GraphInstanceDelete` supprime l’instance.
    * Un appel à `GraphTopologyDelete` supprime la topologie.
    * Un dernier appel à `GraphTopologyList` indique que la liste est vide.
