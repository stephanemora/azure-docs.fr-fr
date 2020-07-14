---
title: 'Tutoriel : Lancer le lecteur immersif à l’aide des exemples de code Android'
titleSuffix: Azure Cognitive Services
description: Dans ce tutoriel, vous allez configurer et exécuter un exemple d’application Android qui lance le lecteur immersif.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.openlocfilehash: d847b4ab9f3c06634390e1f67dfed36df938c3ad
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049309"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-android-java-code-sample"></a>Tutoriel : Lancer le lecteur immersif à l’aide des exemples de code Android Java

Dans la [présentation](./overview.md), vous avez appris ce qu’est le Lecteur immersif et comment il implémente des techniques éprouvées pour améliorer la compréhension de la lecture pour les apprenants en langue, les lecteurs émergents et les étudiants présentant des difficultés d’apprentissage. Ce tutoriel explique comment créer une application Android qui lance le lecteur immersif. Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Configurer et exécuter une application pour Android à l’aide d’un exemple de projet
> * Obtenir un jeton d’accès
> * Lancer le Lecteur immersif avec un exemple de contenu

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* Une ressource Lecteur immersif configurée pour l’authentification Azure Active Directory. Suivez [ces instructions](./how-to-create-immersive-reader.md) pour la configurer. Vous aurez besoin de certaines des valeurs créées ici lors de la configuration des propriétés de l’environnement. Enregistrez la sortie de votre session dans un fichier texte pour référence ultérieure.
* [Git](https://git-scm.com/)
* [Kit SDK Lecteur immersif](https://github.com/microsoft/immersive-reader-sdk)
* [Android Studio](https://developer.android.com/studio)

## <a name="configure-authentication-credentials"></a>Configurer les informations d’identification d’authentification

1. Lancez Android Studio et ouvrez le projet à partir du répertoire **immersive-reader-sdk/js/samples/quickstart-java-android** (Java) ou du répertoire **immersive-reader-sdk/js/samples/quickstart-kotlin** (Kotlin).

2. Créez un fichier nommé **env** dans le dossier **/assets**, puis ajoutez ce qui suit, en fournissant les valeurs appropriées. Veillez à ne pas valider ce fichier dans le contrôle de code source, car il contient des secrets qui ne doivent pas être rendus publics.

```text
TENANT_ID=<YOUR_TENANT_ID>
CLIENT_ID=<YOUR_CLIENT_ID>
CLIENT_SECRET=<YOUR_CLIENT_SECRET>
SUBDOMAIN=<YOUR_SUBDOMAIN>
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>Lancer le Lecteur immersif avec un exemple de contenu

1. Choisissez un émulateur d’appareil à partir du gestionnaire AVD, puis exécutez le projet.

## <a name="next-steps"></a>Étapes suivantes

* Explorer le [SDK Lecteur Immersif](https://github.com/microsoft/immersive-reader-sdk) et la [référence du SDK Lecteur immersif](./reference.md)
* Voir des exemples de code sur [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)