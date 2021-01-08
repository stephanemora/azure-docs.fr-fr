---
title: 'Tutoriel : Charger des données GeoJSON dans le kit Android SDK Azure Maps | Microsoft Azure Maps'
description: Tutoriel expliquant comment charger un fichier de données GeoJSON dans le kit Android SDK Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b527cd7b3f841b6cb3dcf2dce6930f3bd9bcc184
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681236"
---
# <a name="tutorial-load-geojson-data-into-azure-maps-android-sdk"></a>Tutoriel : Charger des données GeoJSON dans le kit Android SDK Azure Maps

Ce tutoriel vous guide tout au long du processus d’importation d’un fichier GeoJSON de données de localisation dans le kit Android SDK Azure Maps. Dans ce tutoriel, vous allez découvrir comment :

> [!div class="checklist"]
> * Ajouter Azure Maps dans une application Android
> * Créer une source de données et charger un fichier GeoJSON à partir d’un fichier local ou du web
> * Afficher les données sur la carte

## <a name="prerequisites"></a>Prérequis

1. Suivez le [Démarrage rapide : Créer une application Android](quick-android-map.md). Ce tutoriel va étendre le code utilisé dans ce guide de démarrage rapide.
2. Téléchargez le fichier GeoJSON [Sample Points of Interest](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json).

### <a name="import-geojson-data-from-web-or-assets-folder"></a>Importer des données GeoJSON à partir d’un dossier web ou de ressources

La plupart des fichiers GeoJSON wrappent toutes les données dans une `FeatureCollection`. Sachant cela, si les fichiers GeoJSON sont chargés dans l’application sous la forme d’une chaîne, ils peuvent être passés dans la méthode `fromJson` statique de la collection de fonctionnalités, qui désérialisera la chaîne en un objet `FeatureCollection` GeoJSON pouvant être ajouté à la carte.

Les étapes suivantes expliquent comment importer un fichier GeoJSON dans l’application et le désérialiser en tant qu’objet `FeatureCollection` GeoJSON.

1. Suivez le [Démarrage rapide : Créer une application Android](quick-android-map.md), car les étapes qui suivent s’appuient sur cette application.
2. Dans le panneau de projet d’Android Studio, cliquez avec le bouton droit sur le dossier **app**, puis accédez à `New > Folder > Assets Folder`.
3. Glissez-déposez le fichier GeoJSON [Sample Points of Interest](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) dans le dossier des ressources.
4. Créez un fichier appelé **Utils.java**, puis ajoutez-y le code qui suit. Ce code fournit une méthode statique appelée `importData` qui importe de façon asynchrone un fichier à partir du dossier `assets` de l’application ou à partir du web à l’aide d’une URL sous forme de chaîne, puis le retourne au thread d’interface utilisateur à l’aide d’une méthode de rappel simple.

    ```java
    //Modify the package name as needed to align with your application.
    package com.example.myapplication;
    
    import android.content.Context;
    import android.os.Handler;
    import android.os.Looper;
    import android.webkit.URLUtil;
    
    import java.io.BufferedReader;
    import java.io.IOException;
    import java.io.InputStream;
    import java.io.InputStreamReader;
    import java.net.URL;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    
    import javax.net.ssl.HttpsURLConnection;
    
    public class Utils {
    
        interface SimpleCallback {
            void notify(String result);
        }
    
        /**
         * Imports data from a web url or asset file name and returns it to a callback.
         * @param urlOrFileName A web url or asset file name that points to data to load.
         * @param context The context of the app.
         * @param callback The callback function to return the data to.
         */
        public static void importData(String urlOrFileName, Context context, SimpleCallback callback){
            importData(urlOrFileName, context, callback, null);
        }
        
        /**
         * Imports data from a web url or asset file name and returns it to a callback.
         * @param urlOrFileName A web url or asset file name that points to data to load.
         * @param context The context of the app.
         * @param callback The callback function to return the data to.
         * @param error A callback function to return errors to.
         */
        public static void importData(String urlOrFileName, Context context, SimpleCallback callback, SimpleCallback error){
            if(urlOrFileName != null && callback != null) {
                ExecutorService executor = Executors.newSingleThreadExecutor();
                Handler handler = new Handler(Looper.getMainLooper());
    
                executor.execute(() -> {
                    String data = null;
    
                    try {
    
                        if(URLUtil.isNetworkUrl(urlOrFileName)){
                            data = importFromWeb(urlOrFileName);
                        } else {
                            //Assume file is in assets folder.
                            data = importFromAssets(context, urlOrFileName);
                        }
    
                        final String result = data;
    
                        handler.post(() -> {
                            //Ensure the resulting data string is not null or empty.
                            if (result != null && !result.isEmpty()) {
                                callback.notify(result);
                            } else {
                                error.notify("No data imported.");
                            }
                        });
                    } catch(Exception e) {
                        if(error != null){
                            error.notify(e.getMessage());
                        }
                    }
                });
            }
        }
    
        /**
         * Imports data from an assets file as a string.
         * @param context The context of the app.
         * @param fileName The asset file name.
         * @return
         * @throws IOException
         */
        private static String importFromAssets(Context context, String fileName) throws IOException {
            InputStream stream = null;
    
            try {
                stream = context.getAssets().open(fileName);
    
                if(stream != null) {
                    return readStreamAsString(stream);
                }
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                // Close Stream and disconnect HTTPS connection.
                if (stream != null) {
                    stream.close();
                }
            }
    
            return null;
        }
    
        /**
         * Imports data from the web as a string.
         * @param url URL to the data.
         * @return
         * @throws IOException
         */
        private static String importFromWeb(String url) throws IOException {
            InputStream stream = null;
            HttpsURLConnection connection = null;
            String result = null;
    
            try {
                connection = (HttpsURLConnection) new URL(url).openConnection();
    
                //For this use case, set HTTP method to GET.
                connection.setRequestMethod("GET");
    
                //Open communications link (network traffic occurs here).
                connection.connect();
    
                int responseCode = connection.getResponseCode();
                if (responseCode != HttpsURLConnection.HTTP_OK) {
                    throw new IOException("HTTP error code: " + responseCode);
                }
    
                //Retrieve the response body as an InputStream.
                stream = connection.getInputStream();
    
                if (stream != null) {
                    return readStreamAsString(stream);
                }
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                // Close Stream and disconnect HTTPS connection.
                if (stream != null) {
                    stream.close();
                }
                if (connection != null) {
                    connection.disconnect();
                }
            }
    
            return result;
        }
    
        /**
         * Reads an input stream as a string.
         * @param stream Stream to convert.
         * @return
         * @throws IOException
         */
        private static String readStreamAsString(InputStream stream) throws IOException {
            //Convert the contents of an InputStream to a String.
            BufferedReader in = new BufferedReader(new InputStreamReader(stream, "UTF-8"));
    
            String inputLine;
            StringBuffer response = new StringBuffer();
    
            while ((inputLine = in.readLine()) != null) {
                response.append(inputLine);
            }
    
            in.close();
    
            return response.toString();
        }
    }
    ```

