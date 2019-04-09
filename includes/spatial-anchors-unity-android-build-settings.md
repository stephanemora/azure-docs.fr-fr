---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 33c932c36cd6de730d3768d596a214c442d74ae1
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632849"
---
Ouvrez **Build Settings** (Paramètres de build) en sélectionnant **File** (Fichier) > **Build Settings** (Paramètres de build).

Dans la section **Platform**, sélectionnez **Android**. Remplacez le **système de build** par **Gradle** et sélectionnez **Export Project**.

Sélectionnez **Switch Platform** (Changer de plateforme) pour passer à la plateforme **Android**. Unity peut vous demander d’installer des composants de prise en charge Android s’ils sont manquants.

![Fenêtre des paramètres de build dans Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Fermez la fenêtre **Build Settings**.

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Télécharger et importer le kit SDK ARCore pour Unity

Téléchargez le fichier `unitypackage` à partir des [Mises en production du kit SDK ARCore pour Unity 1.5](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0). De retour dans le projet Unity, sélectionnez **Assets** (Ressources) > **Import Package** > **Custom Package**, puis sélectionnez le fichier `unitypackage` que vous avez téléchargé précédemment. Dans la boîte de dialogue **Import Unity Package**, vérifiez que tous les fichiers sont sélectionnés avant de sélectionner **Import**.
