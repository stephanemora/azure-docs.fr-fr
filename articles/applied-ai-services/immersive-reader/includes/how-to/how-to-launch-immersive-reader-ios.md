---
author: erhopf
manager: nitinme
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: 90344270938c778d2a264292049d723810481c26
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524420"
---
## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* Une ressource Lecteur immersif configurée pour l’authentification Azure Active Directory. Suivez [ces instructions](../../how-to-create-immersive-reader.md) pour la configurer.  Vous aurez besoin de certaines des valeurs créées ici lors de la configuration des propriétés de l’environnement. Enregistrez la sortie de votre session dans un fichier texte pour référence ultérieure.
* [macOS](https://www.apple.com/macos).
* [Git](https://git-scm.com/).
* [Kit SDK Lecteur immersif](https://github.com/microsoft/immersive-reader-sdk).
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="configure-authentication-credentials"></a>Configurer les informations d’identification d’authentification

1. Ouvrez Xcode, puis ouvrez **immersive-reader-sdk/js/samples/ios/quickstart-swift/quickstart-swift.xcodeproj**.
1. Dans le menu supérieur, sélectionnez **Product** > **Scheme** > **Edit Scheme**.
1. Dans la vue **Run**, cliquez sur l’onglet **Arguments**.
1. Dans la section **Environment Variables**, ajoutez les noms et valeurs suivants. Spécifiez les valeurs fournies au moment de la création de votre ressource Lecteur immersif.

    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

Ne validez pas cette modification dans le contrôle de code source, car elle contient des secrets qui ne doivent pas être rendus publics.

## <a name="start-the-immersive-reader-with-sample-content"></a>Démarrer le Lecteur immersif avec un exemple de contenu

Dans Xcode, appuyez sur **Ctrl+R** pour exécuter le projet.

## <a name="next-steps"></a>Étapes suivantes

* Explorer le [SDK Lecteur Immersif](https://github.com/microsoft/immersive-reader-sdk) et la [référence du SDK Lecteur immersif](../../reference.md)
* Voir des exemples de code sur [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
