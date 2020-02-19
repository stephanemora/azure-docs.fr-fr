---
title: Présentation du verrouillage des ressources
description: Découvrez les options de verrouillage dans Azure Blueprints pour protéger les ressources au moment d’affecter un blueprint.
ms.date: 04/24/2019
ms.topic: conceptual
ms.openlocfilehash: e042a4d117e28a2fd2228ce36f1be98a1da31e91
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057343"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Comprendre le verrouillage de ressources dans les blueprints Azure

La création d’environnements cohérents à l’échelle n’est vraiment utile que s’il existe un mécanisme pour gérer cette cohérence. Cet article explique le fonctionnement du verrouillage de ressources dans les blueprints Azure. Pour voir un exemple de verrouillage des ressources et d’application _d’affectations de refus_, consultez le didacticiel relatif à la [protection des nouvelles ressources](../tutorials/protect-new-resources.md).

## <a name="locking-modes-and-states"></a>Modes et états de verrouillage

Le mode de verrouillage s’applique à l’attribution de blueprint et offre trois options : **Ne pas verrouiller**, **Lecture seule** ou **Ne pas supprimer**. Le mode de verrouillage est configuré durant le déploiement d’artefact au cours d’une attribution de blueprint. Un autre mode de verrouillage peut être défini en mettant à jour l’attribution de blueprint.
Les modes de verrouillage ne peuvent cependant pas être modifiés en dehors des blueprints.

Les ressources créées par des artefacts dans une attribution de blueprint ont quatre états possibles : **Non verrouillé**, **Lecture seule**, **Modification/suppression impossible** ou **Suppression impossible**. Chaque type d’artefact peut être en état **Non verrouillé**. Le tableau suivant permet de déterminer l’état d’une ressource :

|Mode|Type de ressource d’artefact|State|Description|
|-|-|-|-|
|Ne pas verrouiller|*|Non verrouillé|Les ressources ne sont pas protégées par des blueprints. Cet état est également utilisé pour des ressources ajoutées à un artéfact de groupe de ressources **En lecture seule** ou **Ne pas supprimer** à partir de l’extérieur d’une attribution de blueprint.|
|Lecture seule|Resource group|Modification/suppression impossible|Le groupe de ressources est en lecture seule et les balises sur le groupe de ressources ne peuvent pas être modifiées. Des ressources **Non verrouillées** peuvent être ajoutées, déplacées, modifiées ou supprimées dans ce groupe de ressources.|
|Lecture seule|Groupe de non-ressources|Lecture seule|La ressource ne peut pas être modifiée de quelque manière que ce soit (aucune modification possible et suppression impossible).|
|Ne pas supprimer|*|Suppression impossible|Les ressources peuvent être modifiées mais pas supprimées. Des ressources **Non verrouillées** peuvent être ajoutées, déplacées, modifiées ou supprimées dans ce groupe de ressources.|

## <a name="overriding-locking-states"></a>Neutralisation des états de verrouillage

Il est généralement possible pour une personne disposant d’un [contrôle d’accès en fonction du rôle](../../../role-based-access-control/overview.md) (RBAC) approprié sur l’abonnement, tel que le rôle « Propriétaire », d’être autorisée à modifier ou supprimer n’importe quelle ressource. Cet accès n’est pas possible quand les blueprints appliquent un verrouillage dans le cadre d’une affectation déployée. Si l’attribution a été définie avec l’option **Lecture seule** ou **Ne pas supprimer**, même le propriétaire de l’abonnement ne peut pas effectuer l’action bloquée sur la ressource protégée.

Cette mesure de sécurité assure la cohérence du blueprint défini et protège l’environnement pour la création duquel il a été conçu contre toute modification ou suppression accidentelle ou programmatique.

## <a name="removing-locking-states"></a>Suppression des états de verrouillage

S’il devient nécessaire de modifier ou supprimer une ressource protégée par une attribution, il existe deux manières de procéder.

- En mettant à jour de l’attribution de blueprint en la définissant sur un mode de verrouillage **Ne pas verrouiller**
- Supprimer l’attribution de blueprint

Une fois l’affectation supprimée, les verrous créés par les blueprints sont supprimés. Cependant, la ressource est conservée et doit être supprimée selon des procédés normaux.

