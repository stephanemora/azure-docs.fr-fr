---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: mimart
ms.openlocfilehash: ef08f1adc15475cd5dd38548ed39dc57532c0ef5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185831"
---
## <a name="guidelines-for-using-javascript"></a>Instructions pour l’utilisation de JavaScript

Suivez ces instructions lorsque vous personnalisez l’interface de votre application à l’aide de JavaScript :

- Ne pas lier un événement Click aux éléments HTML `<a>`.
- Ne pas prendre de dépendance sur le code ou les commentaires Azure AD B2C.
- Ne pas modifier la séquence ou la hiérarchie des éléments HTML d’Azure AD B2C. Utiliser une stratégie Azure AD B2C pour contrôler l’ordre des éléments d’interface utilisateur.
- Vous pouvez appeler n’importe quel service RESTful en gardant ce qui suit à l’esprit :
    - Vous devrez peut-être définir les éléments CORS de votre service RESTful pour autoriser les appels HTTP côté client.
    - Assurez-vous que votre service RESTful est sécurisé et qu’il utilise uniquement le protocole HTTPS.
    - N’utilisez pas JavaScript directement pour appeler les points de terminaison Azure AD B2C.
- Vous pouvez incorporer votre JavaScript, ou vous pouvez définir un lien vers des fichiers JavaScript externes. Lorsque vous utilisez un fichier JavaScript externe, vérifiez que vous utilisez son URL absolue et pas une URL relative.
- Frameworks JavaScript :
    - Azure AD B2C utilise une version spécifique de jQuery. N’incluez pas une autre version de jQuery. L’utilisation de plusieurs versions sur la même page provoque des problèmes.
    - L’utilisation de RequireJS n’est pas prise en charge.
    - La plupart des frameworks JavaScript ne sont pas pris en charge par Azure AD B2C.
- Les paramètres d’Azure AD B2C peuvent être lus en appelant des objets `window.SETTINGS`, `window.CONTENT`, tels que la langue d’interface utilisateur actuelle. Ne modifiez pas la valeur de ces objets.
- Pour personnaliser le message d’erreur Azure AD B2C, utilisez la localisation dans une stratégie.
- Si un objectif peut être atteint à l’aide d’une stratégie, c’est généralement la méthode recommandée.
