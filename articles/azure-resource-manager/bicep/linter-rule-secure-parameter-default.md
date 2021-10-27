---
title: Règle de linter - Paramètre de sécurité par défaut
description: Règle de linter - Paramètre de sécurité par défaut
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 139b19124f22d5cb42be71d6a6042830ad7809f7
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130166490"
---
# <a name="linter-rule---secure-parameter-default"></a>Règle de linter - Paramètre de sécurité par défaut

Cette règle recherche les valeurs par défaut codées en dur pour les paramètres sécurisés.

## <a name="returned-code"></a>Code renvoyé

`secure-parameter-default`

## <a name="solution"></a>Solution

Ne fournissez pas de valeur par défaut codée en dur pour un [paramètre sécurisé](./parameters.md#secure-parameters) dans votre fichier Bicep, sauf s’il s’agit d’une chaîne vide ou d’une expression appelant la fonction [newGuid()](./bicep-functions-string.md#newguid).

Vous utilisez l’élément décoratif `@secure()` sur des paramètres qui contiennent des valeurs sensibles, comme des mots de passe. Lorsqu’un paramètre utilise un élément décoratif sécurisé, sa valeur n’est pas enregistrée ou stockée dans l’historique de déploiement. Cette action empêche un utilisateur malveillant de découvrir la valeur sensible.

Toutefois, lorsque vous fournissez une valeur par défaut pour un paramètre sécurisé, cette valeur est détectable par quiconque peut accéder au modèle ou à l’historique de déploiement.

L’exemple suivant échoue à ce test, car le paramètre contient une valeur par défaut codée en dur.

```bicep
@secure()
param adminPassword string = 'HardcodedPassword'
```

Vous pouvez résoudre ce problème en supprimant la valeur par défaut.

```bicep
@secure()
param adminPassword string
```

Vous pouvez également fournir une chaîne vide pour la valeur par défaut.

```bicep
@secure()
param adminPassword string = ''
```

Vous pouvez aussi utiliser `newGuid()` pour générer la valeur par défaut.

```bicep
@secure()
param adminPassword string = newGuid()
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le linter, consultez [Utiliser le linter Bicep](./linter.md).
