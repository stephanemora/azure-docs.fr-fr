---
title: Fonctions Bicep - date
description: Décrit les fonctions à utiliser dans un fichier Bicep pour travailler avec les dates.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 3f8715dd0dc1931e9086fd94e99661fb9cfda2d1
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026373"
---
# <a name="date-functions-for-bicep"></a>Fonctions de date pour Bicep

Resource Manager fournit les fonctions ci-après pour utiliser les dates dans votre fichier Bicep :

* [dateTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Ajoute une durée à une valeur de base. Le format ISO 8601 est attendu.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| base | Oui | string | Valeur datetime de début pour l’ajout. Utilisez le [format d’horodatage ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
| duration | Oui | string | Valeur de temps à ajouter à la base. Il peut s’agir d’une valeur négative. Utilisez le [format de durée ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| format | Non | string | Format de sortie pour le résultat de date et d’heure. S’il n’est pas fourni, le format de la valeur de base est utilisé. Utilisez des [chaînes de format standard](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou des [chaînes de format personnalisé](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Valeur retournée

Valeur datetime qui résulte de l’ajout de la valeur de durée à la valeur de base.

### <a name="examples"></a>Exemples

L’exemple suivant montre différentes façons d’ajouter des valeurs de temps.

```bicep
param baseTime string = utcNow('u')

var add3Years = dateTimeAdd(baseTime, 'P3Y')
var subtract9Days = dateTimeAdd(baseTime, '-P9D')
var add1Hour = dateTimeAdd(baseTime, 'PT1H')

output add3YearsOutput string = add3Years
output subtract9DaysOutput string = subtract9Days
output add1HourOutput string = add1Hour
```

Lorsque le modèle précédent est déployé avec la valeur de temps de base `2020-04-07 14:53:14Z`, la sortie est la suivante :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| add3YearsOutput | String | 7/4/2023 14:53:14 |
| subtract9DaysOutput | String | 29/3/2020 14:53:14 |
| add1HourOutput | String | 7/4/2023 15:53:14 |

L’exemple suivant montre comment définir l’heure de début d’une planification Automation.

```bicep
param omsAutomationAccountName string = 'demoAutomation'
param scheduleName string = 'demSchedule1'
param baseTime string = utcNow('u')

var startTime = dateTimeAdd(baseTime, 'PT1H')

...

resource scheduler 'Microsoft.Automation/automationAccounts/schedules@2015-10-31' = {
  name: concat(omsAutomationAccountName, '/', scheduleName)
  properties: {
    description: 'Demo Scheduler'
    startTime: startTime
    interval: 1
    frequency: 'Hour'
  }
}
```

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Retourne la valeur de date/heure (UTC) actuelle au format spécifié. Si aucun format n’est indiqué, le format utilisé est ISO 8601 (`yyyyMMddTHHmmssZ`). **Cette fonction peut uniquement être utilisée dans la valeur par défaut d’un paramètre.**

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| format |Non |string |Valeur encodée de l’URI à convertir en une chaîne. Utilisez des [chaînes de format standard](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou des [chaînes de format personnalisé](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Notes

Vous pouvez uniquement utiliser cette fonction dans une expression pour la valeur par défaut d’un paramètre. Son utilisation partout ailleurs dans un fichier Bicep retourne une erreur. La fonction n’est pas autorisée dans d’autres parties du fichier Bicep, car elle retourne une valeur différente chaque fois qu’elle est appelée. Le déploiement du même fichier Bicep avec les mêmes paramètres ne produit pas forcément les mêmes résultats.

Si vous choisissez l’[option de restauration en cas d’erreur](../templates/rollback-on-error.md) d’un déploiement antérieur ayant abouti et que celui-ci comprend un paramètre qui utilise utcNow, le paramètre n’est pas réévalué. Au lieu de cela, la valeur du paramètre du déploiement précédent est automatiquement réutilisée dans le déploiement de la restauration.

Soyez prudent quand vous redéployez un fichier Bicep basé sur la fonction utcNow pour une valeur par défaut. Si vous effectuez un tel déploiement sans fournir de valeur pour le paramètre, la fonction est réévaluée. Si vous souhaitez mettre à jour une ressource existante au lieu d’en créer une autre, passez la valeur du paramètre qui était utilisée dans le déploiement précédent.

### <a name="return-value"></a>Valeur retournée

Valeur de date/heure UTC actuelle.

### <a name="examples"></a>Exemples

L’exemple suivant montre des formats différents pour la valeur de date/heure.

```bicep
param utcValue string = utcNow()
param utcShortValue string = utcNow('d')
param utcCustomValue string = utcNow('M d')

output utcOutput string = utcValue
output utcShortOutput string = utcShortValue
output utcCustomOutput string = utcCustomValue
```

La sortie de l’exemple précédent varie pour chaque déploiement, mais elle sera semblable à celle-ci :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| utcOutput | string | 20190305T175318Z |
| utcShortOutput | string | 03/05/2019 |
| utcCustomOutput | string | 3 5 |

L’exemple suivant montre comment utiliser une valeur de la fonction pour définir une valeur de balise.

```bicep
param utcShort string = utcNow('d')
param rgName string

resource myRg 'Microsoft.Resources/resourceGroups@2020-10-01' = {
  name: rgName
  location: 'westeurope'
  tags: {
    createdDate: utcShort
  }
}

output utcShortOutput string = utcShort
```

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir une description des sections d’un fichier Bicep, consultez [Comprendre la structure et la syntaxe des fichiers Bicep](./file.md).
