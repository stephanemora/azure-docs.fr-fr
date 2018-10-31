---
title: Valider l’intégration d’ADFS pour Azure Stack
description: Utilisez Azure Stack Readiness Checker pour valider l’intégration de l’ADFS pour Azure Stack.
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
ms.openlocfilehash: 0aa13f2fced9122163d5278b5bb50cc1e11a0379
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49946874"
---
# <a name="validate-adfs-integration-for-azure-stack"></a>Valider l’intégration d’ADFS pour Azure Stack

Utilisez l’outil Azure Stack Readiness Checker (AzsReadinessChecker) pour valider que votre environnement est prêt à être utilisé pour l’intégration d’ADFS à Azure Stack. Vous devez valider l’intégration d’ADFS avant de commencer l’intégration des centres de données ou avant un déploiement Azure Stack.

L’outil Readiness Checker valide ce qui suit :

* Les *métadonnées de fédération* contiennent les éléments XML valides pour la fédération.
* Le *certificat ADFS SSL* peut être récupéré et la chaîne d’approbation peut être générée. L’ADFS sur timbre doit approuver la chaîne de certificats SSL. Le certificat doit être signé par la même *autorité de certification* que les certificats de déploiement Azure Stack ou par un partenaire d’autorité racine approuvé. Pour obtenir la liste complète des partenaires d’autorité racine approuvés, consultez : https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca
* Le *Certificat de signature ADFS* est approuvé et n’approche pas l’expiration.

Pour plus d’informations sur l’intégration des centres de données Azure Stack, consultez [Intégration au centre de données Azure Stack - Identité](azure-stack-integrate-identity.md).

## <a name="get-the-readiness-checker-tool"></a>Obtenir l’outil Readiness Checker

Téléchargez la dernière version de l’outil Azure Stack Readiness Checker (AzsReadinessChecker) sur le site [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Prérequis

Vérifiez les prérequis suivants.

**Ordinateur sur lequel l’outil est exécuté :**

* Windows 10 ou Windows Server 2016, avec connectivité au domaine.
* PowerShell 5.1 ou ultérieur. Pour vérifier votre version, exécutez la commande PowerShell suivante et examinez les versions *Major* et *Minor* :  
   > `$PSVersionTable.PSVersion`
* Dernière version de l’outil [Microsoft Azure Stack Readiness Checker](https://aka.ms/AzsReadinessChecker).

**Environnement services de fédération Active Directory :**

Vous avez besoin au minimum d’une des formes de métadonnées suivantes :

* L’URL des métadonnées de fédération ADFS. Par exemple, `https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`
* Le fichier XML des métadonnées de fédération. Par exemple, FederationMetadata.xml

## <a name="validate-adfs-integration"></a>Valider l’intégration d’ADFS

1. Sur un ordinateur qui répond aux prérequis, ouvrez une invite PowerShell d’administration, puis exécutez la commande suivante pour installer AzsReadinessChecker.

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. À l’invite PowerShell, exécutez ce qui suit pour démarrer la validation. Spécifiez la valeur de **- CustomADFSFederationMetadataEndpointUri** comme étant l’URI pour les métadonnées de fédération :

     `Invoke-AzsADFSValidation -CustomADFSFederationMetadataEndpointUri https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

1. Au terme de l’exécution de l’outil, passez en revue la sortie. Vérifiez que l’état est OK pour les conditions d’intégration d’ADFS. Une validation réussie s’affiche de manière semblable à ce qui suit.

    ```
    Invoke-AzsADFSValidation v1.1809.1001.1 started.

    Testing ADFS Endpoint https://sts.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

            Read Metadata:                         OK
            Test Metadata Elements:                OK
            Test SSL ADFS Certificate:             OK
            Test Certificate Chain:                OK
            Test Certificate Expiry:               OK

    Details:
    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.
    Additional help URL: https://aka.ms/AzsADFSIntegration

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsADFSValidation Completed
    ```

Dans les environnements de production, le test des chaînes de confiance des certificats à partir d’une station de travail d’opérateur ne peut pas être considéré comme un bon indicateur de l’état de la confiance en l’infrastructure à clé publique dans l’infrastructure Azure Stack. Le réseau d’adresse IP virtuelle publique du timbre Azure Stack a besoin de la connectivité à la CRL pour l’infrastructure à clé publique.

## <a name="report-and-log-file"></a>Rapport et fichier journal

Chaque fois qu’une validation s’exécute, les résultats sont journalisés dans **AzsReadinessChecker.log** et **AzsReadinessCheckerReport.json**. L’emplacement de ces fichiers est indiqué avec les résultats de la validation dans PowerShell.

Ces fichiers de validation peuvent vous aider à partager l’état avant de déployer Azure Stack ou à enquêter sur les problèmes de validation. Les deux fichiers conservent les résultats des vérifications de validation postérieures. Le rapport fournit à votre équipe de déploiement la confirmation de la configuration de l’identité. Le fichier journal peut aider l’équipe de déploiement ou de support à enquêter sur les problèmes de validation.

Par défaut, les deux fichiers sont écrits dans `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`.

Utilisez :

* Le paramètre *de chemin* **-OutputPath** à la fin de la ligne de commande d’exécution pour spécifier un emplacement de rapport différent.
* Le paramètre **-CleanReport** à la fin de la commande d’exécution pour effacer les informations *AzsReadinessCheckerReport.json* du rapport précédent. Pour plus d’informations, consultez [Rapport de validation Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Échec de validation

En cas d’échec de vérification de la validation, des détails sont fournis dans la fenêtre PowerShell. L’outil journalise également des informations dans le fichier *AzsReadinessChecker.log*.

Les exemples suivants donnent des conseils sur la façon de résoudre les échecs de validation courants.

### <a name="command-not-found"></a>Commande introuvable

`Invoke-AzsADFSValidation : The term 'Invoke-AzsADFSValidation' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.`

**Cause** : La fonctionnalité de chargement automatique PowerShell n’a pas pu charger le module de Readiness Checker correctement.

**Résolution** : Importez le module Readiness Checker de façon explicite. Copiez et collez le code ci-dessous dans PowerShell et mettez à jour la \<version\> par le numéro de version de la version actuellement installée.

`Import-Module "c:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\<version>\Microsoft.AzureStack.ReadinessChecker.psd1" -Force`

## <a name="next-steps"></a>Étapes suivantes

[Afficher le rapport de préparation](azure-stack-validation-report.md)  
[Considérations générales relatives à l’intégration de Microsoft Azure Stack](azure-stack-datacenter-integration.md)  
