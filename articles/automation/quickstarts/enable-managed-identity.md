---
title: Démarrage rapide - Activer les identités managées pour votre compte Automation avec le portail Azure
description: Ce guide de démarrage rapide vous permet d’activer les identités managées pour votre compte Automation avec le portail Azure.
services: automation
ms.date: 09/07/2021
ms.topic: quickstart
ms.subservice: process-automation
ms.openlocfilehash: 14fc0af0b75639ae79c417645912a868a0df34f6
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544538"
---
# <a name="quickstart-enable-managed-identities-for-your-automation-account-using-the-azure-portal"></a>Démarrage rapide : Activer les identités managées pour votre compte Automation avec le portail Azure

Ce guide de démarrage rapide vous montre comment activer les identités managées pour un compte Azure Automation. Pour plus d’informations sur le fonctionnement des identités managées avec Azure Automation, consultez [Identités managées](../automation-security-overview.md#managed-identities-preview).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Un compte Azure Automation. Pour obtenir des instructions, consultez [Créer un compte Automation](create-account-portal.md).

- Identité managée affectée par l’utilisateur. Pour obtenir des instructions, consultez [Créer une identité managée affectée par l’utilisateur](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity). L’identité managée affectée par l’utilisateur et les ressources Azure cibles que votre runbook gère à l’aide de cette identité doivent se trouver dans le même abonnement Azure.

## <a name="enable-system-assigned-managed-identity"></a>Activer l’identité managée affectée par le système

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre compte Automation.

1. Sous  **Paramètres du compte**, sélectionnez  **Identité (préversion)** .

   :::image type="content" source="media/enable-managed-identity/managed-identity-portal.png" alt-text="Navigation vers Identité dans le portail.":::

1. Définissez l’option **État** affectée par le système sur **Activé**, puis appuyez sur **Enregistrer**. Lorsque vous êtes invité à confirmer, sélectionnez **Oui**.

   Votre compte Automation peut désormais utiliser l’identité affectée par le système qui est inscrite auprès d’Azure AD (Azure Active Directory) et représentée par un ID d’objet.

   :::image type="content" source="media/enable-managed-identity/system-assigned-object-id.png" alt-text="ID d’objet de l’identité managée.":::

## <a name="add-user-assigned-managed-identity"></a>Ajouter une identité managée affectée par l’utilisateur

Cette section reprend à l’endroit où la dernière section s’est terminée.

1. Sélectionnez l’onglet **Affecté(e) par l'utilisateur**, puis sélectionnez **+ Ajouter** ou **Ajouter une identité managée affectée par l’utilisateur** pour ouvrir la page **Ajouter une identité managée affectée par...** .

   :::image type="content" source="media/enable-managed-identity/user-assigned-portal.png" alt-text="Onglet Affecté(e) par l'utilisateur dans le portail.":::

1. Dans la liste déroulante **Abonnement**, sélectionnez l’abonnement pour votre identité managée affectée par l’utilisateur.

   :::image type="content" source="media/enable-managed-identity/add-user-assigned.png" alt-text="Page Ajouter une identité managée affectée par l’utilisateur dans le portail.":::

1. Sous **Identités managées affectées par l’utilisateur**, sélectionnez votre identité managée affectée par l’utilisateur existante, puis sélectionnez **Ajouter**. Vous revenez à l’onglet **Affecté(e) par l’utilisateur**.

   :::image type="content" source="media/enable-managed-identity/added-user-identity-portal.png" alt-text="Ajout de l’identité managée affectée par l’utilisateur dans le portail.":::


## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin de l’identité managée affectée par l’utilisateur jointe à votre compte Automation, effectuez les étapes suivantes :

1. Sous l’onglet **Affecté(e) par l’utilisateur**, sélectionnez votre identité managée affectée par l’utilisateur.

1. Dans le menu supérieur, sélectionnez **Supprimer**, puis sélectionnez **Oui** quand vous êtes invité à confirmer.

Si vous n’avez plus besoin de l’identité managée affectée par le système activée pour votre compte Automation, effectuez les étapes suivantes :

1. Sous l’onglet **Affecté(e) par le système**, sous **État**, sélectionnez **Désactivé**.

1. Dans le menu supérieur, sélectionnez **Enregistrer**, puis sélectionnez **Oui** quand vous êtes invité à confirmer.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez activé des identités managées pour un compte Azure Automation. Pour utiliser votre compte Automation avec des identités managées afin d’exécuter un runbook, consultez :

> [!div class="nextstepaction"]
> [Tutoriel : Créer un runbook PowerShell Automation avec une identité managée](../learn/powershell-runbook-managed-identity.md)