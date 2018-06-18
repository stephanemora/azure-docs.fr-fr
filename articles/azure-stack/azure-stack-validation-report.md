---
title: Rapport de validation pour Azure Stack | Microsoft Docs
description: Utilisez le rapport de l’outil Azure Stack Readiness Checker pour passer en revue les résultats de la validation.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a0ca0ae3ed615f6bc2774364f7a443023b911b5d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937553"
---
# <a name="azure-stack-validation-report"></a>Rapport de validation Azure Stack
L’outil Azure Stack Readiness Checker exécute des validations qui prennent en charge le déploiement et la maintenance d’un environnement Azure Stack. L’outil écrit les résultats de la validation dans un fichier de rapport .json. Le rapport présente des données résumées et détaillées sur l’état des prérequis en vue du déploiement d’Azure Stack et sur la rotation des secrets pour les déploiements existants d’Azure Stack.  

 ## <a name="where-to-find-the-report"></a>Emplacement du rapport
Quand l’outil s’exécute, il journalise les résultats dans **AzsReadinessCheckerReport.json**. L’outil crée également un journal nommé **AzsReadinessChecker.log**. L’emplacement de ces fichiers est indiqué avec les résultats de la validation dans PowerShell.

![run-validation](./media/azure-stack-validation-report/validation.png)

Les deux fichiers conservent les résultats des vérifications de validation exécutées postérieurement sur le même ordinateur.  Par exemple, vous pouvez exécuter l’outil pour valider des certificats, le réexécuter pour valider l’identité Azure, puis l’exécuter une troisième fois pour valider l’inscription. Les résultats des trois validations sont disponibles dans le rapport .json généré.  

Par défaut, les deux fichiers sont écrits à l’emplacement *C:\Users\<nom_utilisateur>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
- Utilisez le paramètre **-OutputPath** ***&lt;chemin&gt;*** à la fin de la ligne de commande d’exécution pour spécifier un emplacement de rapport différent.   
- Utilisez le paramètre **-CleanReport** à la fin de la commande d’exécution pour effacer les informations du fichier *AzsReadinessCheckerReport.json*. concernant les exécutions précédentes de l’outil.

## <a name="view-the-report"></a>Afficher le rapport
Pour voir le rapport dans PowerShell, affectez à la valeur de **-ReportPath** le chemin au rapport. Cette commande affiche le contenu du rapport et identifie les validations qui n’ont pas encore de résultats.

Par exemple, pour voir le rapport à partir d’une invite PowerShell ouverte à l’emplacement du rapport, exécutez : 
   > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json` 

La sortie ressemble à celle de l’image suivante :

![view-report](./media/azure-stack-validation-report/view-report.png)

## <a name="view-the-report-summary"></a>Afficher le récapitulatif du rapport
Pour voir un récapitulatif du rapport, ajoutez le commutateur **-Summary** à la fin de la ligne de commande PowerShell. Par exemple :  
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -summary`  

Le récapitulatif affiche les validations qui n’ont pas de résultats et indique l’état de réussite ou d’échec des validations terminées. La sortie ressemble à celle de l’image suivante :

![report-summary](./media/azure-stack-validation-report/report-summary.png)


## <a name="view-a-filtered-report"></a>Afficher un rapport filtré
Pour voir un rapport filtré sur un seul type de validation, utilisez le paramètre **-ReportSections** et spécifiez l’une des valeurs suivantes en fonction du type de validation à afficher :
- Certificat
- AzureRegistration
- AzureIdentity
- Tâches   
- Tous  

Par exemple, pour afficher le récapitulatif du rapport pour les certificats uniquement, utilisez la ligne de commande PowerShell suivante : 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>Voir aussi
[Informations de référence sur l’applet de commande Start-AzsReadinessChecker](azure-stack-azsreadiness-cmdlet.md)
