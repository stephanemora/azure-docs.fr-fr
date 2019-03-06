---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b802c9dbd0cef65325cb03538b68b49c57b85bb3
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56890969"
---
Sélectionnez **Build** pour ouvrir une boîte de dialogue. Ensuite, sélectionnez un dossier pour exporter le projet Xcode.

À la fin de l’exportation, un dossier s’affiche contenant le projet Xcode exporté.

### <a name="open-the-xcode-project"></a>Ouvrir le projet Xcode

Dans le dossier du projet Xcode exporté, exécutez la commande suivante pour installer les CocoaPods nécessaires au projet :

```bash
pod install --repo-update
```

À présent, vous pouvez ouvrir `Unity-iPhone.xcworkspace` pour ouvrir le projet dans Xcode :

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Si l’erreur `library not found for -lPods-Unity-iPhone` s’affiche, vous avez probablement ouvert le fichier `.xcodeproj` à la place de `.xcworkspace`. Ouvrez `.xcworkspace` et réessayez.

Sélectionnez le nœud racine **Unity-iPhone** pour afficher les paramètres du projet, puis sélectionnez l’onglet **General**.

Sous **Signing**, sélectionnez **Automatically manage signing** (Gérer automatiquement la signature). Sélectionnez **Enable Automatic** (Rendre automatique) dans la boîte de dialogue qui s’affiche pour réinitialiser les paramètres de build.

Sous **Deployment Info**, assurez-vous que **Deployment Target** est défini sur `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Déployer l’application sur votre appareil iOS

Connectez l’appareil iOS au Mac et définissez le **schéma actif** sur votre appareil iOS.

![Sélectionner l’appareil](./media/spatial-anchors-unity/select-device.png)

Sélectionnez **Build and then run the current scheme**.

![Déployer et exécuter](./media/spatial-anchors-unity/deploy-run.png)