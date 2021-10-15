---
title: Créer une application iOS
description: Étapes de création d’un compte Azure Maps et de la première application iOS.
author: deniseatmicrosoft
ms.author: v-dbogomolov
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: azure-maps
ms.openlocfilehash: 46e5ec41c0cea9a417b920e8441ff1129040c209
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389605"
---
#  <a name="create-an-ios-app-public-preview"></a>Créer une application iOS (préversion publique)

Cet article vous montre comment ajouter Azure Maps à une application iOS. Il vous guide pas à pas durant ces étapes de base :

* Configurer votre environnement de développement.
* Créer votre propre compte Azure Maps.
* Récupérer votre clé Azure Maps primaire à utiliser dans l’application.
* Référencer les bibliothèques Azure Maps à partir du projet.
* Ajouter un contrôle Azure Maps à l’application.

## <a name="prerequisites"></a>Prérequis

* Créez un compte Azure Maps en vous connectant au [portail Azure](https://portal.azure.com/). Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* [Créer un compte Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
* [Obtenez une clé d’abonnement principale](quick-demo-map-app.md#get-the-primary-key-for-your-account), également appelée clé primaire ou clé d’abonnement. Pour plus d’informations sur l’authentification dans Azure Maps, consultez [Gérer l’authentification dans Azure Maps](how-to-manage-authentication.md).
* Téléchargez gratuitement [Xcode à partir d’App Store](https://apps.apple.com/cz/app/xcode/id497799835?mt=12).

## <a name="create-an-azure-maps-account"></a>Créer un compte Azure Maps

Créez un compte Azure Maps en effectuant les étapes suivantes :

1. Dans le coin supérieur gauche du [portail Azure](https://portal.azure.com/), cliquez sur **Créer une ressource**.

2. Dans la zone _Rechercher dans la Place de marché_, tapez **Azure Maps**.

3. Dans les _Résultats_, sélectionnez **Azure Maps**. Cliquez sur le bouton **Créer** qui s’affiche sous la carte.

4. Sur la page **Créer un compte Maps**, entrez les valeurs suivantes :
   - _L’Abonnement_ à utiliser pour ce compte.
   - Le _Groupe de ressources_ pour ce compte. Vous pouvez choisir de _Créer_ ou d’utiliser un groupe de ressources _Existant_.
   - Le _Nom_ de votre nouveau compte.
   - Le _niveau tarifaire_ pour ce compte.
   - Lisez la _Licence_ et la _Déclaration de confidentialité_, puis cochez la case pour accepter les conditions.
   - Cliquez sur le bouton **Créer**.

   :::image source="./media/quick-ios-app/create-account.png" alt-text="Capture d’écran de la création d’un compte Azure Maps.":::

## <a name="get-the-primary-key-for-your-account"></a>Obtenir la clé primaire de votre compte

Une fois le compte Maps créé, récupérez la clé principale qui vous permet d’interroger les API Maps.

1. Ouvrez votre compte Maps dans le portail.

2. Dans la section des paramètres, sélectionnez **Authentification**.

3. Copiez la **Clé primaire** dans le Presse-papiers. Enregistrez-la localement, vous l’utiliserez plus tard dans ce didacticiel.

   > [!Note]
   > Si vous utilisez la clé d’abonnement Azure au lieu de la clé primaire Azure Maps, votre carte ne s’affiche pas correctement. En outre, pour des raisons de sécurité, il est recommandé de permuter vos clés principale et secondaire. Pour permuter les clés, mettez à jour votre application pour utiliser la clé secondaire, déployez, puis appuyez sur le bouton cycle/actualiser en regard de la clé principale pour générer une nouvelle clé principale. L’ancienne clé principale est désactivée. Pour plus d’informations sur la permutation des clés, consultez [Configurer Azure Key Vault avec la permutation des clés et l’audit](../key-vault/secrets/tutorial-rotation-dual.md)

   :::image source="./media/quick-ios-app/get-key.png" alt-text="Capture d’écran d’obtention de la clé d’abonnement.":::

## <a name="create-a-project-in-xcode"></a>Créer un projet dans Xcode

Tout d’abord, créez un nouveau projet d’application iOS. Suivez ces étapes pour créer un projet Xcode :

1. Sous **File (Fichier)** , sélectionnez **New (Nouveau)**  -> **Project (Projet)** .

2. Dans l’onglet **iOS**, sélectionnez **App (Application)** , puis **Next (Suivant)** .

3. Entrez le nom de l’application et l’ID du pack, puis sélectionnez **Next (Suivant)** .

   Pour plus d’informations sur la création d’un nouveau projet, consultez [Creating an Xcode Project for an App](https://developer.apple.com/documentation/xcode/creating-an-xcode-project-for-an-app) (Création d’un projet Xcode pour une application).

:::image source="./media/quick-ios-app/create-app.png" alt-text="Créer la première application iOS.":::

## <a name="install-the-azure-maps-ios-sdk"></a>Installer le Kit de développement logiciel (SDK) iOS Azure Maps

L’étape suivante de la création de votre application consiste à installer le Kit de développement logiciel (SDK) iOS Azure Maps. Pour installer le Kit de développement logiciel (SDK), procédez comme suit :

1. Dans **Project navigator (Navigateur de projets)** , sélectionnez le fichier du projet.

2. Dans **Project Editor (Éditeur de projet)** ouvert, sélectionner un projet.

3. Basculez sur l’onglet **Swift Package (Package Swift)** .

4. Ajoutez le Kit de développement logiciel (SDK) iOS Azure Maps :`{link goes here}`.

   :::image source="./media/quick-ios-app/add-project.png" alt-text="Capture d’écran de l’ajout d’un projet iOS.":::
  
## <a name="add-mapcontrol-view"></a>Ajouter un affichage MapControl

1. Ajoutez un `UIView` personnalisé au contrôleur d’affichage.

2. Sélectionnez une classe `MapControl` dans le module `AzureMapsControl`.

   :::image source="./media/quick-ios-app/add-map-control.png" alt-text="Capture d’écran de l’ajout d’un contrôle Azure Maps.":::

3. Dans le fichier **AppDelegate.swift** :

   - Ajoutez l’importation pour le Kit de développement logiciel (SDK) Azure Maps.
   - Définissez vos informations d’authentification Azure Maps.
   
   Si vous définissez les informations d’authentification sur la classe AzureMaps de façon globale à l’aide des méthodes `AzureMaps.configure(subscriptionKey:)` ou `AzureMaps.configure(aadClient:aadAppId:aadTenant:)`, vous n’aurez pas besoin d’ajouter vos informations d’authentification à chaque affichage.

4. Sélectionnez le bouton d’exécution comme illustré dans le graphique suivant (ou appuyez sur les touches `CMD` + `R`) pour générer votre application.

   :::image source="./media/quick-ios-app/run.png" alt-text="Capture d’écran de l’exécution de l’application iOS.":::

   Xcode prendra quelques secondes pour créer l’application. Une fois la création terminée, vous pouvez tester votre application dans l’appareil iOS simulé. La carte doit ressembler à ceci :

   :::image source="./media/quick-ios-app/example.png" alt-text="Capture d’écran de votre première carte sur une application iOS.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

> [!WARNING]
> Les tutoriels répertoriés dans la section [Étapes suivantes](#next-steps) expliquent en détail comment utiliser et configurer Azure Maps avec votre compte. Si vous souhaitez suivre les tutoriels, ne nettoyez pas les ressources créées dans ce guide de démarrage rapide.

Sinon, effectuez ces étapes pour nettoyer les ressources :

1. Fermez Xcode et supprimez le projet que vous avez créé.
2. Si vous avez testé l’application sur un appareil externe, désinstallez l’application de cet appareil.

Si vous n’envisagez pas de continuer à développer à l’aide du Kit de développement logiciel (SDK) iOS Azure Maps :

1. Accédez à la page du portail Azure. Sélectionnez **Toutes les ressources** dans la page principale du portail. Ou cliquez sur l’icône de menu dans le coin supérieur gauche. Sélectionnez **Toutes les ressources**.
2. Cliquez sur votre compte Azure Maps. En haut de la page, cliquez sur **Supprimer**.
3. Si vous n’envisagez pas de poursuivre le développement d’applications iOS, désinstallez Xcode.

<!--
For more code examples, see these guides:

*  [Manage authentication in Azure Maps](how-to-manage-authentication.md)
*  [Change map styles in iOS maps](Set%20map%20style%20%28iOS%20SDK%29.md)
*  [Add a symbol layer](Add%20a%20symbol%20layer%20%28iOS%20SDK%29.md)
*  [Add a line layer](Add%20a%20line%20layer%20to%20the%20map%20%28iOS%20SDK%29.md)
*  [Add a polygon layer](Add%20a%20polygon%20layer%20to%20the%20map%20%28iOS%20SDK%29.md)
-->

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé votre compte Azure Maps ainsi qu’une application de démonstration. Pour en savoir plus sur Azure Maps, consultez les tutoriels suivants :

> [!div class="nextstepaction"]
> [Charger des données GeoJSON dans Azure Maps](tutorial-load-geojson-file-android.md)