## <a name="how-blueprint-locks-work"></a>Fonctionnement des verrous de blueprint

Une action de refus de type [Refuser les attributions](../../../role-based-access-control/deny-assignments.md) de contrôle d’accès en fonction du rôle (RBAC) est appliquée aux ressources d’artefact lors de l’attribution d’un blueprint si cette attribution a sélectionné l’option **Lecture seule** ou **Ne pas supprimer**. L’action de refus est ajoutée par l’identité managée de l’attribution de blueprint, et ne peut être supprimée des ressources d’artefacts que par cette même identité managée. Cette mesure de sécurité a pour effet d’appliquer le mécanisme de verrouillage et d’empêcher la suppression du verrou du blueprint en dehors de blueprints.

![Affectation de refus blueprint sur groupe de ressources](../media/resource-locking/blueprint-deny-assignment.png)

[Propriétés d’affectation de refus](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) de chaque mode :

|Mode |Permissions.Actions |Permissions.NotActions |Principals[i].Type |ExcludePrincipals[i].Id | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Lecture seule |**\*** |**\*/read** |SystemDefined (Everyone) |affectation blueprint et paramètres définis par l’utilisateur dans **excludedPrincipals** |Groupe de ressources - _true_ ; ressource - _false_ |
|Ne pas supprimer |**\*/delete** | |SystemDefined (Everyone) |affectation blueprint et paramètres définis par l’utilisateur dans **excludedPrincipals** |Groupe de ressources - _true_ ; ressource - _false_ |

> [!IMPORTANT]
> Azure Resource Manager met en cache les détails des affectations de rôles pendant 30 minutes au maximum. Par conséquent, une action de refus de type Refuser les attributions sur des ressources de blueprint risque de ne pas être immédiatement effective. Pendant cette période de temps, il peut être possible de supprimer une ressource destinée à être protégée par des verrous de blueprint.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Exclure un principal d’une affectation de refus

Dans certains scénarios de conception ou de sécurité, il peut être nécessaire d’exclure un principal de [l’affectation de refus](../../../role-based-access-control/deny-assignments.md) créée par l’affectation blueprint. Cette opération est effectuée dans l’API REST en ajoutant jusqu’à cinq valeurs au tableau **excludedPrincipals** de la propriété **locks** lors de la [création de l’affectation](/rest/api/blueprints/assignments/createorupdate). Exemple d’un corps de requête qui inclut **excludedPrincipals** :

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="exclude-an-action-from-a-deny-assignment"></a>Exclure une action d’une affectation de refus

À l’instar de l’[exclusion d’un principal](#exclude-a-principal-from-a-deny-assignment) sur une [affectation de refus](../../../role-based-access-control/deny-assignments.md) dans une affectation de blueprint, vous pouvez exclure des [opérations RBAC](../../../role-based-access-control/resource-provider-operations.md) spécifiques. Dans le bloc **properties.locks**, au même emplacement que **excludedPrincipals**, vous pouvez ajouter un **excludedActions** :

```json
"locks": {
    "mode": "AllResourcesDoNotDelete",
    "excludedPrincipals": [
        "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
        "38833b56-194d-420b-90ce-cff578296714"
    ],
    "excludedActions": [
        "Microsoft.ContainerRegistry/registries/push/write",
        "Microsoft.Authorization/*/read"
    ]
},
```

Bien que **excludedPrincipals** doive être explicite, les entrées **excludedActions** peuvent tirer parti de `*` pour la correspondance de caractères génériques des opérations RBAC.

## <a name="next-steps"></a>Étapes suivantes

- Suivre le didacticiel relatif à la [protection des nouvelles ressources](../tutorials/protect-new-resources.md).
- En savoir plus sur le [cycle de vie des blueprints](lifecycle.md)
- Comprendre comment utiliser les [paramètres statiques et dynamiques](parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](sequencing-order.md).
- Découvrir comment [mettre à jour des affectations existantes](../how-to/update-existing-assignments.md).
- Résoudre les problèmes durant l’affectation d’un blueprint en suivant les étapes de [dépannage général](../troubleshoot/general.md).