5. Accédez au fichier **MainActivity.java**, puis ajoutez le code suivant dans le rappel de l’événement `mapControl.onReady`, qui se trouve à l’intérieur de la méthode `onCreate`. Ce code utilise l’utilitaire d’importation pour lire le fichier **SamplePoiDataSet.json** comme une chaîne, puis le désérialise comme une collection de fonctionnalités à l’aide de la méthode statique `fromJson` de la classe `FeatureCollection`. Ce code calcule également le cadre englobant pour toutes les données de la collection de fonctionnalités, et utilise les résultats pour indiquer à l’appareil photo de la carte de faire un focus sur les données.

    ```java
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);
    
    //Import the GeoJSON data and add it to the data source.
    Utils.importData("SamplePoiDataSet.json",
        this,
        (String result) -> {
            //Parse the data as a GeoJSON Feature Collection.
            FeatureCollection fc = FeatureCollection.fromJson(result);
    
            //Add the feature collection to the data source.
            source.add(fc);
    
            //Optionally, update the maps camera to focus in on the data.
    
            //Calculate the bounding box of all the data in the Feature Collection.
            BoundingBox bbox = MapMath.fromData(fc);
    
            //Update the maps camera so it is focused on the data.
            map.setCamera(
                bounds(bbox),

                //Padding added to account for pixel size of rendered points.
                padding(20)
            );
        });
    ```

6. Maintenant que nous avons du code pour charger les données GeoJSON dans la carte à l’aide d’une source de données, nous devons spécifier comment ces données doivent être affichées sur la carte. Il existe différents calques de rendu pour les données de point. Les calques les plus couramment utilisés sont le [calque de bulles](map-add-bubble-layer-android.md), le [calque de symboles](how-to-add-symbol-to-android-map.md) et le [calque de carte thermique](map-add-heat-map-layer-android.md). Ajoutez le code suivant pour afficher les données dans un calque de bulles, dans le rappel de l’événement `mapControl.onReady`, après le code permettant d’importer les données.

    ```java
    //Create a layer and add it to the map.
    BubbleLayer layer = new BubbleLayer(source);
    map.layers.add(layer);
    ```

7. Exécutez l'application. Une carte s’affichera avec au centre les États-Unis et des cercles superposés pour chaque localisation du fichier GeoJSON.

    ![Carte des États-Unis montrant les données contenues dans un fichier GeoJSON](media/tutorial-load-geojson-file-android/android-import-geojson.png)


## <a name="clean-up-resources"></a>Nettoyer les ressources

Suivez les étapes ci-dessous pour nettoyer les ressources de ce tutoriel :

1. Fermez Android Studio, puis supprimez l’application que vous avez créée.
2. Si vous avez testé l’application sur un appareil externe, désinstallez l’application de cet appareil.

## <a name="next-steps"></a>Étapes suivantes

Pour bénéficier de davantage d’exemples de code et d’une expérience de codage interactive :

> [!div class="nextstepaction"]
> [Utiliser des expressions de style basées sur les données](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Afficher des informations sur les caractéristiques](display-feature-information-android.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de symboles](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de lignes](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de polygones](how-to-add-shapes-to-android-map.md)
