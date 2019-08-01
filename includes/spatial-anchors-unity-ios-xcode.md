---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: d8b6b1bfcbceb1168d0f74c73e72bd42b41bb2ec
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562499"
---
Sélectionnez **Build**. Dans la boîte de dialogue qui s’ouvre, sélectionnez un dossier de destination pour exporter le projet Xcode.

À l’issue de l’exportation, un dossier avec le projet Xcode exporté s’affiche.

> [!NOTE]
> Si une fenêtre vous demande de choisir remplacer ou ajouter, nous vous recommandons de sélectionner **Append** (Ajouter), car c’est plus rapide. Vous devez sélectionner **Replace** seulement si vous changez des ressources dans votre scène. (Par exemple, si vous ajoutez, supprimer ou changer des relations parent/enfant, ou si vous ajoutez, supprimer ou changer des propriétés.) Si vous changez seulement le code source, **Append** doit suffire.

### <a name="convert-the-xcode-project-to-xcworkspace-containing-azure-spatial-anchors-references"></a>Convertir le projet Xcode en xcworkspace contenant les références Azure Spatial Anchors

Dans le dossier du projet Xcode exporté, exécutez cette commande dans le Terminal pour installer les CocoaPods nécessaires au projet :

```bash
pod install --repo-update
```

À présent, vous pouvez ouvrir `Unity-iPhone.xcworkspace` pour ouvrir le projet dans Xcode :

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Si l’erreur `library not found for -lPods-Unity-iPhone` s’affiche, vous avez probablement ouvert le fichier `.xcodeproj` à la place de `.xcworkspace`. 

Sélectionnez le nœud racine **Unity-iPhone** pour voir les paramètres du projet, puis sélectionnez l’onglet **General**.

Sous **Signing** (Signature), vérifiez que **Automatically manage signing** (Gérer automatiquement la signature) est activé. Sinon, activez-le, puis sélectionnez **Enable Automatic** (Activer la signature automatique) dans la boîte de dialogue qui s’affiche pour réinitialiser les paramètres de build.

Sous **Deployment Info**, assurez-vous que **Deployment Target** est défini sur `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Déployer l’application sur votre appareil iOS

Connectez l’appareil iOS au Mac et définissez le **schéma actif** sur votre appareil iOS.

![Sélectionner l’appareil](./media/spatial-anchors-unity/select-device.png)

Sélectionnez **Build and then run the current scheme**.

![Déployer et exécuter](./media/spatial-anchors-unity/deploy-run.png)