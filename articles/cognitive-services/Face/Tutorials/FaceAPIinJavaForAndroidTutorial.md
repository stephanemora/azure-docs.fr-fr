---
title: Didacticiel sur l’API Visage Java pour Android | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Créer une application Android simple qui utilise l’API Visage de Cognitive Services pour détecter et encadrer des visages humains dans une photo.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 5164a261d482d0cca3842a973d2109b17999bd25
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370380"
---
# <a name="getting-started-with-face-api-in-java-for-android-tutorial"></a>Didacticiel de prise en main de l’API Visage en Java pour Android

Ce didacticiel explique comment créer et développer une application Android simple qui appelle l’API Visage pour détecter des visages humains dans une image. L’application affiche le résultat en encadrant les visages qu’elle détecte.

![GettingStartedAndroid](../Images/android_getstarted2.1.PNG)

## <a name="preparation"></a> Préparation

Pour suivre ce didacticiel, vous devez disposer des éléments suivants :

- Android Studio et Kit de développement logiciel installés
- Appareil Android (facultatif pour tester).

## <a name="step1"></a>Étape 1 : s’abonner à l’API Visage et obtenir la clé d’abonnement

Avant d’utiliser une API Visage, vous devez vous inscrire pour vous abonner à l’API Visage via le portail Microsoft Cognitive Services. Voir [abonnements](https://azure.microsoft.com/try/cognitive-services/). Dans le cadre de ce didacticiel, vous pouvez utiliser une clé primaire ou secondaire.

## <a name="step2"></a>Étape 2 : créer l’infrastructure d’application

Dans le cadre de cette étape, vous allez créer un projet d’application Android visant à implémenter l’interface utilisateur de base pour la sélection et l’affichage d’image. Suivez simplement les instructions ci-dessous : 

1. Ouvrez Android Studio.
2. Dans le menu Fichier, cliquez sur **Nouveau projet...**
3. Nommez l’application **MyFirstApp**, puis cliquez sur Suivant. 

    ![GettingStartAndroidNewProject](../Images/AndroidNewProject.png)

4. Choisissez la plateforme cible appropriée, puis cliquez sur Suivant. 

    ![GettingStartAndroidNewProject2](../Images/AndroidNewProject2.png)

5. Sélectionnez **Activité de base**, puis cliquez sur Suivant.
6. Nommez l’activité comme suit, puis cliquez sur Terminer. 

    ![GettingStartAndroidNewProject4](../Images/AndroidNewProject4.png)

7. Ouvrez **activity_main.xml** pour voir l’Éditeur de mise en page de cette activité.
8. Affichez le fichier texte source, puis modifiez la disposition de l’activité comme suit :

    ```xml
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
        android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
        android:paddingRight="@dimen/activity_horizontal_margin"
        android:paddingTop="@dimen/activity_vertical_margin"
        android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">
     
        <ImageView
            android:layout_width="match_parent"
            android:layout_height="fill_parent"
            android:id="@+id/imageView1"
            android:layout_above="@+id/button1" />
    
        <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Browse"
            android:id="@+id/button1"
            android:layout_alignParentBottom="true" />
    </RelativeLayout>
    ```  

9. Ouvrez **MainActivity.java**, puis insérez les directives d’importation suivantes au début du fichier :

    ```java
    import java.io.*; 
    import android.app.*; 
    import android.content.*; 
    import android.net.*; 
    import android.os.*; 
    import android.view.*; 
    import android.graphics.*; 
    import android.widget.*; 
    import android.provider.*;
    ```
      
    Ensuite, modifiez la classe comme suit :  
    
    ```java
    private final int PICK_IMAGE = 1;
    private ProgressDialog detectionProgressDialog;
         
    @Override
    protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);
           Button button1 = (Button)findViewById(R.id.button1);
           button1.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                Intent gallIntent = new Intent(Intent.ACTION_GET_CONTENT);
                gallIntent.setType("image/*");
                startActivityForResult(Intent.createChooser(gallIntent, "Select Picture"), PICK_IMAGE);
            }
        });
         
        detectionProgressDialog = new ProgressDialog(this);
    }
    
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK && data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);
                } catch (IOException e) {
                e.printStackTrace();
                }
        }
    }
    ```

Maintenant, votre application peut rechercher une photo dans la galerie et l’afficher dans la fenêtre, comme illustré ci-dessous :

![GettingStartAndroidUI](../Images/android_getstarted1.1.PNG)

## <a name="step3"></a>Étape 3 : configurer la bibliothèque de client de l’API Visage

L’API Visage est une API cloud que vous pouvez appeler à l’aide d’une requête HTTPS. Afin de faciliter l’utilisation de l’API Visage dans des applications de la plateforme .NET, une bibliothèque de client est également fournie pour encapsuler les requêtes web. Dans cet exemple, nous utilisons la bibliothèque de client pour simplifier le travail. 

Suivez les instructions ci-dessous pour configurer la bibliothèque de client : 

1. Recherchez le fichier **build.gradle** de niveau supérieur de votre projet dans le panneau Projet présenté dans l’exemple. Notez qu’il existe quelques autres fichiers **build.gradle** dans l’arborescence de votre projet, et que vous devez commencer par ouvrir le fichier **build.gradle** de niveau supérieur.
2. Ajoutez **mavenCentral()** aux référentiels de votre projet. Vous pouvez également utiliser jcenter(), qui est le référentiel par défaut d’Android Studio, car il s’agit d’un sur-ensemble de mavenCentral().  

```
    allprojects {
        repositories {
            ...
            mavenCentral()
        }
    }
```

3. Ouvrez le fichier **build.gradle** dans votre projet « app ».
4. Ajoutez une dépendance pour notre bibliothèque de client stockée dans le référentiel Central Maven :

```
    dependencies {  
        ...  
        implementation 'com.microsoft.projectoxford:face:1.4.3'  
    }
```

5. Ouvrez **MainActivity.java** dans votre projet « app », et insérez les directives d’importation suivantes : 
    
    ```java
    import com.microsoft.projectoxford.face.*;  
    import com.microsoft.projectoxford.face.contract.*;  
    ```
    
   Ensuite, insérez le code suivant dans la classe :

    ```java
    private FaceServiceClient faceServiceClient = new FaceServiceRestClient("your API endpoint", "<Subscription Key>");
    ```

   Remplacez le premier paramètre ci-dessus par le point de terminaison d’API qui a été attribué à votre clé à l’étape 1. Par exemple : 
   
        https://eastus2.api.cognitive.microsoft.com/face/v1.0
   
   Remplacez le deuxième paramètre par la clé d’abonnement que vous avez obtenue à l’étape 1.
   
6. Ouvrez le fichier **AndroidManifest.xml** dans votre projet « app ». Insérez l’élément suivant en tant qu’enfant de l’élément **manifest** :  

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />  
    ```

7. Vous êtes maintenant prêt à appeler l’API Visage à partir de votre application. 

## <a name="step4"></a>Étape 4 : charger des images pour détecter des visages

La façon la plus simple de détecter des visages consiste à appeler l’API [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) en chargeant directement le fichier image. Lorsque vous utilisez la bibliothèque de client, cela est possible en utilisant la méthode asynchrone **DetectAsync** de la classe **FaceServiceClient**. Chaque visage renvoyé est entouré d’un rectangle pour indiquer son emplacement, combiné avec une série d’attributs de visage facultatifs. Dans cet exemple, nous devons uniquement récupérer l’emplacement du visage. Nous devons à présent insérer une méthode dans la casse **MainActivity** pour la détection des visages : 

```java

    // Detect faces by uploading face images
    // Frame faces after detection
    
    private void detectAndFrame(final Bitmap imageBitmap)
    {
        ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
        imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
        ByteArrayInputStream inputStream = 
            new ByteArrayInputStream(outputStream.toByteArray());
        AsyncTask<InputStream, String, Face[]> detectTask =
            new AsyncTask<InputStream, String, Face[]>() {
                @Override
                protected Face[] doInBackground(InputStream... params) {
                    try {
                        publishProgress("Detecting...");
                        Face[] result = faceServiceClient.detect(
                                params[0], 
                                true,         // returnFaceId
                                false,        // returnFaceLandmarks
                                null           // returnFaceAttributes: a string like "age, gender"
                /* If you want value of FaceAttributes, try adding 4th argument like below.
                            new FaceServiceClient.FaceAttributeType[] {
                    FaceServiceClient.FaceAttributeType.Age,
                    FaceServiceClient.FaceAttributeType.Gender }
                */              
                        );
                        if (result == null)
                        {
                            publishProgress("Detection Finished. Nothing detected");
                            return null;
                        }
                        publishProgress(
                                String.format("Detection Finished. %d face(s) detected",
                                        result.length));
                        return result;
                    } catch (Exception e) {
                        publishProgress("Detection failed");
                        return null;
                    }
                }
                @Override
                protected void onPreExecute() {
                    //TODO: show progress dialog
                }
                @Override
                protected void onProgressUpdate(String... progress) {
                    //TODO: update progress
                }
                @Override
                protected void onPostExecute(Face[] result) {
                    //TODO: update face frames
                }
            };
        detectTask.execute(inputStream);
    }
```

## <a name="step5"></a>Étape 5 : marquer les visages dans l’image

Dans le cadre de cette dernière étape, nous allons combiner toutes les étapes ci-dessus et marquer les visages détectés à l’aide de cadres dans l’image. Tout d’abord, ouvrez **MainActivity.java**, puis insérez une méthode d’assistance pour dessiner des rectangles : 

```java
    private static Bitmap drawFaceRectanglesOnBitmap(Bitmap originalBitmap, Face[] faces) {
        Bitmap bitmap = originalBitmap.copy(Bitmap.Config.ARGB_8888, true);
        Canvas canvas = new Canvas(bitmap);
        Paint paint = new Paint();
        paint.setAntiAlias(true);
        paint.setStyle(Paint.Style.STROKE);
        paint.setColor(Color.RED);
        int stokeWidth = 2;
        paint.setStrokeWidth(stokeWidth);
        if (faces != null) {
            for (Face face : faces) {
                FaceRectangle faceRectangle = face.faceRectangle;
                canvas.drawRect(
                        faceRectangle.left,
                        faceRectangle.top,
                        faceRectangle.left + faceRectangle.width,
                        faceRectangle.top + faceRectangle.height,
                        paint);
            }
        }
        return bitmap;
    }
```

Finissez à présent les parties TODO dans la méthode **detectAndFrame** afin d’encadrer les visages et de rapporter l’état.

```java
    @Override
    protected void onPreExecute() {
        detectionProgressDialog.show();
    }
    @Override
    protected void onProgressUpdate(String... progress) {
        detectionProgressDialog.setMessage(progress[0]);
    }
    @Override
    protected void onPostExecute(Face[] result) {
        detectionProgressDialog.dismiss();
        if (result == null) return;
        ImageView imageView = (ImageView)findViewById(R.id.imageView1);
        imageView.setImageBitmap(drawFaceRectanglesOnBitmap(imageBitmap, result));
        imageBitmap.recycle();
    }
```
 
Enfin, ajoutez un appel à la méthode **detectAndFrame** à partir de la méthode **onActivityResult**, comme ci-dessous. 

```java
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK && data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);
     
                // This is the new addition.
                // detectAndFrame(bitmap);
     
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

Exécutez cette application et recherchez une image contenant un visage. Patientez pendant quelques secondes pour laisser à l’API cloud le temps de répondre. Après cela, vous obtiendrez un résultat similaire à l’image ci-dessous : 

![GettingStartAndroid](../Images/android_getstarted2.1.PNG)

## <a name="summary"></a> Résumé

Dans ce didacticiel, vous avez appris le processus de base pour l’utilisation de l’API Visage, et créé une application pour afficher des marques de visage dans des images. Pour plus d’informations sur l’API Visage, consultez le guide et les [informations de référence sur l’API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236). 

## <a name="related"></a> Didacticiels associés

- [Didacticiel sur la prise en main de l’API Visage dans CSharp](FaceAPIinCSharpTutorial.md)
- [Didacticiel sur la prise en main de l’API Visage dans Python](FaceAPIinPythonTutorial.md)
