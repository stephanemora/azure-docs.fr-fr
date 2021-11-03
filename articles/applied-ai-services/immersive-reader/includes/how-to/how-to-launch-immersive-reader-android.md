---
author: nitinme
manager: nitinme
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: nitinme
ms.openlocfilehash: e080a0b7cc05b776e3085715e20d21bfb2545dd7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253471"
---
## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* Une ressource Lecteur immersif configurée pour l’authentification Azure Active Directory. Suivez [ces instructions](../../how-to-create-immersive-reader.md) pour la configurer.  Vous aurez besoin de certaines des valeurs créées ici lors de la configuration des propriétés de l’environnement. Enregistrez la sortie de votre session dans un fichier texte pour référence ultérieure.
* [Git](https://git-scm.com/).
* [Kit SDK Lecteur immersif](https://github.com/microsoft/immersive-reader-sdk).
* [Android Studio](https://developer.android.com/studio).

## <a name="configure-authentication-credentials"></a>Configurer les informations d’identification d’authentification

1. Démarrez Android Studio et ouvrez le projet à partir du répertoire **immersive-reader-sdk/js/samples/quickstart-java-android** (Java) ou **immersive-reader-sdk/js/samples/quickstart-kotlin** (Kotlin).

1. Créez un fichier nommé **env** dans le dossier **/assets**. Ajoutez les noms et valeurs suivants, puis indiquez les valeurs appropriées. Ne validez pas ce fichier dans le contrôle de code source, car il contient des secrets qui ne doivent pas être rendus publics.
    
    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET=<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

## <a name="start-the-immersive-reader-with-sample-content"></a>Démarrer le Lecteur immersif avec un exemple de contenu

Choisissez un émulateur d’appareil à partir du gestionnaire AVD, puis exécutez le projet.

## <a name="next-steps"></a>Étapes suivantes

* Explorer le [SDK Lecteur Immersif](https://github.com/microsoft/immersive-reader-sdk) et la [référence du SDK Lecteur immersif](../../reference.md)
* Voir des exemples de code sur [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)