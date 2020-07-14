---
title: 'Tutoriel : Lancer le lecteur immersif à l’aide de l’exemple de code Swift iOS'
titleSuffix: Azure Cognitive Services
description: Dans ce tutoriel, vous allez créer un exemple d’application Swift qui lance le lecteur immersif.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.openlocfilehash: a9259026747102dd65be3bb8da853735098667db
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049317"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-swift-ios-code-sample"></a>Tutoriel : Lancer le lecteur immersif à l’aide de l’exemple de code Swift iOS

Dans la [présentation](./overview.md), vous avez appris ce qu’est le Lecteur immersif et comment il implémente des techniques éprouvées pour améliorer la compréhension de la lecture pour les apprenants en langue, les lecteurs émergents et les étudiants présentant des difficultés d’apprentissage. Ce tutoriel explique comment créer une application iOS qui lance le lecteur immersif. Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Configurer et exécuter une application Swift pour iOS à l’aide d’un exemple de projet
> * Obtenir un jeton d’accès
> * Lancer le Lecteur immersif avec un exemple de contenu

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* Une ressource Lecteur immersif configurée pour l’authentification Azure Active Directory. Suivez [ces instructions](./how-to-create-immersive-reader.md) pour la configurer. Vous aurez besoin de certaines des valeurs créées ici lors de la configuration des propriétés de l’environnement. Enregistrez la sortie de votre session dans un fichier texte pour référence ultérieure.
* [macOS](https://www.apple.com/macos)
* [Git](https://git-scm.com/)
* [Kit SDK Lecteur immersif](https://github.com/microsoft/immersive-reader-sdk)
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)

## <a name="configure-authentication-credentials"></a>Configurer les informations d’identification d’authentification

1. Ouvrez Xcode, puis ouvrez **immersive-reader-sdk/js/samples/ios/quickstart-swift/quickstart-swift.xcodeproj**.
2. Dans le menu supérieur, cliquez sur **Produit > Schéma > Modifier le schéma...** .
3. Dans la vue **Exécuter**, cliquez sur l’onglet **Arguments**.
4. Dans la section **Variables d’environnement**, ajoutez les noms et les valeurs ci-dessous, en indiquant les valeurs fournies au moment de la création de votre ressource Lecteur immersif.

```text
TENANT_ID=<YOUR_TENANT_ID>
CLIENT_ID=<YOUR_CLIENT_ID>
CLIENT_SECRET<YOUR_CLIENT_SECRET>
SUBDOMAIN=<YOUR_SUBDOMAIN>
```

Veillez à ne pas valider la modification ci-dessus dans le contrôle de code source, car celle-ci contient des secrets qui ne doivent pas être rendus publics.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Lancer le Lecteur immersif avec un exemple de contenu

1. Dans Xcode, appuyez sur **Ctrl-R** pour exécuter le projet.

## <a name="next-steps"></a>Étapes suivantes

* Explorer le [SDK Lecteur Immersif](https://github.com/microsoft/immersive-reader-sdk) et la [référence du SDK Lecteur immersif](./reference.md)
* Voir des exemples de code sur [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
