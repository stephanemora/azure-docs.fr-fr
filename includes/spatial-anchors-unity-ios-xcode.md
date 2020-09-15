---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/14/2020
ms.author: rgarcia
ms.openlocfilehash: b93243a537fafce6d865ec207b12dc2654cafd20
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/08/2020
ms.locfileid: "89536253"
---
Sélectionnez **Build**. Dans la boîte de dialogue qui s’ouvre, sélectionnez un dossier de destination pour exporter le projet Xcode.

À l’issue de l’exportation, un dossier avec le projet Xcode exporté s’affiche.

> [!NOTE]
> Si une fenêtre vous demande de choisir remplacer ou ajouter, nous vous recommandons de sélectionner **Append** (Ajouter), car c’est plus rapide. Vous devez sélectionner **Replace** seulement si vous changez des ressources dans votre scène. (Par exemple, si vous ajoutez, supprimer ou changer des relations parent/enfant, ou si vous ajoutez, supprimer ou changer des propriétés.) Si vous changez seulement le code source, **Append** doit suffire.

## <a name="open-the-xcode-project"></a>Ouvrir le projet Xcode

Vous pouvez maintenant ouvrir `Unity-iPhone.xcodeproj` dans Xcode. Vous pouvez soit lancer Xcode et ouvrir le projet `Unity-iPhone.xcodeproj` exporté, soit lancer le projet dans Xcode en exécutant la commande suivante à partir de l’emplacement où vous avez exporté le projet :

```bash
open ./Unity-iPhone.xcodeproj
```

Sélectionnez le nœud racine **Unity-iPhone** pour voir les paramètres du projet, puis sélectionnez l’onglet **General**.

Sous **Signing** (Signature), vérifiez que **Automatically manage signing** (Gérer automatiquement la signature) est activé. Sinon, activez-le, puis sélectionnez **Enable Automatic** (Activer la signature automatique) dans la boîte de dialogue qui s’affiche pour réinitialiser les paramètres de build.

Sous **Deployment Info**, assurez-vous que **Deployment Target** est défini sur `11.0`.

## <a name="deploy-the-app-to-your-ios-device"></a>Déployer l’application sur votre appareil iOS

Connectez l’appareil iOS au Mac et définissez le **schéma actif** sur votre appareil iOS.

![Sélectionner l’appareil](./media/spatial-anchors-unity/select-device.png)

Sélectionnez **Build and then run the current scheme**.

![Déployer et exécuter](./media/spatial-anchors-unity/deploy-run.png)
