---
title: Prise en main d’Azure AD v2 avec les applications de bureau Windows - Configuration | Microsoft Docs
description: Cet article explique comment une application de bureau Windows .NET (XAML) peut obtenir un jeton d’accès et appeler une API protégée par un point de terminaison Azure Active Directory v2.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/29/2020
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: 4c757b52e7378bffe4a4954022b131b772a337cf
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76897692"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Ajouter les informations d’inscription de l’application à votre application
Dans cette étape, vous devez ajouter l’ID d’application à votre projet.

1.  Ouvrez `App.xaml.cs` et remplacez la ligne contenant `ClientId` par :

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Étapes suivantes

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
