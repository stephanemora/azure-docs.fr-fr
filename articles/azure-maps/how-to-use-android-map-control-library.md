---
title: Bien démarrer avec le contrôle de carte Android | Microsoft Azure Maps
description: Familiarisez-vous avec l’Android SDK Azure Maps. Apprenez à créer un projet dans Android Studio, à installer le Kit de développement logiciel (SDK) et à créer un mappage interactif.
author: stevemunk
ms.author: v-munksteve
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: eriklind
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 137b0d56a566d2e62f5559a30b391e6418139e59
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005069"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Bien démarrer avec Android SDK Azure Maps

Android SDK Azure Maps est une bibliothèque de cartes de vecteur pour Android. Cet article vous guide tout au long des processus d’installation d’Android SDK Azure Maps et de chargement d’une carte.

## <a name="prerequisites"></a>Prérequis

Veillez à suivre la procédure de l’article [Démarrage rapide : Créer une application Android](quick-android-map.md).

## <a name="localizing-the-map"></a>Localisation de la carte

Le kit Android SDK Azure Maps propose trois méthodes pour définir la langue et l’affichage régional de la carte. Le code suivant montre comment définir la langue sur le français (« fr-FR ») et l'affichage régional sur « Auto ».

1. Transmettre les informations sur la langue et l’affichage régional à la classe `AzureMaps` en utilisant les propriétés statiques `setLanguage` et `setView`. Cette opération définit les propriétés de langue et d’affichage régional par défaut dans votre application.
    
    ::: zone pivot="programming-language-java-android"
    
    ```java
    static {
        //Alternatively use Azure Active Directory authenticate.
        AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
    
        //Set your Azure Maps Key.
        //AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");   
    
        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");
    
        //Set the regional view to be used by Azure Maps.
        AzureMaps.setView("Auto");
    }
    ```
    
    ::: zone-end
    
    ::: zone pivot="programming-language-kotlin"
    
    ```kotlin
    companion object {
        init {
            //Alternatively use Azure Active Directory authenticate.
            AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
    
            //Set your Azure Maps Key.
            //AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");
        
            //Set the language to be used by Azure Maps.
            AzureMaps.setLanguage("fr-FR");
        
            //Set the regional view to be used by Azure Maps.
            AzureMaps.setView("Auto");
        }
    }
    ```
    
    ::: zone-end

1. Vous pouvez également transmettre les informations sur la langue et l’affichage régional au XML du contrôle de carte.

    ```XML
    <com.azure.android.maps.control.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:azure_maps_language="fr-FR"
        app:azure_maps_view="Auto"
        />
    ```
1. La dernière manière de définir programmatiquement les propriétés de langue et de vue régionale utilise la méthode Maps `setStyle`. Cette opération peut être effectuée à tout moment pour changer la langue et l'affichage régional de la carte.

    ::: zone pivot="programming-language-java-android"

    ```java
    mapControl.onReady(map -> {
        map.setStyle(
            language("fr-FR"),
            view("Auto")
        );
    });
    ```

    ::: zone-end

    ::: zone pivot="programming-language-kotlin"

    ```kotlin
    mapControl.onReady(OnReady { map: AzureMap ->
        map.setStyle(
            language("fr-FR"),
            view("Auto")
        )
    })
    ```

    ::: zone-end

Voici un exemple Azure Maps dans lequel la langue est définie sur « fr-FR » et l'affichage régional sur « Auto ».

![Azure Maps, image de carte affichant des étiquettes en français](media/how-to-use-android-map-control-library/android-localization.png)

Pour obtenir la liste complète des langues et des affichages régionaux pris en charge, consultez [Prise en charge de la localisation dans Azure Maps](supported-languages.md).

## <a name="navigating-the-map"></a>Navigation dans la carte

Il existe plusieurs façons d’effectuer des zooms, des panoramiques, des rotations et des tangages de la carte. Les informations suivantes détaillent les différentes façons de naviguer dans la carte.

### <a name="zoom-the-map"></a>Faire un zoom sur la carte

* Touchez la carte avec deux doigts et rapprochez-les pour faire un zoom arrière ou écartez-les pour faire un zoom avant.
* Appuyez deux fois sur la carte pour faire un zoom avant d’un niveau.
* Appuyez deux fois avec deux doigts pour faire un zoom arrière d’un niveau de la carte.
* Appuyez deux fois ; sur le second appui, maintenez votre doigt sur la carte et faites glisser vers le haut pour effectuer un zoom avant, ou vers le bas pour effectuer un zoom arrière.

### <a name="pan-the-map"></a>Effectuer un panoramique sur la carte

* Touchez la carte et faites-la glisser dans n’importe quelle direction.

### <a name="rotate-the-map"></a>Faire pivoter la carte

* Touchez la carte avec deux doigts et effectuez une rotation.

### <a name="pitch-the-map"></a>Incliner la carte

* Touchez la carte avec deux doigts et faites-les glisser ensemble vers le haut ou vers le bas.

## <a name="azure-government-cloud-support"></a>Prise en charge du cloud Azure Government

Android SDK d’Azure Maps prend en charge le cloud Azure Government. Android SDK d’Azure Maps est accessible à partir du même référentiel Maven. Les tâches suivantes devront être effectuées pour se connecter à la version cloud d'Azure Government de la plateforme Azure Maps.

Au même endroit que les détails d’authentification Azure Maps sont spécifiés, ajoutez la ligne de code suivante pour indiquer à la carte d’utiliser le domaine du Cloud Azure Maps Government.

::: zone pivot="programming-language-java-android"

```java
AzureMaps.setDomain("atlas.azure.us");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
AzureMaps.setDomain("atlas.azure.us")
```

::: zone-end

Veillez à utiliser les détails d'authentification Azure Maps à partir de la plateforme du cloud Azure Government lors de l'authentification de la carte et des services.

## <a name="migrating-from-a-preview-version"></a>Migration à partir d’une préversion

Avec le passage de la préversion à la disponibilité générale, certaines modifications importantes ont été introduites dans le SDK Android Azure Maps. Voici les détails essentiels :

* L’identifiant Maven est passé de `"com.microsoft.azure.maps:mapcontrol:0.7"` à `"com.azure.android:azure-maps-control:1.0.0"`. L’espace de noms et le numéro de version principale ont été modifiés.
* L’espace de noms d’importation est passé de `com.microsoft.azure.maps.mapcontrol` à `com.azure.android.maps.control`
* Les noms de ressources pour les options XML, les ressources de couleur et les ressources d’image ont vu le texte `mapcontrol_` remplacé par `azure_maps_`.

    **Avant :**

    ```xml
    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_language="fr-FR"
        app:mapcontrol_view="Auto"
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
    />
    ```

    **Après :**

    ```xml
    <com.azure.android.maps.control.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:azure_maps_language="fr-FR"
        app:azure_maps_view="Auto"
        app:azure_maps_centerLat="47.602806"
        app:azure_maps_centerLng="-122.329330"
        app:azure_maps_zoom="12"
    />
    ```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment ajouter des données superposées sur la carte :

> [!div class="nextstepaction"]
> [Gérer l’authentification dans Azure Maps](how-to-manage-authentication.md)

> [!div class="nextstepaction"]
> [Change map styles in Android maps](set-android-map-styles.md) (Modifier les styles de carte dans les cartes Android)

> [!div class="nextstepaction"]
> [Ajouter une couche de symboles](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de lignes](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de polygones](how-to-add-shapes-to-android-map.md)
