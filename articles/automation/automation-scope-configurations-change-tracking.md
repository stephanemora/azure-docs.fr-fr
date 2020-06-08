---
title: Utiliser des configurations d’étendue pour la fonctionnalité Suivi des modifications et inventaire Azure Automation
description: Cet article explique comment utiliser les configurations d’étendue lorsque vous utilisez Suivi des modifications et inventaire.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 4fac94cc2f8f378b7e9d8e9485baed6a0ffa838b
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832161"
---
# <a name="work-with-scope-configurations-for-change-tracking-and-inventory"></a>Utiliser des configurations d’étendue pour la fonctionnalité Suivi des modifications et inventaire

Cet article explique comment vous pouvez utiliser les configurations d’étendue lors de l’activation de la fonctionnalité [Update Management](automation-update-management.md) sur des machines virtuelles. 

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Vérifier la configuration de l’étendue

Update Management utilise une configuration d’étendue au sein de l’espace de travail Log Analytics pour cibler les ordinateurs qui activent Update Management. La configuration d’étendue est un groupe d’une ou plusieurs recherches enregistrées qui est utilisé pour limiter l’étendue de la fonctionnalité à des ordinateurs spécifiques. Pour accéder aux configurations d’étendue :

1. Dans votre compte Automation, sous **Ressources associées**, sélectionnez **Espace de travail**. 

2. Choisissez l’espace de travail, sous **Sources de données de l’espace de travail**, sélectionnez **Configurations des étendues**.

3. Si la fonctionnalité Update Management n’est pas encore activée pour l’espace de travail sélectionné, la configuration de l’étendue `MicrosoftDefaultScopeConfig-ChangeTracking` est créée. 

4. Si la fonctionnalité est déjà activée pour l’espace de travail sélectionné, elle n’est pas redéployée et la configuration de l’étendue n’est pas ajoutée à celle-ci. 

5. Sélectionnez les points de suspension dans toutes les configurations d’étendue, puis cliquez sur **Modifier**. 

6. Dans le volet de modification, choisissez **Sélectionner des groupes d’ordinateurs**. Le volet Groupes d’ordinateurs affiche les recherches enregistrées et utilisées pour créer la configuration d’étendue.

## <a name="view-a-saved-search"></a>Afficher une recherche enregistrée

Lorsqu’un ordinateur est ajouté à Suivi des modifications et inventaire, il est également ajouté à une recherche enregistrée dans votre espace de travail. La recherche enregistrée est une requête qui contient les ordinateurs cibles.

1. Accédez à votre espace de travail Log Analytics et sélectionnez **Recherches enregistrées** sous **Général**. La recherche enregistrée utilisée par Update Management est :

    |Nom     |Category  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

2. Sélectionnez la recherche enregistrée pour afficher la requête utilisée pour remplir le groupe. L’image suivante montre la requête et ses résultats :

    ![Recherches enregistrées](media/automation-scope-configurations-change-tracking/logsearch.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour utiliser la fonctionnalité Suivi des modifications et inventaire, consultez [Gérer Suivi des modifications et inventaire](change-tracking-file-contents.md).
* Pour résoudre les problèmes généraux liés à la fonctionnalité, consultez [Résoudre les problèmes liés à Suivi des modifications et inventaire](troubleshoot/change-tracking.md).