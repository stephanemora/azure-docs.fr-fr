---
title: Désactiver votre identité managée de compte Azure Automation (préversion)
description: Cet article explique comment désactiver et supprimer une identité managée pour un compte Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: e17e1afda50d9a0263067a77bf26435f53b4f237
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519270"
---
# <a name="disable-your-azure-automation-account-managed-identity-preview"></a>Désactiver votre identité managée de compte Azure Automation (préversion)

Il existe deux façons de désactiver une identité affectée par le système dans Azure Automation. Vous pouvez effectuer cette tâche à partir du portail Azure ou à l’aide d’un modèle Azure Resource Manager (ARM).

## <a name="disable-managed-identity-in-the-azure-portal"></a>Désactiver l’identité managée dans le portail Azure

Vous pouvez désactiver l’identité managée à partir du portail Azure quelle que soit la façon dont l’identité managée a été initialement configurée.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Accédez à votre compte Automation et sélectionnez **Identité** sous **Paramètres du compte**.

1. Affectez la valeur **Désactivé** à l’option **Affecté par le système**, puis appuyez sur **Enregistrer**. Lorsque vous êtes invité à confirmer, appuyez sur **Oui**.

L’identité managée est supprimée et n’a plus accès à la ressource cible.

## <a name="disable-using-azure-resource-manager-template"></a>Désactiver l’utilisation d’un modèle Azure Resource Manager

Si vous avez créé l’identité managée pour votre compte Automation à l’aide d’un modèle Azure Resource Manager, vous pouvez désactiver l’identité managée en réutilisant ce modèle et en modifiant ses paramètres. Définissez le type de la propriété enfant de l’objet d’identité sur **Aucun** comme indiqué dans l’exemple suivant, puis réexécutez le modèle.

```json
"identity": { 
   "type": "None" 
} 
```

Si vous supprimez une identité affectée par le système par cette méthode, vous la supprimez également d’Azure AD. Les identités affectées par le système sont aussi automatiquement supprimées d’Azure AD quand la ressource d’application à laquelle elles sont affectées est supprimée.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’activation de l’identité managée dans Azure Automation, consultez [Activer et utiliser l’identité managée pour Automation (préversion)](enable-managed-identity-for-automation.md).

- Pour obtenir une vue d’ensemble de la sécurité du compte Automation, consultez [Vue d’ensemble de l’authentification du compte Automation](automation-security-overview.md).
