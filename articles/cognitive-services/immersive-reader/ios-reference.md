---
title: Référence sur le SDK iOS du Lecteur immersif
titlesuffix: Azure Cognitive Services
description: Informations de référence sur le SDK iOS du Lecteur immersif
services: cognitive-services
author: MeganRoach
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 08/01/2019
ms.author: t-meroa
ms.openlocfilehash: acdaaf0bf08644053e86343ae4b002002fee6a84
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966595"
---
# <a name="immersive-reader-sdk-reference"></a>Référence du SDK du Lecteur immersif

Le SDK iOS du Lecteur immersif est un CocoaPod Swift qui vous permet d’intégrer le lecteur immersif à votre application iOS.

## <a name="functions"></a>Fonctions

Le SDK expose une seule fonction, `launchImmersiveReader(navController, token, subdomain, content, options, onSuccess, onFailure)`.

### <a name="launchimmersivereader"></a>launchImmersiveReader

Lance le Lecteur immersif en démarrant un contrôleur d’affichage dans votre application iOS.

```swift
public func launchImmersiveReader(navController: UINavigationController, token: String, subdomain: String, content: Content, options: Options?, onSuccess: @escaping () -> Void, onFailure: @escaping (_ error: Error) -> Void)
```

#### <a name="parameters"></a>parameters

| Nom | type | Description |
| ---- | ---- |------------ |
| `navController` | UINavigationController | Contrôleur de navigation de l’application iOS à partir de laquelle la fonction est appelée. |
| `token` | Chaîne | Le jeton d’authentification Azure AD. Consultez le [guide pratique sur l’authentification Azure AD](./azure-active-directory-authentication.md). |
| `subdomain` | Chaîne | Sous-domaine personnalisé de votre ressource Lecteur immersif dans Azure. Consultez le [guide pratique sur l’authentification Azure AD](./azure-active-directory-authentication.md). |
| `content` | [Contenu](#content) | Objet dans lequel figure le contenu à afficher dans le lecteur immersif. |
| `options` | [Options](#options) | Options de configuration de certains comportements du lecteur immersif. facultatif. |
| `onSuccess` | () -> Void | Fermeture qui est appelée lorsque le Lecteur immersif est lancé. |
| `onFailure` | (_ error: [Error](#error)) -> Void | Fermeture qui est appelée lorsque le chargement du Lecteur immersif échoue. Cette fermeture retourne un objet [`Error`](#error) qui représente le code d’erreur et le message d’erreur associés à l’échec. Pour plus d’informations, consultez les [codes d’erreur](#error-codes). |

## <a name="types"></a>Types

### <a name="content"></a>Contenu

Contient le contenu à afficher dans le lecteur immersif.

```swift
struct Content: Encodable {
    var title: String
    var chunks: [Chunk]
}
```

#### <a name="supported-mime-types"></a>Types MIME pris en charge

| Type MIME | Description |
| --------- | ----------- |
| texte/brut | Texte brut. |
| application/mathml+xml | Langage de balisage mathématique (MathML). [Plus d’informations](https://developer.mozilla.org/en-US/docs/Web/MathML)

### <a name="options"></a>Options

Contient les propriétés qui configurent certains comportements du lecteur immersif.

```swift
struct Options {
    var uiLang: String?
    var timeout: TimeInterval?
}
```

### <a name="error"></a>Error

Contient des informations sur l'erreur.

```swift
struct Error {
    var code: String
    var message: String
}
```

#### <a name="error-codes"></a>Codes d’erreur

| Code | Description |
| ---- | ----------- |
| BadArgument | L'argument fourni n’est pas valide. Pour plus d'informations, voir `message`. |
| Délai d'expiration | Le lecteur immersif n'a pas été chargé dans le délai spécifié. |
| TokenExpired | Le jeton fourni a expiré. |
| Throttled | Le taux d’appels maximal a été dépassé. |
| InternalError | Une erreur interne s’est produite dans le contrôleur d’affichage du Lecteur immersif. Pour plus d’informations, consultez `message`.|

## <a name="os-version-support"></a>Versions de système d’exploitation prises en charge

Le SDK iOS du Lecteur immersif est pris en charge par iOS 9.0 et versions ultérieures, sur iPad et iPhone.

## <a name="next-steps"></a>Étapes suivantes

* Explorer le [SDK iOS du Lecteur immersif sur GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS)
* [Démarrage rapide : Créer une application iOS qui lance le Lecteur immersif (Swift)](./ios-quickstart.md)