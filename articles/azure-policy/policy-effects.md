---
title: Compréhension des effets d’Azure Policy
description: Les effets différents de la définition Azure Policy déterminent la manière dont la conformité est gérée et rapportée.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 23bbbe9cf86268f93ae1f8fcec9303efa8a673de
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796714"
---
# <a name="understanding-policy-effects"></a>Compréhension des effets de Policy

Chaque définition de stratégie dans Azure Policy possède un seul effet qui détermine ce qui se passe pendant l’analyse lorsque le segment **if** de la règle de stratégie est évalué pour la ressource analysée. Les effets peuvent aussi se comporter différemment selon qu’ils concernent une nouvelle ressource, une ressource mise à jour ou une ressource existante.

Une définition de stratégie prend en charge les cinq effets suivants :

- Append
- Audit
- AuditIfNotExists
- Deny
- DeployIfNotExists

## <a name="order-of-evaluation"></a>Ordre d’évaluation

Lorsqu’une requête de création ou de mise à jour d’une ressource est effectuée via Azure Resource Manager, la stratégie traite plusieurs effets avant de transmettre la requête au fournisseur de ressources approprié.
Cela empêche un fournisseur de ressources d’effectuer un traitement inutile de la ressource lorsque celle-ci ne satisfait pas aux contrôles de gouvernance d’Azure Policy. Azure Policy crée la liste de toutes les définitions de stratégie affectées par le biais d’une affectation de stratégie ou d’initiative, lesquelles s’appliquent par portée (moins les exclusions) à la ressource ; la stratégie se prépare à évaluer la ressource en fonction de chaque définition.

- **Append** est évalué en premier. Comme Append risque de modifier la requête, une modification apportée par Append peut empêcher le déclenchement d’un effet Audit ou Deny.
- **Deny** est ensuite évalué. L’évaluation de Deny avant Audit empêche la double journalisation d’une ressource indésirable.
- **Audit** est alors évalué avant que la requête ne soit envoyée au fournisseur de ressources.

Une fois que la requête parvient au fournisseur de ressources et que celui-ci envoie en retour un code d’état de réussite, **AuditIfNotExists** et **DeployIfNotExists** sont évalués pour déterminer si la journalisation ou une action de conformité du suivi est nécessaire.

## <a name="append"></a>Append

Append permet d’ajouter des champs supplémentaires à la ressource demandée lors de la création ou de la mise à jour. Cela peut être utile pour ajouter des balises sur des ressources, telles que costCenter, ou pour spécifier des adresses IP autorisées pour une ressource de stockage.

### <a name="append-evaluation"></a>Évaluation Append

Comme mentionné, l’évaluation Append se produit avant que la requête ne soit traitée par un fournisseur de ressources lors de la création ou de la mise à jour d’une ressource. Append ajout un ou des champs à la ressource lorsque la condition **if** de la règle de stratégie est remplie. Si l’effet Append remplace une valeur dans la requête d’origine avec une valeur différente, il agit comme un effet Deny et rejette la demande.

Lorsqu’une définition de stratégie utilisant l’effet Append est exécutée dans le cadre d’un cycle d’évaluation, elle n’apporte pas de modifications aux ressources qui existent déjà. Au lieu de cela, elle marque comme non conforme toute ressource qui répond à la condition **if**.

### <a name="append-properties"></a>Propriétés d’Append

