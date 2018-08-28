---
title: Automatiser la validation Azure Stack avec PowerShell | Microsoft Docs
description: Vous pouvez automatiser la validation Azure Stack avec PowerShell.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 1cb4b1a7cfd72ea302676244a53af58e77215aa9
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234446"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>Automatiser la validation Azure Stack avec PowerShell 

La validation en tant que service (VaaS) offre la possibilité d’automatiser le lancement de tests à l’aide du script **LaunchVaaSTests.ps1**.

Vous pouvez utiliser PowerShell pour le flux de travail suivant :

- Flux de travail de passe de test

Ce script comprend les quatre éléments d’un flux de travail :

- Installation des composants requis.
- Installation et démarrage de l’agent local.
- Lancement d’une catégorie de tests, tels que l’intégration le fonctionnement, la fiabilité.
- Génération de fichiers de surveillance et de rapports pour chaque résultat de test.

## <a name="launch-the-test-pass-workflow"></a>Lancer le flux de travail de passe de test

1. Ouvrez une invite PowerShell avec élévation de privilèges.

2. Exécutez le script suivant pour télécharger votre script d’automatisation :

    ````PowerShell  
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://vaastestpacksprodeastus.blob.core.windows.net/packages/Microsoft.VaaS.Scripts.3.0.0.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ````

3. Exécutez le script suivant avec vos valeurs :

    ````PowerShell  
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>"  -AsPlainText -Force)
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<ServiceAdminUser>", (ConvertTo-SecureString "<ServiceAdminPassword>" -AsPlainText -Force)
    $TenantAdminCreds = New-Object System.Management.Automation.PSCredential "<TenantAdminUser>", (ConvertTo-SecureString "<TenantAdminPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
        -VaaSAccountTenantId <VaaSAccountTenantId> `
        -VaaSSolutionName <VaaSSolutionName> `
        -VaaSTestPassName <VaaSTestPassName> `
        -VaaSTestCategories Integration,Functional `
        -MaxScriptWaitTimeInHours 12 `
        -ServiceAdminCreds $ServiceAdminCreds `
    ````

    **Paramètres**

    | Paramètre | Description |
    | --- | --- |
    | VaaSUserld | Votre ID d’utilisateur VaaS. | 
    | VaaSUserPassword | Votre mot de passe VaaS. |
    | ServiceAdminUser | Votre compte d’administration de service Azure Stack.  |
    | ServiceAdminPassword | Votre mot de passe de service Azure Stack.  |
    | TenantAdminUser | L’administrateur du locataire principal.  |
    | TenantAdminPassword | Le mot de passe du locataire principal.  |
    | FunctionalCategory| Intégration, Fonctionnelle, ou Fiabilité. Si vous utilisez plusieurs valeurs, séparez-les avec une virgule.  |

4. Passez en revue les résultats de votre test. Pour plus d’informations sur la lecture de vos résultats de test, consultez [Surveiller des tests](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>Étapes suivantes

 - Apprenez-en davantage sur le service [Validation en tant que service pour Azure Stack](https://docs.microsoft.com/azure/azure-stack/partner).
 - Pour en savoir plus à propos de PowerShell sur Azure Stack, consultez la référence [Azure Stack Module](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0).