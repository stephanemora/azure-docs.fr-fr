---
title: 'Démarrage rapide : Créer une application HoloLens avec Unity et MRTK'
description: Ce démarrage rapide explique comment générer une application HoloLens en utilisant MRTK et Object Anchors.
author: craigktreasure
manager: virivera
services: azure-object-anchors
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 5cccfb2b750e9ea1a167f8bbbddcb2621bd19b5b
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112008081"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity-with-mrtk"></a>Démarrage rapide : Créer une application HoloLens avec Azure Object Anchors, dans Unity avec MRTK

Ce guide de démarrage rapide explique comment créer une application HoloLens Unity qui utilise [Azure Object Anchors](../overview.md). Azure Object Anchors est un service cloud géré qui convertit les ressources 3D en modèles IA qui permettent des expériences de réalité mixte prenant en charge les objets pour HoloLens. Lorsque vous aurez terminé, vous disposerez d’une application HoloLens générée avec Unity, capable de détecter des objets dans le monde physique.

Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Préparer les paramètres de build Unity.
> * Exporter le projet HoloLens Visual Studio.
> * Déployer l’application et l’exécuter sur un appareil HoloLens 2.

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

## <a name="open-the-sample-project"></a>Ouvrir l’exemple de projet

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

Dans Unity, ouvrez le projet `quickstarts/apps/unity/mrtk`.

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

Quand une boîte de dialogue « Importateur TMP » vous invite à importer des ressources TextMesh Pro, sélectionnez « Importer TMP Essentials » pour ce faire.
:::image type="content" source="./media/textmesh-pro-importer-dialog.png" alt-text="Importer des ressources TextMesh Pro":::

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

### <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

Allumez l’appareil, sélectionnez **Toutes les applications**, puis localisez et démarrez l’application. Après l’écran de démarrage de Unity, vous devrez voir un rectangle englobant blanc. Vous pouvez utiliser votre main pour déplacer, mettre à l’échelle ou faire pivoter le rectangle englobant. Positionnez celui-ci de façon à couvrir l’objet que vous souhaitez détecter.

Ouvrez le <a href="https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_HandMenu.html" target="_blank">menu de la main</a> et sélectionnez **Verrouiller la zone de recherche** pour empêcher le déplacement du rectangle englobant. Sélectionnez **Démarrer la recherche** pour démarrer la détection d’objets. Une fois l’objet est détecté, un maillage s’affiche sur l’objet. Les détails d’une instance détectée s’affichent à l’écran, tels que l’horodatage mis à jour et le ratio de couverture des surfaces. Sélectionnez **Arrêter la recherche** pour arrêter le suivi. Toutes les instances détectées seront supprimées.

#### <a name="the-app-menus"></a>Menus de l’application

Vous pouvez également effectuer d’autres actions à l’aide du <a href="https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_HandMenu.html" target="_blank">menu de la main</a>.

##### <a name="primary-menu"></a>Menu principal

* **Démarrer la recherche/Arrêter la recherche** : démarre ou arrête le processus de détection d’objet.
* **Activer/désactiver le mappage spatial** : afficher/masquer le rendu de mappage spatial. Cette option peut être utilisée pour déterminer si l’analyse est terminée ou non.
* **Paramètres du dispositif de suivi** : active/désactive le menu des paramètres du dispositif de suivi.
* **Paramètres de la zone de recherche** : active/désactive le menu des paramètres de la zone de recherche.
* **Démarrer le suivi** : capture les données de diagnostic et les enregistre sur l’appareil. Pour plus d’informations, consultez la section **Déboguer les problèmes de détection et capturer les diagnostics**.
* **Charger le suivi** : charge les données de diagnostics dans le service Object Anchors. Un utilisateur doit fournir son compte d’abonnement dans `subscription.json` et le charger dans le dossier `LocalState`. Un exemple de fichier `subscription.json` figure ci-dessous.

    :::image type="content" source="./media/mrtk-hand-menu-primary.png" alt-text="Menu de la main principal de Unity":::

##### <a name="tracker-settings-menu"></a>Menu des paramètres du dispositif de suivi

* **Haute précision** : fonctionnalité expérimentale utilisée pour obtenir une position plus précise. L’activation de cette option nécessite davantage de ressources système lors de la détection d’objet. Le maillage de l’objet s’affiche en rose dans ce mode. Cliquez de nouveau sur ce bouton pour revenir au mode de suivi normal.
* **Alignement vertical souple** : permet de détecter un objet à un angle non vertical. Utile pour la détection d’objets sur des pentes.
* **Autoriser le changement d’échelle** : permet au dispositif de suivi de modifier la taille de l’objet détecté en fonction d’informations environnementales.
* **Curseur de ratio de couverture** : ajuste la proportion des points de surface qui doivent correspondre pour que le dispositif de suivi détecte un objet.  Des valeurs inférieures permettent au dispositif de suivi de mieux détecter des objets à détecter pour les capteurs HoloLens, tels que des objets sombres ou très réfléchissants. Des valeurs plus élevées réduisent la fréquence des fausses détections.

    :::image type="content" source="./media/mrtk-hand-menu-tracker.png" alt-text="Menu de la main du dispositif de suivi de Unity":::

##### <a name="search-area-settings-menu"></a>Menu des paramètres de zone de recherche

* **Verrouiller la zone de recherche** : verrouille le rectangle englobant de la zone de recherche pour empêcher tout déplacement accidentel résultant d’un mouvement de main.
* **Ajuster automatiquement la zone de recherche** : permet que la zone de recherche se repositionne d’elle-même lors de la détection d’objet.
* **Cycle de maillage** : parcourt la visualisation des maillages chargés dans la zone de recherche.  Cette option permet aux utilisateurs d’aligner la zone de recherche pour détecter des objets difficiles à détecter.

    :::image type="content" source="./media/mrtk-hand-menu-search-area.png" alt-text="Menu de la main de la zone de recherche de Unity":::

Exemple `subscription.json`:

```json
{
  "AccountId": "<your account id>",
  "AccountKey": "<your account key>",
  "AccountDomain": "<your account domain>"
}
```

[!INCLUDE [Unity setup Windows Device Portal](../../../includes/object-anchors-quickstart-unity-setup-device-portal.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Concepts : Vue d’ensemble du Kit de développement logiciel (SDK)](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [FORUM AUX QUESTIONS](../faq.md)

> [!div class="nextstepaction"]
> [Kit SDK de conversion](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
