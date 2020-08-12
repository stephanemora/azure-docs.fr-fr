---
title: Résoudre les problèmes de connectivité Synapse Studio (préversion) à l'aide de PowerShell
description: Résoudre les problèmes de connectivité Azure Synapse Studio à l’aide de PowerShell
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: f4afaf536a9c65758ad030e5cdeeee5fb97074d7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87046676"
---
# <a name="diagnose-azure-synapse-studio-preview-connectivity-issues-with-powershell-script"></a>Diagnostiquer les problèmes de connectivité Azure Synapse Studio (préversion) à l'aide d'un script PowerShell

Le bon fonctionnement d'Azure Synapse Studio (préversion) s'appuie sur un ensemble de points de terminaison d’API web. Ce guide vous aidera à identifier les causes des problèmes de connectivité lorsque vous :
- configurez votre réseau local (réseau derrière un pare-feu d’entreprise, par exemple) pour accéder à Azure Synapse Studio.
- rencontrez des problèmes de connectivité avec Azure Synapse Studio.

## <a name="prerequisite"></a>Configuration requise

* PowerShell 5.0 ou version ultérieure sur Windows, ou
* PowerShell Core 6.0 ou version ultérieure sur Windows ou Linux.

## <a name="troubleshooting-steps"></a>Étapes de dépannage

Cliquez avec le bouton droit sur le lien suivant, plus cliquez sur « Enregistrer la cible sous » :

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

Vous pouvez aussi ouvrir le lien directement, puis enregistrer le fichier de script ouvert. N’enregistrez pas l’adresse du lien ci-dessus car celle-ci peut être amenée à changer.

Dans l’Explorateur de fichiers, cliquez avec le bouton droit sur le fichier de script téléchargé, puis cliquez sur « Exécuter avec PowerShell ».

![Exécuter le fichier de script téléchargé avec PowerShell](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

Lorsque vous y êtes invité, entrez le nom de l’espace de travail Azure Synapse qui rencontre un problème ou dont vous souhaitez tester la connectivité, puis appuyez sur Entrée.

![Entrer le nom de l’espace de travail](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

La session de diagnostic démarre. Patientez jusqu’à la fin de son exécution.

![Attendre la fin du diagnostic](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

Une fois le diagnostic terminé, un résumé s'affiche. Si votre ordinateur n'est pas en mesure de se connecter à un ou plusieurs points de terminaison, des suggestions s’affichent dans la section « Résumé ».

![Examiner le résumé de diagnostic](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

En outre, un fichier journal de diagnostic correspondant à cette session est généré dans le même dossier que le script de résolution des problèmes. Son emplacement est indiqué dans la section « Conseils généraux » (`D:\TestAzureSynapse_2020....log`). Si nécessaire, vous pouvez envoyer ce fichier au support technique.

En tant qu'administrateur réseau, lorsque vous ajustez la configuration de votre pare-feu pour Azure Synapse Studio, les détails techniques qui s'affichent au-dessus de la section « Résumé » peuvent vous être utiles.

* Tous les éléments de test (requêtes) marqués avec « Réussi » indique que les tests de connectivité ont abouti, quel que soit le code d’état HTTP.
 En cas d'échec, la raison est indiquée en jaune, par exemple `NamedResolutionFailure` ou `ConnectFailure`. Ces raisons peuvent vous aider à déterminer d'éventuelles erreurs de configuration liées à votre environnement réseau.


## <a name="next-steps"></a>Étapes suivantes
Si les étapes précédentes ne permettent pas de résoudre le problème, [créez un ticket de support](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).
