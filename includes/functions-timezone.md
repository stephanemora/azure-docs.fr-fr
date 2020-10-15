---
ms.openlocfilehash: dba7a3cc7a68d360fd6e56511b71ae364f624646
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89569274"
---
Le fuseau horaire par défaut utilisé avec les expressions CRON est le Temps universel coordonné (UTC). Pour baser votre expression CRON sur un autre fuseau horaire, créez un paramètre d’application nommé `WEBSITE_TIME_ZONE` pour votre application de fonction. 

La valeur de ce paramètre dépend du système d’exploitation et du plan sur lequel l’application de fonction s’exécute.

|Système d’exploitation |Plan |Valeur |
|-|-|-|
| **Windows** |Tous | Définissez la valeur sur le nom du fuseau horaire souhaité comme indiqué dans l’[index des fuseaux horaires de Microsoft](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10)). |
| **Linux** |Premium<br/>Dédié |Définissez la valeur sur le nom du fuseau horaire souhaité comme indiqué dans la [base de données tz](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). |

> [!NOTE]
> `WEBSITE_TIME_ZONE` n’est pas pris en charge sur le plan consommation Linux.

Par exemple, *Heure standard de l’Est* (Windows) ou *America/New_York* (Linux) correspond à UTC-05:00. Pour que votre déclencheur de minuteur se déclenche à 10:00 AM est tous les jours, utilisez l’expression NCRONTAB suivante qui compte pour le fuseau horaire UTC :

```
"0 0 15 * * *"
``` 

Ou créez un paramètre d’application pour votre application de fonction nommé `WEBSITE_TIME_ZONE`, affectez la valeur `Eastern Standard Time` (Windows) ou `America/New_York` (Linux), puis utilisez l’expression NCRONTAB suivante : 

```
"0 0 10 * * *"
``` 

Quand vous utilisez `WEBSITE_TIME_ZONE`, l’heure est ajustée en fonction des changements d’heure du fuseau horaire spécifique (par exemple, pour tenir compte de l’heure d’été). 