Un effet Append prend en charge la propriété obligatoire **details** uniquement, laquelle est un tableau de données. En tant que tableau, **details** peut contenir une ou plusieurs paires **champ/valeur**. Reportez-vous à [policy definition](policy-definition.md#fields) pour afficher la liste des champs autorisés.

### <a name="append-examples"></a>Exemples Append

Exemple 1 : paire **champ/valeur** unique à ajouter à une balise.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "tags.myTag",
        "value": "myTagValue"
    }]
}
```

Exemple 2 : paires **champ/valeur** multiples à ajouter à une balise.

```json
"then": {
    "effect": "append",
    "details": [{
            "field": "tags.myTag",
            "value": "myTagValue"
        },
        {
            "field": "tags.myOtherTag",
            "value": "myOtherTagValue"
        }
    ]
}
```

Exemple 3 : paire **champ/valeur** unique utilisant un [alias](policy-definition.md#aliases) avec un tableau **value** afin de définir des règles IP sur un compte de stockage.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

## <a name="deny"></a>Deny

Deny empêche l’exécution d’une requête de ressource qui ne correspond pas aux normes souhaitées via une définition de stratégie et qui fait échouer la requête.

### <a name="deny-evaluation"></a>Évaluation Deny

Lors de la création ou de la mise à jour d’une ressource, Deny empêche l’envoi de la requête au fournisseur de ressources. La requête renvoie une erreur 403 (Interdit). Dans le portail, l’erreur 403 (Interdit) peut être considérée comme l’état du déploiement qui a été empêché en raison de l’affectation de stratégie.

Au cours d’un cycle d’évaluation, les définitions de stratégie ayant un effet Deny pour les ressources sont marquées comme non conformes, mais aucune action n’est effectuée sur ces ressources.

### <a name="deny-properties"></a>Propriétés de Deny

L’effet Deny n’a pas d’autres propriétés utilisables dans la condition **if** de la définition de stratégie.

### <a name="deny-example"></a>Exemple Deny

Exemple : utilisation de l’effet Deny.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Audit

L’effet Audit permet de créer un événement d’avertissement dans le journal d’audit lorsqu’une ressource non conforme est évaluée, mais n’arrête pas la requête.

### <a name="audit-evaluation"></a>Évaluation Audit

L’effet Audit est le dernier à être exécuté lors de la création ou de la mise à jour d’une ressource avant l’envoi de celle-ci au fournisseur de ressources. Audit fonctionne de la même façon pour une requête de ressource et un cycle d’évaluation, et exécute une opération `Microsoft.Authorization/policies/audit/action` dans le journal d’activité. Dans les deux cas, la ressource est marquée comme non conforme.

### <a name="audit-properties"></a>Propriétés d’Audit

L’effet Audit n’a pas d’autres propriétés utilisables dans la condition **if** de la définition de stratégie.

### <a name="audit-example"></a>Exemple Audit

Exemple : utilisation de l’effet Audit.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists active l’audit sur une ressource qui satisfait à la condition **if**, mais dont les composants ne sont pas spécifiés dans la propriété **details** de la condition **then**.

### <a name="auditifnotexists-evaluation"></a>Évaluation AuditIfNotExists

AuditIfNotExists s’exécute après qu’un fournisseur de ressources a traité une requête de création ou de mise à jour vers une ressource et a renvoyé un code d’état de réussite. L’effet est déclenché s’il n’existe pas de ressources connexes ou si les ressources définies par **ExistenceCondition** ne retournent pas de valeur true. Lorsque l’effet est déclenché, une opération `Microsoft.Authorization/policies/audit/action` vers le journal d’activité est exécutée de la même manière que l’effet Audit. Dans ce cas, la ressource qui a rempli la condition **if** est en fait la ressource qui est marquée comme non conforme.

### <a name="auditifnotexists-properties"></a>Propriétés d’AuditIfNotExists

La propriété **details** des effets AuditIfNotExists possède toutes les sous-propriétés qui définissent les ressources connexes testées.

- **Type** [obligatoire]
  - Spécifie le type de la ressource connexe à évaluer.
  - Commence par tenter d’extraire une ressource sous la ressource de condition **if**, puis effectue une requête dans le même groupe de ressources que la ressource de condition **if**.
- **Name** (facultatif)
  - Spécifie le nom exact de la ressource à tester et amène la stratégie à extraire une ressource spécifique au lieu de toutes les ressources du type spécifié.
- **ResourceGroupName** (facultatif)
  - Permet l’évaluation de la ressource connexe provenant d’un groupe de ressources différent.
  - Ne s’applique pas si **type** est une ressource qui serait située sous la ressource de condition **if**.
  - La valeur par défaut est le groupe de ressources de la ressource de condition **if**.
- **ExistenceScope** (facultatif)
  - Les valeurs autorisées sont _Subscription_ et _ResourceGroup_.
  - Définit la portée d’où la ressource connexe évaluée est extraite.
  - Ne s’applique pas si **type** est une ressource qui serait située sous la ressource de condition **if**.
  - Pour _ResourceGroup_, le traitement se limiterait au groupe de ressources de la ressource de condition **if** ou au groupe de ressources spécifié dans **ResourceGroupName**.
  - Pour _Subscription_, le traitement interroge l’abonnement entier pour la ressource associée.
  - La valeur par défaut est _ResourceGroup_.
- **ExistenceCondition** (facultatif)
  - Si elle n’est pas spécifiée, toute ressource connexe du **type** remplit les conditions de l’effet et ne déclenche pas l’audit.
  - Utilise la même langue que la règle de stratégie pour la condition **if** mais est évaluée individuellement sur chaque ressource associée.
  - Si une ressource connexe correspondante renvoie true, l’effet est satisfait et ne déclenche pas l’audit.
  - Peut utiliser [field()] pour vérifier l’équivalence des valeurs dans la condition **if**.
  - Par exemple, permet de vérifier que la ressource parent (dans la condition **if**) réside dans le même emplacement de la ressource en tant que ressource connexe correspondante.

### <a name="auditifnotexists-example"></a>Exemple AuditIfNotExists

Exemple : évalue les Machines virtuelles Microsoft Azure pour déterminer si l’extension Antimalware existe, puis effectue un audit si l’extension est absente.

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

Comme pour AuditIfNotExists, DeployIfNotExists exécute un déploiement de modèle lorsque la condition est remplie.

### <a name="deployifnotexists-evaluation"></a>Évaluation DeployIfNotExists

DeployIfNotExists est également exécuté après qu’un fournisseur de ressources a traité une requête de création ou de mise à jour vers une ressource et a renvoyé un code d’état de réussite. L’effet est déclenché s’il n’existe pas de ressources connexes ou si les ressources définies par **ExistenceCondition** ne retournent pas de valeur true. Le déclenchement de l’effet entraîne l’exécution d’un déploiement de modèle.

Au cours d’un cycle d’évaluation, les définitions de stratégie ayant un effet DeployIfNotExists sur les ressources sont marquées comme non conformes, mais aucune action n’est effectuée sur ces ressources.

### <a name="deployifnotexists-properties"></a>Propriétés de DeployIfNotExists

La propriété **details** des effets DeployIfNotExists possède toutes les sous-propriétés qui définissent les ressources connexes testées ainsi que le déploiement de modèle à exécuter.

- **Type** [obligatoire]
  - Spécifie le type de la ressource connexe à évaluer.
  - Commence par tenter d’extraire une ressource sous la ressource de condition **if**, puis effectue une requête dans le même groupe de ressources que la ressource de condition **if**.
- **Name** (facultatif)
  - Spécifie le nom exact de la ressource à tester et amène la stratégie à extraire une ressource spécifique au lieu de toutes les ressources du type spécifié.
- **ResourceGroupName** (facultatif)
  - Permet l’évaluation de la ressource connexe provenant d’un groupe de ressources différent.
  - Ne s’applique pas si **type** est une ressource qui serait située sous la ressource de condition **if**.
  - La valeur par défaut est le groupe de ressources de la ressource de condition **if**.
  - L’exécution d’un déploiement de modèle s’effectue dans le groupe de ressources de cette valeur.
- **ExistenceScope** (facultatif)
  - Les valeurs autorisées sont _Subscription_ et _ResourceGroup_.
  - Définit la portée d’où la ressource connexe évaluée est extraite.
  - Ne s’applique pas si **type** est une ressource qui serait située sous la ressource de condition **if**.
  - Pour _ResourceGroup_, le traitement se limiterait au groupe de ressources de la ressource de condition **if** ou au groupe de ressources spécifié dans **ResourceGroupName**.
  - Pour _Subscription_, le traitement interroge l’abonnement entier pour la ressource associée.
  - La valeur par défaut est _ResourceGroup_.
- **ExistenceCondition** (facultatif)
  - Si elle n’est pas spécifiée, toute ressource connexe de **type** satisfait à l’effet en question et ne déclenche pas le déploiement.
  - Utilise la même langue que la règle de stratégie pour la condition **if** mais est évaluée individuellement sur chaque ressource associée.
  - Si une ressource connexe correspondante renvoie true, l’effet est satisfait et ne déclenche pas le déploiement.
  - Peut utiliser [field()] pour vérifier l’équivalence des valeurs dans la condition **if**.
  - Par exemple, permet de vérifier que la ressource parent (dans la condition **if**) réside dans le même emplacement de la ressource en tant que ressource connexe correspondante.
- **Deployment** [obligatoire]
  - Cette propriété doit contenir le déploiement de modèle complet car elle est transmise à l’API PUT `Microsoft.Resources/deployments`. Pour plus d’informations, consultez [l’API REST Deployments](/rest/api/resources/deployments).

  > [!NOTE]
  > Toutes les fonctions à l’intérieur de la propriété **Deployment** sont évaluées en tant que composants du modèle, et non pas de la stratégie. La propriété **parameters** y fait exception car elle transmet les valeurs de la stratégie au modèle. Dans cette section, la propriété **value** sous le nom de paramètre du modèle permet d’effectuer la transmission de valeurs (voir _fullDbName_ dans l’exemple DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Exemple DeployIfNotExists

Exemple : évalue les bases de données SQL Server pour déterminer si transparentDataEncryption est activée. Sinon, un déploiement destiné à l’activer est exécuté.

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="layering-policies"></a>Superposition de stratégies

Une ressource peut subir les effets de plusieurs affectations. Celles-ci peuvent se situer dans la même portée (ressource spécifique, groupe de ressources, abonnement ou groupe de gestion) ou dans des portées différentes. Chaque affectation est également susceptible d’avoir un effet différent. Quoi qu’il en soit, la condition et l’effet de chaque stratégie (assignés directement ou dans le cadre d’une initiative) sont évalués indépendamment. Par exemple, si la stratégie 1 impose une restriction empêchant la création de l’emplacement de l’abonnement A dans « westus » avec effet Deny et que la stratégie 2 limitant les ressources du groupe de ressources B (dans l’abonnement A) à « eastus » avec effet Audit sont tous deux assignés, le résultat obtenu est le suivant :

- Toute ressource figurant déjà dans le groupe de ressources B dans « eastus » est marquée comme conforme à la stratégie 2 mais comme non conforme à la stratégie 1.
- Toute ressource figurant déjà dans le groupe de ressources B et non dans « eastus » est marquée comme non conforme à la stratégie 2 et mais aussi comme non conforme à la stratégie 1 si elle est différente de « westus ».
- Toute nouvelle ressource figurant dans l’abonnement A mais pas dans « westus » est refusée par la stratégie 1.
- Toute nouvelle ressource figurant dans l’abonnement A / groupe de ressources B d’abonnement dans « westus » est marquée comme non conforme dans la stratégie 2 mais créée (conforme à la stratégie 1 et la stratégie 2 avec Audit et non pas Deny).

Si, dans cet exemple, la stratégie 1 et la stratégie 2 a pour effet Deny, le scénario est le suivant :

- Toute ressource figurant déjà dans le groupe de ressources B mais pas dans « eastus » est marquée comme non conforme à la stratégie 2.
- Toute ressource figurant déjà dans le groupe de ressources B mais pas dans « westus » est marquée comme non conforme à la stratégie 1.
- Toute nouvelle ressource figurant dans l’abonnement A mais pas dans « westus » est refusée par la stratégie 1.
- Toute nouvelle ressource figurant dans l’abonnement A / le groupe de ressources B est refusée (car son emplacement ne peut jamais satisfaire à la fois à la stratégie 1 et à la stratégie 2).

Comme chaque affectation est évaluée individuellement, une ressource ne peut pas contourner un écart en raison de différences de portée. Par conséquent, la superposition de stratégies ou le chevauchement de stratégies est considéré comme **cumulatif et le plus restrictif**. En d’autres termes, une ressource que vous voulez créer peut être bloquée en raison de chevauchements et de conflits de règles, comme dans l’exemple ci-dessus, si les stratégies 1 et 2 engendrent un refus. Si vous avez toujours besoin de la ressource à créer dans l’étendue cible, révisez les exclusions de chaque affectation pour vous assurer que les bonnes stratégies affectent les bonnes étendues.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une meilleure compréhension des effets de définition de stratégie, examinez les exemples de stratégie :

- Consultez d’autres exemples dans [Exemples Azure Policy](json-samples.md).
