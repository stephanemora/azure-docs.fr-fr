---
title: Azure Event Grid - Guide de résolution des problèmes
description: Cet article fournit une liste de codes d’erreur avec les messages d’erreur correspondants, leur description et les actions recommandées.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 1dd464339e7654f8886224ff07cf368b4724ff82
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041400"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Résoudre les erreurs liées à Azure Event Grid
Cet article fournit une liste de codes d’erreur relatifs à Azure Event Grid, avec les messages d’erreur correspondants, leur description et les actions recommandées si vous rencontrez ces erreurs. 

## <a name="error-code-400"></a>Code d’erreur : 400
| Code d'erreur | Message d’erreur | Description | Recommandation |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.BadRequest<br/>400 | Le nom de la rubrique doit compter 3 à 50 caractères. | La longueur du nom de la rubrique personnalisée doit être comprise entre 3 et 50 caractères. Seuls les caractères alphanumériques, les chiffres et le caractère « - » sont autorisés dans le nom de la rubrique. De plus, le nom ne doit pas commencer par les mots réservés suivants : <ul><li>Microsoft-</li><li>EventGrid-</li><li>System-</li></ul> | Choisissez un autre nom conforme aux exigences relatives aux noms de rubrique. |
| HttpStatusCode.BadRequest<br/>400 | Le nom de domaine doit compter 3 à 50 caractères. | La longueur du nom de domaine doit être comprise entre 3 et 50 caractères. Seuls les caractères alphanumériques, les chiffres et le caractère « - » sont autorisés dans le nom de domaine. De plus, le nom ne doit pas commencer par les mots réservés suivants :<ul><li>Microsoft-</li><li>EventGrid-</li><li>System-</li> | Choisissez un autre nom conforme aux exigences relatives aux noms de domaine. |
| HttpStatusCode.BadRequest<br/>400 | Délai d’expiration non valide. | Le délai d’expiration de l’abonnement aux événements détermine à quel moment l’abonnement aux événements sera mis hors service. Cette valeur doit être une valeur DateHeure valide dans le futur.| Assurez-vous que le délai d’expiration de l’abonnement aux événements respecte un format DateHeure valide et qu’il est défini dans le futur. |

## <a name="error-code-409"></a>Code d’erreur : 409
| Code d'erreur | Message d’erreur | Description | Action recommandée |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.Conflict <br/>409 | Une rubrique existe déjà sous le nom spécifié. Choisissez un autre nom de rubrique.   | Le nom de la rubrique personnalisée doit être unique au sein d’une même région Azure pour que l’opération de publication s’effectue correctement. Il est possible d’utiliser le même nom dans des régions Azure différentes. | Choisissez un autre nom de rubrique. |
| HttpStatusCode.Conflict <br/> 409 | Un domaine existe déjà sous le nom spécifié. Choisissez un autre nom de domaine. | Le nom de domaine doit être unique au sein d’une même région Azure pour que l’opération de publication s’effectue correctement. Il est possible d’utiliser le même nom dans des régions Azure différentes. | Choisissez un autre nom de domaine. |
| HttpStatusCode.Conflict<br/>409 | Limite du quota atteinte. Pour plus d’informations sur ces limites, consultez [Limites d’Azure Event Grid](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits).  | Le nombre de ressources Azure Event Grid que peut utiliser chaque abonnement Azure est limité. Tout ou partie de ce quota a été dépassé et aucune ressource supplémentaire n’a pu être créée. |    Vérifiez l’utilisation des ressources actuelles et supprimez celles qui ne sont pas nécessaires. Si vous avez quand même besoin d’augmenter votre quota, envoyez un e-mail à [aeg@microsoft.com](mailto:aeg@microsoft.com) en indiquant le nombre exact de ressources nécessaires. |

## <a name="error-code-403"></a>Code d’erreur : 403

| Code d'erreur | Message d’erreur | Description | Action recommandée |
| ---------- | ------------- | ----------- | ------------------ |
| HttpStatusCode.Forbidden <br/>403 | La publication sur {Rubrique/Domaine} par le client {AdresseIP} est rejetée en raison des règles de filtrage d’adresse IP. | La rubrique ou le domaine a des règles de pare-feu IP configurées et l’accès est limité uniquement aux adresses IP configurées. | Ajoutez l’adresse IP aux règles de pare-feu IP ; consultez [Configurer le pare-feu IP](configure-firewall.md) |
| HttpStatusCode.Forbidden <br/> 403 | La publication sur {Rubrique/Domaine} par le client est rejetée, car la demande émane du point de terminaison privé et aucune connexion de point de terminaison privée correspondante n’a été trouvée pour la ressource. | La rubrique ou le domaine a des points de terminaison privés configurés et la demande de publication provient d’un point de terminaison privé qui n’est pas configuré/approuvé. | Configurez un point de terminaison privé pour la rubrique/le domaine. [Configurer des points de terminaison privés](configure-private-endpoints.md) |

## <a name="troubleshoot-event-subscription-validation"></a>Résoudre les problèmes de validation des abonnements aux événements

Si, pendant la création d’un abonnement aux événements, vous voyez un message d’erreur tel que `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation`, cela est le signe d’un échec dans le processus de négociation de la validation. Pour résoudre cette erreur, vérifiez les points suivants :

- Envoyez une requête HTTP POST à l'URL de votre webhook avec un échantillon de [SubscriptionValidationEvent](webhook-event-delivery.md#validation-details) dans le corps de la requête à l'aide de Postman, de Curl ou d'un outil similaire.
- Si votre webhook implémente un mécanisme d'établissement de liaison de validation synchrone, vérifiez que le code de validation est renvoyé dans le cadre de la réponse.
- Si votre webhook implémente un mécanisme d'établissement de liaison de validation asynchrone, assurez-vous que la requête HTTP POST renvoie 200 OK.
- Si votre webhook renvoie 403 (interdit) dans la réponse, vérifiez qu'il se trouve derrière une instance d'Azure Application Gateway ou un pare-feu d'applications web. Si tel est le cas, vous devez désactiver ces règles de pare-feu et renvoyer une requête HTTP POST :

  920300 (En-tête Accept manquant dans la requête, nous pouvons y remédier)

  942430 (Détection restreinte des anomalies de caractères SQL (args) : nombre de caractères spéciaux dépassé (12))

  920230 (Détection d'encodage de plusieurs URL)

  942130 (Attaque par injection de code SQL : Tautologie SQL détectée.)

  931130 (Attaque possible par inclusion de fichier distant = Référence/Lien hors domaine)


## <a name="next-steps"></a>Étapes suivantes
Si vous avez besoin d’une aide supplémentaire, publiez votre problème sur le forum [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou ouvrez un [ticket de support](https://azure.microsoft.com/support/options/). 
