---
title: Fonctionnalités de style de carte dans Azure Maps | Microsoft Docs
description: En savoir plus sur Azure Maps style liés des fonctionnalités du SDK Android.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3c8c5d4bae16d8e15c8f2c5b1cc8e00eb14e4ce3
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870971"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Définir le style de carte à l’aide du Kit de développement logiciel Android d’Azure Maps

Cet article vous montre deux façons de définir des styles de carte à l’aide d’Azure Maps Android SDK. Azure Maps a le choix de six styles différents mappages. Pour plus d’informations sur les styles de carte pris en charge, consultez [pris en charge des styles de carte dans Azure Maps](./supported-map-styles.md).


## <a name="prerequisites"></a>Conditions préalables

Pour terminer le processus dans cet article, vous devez installer [Azure Maps du Kit Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) charger une carte.


## <a name="set-map-style-in-the-layout"></a>Définir le style de carte dans la disposition

Vous pouvez définir un style de carte dans le fichier de disposition pour votre classe d’activité. Modifier **res > Disposition > activity_main.xml**, afin qu’il ressemble à celle présentée ci-dessous :

```XML
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    >

    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/mapcontrol"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

Le `mapcontrol_style` attribut ci-dessus définit le style de carte **grayscale_dark**. 

<center>

![style-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Définir le style de carte dans la classe d’activité

Style de carte peut être définie dans la classe d’activité. Copier l’extrait de code suivant dans le **onCreate()** méthode de votre `MainActivity.java` classe. Ceci définit le style de carte **satellite_road_labels**.

```Java
    mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![style-satellite-road-labels](./media/set-android-map-styles/satellite-road-labels.png)</center>