---
title: Enregistrement de kit de développement logiciel Speech - Services de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Activer la journalisation dans le Speech SDK.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: amishu
ms.openlocfilehash: 75eaea22c4809eda78e54514961d13113b4a5f3a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59012447"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Activer la journalisation dans le Speech SDK

La journalisation au fichier est une fonctionnalité facultative pour le Speech SDK. Au cours du développement journalisation fournit des informations supplémentaires et des diagnostics à partir de composants de base de la Speeck SDK. Il peut être activé en définissant la propriété `Speech_LogFilename` sur un objet de configuration de reconnaissance vocale à l’emplacement et le nom du fichier journal. Journalisation sera activée dans le monde entier une fois qu’un module de reconnaissance est créé à partir de cette configuration et ne peut pas être désactivée par la suite. Vous ne pouvez pas modifier le nom d’un fichier journal pendant une session de journalisation en cours d’exécution.

> [!NOTE]
> La journalisation est disponible dans le Kit de développement de Speech pris en charge tous les langages de programmation, à l’exception de JavaScript.

## <a name="sample"></a>Exemple

Le nom du fichier journal est spécifié sur un objet de configuration. En prenant le `SpeechConfig` comme exemple et en supposant que vous avez créé une instance appelée `config`:

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

Vous pouvez créer un module de reconnaissance à partir de l’objet de configuration. Cela active la journalisation pour tous les modules de reconnaissance.

> [!NOTE]
> Si vous créez un `SpeechSynthesizer` à partir de l’objet de configuration, il n’activera pas la journalisation. Si la journalisation est activée Cependant, vous recevrez également des diagnostics à partir de la `SpeechSynthesizer`.

## <a name="create-a-log-file-on-different-platforms"></a>Créer un fichier journal sur différentes plateformes

Pour Windows ou Linux, le fichier journal peut être dans n’importe quel chemin d’accès de que l’utilisateur a accès en écriture. Autorisations d’écriture sur les emplacements de système de fichiers dans d’autres systèmes d’exploitation peuvent être limitées ou restreinte par défaut.

### <a name="universal-windows-platform-uwp"></a>Plateforme Windows universelle

Applications de UWP doivent être les emplacements des fichiers journaux dans un des emplacements de données d’application (locales, itinérants ou temporaires). Un fichier journal peut être créé dans le dossier d’application local :

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Plus l’autorisation pour les applications UWP est disponible sur l’accès aux fichiers [ici](https://docs.microsoft.com/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

Vous pouvez enregistrer un fichier journal pour le stockage interne, un stockage externe ou le répertoire de cache. Fichiers créés dans le stockage interne ou le répertoire de cache sont privés à l’application. Il est préférable de créer un fichier journal dans un stockage externe.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

Le code ci-dessus sera enregistrer un fichier journal dans le stockage externe à la racine d’un répertoire spécifique à l’application. Un utilisateur peut accéder au fichier avec le Gestionnaire de fichiers (généralement dans `Android/data/ApplicationName/logfile.txt`). Le fichier sera supprimé lorsque l’application est désinstallée.

Vous devez également demander `WRITE_EXTERNAL_STORAGE` autorisation dans le fichier manifeste :

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Plus le stockage pour les applications Android est disponible sur les données et le fichier [ici](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Seuls les répertoires dans le bac à sable d’application sont accessibles. Fichiers peuvent être créés dans les documents, bibliothèque et répertoires temporaires. Fichiers dans le répertoire documents peuvent être accessibles à un utilisateur. L’extrait de code suivant illustre la création d’un fichier journal dans le répertoire de document d’application :

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Pour accéder à un fichier créé, ajoutez les propriétés à ci-dessous le `Info.plist` liste de propriétés de l’application :

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Plus système de fichiers est disponible sur iOS [ici](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer nos exemples sur GitHub](https://aka.ms/csspeech/samples)

