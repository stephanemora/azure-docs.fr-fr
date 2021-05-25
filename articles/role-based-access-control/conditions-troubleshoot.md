---
title: Résoudre les problèmes liés aux conditions d’attribution de rôle Azure (préversion)
description: Résoudre les problèmes liés aux conditions d’attribution de rôle Azure (préversion)
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/06/2021
ms.author: rolyon
ms.openlocfilehash: eddebb6742a50ce729d436fc19ce4ff32491f75f
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109489617"
---
# <a name="troubleshoot-azure-role-assignment-conditions-preview"></a>Résoudre les problèmes liés aux conditions d’attribution de rôle Azure (préversion)

> [!IMPORTANT]
> Azure ABAC et les conditions d’attribution de rôle Azure sont actuellement en préversion.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="symptom---condition-is-not-enforced"></a>Symptôme - Condition non appliquée

**Cause 1**

Les principaux de sécurité présentent au moins une attribution de rôle dans une étendue identique ou supérieure.

**Solution 1**

Assurez-vous que les principaux de sécurité n’ont pas plusieurs attributions de rôle (avec ou sans conditions) qui accordent l’accès à la même action de données, ce qui empêcherait l’application des conditions. Pour plus d’informations sur la logique d’évaluation, consultez [Comment le contrôle RBAC Azure détermine si un utilisateur a accès à une ressource](overview.md#how-azure-rbac-determines-if-a-user-has-access-to-a-resource).

**Cause 2**

Votre attribution de rôle comporte plusieurs actions qui accordent une autorisation et votre condition ne cible pas toutes les actions. Par exemple, vous pouvez créer un objet BLOB si vous avez des actions de données `/blobs/write` ou `/blobs/add/action`. Si votre attribution de rôle comporte à la fois des actions de données et que vous ciblez une seule d’entre elles dans une condition, l’attribution de rôle accorde l’autorisation de créer des objets BLOB et de contourner la condition.

**Solution 2**

Si votre attribution de rôle comporte plusieurs actions qui accordent une autorisation, assurez-vous que vous ciblez toutes les actions pertinentes.

**Cause 3**

Lorsque vous ajoutez une condition à une attribution de rôle, il peut falloir jusqu’à 5 minutes pour que la condition soit appliquée. Lorsque vous ajoutez une condition, les fournisseurs de ressources (tels que Microsoft.Storage) sont avertis de la mise à jour. Les fournisseurs de ressources mettent immédiatement à jour leurs caches locaux pour s’assurer qu’ils disposent des dernières attributions de rôle. Ce processus s’effectue en 1 ou 2 minutes, mais peut prendre jusqu’à 5 minutes.

**Solution 3**

Patientez 5 minutes et retestez la condition.

## <a name="symptom---condition-is-not-valid-error-when-adding-a-condition"></a>Symptôme - Erreur Condition non valide lors de l’ajout d’une condition

Lorsque vous essayez d’ajouter une attribution de rôle à une condition, une erreur semblable à la suivante s’affiche :

`The given role assignment condition is invalid.`

**Cause**

Votre condition n’est pas mise en forme correctement. 

**Solution**

Corrigez tout problème de [format ou de syntaxe de la condition](conditions-format.md). Vous pouvez également ajouter la condition à l’aide de l’[éditeur visuel dans le portail Azure](conditions-role-assignments-portal.md).

## <a name="symptom---resource-attribute-is-not-valid-error-when-adding-a-condition-using-azure-powershell"></a>Symptôme - Erreur Attribut de ressource non valide lors de l’ajout d’une condition à l’aide d’Azure PowerShell

Lorsque vous essayez d’ajouter une attribution de rôle à une condition à l’aide d’Azure PowerShell, une erreur semblable à la suivante s’affiche :

```
New-AzRoleAssignment : Resource attribute
Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$> is not valid.
```

**Cause**

Si votre condition comprend un signe dollar ($), vous devez le faire précéder d’un guillemet inversé (\`).

**Solution**

Ajoutez un guillemet inversé (\`) avant chaque signe dollar. Consultez l’exemple ci-dessous. Pour plus d’informations sur les règles liées aux guillemets dans PowerShell, consultez [À propos des règles liées aux guillemets](/powershell/module/microsoft.powershell.core/about/about_quoting_rules).

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade'))"
```

## <a name="symptom---resource-attribute-is-not-valid-error-when-adding-a-condition-using-azure-cli"></a>Symptôme - Erreur Attribut de ressource non valide lors de l’ajout d’une condition à l’aide d’Azure CLI

Lorsque vous essayez d’ajouter une attribution de rôle à une condition à l’aide d’Azure CLI, une erreur semblable à la suivante s’affiche :

```
Resource attribute Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$> is not valid.
```

**Cause**

Si votre condition comprend un signe dollar ($), vous devez le faire précéder d’une barre oblique inverse (\\).

**Solution**

Ajoutez une barre oblique inverse (\\) avant chaque signe dollar. Consultez l’exemple ci-dessous. Pour plus d’informations sur les règles liées aux guillemets dans Bash, consultez [Guillemets doubles](https://www.gnu.org/software/bash/manual/html_node/Double-Quotes.html).

```azurecli
condition="((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<\$key_case_sensitive\$>] StringEquals 'Cascade'))"
```

## <a name="symptom---error-when-assigning-a-condition-string-to-a-variable-in-bash"></a>Symptôme - Erreur lors de l’affectation d’une chaîne de condition à une variable dans Bash

Lorsque vous essayez d’affecter une chaîne de condition à une variable dans Bash, le message `bash: !: event not found` s’affiche.

**Cause**

Dans Bash, si le développement de l’historique est activé, vous pouvez voir le message `bash: !: event not found` en raison du point d’exclamation (!).

**Solution**

Pour désactiver le développement de l’historique, utilisez la commande `set +H`. Pour réactiver le développement de l’historique, utilisez `set -H`.

## <a name="symptom---unrecognized-arguments-error-when-adding-a-condition-using-azure-cli"></a>Symptôme - Erreur Arguments non reconnus lors de l’ajout d’une condition à l’aide d’Azure CLI

Lorsque vous essayez d’ajouter une attribution de rôle à une condition à l’aide d’Azure CLI, une erreur semblable à la suivante s’affiche :

`az: error: unrecognized arguments: --description {description} --condition {condition} --condition-version 2.0`

**Cause**

Vous utilisez probablement une version antérieure d’Azure CLI qui ne prend pas en charge les paramètres de condition d’attribution de rôle.

**Solution**

Procédez à la mise à jour vers la dernière version d’Azure CLI (2.18 ou version ultérieure). Pour plus d’informations, consultez la rubrique [Installation de l’interface de ligne de commande Azure (CLI)](/cli/azure/install-azure-cli).

## <a name="symptom---condition-not-recognized-in-visual-editor"></a>Symptôme - Condition non reconnue dans l’éditeur visuel

Après avoir utilisé l’éditeur de code, vous basculez vers l’éditeur visuel et un message similaire à ce qui suit s’affiche :

`The current expression cannot be recognized. Switch to the code editor to edit the expression or delete the expression and add a new one.`

**Cause**

Des mises à jour ont été apportées à la condition que l’éditeur visuel ne peut pas analyser.

**Solution**

Corrigez tout problème de [format ou de syntaxe de la condition](conditions-format.md). Vous pouvez également supprimer la condition et réessayer.

## <a name="symptom---error-when-copying-and-pasting-a-condition"></a>Symptôme - Erreur lors de la copie et du collage d’une condition

**Cause**

Si vous copiez une condition à partir d’un document, elle peut inclure des caractères spéciaux et générer des erreurs. Certains éditeurs (tels que Microsoft Word) ajoutent des caractères de contrôle lors de la mise en forme du texte qui n’apparaît pas.

**Solution**

Si vous êtes certain que votre condition est correcte, supprimez tous les espaces et les retours, puis rajoutez les espaces appropriés.

## <a name="next-steps"></a>Étapes suivantes

- [Format et syntaxe des conditions d’attribution de rôle Azure (préversion)](conditions-format.md)
- [Questions fréquentes (FAQ) sur les conditions d’attribution de rôle Azure (préversion)](conditions-faq.md)
- [Exemples de conditions d’attribution de rôle Azure (préversion)](../storage/common/storage-auth-abac-examples.md)
