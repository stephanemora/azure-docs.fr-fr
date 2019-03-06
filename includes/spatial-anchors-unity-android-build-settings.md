---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 662aced6df27febdf29f2645725962763e89cfa2
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752551"
---
Ouvrez Unity, puis ouvrez le projet au dossier `Unity`.

Ouvrez **Build Settings** (Paramètres de build) en sélectionnant **File** -> **Build Settings**.

Dans la section **Platform**, sélectionnez **Android**. Ensuite, remplacez le **système de build** par **Gradle** et cochez l’option **Export Project**.

Sélectionnez **Switch Platform** (Changer de plateforme) pour passer à la plateforme **Android**. Unity peut vous demander d’installer des composants de prise en charge d’Android s’ils sont manquants.

![Paramètres de build Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Fermez la fenêtre **Build Settings**.

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Télécharger et importer le kit SDK ARCore pour Unity

Téléchargez le fichier `unitypackage` à partir des [Mises en production du kit SDK ARCore pour Unity](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0). De retour dans le projet Unity, sélectionnez **Assets** (Ressources) -> **Import Package** -> **Custom Package...** , puis sélectionnez le fichier `unitypackage` que vous avez téléchargé précédemment. Dans la boîte de dialogue **Import Unity Package**, vérifiez que tous les fichiers sont sélectionnés avant de sélectionner **Import**.
