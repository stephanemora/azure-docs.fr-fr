---
title: Journalisation du SDK Speech – Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez-en plus sur l’activation de la journalisation dans le SDK Speech (C++, C#, Python, Objective-C, Java).
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 611d41b166a283dffd36729c0f4516ff80ecd35f
ms.sourcegitcommit: 2cb7772f60599e065fff13fdecd795cce6500630
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108803077"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Activer la journalisation dans le SDK Speech

La journalisation dans un fichier est une fonctionnalité facultative pour le SDK Speech. Pendant le développement, la journalisation fournit des informations et des diagnostics supplémentaires en provenance des composants de base du SDK Speech. Elle peut être activée en attribuant à la propriété `Speech_LogFilename` d’un objet de configuration Speech l’emplacement et le nom du fichier journal. La journalisation est gérée par une classe statique dans la bibliothèque native du kit de développement logiciel (SDK) Speech. Vous pouvez activer la journalisation pour n’importe quelle instance de module de reconnaissance ou de synthétiseur du SDK Speech. Toutes les instances dans le même processus écrivent leurs entrées de journal dans le même fichier journal.

> [!NOTE]
> La journalisation est disponible depuis la version 1.4.0 du SDK Speech dans tous les langages de programmation pris en charge par le SDK Speech, à l’exception de JavaScript.

## <a name="sample"></a>Exemple

Le nom du fichier journal est spécifié dans un objet de configuration. En prenant `SpeechConfig` comme exemple et en supposant que vous avez créé une instance appelée `config` :

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

Vous pouvez créer un module de reconnaissance à partir de l’objet de configuration. La journalisation est alors activée pour tous les modules de reconnaissance.

> [!NOTE]
> Si vous créez un `SpeechSynthesizer` à partir de l’objet de configuration, la journalisation n’est pas activée. S’il s’avère qu’elle est quand même activée, vous recevez aussi les diagnostics de `SpeechSynthesizer`.

## <a name="create-a-log-file-on-different-platforms"></a>Créer un fichier journal sur différentes plateformes

Pour Windows ou Linux, le fichier journal peut figurer dans n’importe quel chemin pour lequel l’utilisateur dispose d’une autorisation d’accès en écriture. Les autorisations d’accès en écriture aux emplacements du système de fichiers des autres systèmes d’exploitation peuvent être limitées ou restreintes par défaut.

### <a name="universal-windows-platform-uwp"></a>Plateforme Windows universelle (UWP)

Les applications UWP doivent placer les fichiers journaux à l’un des emplacements de données d’application (locaux, itinérants ou temporaires). Un fichier journal peut être créé dans le dossier d’application local :

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Vous trouverez des informations supplémentaires sur l’autorisation d’accès aux fichiers pour les applications UWP [ici](/windows/uwp/files/file-access-permissions).

### <a name="universal-windows-platform-uwp-on-unity"></a>Plateforme Windows universelle (UWP) sur Unity

Dans une application Unity et UWP, un fichier journal peut être créé dans le dossier persistant de l’application comme suit :

```csharp
#if ENABLE_WINMD_SUPPORT
        string logFile = Application.persistentDataPath + "/logFile.txt";
        config.SetProperty(PropertyId.Speech_LogFilename, logFile);
#endif
```

### <a name="android"></a>Android

Vous pouvez enregistrer un fichier journal dans un espace de stockage interne, externe ou dans le répertoire du cache. Les fichiers créés dans l’espace de stockage interne ou dans le répertoire du cache sont privés pour l’application. Il est préférable de créer un fichier journal dans un espace de stockage externe.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

Le code ci-dessus enregistre un fichier journal dans l’espace de stockage externe à la racine d’un répertoire propre à l’application. Un utilisateur peut accéder au fichier avec le gestionnaire de fichiers (généralement dans `Android/data/ApplicationName/logfile.txt`). Le fichier est supprimé quand l’application est désinstallée.

Vous devez aussi demander une autorisation `WRITE_EXTERNAL_STORAGE` dans le fichier manifeste :

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Vous trouverez des informations supplémentaires sur le stockage de données et de fichiers pour les applications Android [ici](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Seuls les répertoires à l’intérieur du bac à sable d’application sont accessibles. Des fichiers peuvent être créés dans les répertoires documents, library et temp. Les fichiers contenus dans le répertoire documents peuvent être mis à la disposition d’un utilisateur. L’extrait de code suivant illustre la création d’un fichier journal dans le répertoire documents de l’application :

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Pour accéder à un fichier créé, ajoutez les propriétés ci-dessous à la liste de propriétés `Info.plist` de l’application :

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Vous trouverez des informations complémentaires sur le système de fichiers iOS [ici](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer nos exemples sur GitHub](https://aka.ms/csspeech/samples)
