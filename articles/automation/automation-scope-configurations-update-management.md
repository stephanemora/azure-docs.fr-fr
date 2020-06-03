---
title: Utiliser des configurations d’étendue pour Azure Automation Update Management
description: Cet article explique comment utiliser les configurations d’étendue lorsque vous utilisez Update Management.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 23ec49f2d68cf376ef0beb118d8bf69ada7bc0de
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832025"
---
# <a name="work-with-scope-configurations-for-update-management"></a>Utiliser des configurations d’étendue pour Update Management

Cet article explique comment vous pouvez utiliser les configurations d’étendue lors de l’utilisation de la fonctionnalité [Update Management](automation-update-management.md) sur des machines virtuelles. 

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Vérifier la configuration de l’étendue

Update Management utilise une configuration d’étendue au sein de l’espace de travail Log Analytics pour cibler les ordinateurs qui activent la fonctionnalité. La configuration d’étendue est un groupe d’une ou plusieurs recherches enregistrées qui est utilisé pour limiter l’étendue de la fonctionnalité à des ordinateurs spécifiques. Pour accéder aux configurations d’étendue :

1. Dans votre compte Automation, sous **Ressources associées**, sélectionnez **Espace de travail**. 

2. Choisissez l’espace de travail, sous **Sources de données de l’espace de travail**, sélectionnez **Configurations des étendues**.

3. Si la fonctionnalité Update Management n’est pas encore activée pour l’espace de travail sélectionné, la configuration de l’étendue `MicrosoftDefaultScopeConfig-Updates` est créée. 

4. Si la fonctionnalité est déjà activée pour l’espace de travail sélectionné, elle n’est pas redéployée et la configuration de l’étendue n’est pas ajoutée à celle-ci. 

5. Sélectionnez les points de suspension dans toutes les configurations d’étendue, puis cliquez sur **Modifier**. 

6. Dans le volet de modification, choisissez **Sélectionner des groupes d’ordinateurs**. Le volet Groupes d’ordinateurs affiche les recherches enregistrées et utilisées pour créer la configuration d’étendue.

## <a name="view-a-saved-search"></a>Afficher une recherche enregistrée

Lorsqu’un ordinateur est ajouté à Update Management, il est également ajouté à une recherche enregistrée dans votre espace de travail. La recherche enregistrée est une requête qui contient les ordinateurs cibles.

1. Accédez à votre espace de travail Log Analytics et sélectionnez **Recherches enregistrées** sous **Général**. La recherche enregistrée utilisée par Update Management est :

|Nom     |Category  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     | Mises à jour        | Updates__MicrosoftDefaultComputerGroup         |

2. Sélectionnez la recherche enregistrée pour afficher la requête utilisée pour remplir le groupe. L’image suivante montre la requête et ses résultats :

    ![Recherches enregistrées](media/automation-scope-configurations-update-management/logsearch.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour utiliser la fonctionnalité, consultez [Gérer les mises à jour et les correctifs pour vos machines virtuelles Azure](automation-tutorial-update-management.md).
* Pour détecter un problème lié aux fonctionnalités, consultez [Résolution des problèmes d’Update Management](troubleshoot/update-management.md).
* Pour détecter un problème de l’agent de mise à jour Windows, consultez [Résolution des problèmes liés à l’agent de mise à jour Windows](troubleshoot/update-agent-issues.md).
* Pour détecter un problème lié à l’agent de mise à jour Linux, consultez [Résolution des problèmes de l’agent de mise à jour Linux](troubleshoot/update-agent-issues-linux.md).