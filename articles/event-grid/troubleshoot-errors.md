---
title: Azure Event Grid - Guide de résolution des problèmes
description: Cet article fournit une liste de codes d’erreur avec les messages d’erreur correspondants, leur description et les actions recommandées.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 1ab9aeac0bde21e229fdb57b7ad02d5d48471551
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645070"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Résoudre les erreurs liées à Azure Event Grid
Cet article fournit une liste de codes d’erreur relatifs à Azure Event Grid, avec les messages d’erreur correspondants, leur description et les actions recommandées si vous rencontrez ces erreurs. 

## <a name="error-code-400"></a>Code d’erreur : 400
| Code d'erreur | Message d’erreur | Description | Recommandation |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.BadRequest<br/>400 | Le nom de la rubrique doit compter 3 à 50 caractères. | La longueur du nom de la rubrique personnalisée doit être comprise entre 3 et 50 caractères. Seuls les caractères alphanumériques, les chiffres et le caractère « - » sont autorisés dans le nom de la rubrique. De plus, le nom ne doit pas commencer par les mots réservés suivants : <ul><li>Microsoft</li><li>EventGrid</li><li>Système</li></ul> | Choisissez un autre nom conforme aux exigences relatives aux noms de rubrique. |
| HttpStatusCode.BadRequest<br/>400 | Le nom de domaine doit compter 3 à 50 caractères. | La longueur du nom de domaine doit être comprise entre 3 et 50 caractères. Seuls les caractères alphanumériques, les chiffres et le caractère « - » sont autorisés dans le nom de la rubrique. De plus, le nom ne doit pas commencer par les mots réservés suivants :<ul><li>Microsoft</li><li>EventGrid</li><li>Système</li> | Choisissez un autre nom conforme aux exigences relatives aux noms de domaine. |
| HttpStatusCode.BadRequest<br/>400 | Délai d’expiration non valide. | Le délai d’expiration de l’abonnement aux événements détermine à quel moment l’abonnement aux événements sera mis hors service. Cette valeur doit être une valeur DateHeure valide dans le futur.| Assurez-vous que le délai d’expiration de l’abonnement aux événements respecte un format DateHeure valide et qu’il est défini dans le futur. |

## <a name="error-code-409"></a>Code d’erreur : 409
| Code d'erreur | Message d’erreur | Description | Action recommandée |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.Conflict <br/>409 | Une rubrique existe déjà sous le nom spécifié. Choisissez un autre nom de rubrique.   | Le nom de la rubrique personnalisée doit être unique au sein d’une même région Azure pour que l’opération de publication s’effectue correctement. Il est possible d’utiliser le même nom dans des régions Azure différentes. | Choisissez un autre nom de rubrique. |
| HttpStatusCode.Conflict <br/> 409 | Un domaine existe déjà sous le nom spécifié. Choisissez un autre nom de domaine. | Le nom de domaine doit être unique au sein d’une même région Azure pour que l’opération de publication s’effectue correctement. Il est possible d’utiliser le même nom dans des régions Azure différentes. | Choisissez un autre nom de domaine. |
| HttpStatusCode.Conflict<br/>409 | Limite du quota atteinte. Pour plus d’informations sur ces limites, consultez [Limites d’Azure Event Grid](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits).  | Le nombre de ressources Azure Event Grid que peut utiliser chaque abonnement Azure est limité. Tout ou partie de ce quota a été dépassé et aucune ressource supplémentaire n’a pu être créée. |    Vérifiez l’utilisation des ressources actuelles et supprimez celles qui ne sont pas nécessaires. Si vous avez quand même besoin d’augmenter votre quota, envoyez un e-mail à [aeg@microsoft.com](mailto:aeg@microsoft.com) en indiquant le nombre exact de ressources nécessaires. |


## <a name="next-steps"></a>Étapes suivantes
Si vous avez besoin d’une aide supplémentaire, publiez votre problème sur le forum [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou ouvrez un [ticket de support](https://azure.microsoft.com/support/options/). 
