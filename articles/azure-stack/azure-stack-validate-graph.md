---
title: Valider l’intégration d’Azure Graph pour Azure Stack
description: Utilisez Azure Stack Readiness Checker pour valider l’intégration des graphiques pour Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: patricka
ms.reviewer: jerskine
ms.openlocfilehash: e1c1ba0a065a20874bf51d7464cbcfdfa13a571d
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49946873"
---
# <a name="validate-graph-integration-for-azure-stack"></a>Valider l’intégration des graphiques pour Azure Stack

Utilisez l’outil Azure Stack Readiness Checker (AzsReadinessChecker) pour valider que votre environnement est prêt à être utilisé pour l’intégration des graphiques à Azure Stack. Vous devez valider l’intégration des graphiques avant de commencer l’intégration des centres de données ou avant un déploiement Azure Stack.

L’outil Readiness Checker valide ce qui suit :

* Les informations d’identification sur le compte de service créé pour l’intégration des graphiques disposent des droits appropriés pour interroger Active Directory.
* Le *catalogue global* peut être résolu et est joignable.
* Le KDC peut être résolu et est joignable.
* La connectivité réseau nécessaire est en place.

Pour plus d’informations sur l’intégration des centres de données Azure Stack, consultez [Intégration au centre de données Azure Stack - Identité](azure-stack-integrate-identity.md)

## <a name="get-the-readiness-checker-tool"></a>Obtenir l’outil Readiness Checker

Téléchargez la dernière version de l’outil Azure Stack Readiness Checker (AzsReadinessChecker) sur le site [PSGallery](https://aka.ms/AzsReadinessChecker).

## <a name="prerequisites"></a>Prérequis

Vérifiez les prérequis suivants.

**Ordinateur sur lequel l’outil est exécuté :**

* Windows 10 ou Windows Server 2016, avec connectivité au domaine.
* PowerShell 5.1 ou ultérieur. Pour vérifier votre version, exécutez la commande PowerShell suivante et examinez les versions *Major* et *Minor* :  
   > `$PSVersionTable.PSVersion`
* Module Active Directory PowerShell
* Dernière version de l’outil [Microsoft Azure Stack Readiness Checker](https://aka.ms/AzsReadinessChecker)

**Environnement Active Directory :**

* Identifier le nom d’utilisateur et le mot de passe d’un compte pour le service Graph dans l’Active Directory existant
* Identifier le FQDN de racine de forêt Active Directory

## <a name="validate-graph"></a>Valider le graphique

1. Sur un ordinateur qui répond aux prérequis, ouvrez une invite PowerShell d’administration, puis exécutez la commande suivante pour installer AzsReadinessChecker.

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. À l’invite PowerShell, exécutez la commande suivante pour définir la variable *$graphCredential* sur le compte de graphique. Remplacez `contoso\graphservice` par votre compte en utilisant le format `domain\username`.

    `$graphCredential = Get-Credential contoso\graphservice -Message "Enter Credentials for the Graph Service Account"`

1. À l’invite PowerShell, exécutez ce qui suit pour démarrer la validation du graphique. Spécifiez la valeur de **-ForestFQDN** en tant que FQDN de la racine de forêt :

     `Invoke-AzsGraphValidation -ForestFQDN contoso.com -Credential $graphCredential`

1. Au terme de l’exécution de l’outil, passez en revue la sortie. Vérifiez que l’état est OK pour les conditions d’intégration des graphiques. Une validation réussie génère une image semblable à ce qui suit :

    ```
    Testing Graph Integration (v1.0)
            Test Forest Root:            OK
            Test Graph Credential:       OK
            Test Global Catalog:         OK
            Test KDC:                    OK
            Test LDAP Search:            OK
            Test Network Connectivity:   OK

    Details:

    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.

    Additional help URL: https://aka.ms/AzsGraphIntegration

    AzsReadinessChecker Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log

    AzsReadinessChecker Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsGraphValidation Completed
    ```

Dans les environnements de production, le test de la connectivité réseau à partir d’une station de travail opérateur ne peut pas être considéré comme indiquant pleinement la connectivité disponible pour Azure Stack. Le réseau d’adresses IP virtuelles publiques de l’horodatage d’Azure Stack a besoin de la connectivité de trafic LDAP pour effectuer l’intégration des identités.

## <a name="report-and-log-file"></a>Rapport et fichier journal

Chaque fois qu’une validation s’exécute, les résultats sont journalisés dans **AzsReadinessChecker.log** et **AzsReadinessCheckerReport.json**. L’emplacement de ces fichiers est indiqué avec les résultats de la validation dans PowerShell.

Ces fichiers de validation peuvent vous aider à partager l’état avant de déployer Azure Stack ou à enquêter sur les problèmes de validation. Les deux fichiers conservent les résultats des vérifications de validation postérieures. Le rapport fournit à votre équipe de déploiement la confirmation de la configuration de l’identité. Le fichier journal peut aider l’équipe de déploiement ou de support à enquêter sur les problèmes de validation.

Par défaut, les deux fichiers sont écrits dans `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`

Utilisez :

* Le paramètre **-OutputPath** *chemin* à la fin de la ligne de commande d’exécution pour spécifier un emplacement de rapport différent.
* Le paramètre **-CleanReport** à la fin de la commande d’exécution pour effacer les informations du rapport précédent *AzsReadinessCheckerReport.json*. Pour plus d’informations, consultez [Rapport de validation Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Échec de validation

En cas d’échec de vérification de la validation, des détails sont fournis dans la fenêtre PowerShell. L’outil enregistre également des informations dans le fichier *AzsGraphIntegration.log*.

## <a name="next-steps"></a>Étapes suivantes

[Afficher le rapport de préparation](azure-stack-validation-report.md)  
[Considérations générales relatives à l’intégration de Microsoft Azure Stack](azure-stack-datacenter-integration.md)  
