---
title: 'Didacticiel : Détecter et encadrer des visages sur une image à l’aide d’Android SDK'
titleSuffix: Azure Cognitive Services
description: Dans ce tutoriel, vous créez une application Android simple qui utilise l’API Visage pour détecter et encadrer des visages dans une image.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 07/12/2018
ms.author: pafarley
ms.openlocfilehash: 99b2734745df722f45443b5347ae6dd054c8aa31
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957035"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Didacticiel : créer une application Android pour détecter et encadrer des visages humains dans une image

Dans ce didacticiel, vous créez une simple application Android qui utilise la bibliothèque de classes Java de l’Api Visage pour détecter des visages humains dans une image. L’application affiche une image sélectionnée avec chaque visage détecté encadré par un rectangle. L’exemple de code complet est disponible sur GitHub à l’adresse [Détecter et encadrer des visages dans une image sur Android](https://github.com/Azure-Samples/cognitive-services-face-android-sample).

![Capture d’écran Android d’une photo avec des visages encadrés par un rectangle rouge](../Images/android_getstarted2.1.PNG)

Ce didacticiel vous explique les procédures suivantes :

> [!div class="checklist"]
> - Créer une application Android
> - Installer la bibliothèque de client de l’API Visage
> - Utiliser la bibliothèque de client pour détecter les visages dans une image
> - Tracer un cadre autour de chaque visage détecté

## <a name="prerequisites"></a>Prérequis

- Vous avez besoin d’une clé d’abonnement pour exécuter l’exemple. Vous pouvez obtenir des clés d’abonnement d’essai à partir de la page [Essayez Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
- [Android Studio](https://developer.android.com/studio/) au minimum SDK 22 (requis par la bibliothèque de client de l’API Visage).
- La bibliothèque de client de l’API Visage[com.microsoft.projectoxford:face:1.4.3](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.microsoft.projectoxford%22) de Maven. Il n’est pas nécessaire de télécharger le package. Vous trouverez ci-dessous des instructions d’installation.

## <a name="create-the-project"></a>Création du projet

Créez votre projet d’application Android en suivant ces étapes :

1. Ouvrez Android Studio. Ce didacticiel utilise Android Studio 3.1.
1. Sélectionnez **Commencer un nouveau projet Android Studio**.
1. Sur l’écran **Créer un projet Android**, modifiez les champs par défaut, si nécessaire, puis cliquez sur **Suivant**.
1. Sur l’écran **Cibler des appareils Android**, utilisez le sélecteur de menu déroulant pour choisir **API 22** ou une version ultérieure, puis cliquez sur **Suivant**.
1. Sélectionnez **Activité vide**, puis cliquez sur **Suivant**.
1. Décochez la case **Compatibilité descendante**, puis cliquez sur **Terminer**.

## <a name="create-the-ui-for-selecting-and-displaying-the-image"></a>Créer l’interface utilisateur pour sélectionner et afficher l’image

Ouvrez *activity_main.xml*; pour voir l’Éditeur de mise en page. Sélectionnez l’onglet **Texte**, puis remplacez le contenu par le code suivant.

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <ImageView
        android:layout_width="match_parent"
        android:layout_height="fill_parent"
        android:id="@+id/imageView1"
        android:layout_above="@+id/button1"
        android:contentDescription="Image with faces to analyze"/>

    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Browse for face image"
        android:id="@+id/button1"
        android:layout_alignParentBottom="true"/>
</RelativeLayout>
```

Ouvrez *MainActivity.java*, puis remplacez tout sauf la première instruction `package` par le code suivant.

Le code définit un gestionnaire d’événements sur le `Button` qui démarre une nouvelle activité pour permettre à l’utilisateur de sélectionner une image. Une fois sélectionnée, l’image est affichée dans le `ImageView`.

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

public class MainActivity extends Activity {
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
                Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
                intent.setType("image/*");
                startActivityForResult(Intent.createChooser(
                        intent, "Select Picture"), PICK_IMAGE);
            }
        });

        detectionProgressDialog = new ProgressDialog(this);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK &&
                data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(
                        getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);

                // Uncomment
                //detectAndFrame(bitmap);
                } catch (IOException e) {
                    e.printStackTrace();
                }
        }
    }
}
```

Maintenant, votre application peut rechercher une photo et l’afficher dans la fenêtre, comme illustré ci-dessous.

![Capture d’écran Android d’une photo avec des visages](../Images/android_getstarted1.1.PNG)

## <a name="configure-the-face-client-library"></a>Configurer la bibliothèque de client de l’API Visage

L’API Visage est une API cloud que vous pouvez appeler à l’aide de requêtes HTTPS. Ce didacticiel utilise la bibliothèque de client de l’API Visage, qui encapsule ces requêtes web, pour simplifier votre travail.

Dans le volet **Projet**, utilisez le sélecteur du menu déroulant pour sélectionner **Android**. Développez **Scripts Gradle**, puis ouvrez *build.gradle (Module: app)*.

Ajoutez une dépendance de la bibliothèque de client de l’API Visage, `com.microsoft.projectoxford:face:1.4.3`, comme indiqué dans la capture d’écran ci-dessous, puis cliquez sur **Synchroniser maintenant**.

![Capture d’écran Android Studio du fichier d’application build.gradle](../Images/face-tut-java-gradle.png)

Ouvrez **MainActivity.java** et ajoutez les directives d’importation suivantes :

```java
import com.microsoft.projectoxford.face.*;
import com.microsoft.projectoxford.face.contract.*;
```

## <a name="add-the-face-client-library-code"></a>Ajouter le code de la bibliothèque de client de l’API Visage

Insérez le code suivant dans la classe `MainActivity` au-dessus de la méthode `onCreate` :

```java
private final String apiEndpoint = "<API endpoint>";
private final String subscriptionKey = "<Subscription Key>";

private final FaceServiceClient faceServiceClient =
        new FaceServiceRestClient(apiEndpoint, subscriptionKey);
```

Remplacez `<API endpoint>` par le point de terminaison d’API qui a été assigné à votre clé. Les clés d’abonnement d’essai sont générées dans la région **westcentralus**. Par conséquent, si vous utilisez une clé d’abonnement d’essai, l’instruction serait :

```java
apiEndpoint = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0";
```

Remplacez `<Subscription Key>` par votre clé d’abonnement. Par exemple : 

```java
subscriptionKey = "0123456789abcdef0123456789ABCDEF"
```

Dans le volet **Projet**, développez **Application**, puis **Manifestes**, et ouvrez le fichier *AndroidManifest.xml*.

Insérez l’élément suivant en tant qu’enfant direct de l’élément `manifest` :

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Générez votre projet pour vérifier les erreurs. Vous êtes désormais prêt à appeler le service de l’API Visage.

## <a name="upload-an-image-to-detect-faces"></a>Charger une image pour détecter les visages

La façon la plus simple pour détecter les visages consiste à appeler la méthode `FaceServiceClient.detect`. Cette méthode inclut dans un wrapper la méthode d’API [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) et retourne un tableau de `Face`.

Chaque visage renvoyé `Face` inclut un rectangle pour indiquer son emplacement, combiné avec une série d’attributs de visage facultatifs. Dans cet exemple, seuls les emplacements de visage sont nécessaires.

Si une erreur se produit, une `AlertDialog` affiche la raison sous-jacente.

Insérez les méthodes suivantes dans la classe `MainActivity`.

```java
// Detect faces by uploading a face image.
// Frame faces after detection.
private void detectAndFrame(final Bitmap imageBitmap) {
    ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
    imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
    ByteArrayInputStream inputStream =
            new ByteArrayInputStream(outputStream.toByteArray());

    AsyncTask<InputStream, String, Face[]> detectTask =
            new AsyncTask<InputStream, String, Face[]>() {
                String exceptionMessage = "";

                @Override
                protected Face[] doInBackground(InputStream... params) {
                    try {
                        publishProgress("Detecting...");
                        Face[] result = faceServiceClient.detect(
                                params[0],
                                true,         // returnFaceId
                                false,        // returnFaceLandmarks
                                null          // returnFaceAttributes:
                                /* new FaceServiceClient.FaceAttributeType[] {
                                    FaceServiceClient.FaceAttributeType.Age,
                                    FaceServiceClient.FaceAttributeType.Gender }
                                */
                        );
                        if (result == null){
                            publishProgress(
                                    "Detection Finished. Nothing detected");
                            return null;
                        }
                        publishProgress(String.format(
                                "Detection Finished. %d face(s) detected",
                                result.length));
                        return result;
                    } catch (Exception e) {
                        exceptionMessage = String.format(
                                "Detection failed: %s", e.getMessage());
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

private void showError(String message) {
    new AlertDialog.Builder(this)
    .setTitle("Error")
    .setMessage(message)
    .setPositiveButton("OK", new DialogInterface.OnClickListener() {
            public void onClick(DialogInterface dialog, int id) {
        }})
    .create().show();
}
```

## <a name="frame-faces-in-the-image"></a>Encadrer les visages dans l’image

Insérez les méthodes d’assistance suivantes dans la classe `MainActivity`. Cette méthode dessine un rectangle autour de chaque visage détecté.

```java
private static Bitmap drawFaceRectanglesOnBitmap(
        Bitmap originalBitmap, Face[] faces) {
    Bitmap bitmap = originalBitmap.copy(Bitmap.Config.ARGB_8888, true);
    Canvas canvas = new Canvas(bitmap);
    Paint paint = new Paint();
    paint.setAntiAlias(true);
    paint.setStyle(Paint.Style.STROKE);
    paint.setColor(Color.RED);
    paint.setStrokeWidth(10);
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

Effectuez les méthodes `AsyncTask`, indiquées par les commentaires `TODO`, dans la méthode `detectAndFrame`. En cas de réussite, l’image sélectionnée s’affiche avec les visages encadrés dans le `ImageView`.

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
    if(!exceptionMessage.equals("")){
        showError(exceptionMessage);
    }
    if (result == null) return;
    ImageView imageView = findViewById(R.id.imageView1);
    imageView.setImageBitmap(
            drawFaceRectanglesOnBitmap(imageBitmap, result));
    imageBitmap.recycle();
}
```

Enfin, dans la méthode `onActivityResult`, supprimez les marques de commentaire de l’appel à la méthode `detectAndFrame`, comme indiqué ci-dessous.

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    if (requestCode == PICK_IMAGE && resultCode == RESULT_OK &&
                data != null && data.getData() != null) {
        Uri uri = data.getData();
        try {
            Bitmap bitmap = MediaStore.Images.Media.getBitmap(
                    getContentResolver(), uri);
            ImageView imageView = findViewById(R.id.imageView1);
            imageView.setImageBitmap(bitmap);

            // Uncomment
            detectAndFrame(bitmap);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## <a name="run-the-app"></a>Exécution de l'application

Exécutez l’application et recherchez une image contenant un visage. Patientez pendant quelques secondes pour laisser à l’API Visage le temps de répondre. Après cela, vous obtiendrez un résultat similaire à l’image ci-dessous :

![GettingStartAndroid](../Images/android_getstarted2.1.PNG)

## <a name="summary"></a>Résumé

Dans ce didacticiel, vous avez appris le processus de base pour l’utilisation de l’API Visage, et créé une application pour afficher des visages encadrés dans une image.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la détection et l’utilisation des points de repère du visage.

> [!div class="nextstepaction"]
> [Comment détecter des visages dans une image](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)

Explorez les API Visage utilisées pour détecter des visages et leurs attributs tels que la pose, le sexe, l’âge, la pose de tête, la barbe, et les lunettes.

> [!div class="nextstepaction"]
> [Référence à l'API Visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).