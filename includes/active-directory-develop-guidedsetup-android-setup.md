---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 3b1f705b14bdae43fb079f2039ca8c288dd6609e
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988331"
---
## <a name="set-up-your-project"></a>Configuration de votre projet

Voulez-vous télécharger le projet Android Studio de cet exemple ? [Téléchargez un projet](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip) et passez à [l’étape Configuration](#register-your-application) pour configurer l’exemple de code avant de l’exécuter.

### <a name="create-a-new-project"></a>Création d'un projet

1. Ouvrez Android Studio, puis sélectionnez **Fichier** > **Nouveau** > **Nouveau projet**.
2. Nommez votre application, puis sélectionnez **Suivant**.
3. Sélectionnez **API 21 ou plus récente (Android 5.0)**, puis sélectionnez **Suivant**.
4. Laissez **Activité vide** tel quel, sélectionnez **Suivant**, puis sélectionnez **Terminer**.

### <a name="add-msal-to-your-project"></a>Ajouter MSAL à votre projet

1. Dans Android Studio, sélectionnez **Scripts Gradle** > **build.gradle (Module : application)**.
2. Sous **Dépendances**, collez le code suivant :

    ```gradle  
    compile ('com.microsoft.identity.client:msal:0.1.+') {
        exclude group: 'com.android.support', module: 'appcompat-v7'
    }
    compile 'com.android.volley:volley:1.0.0'
    ```

<!--start-collapse-->
### <a name="about-this-package"></a>À propos de ce package

Le package dans le code précédent installe Microsoft Authentication Library (MSAL), qui gère toutes les opérations de jeton, y compris l’acquisition, la mise en cache, l’actualisation et la suppression. Les jetons sont nécessaires pour accéder aux API protégées par la plateforme d’identité Microsoft.
<!--end-collapse-->

## <a name="create-the-apps-ui"></a>Créer l’interface utilisateur de l’application

1. Accédez à **res** > **layout**, puis ouvrez **activity_main.xml**.
2. Modifiez la disposition de l’activité `android.support.constraint.ConstraintLayout` ou autre pour `LinearLayout`.
3. Ajoutez la propriété `android:orientation="vertical"` au nœud `LinearLayout`.
4. Collez le code suivant dans le nœud `LinearLayout`, en remplaçant le contenu actuel :

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```
