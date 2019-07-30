---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 6d6451d50a00569eb1da8f5b0a0dc10d3c6b1115
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841541"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Configurer une variable d’environnement pour l’authentification

Les applications ont besoin d’authentifier l’accès à Cognitive Services qu’elles utilisent. Pour l’authentification, nous vous recommandons de créer une variable d’environnement destinée à stocker les clés pour vos ressources Azure. 

Après avoir obtenu votre clé, écrivez-la dans une variable d’environnement sur la machine locale exécutant l’application. Pour définir la variable d’environnement, ouvrez une fenêtre de console et suivez les instructions pour votre système d’exploitation. Remplacez `your-key` par l’une des clés de votre ressource.

* Windows

    ```console
    setx COGNITIVE_SERVICE_KEY "your-key"
    ```

    Après avoir ajouté la variable d’environnement, vous devrez peut-être redémarrer tous les programmes en cours d’exécution qui devront la lire, y compris la fenêtre de console. Par exemple, si vous utilisez Visual Studio comme éditeur, redémarrez Visual Studio avant d’exécuter l’exemple.

* Linux
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    Après avoir ajouté la variable d’environnement, exécutez `source ~/.bashrc` depuis la fenêtre de console pour appliquer les changements.
    
* macOS
    
    Modifiez votre profil bash_profile, et ajoutez la variable d’environnement :
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    Après avoir ajouté la variable d’environnement, exécutez `source .bash_profile` depuis la fenêtre de console pour appliquer les changements.
