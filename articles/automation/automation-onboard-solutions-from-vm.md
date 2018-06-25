---
title: Découvrez comment intégrer les solutions Update Management, Change Tracking et Inventory à partir d’une machine virtuelle Azure.
description: Découvrez comment intégrer une machine virtuelle Azure avec Update Management, Change Tracking et Inventory qui font partie d’Azure Automation.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 675aebf35a6bee6e4cc4fd884204edb5bae4b848
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830559"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Intégrer les solutions Update Management, Change Tracking et Inventory à partir d’une machine virtuelle Azure

Azure Automation fournit des solutions pour gérer les mises à jour de sécurité du système d’exploitation, le suivi des modifications et l’inventaire de ce qui est installé sur vos ordinateurs. Vous pouvez intégrer des machines de plusieurs façons : vous pouvez intégrer la solution à partir d’une machine virtuelle, [à partir de votre compte Automation](automation-onboard-solutions-from-automation-account.md), [par la navigation sur plusieurs machines](automation-onboard-solutions-from-browse.md) ou par [runbook](automation-onboard-solutions.md). Cet article traite de l’intégration de ces solutions à partir d’une machine virtuelle Azure.

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous à Azure à l’adresse https://portal.azure.com.

## <a name="enable-the-solutions"></a>Activer les solutions

Accédez à une machine virtuelle existante et sélectionnez **Update Management**, **Inventory** ou **Change Tracking** sous **OPÉRATIONS**.

Pour activer la solution pour la machine virtuelle uniquement, vérifiez que la case d’option **Activer pour cette machine virtuelle** est sélectionnée, pour intégrer plusieurs machines à la solution, sélectionnez **Activer pour les machines virtuelles dans cet abonnement** et cliquez sur **Cliquer pour sélectionner les machines à activer**. Consultez [Intégrer les solutions Update Management, Change Tracking et Inventory](automation-onboard-solutions-from-automation-account.md) pour consulter les étapes à exécuter pour intégrer plusieurs machines à la fois.

Choisissez l’espace de travail Log Analytics et un compte Automation, puis cliquez sur **Activer** pour activer la solution. L’activation de la solution prend jusqu’à 15 minutes.

![Intégrer la solution Update](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Accédez aux autres solutions, puis cliquez sur **Activer**. Les listes déroulantes des comptes Log Analytics et Automation sont désactivées, car elles utilisent le même espace de travail et le même compte Automation que la solution précédemment activée.

> [!NOTE]
> **Change Tracking** et **Inventory** utilisent la même solution. Quand l’une est activée, l’autre l’est aussi.

## <a name="scope-configuration"></a>Configuration d’étendue

Chaque solution utilise une configuration d’étendue au sein de l’espace de travail pour cibler les ordinateurs qui obtiennent la solution. La configuration d’étendue est un groupe de recherches enregistrées utilisé pour limiter l’étendue de la solution à des ordinateurs spécifiques. Pour accéder aux configurations d’étendue, dans votre compte Automation sous **RESSOURCES CONNEXES**, sélectionnez **Espace de travail**. Ensuite, dans l’espace de travail sous **SOURCES DE DONNÉES DE L’ESPACE DE TRAVAIL**, sélectionnez **Configurations des étendues**.

Si l’espace de travail sélectionné n’a pas encore les solutions de gestion des mises à jour ou de suivi des modifications, les configurations de portée suivantes sont créées :

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Si l’espace de travail sélectionné a déjà la solution. La solution n’est pas de nouveau déployée et la configuration d’étendue ne lui est pas ajoutée à.

Cliquez sur le bouton de sélection (...) dans l’une des configurations et sélectionnez **Modifier**. Dans la page **Modifier la configuration d’étendue**, sélectionnez **Sélectionner des groupes d’ordinateurs** pour ouvrir la page **Groupes d’ordinateurs**. Cette page affiche les recherches enregistrées utilisées pour créer la configuration d’étendue.

## <a name="saved-searches"></a>Recherches enregistrées

Quand un ordinateur est ajouté aux solutions Update Management, Change Tracking et Inventaire, il est ajouté à l’une des deux recherches enregistrées dans votre espace de travail. Ces recherches enregistrées sont des requêtes qui contiennent les ordinateurs ciblés pour ces solutions.

Accédez à votre espace de travail et sélectionnez **Recherches enregistrées** sous **Général**. Les deux recherches enregistrées utilisées par ces solutions sont présentées dans le tableau suivant :

|NOM     |Catégorie  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Mises à jour        | Updates__MicrosoftDefaultComputerGroup         |

Sélectionnez l’une des deux recherches enregistrées pour afficher la requête utilisée pour remplir le groupe. L’image suivante montre la requête et ses résultats.

![Recherches enregistrées](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="next-steps"></a>Étapes suivantes

Poursuivez avec les tutoriels sur les solutions pour apprendre à les utiliser.

* [Tutoriel - Gérer les mises à jour pour votre machine virtuelle](automation-tutorial-update-management.md)

* [Tutoriel - Identifier les logiciels sur une machine virtuelle](automation-tutorial-installed-software.md)

* [Tutoriel - Résoudre les modifications sur une machine virtuelle](automation-tutorial-troubleshoot-changes.md)
