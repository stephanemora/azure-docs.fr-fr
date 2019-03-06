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
ms.date: 01/17/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 99a1b218919380dcdb0c1dba4540d9f8219d2df3
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593639"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>Automatiser la validation Azure Stack avec PowerShell

La validation en tant que service (VaaS) offre la possibilité d’automatiser le lancement de tests à l’aide du script **LaunchVaaSTests.ps1**.

> [!NOTE]
> Automation est uniquement disponible pour le workflow de passe de test. Les workflows de validation de package et de validation de solution sont uniquement pris en charge par le biais du portail VaaS.

Ce didacticiel vous guide dans la création d’un script qui :

> [!div class="checklist"]
> * Installe les composants requis
> * Installe et démarre l’agent local
> * Lance une catégorie de tests, tels que l’intégration, le fonctionnement, la fiabilité
> * Affiche les résultats du test dans un rapport

## <a name="launch-the-test-pass-workflow"></a>Lance le workflow de passe de test

1. Ouvrez une invite PowerShell avec élévation de privilèges.

2. Exécutez le script suivant pour télécharger votre script d’automatisation :

    ```PowerShell
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://storage.azurestackvalidation.com/packages/Microsoft.VaaS.Scripts.latest.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ```

3. Exécutez le script suivant, avec les valeurs de paramètre appropriées :

    ```PowerShell
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
                          -VaaSAccountTenantId <VaaSAccountTenantId> `
                          -VaaSSolutionName <VaaSSolutionName> `
                          -VaaSTestPassName <VaaSTestPassName> `
                          -VaaSTestCategories Integration,Functional `
                          -MaxScriptWaitTimeInHours 12 `
                          -ServiceAdminUserName <AzSServiceAdminUser> `
                          -ServiceAdminUserPassword <AzSServiceAdminPassword> `
                          -TenantAdminUserName <AzSTenantAdminUser> `
                          -TenantAdminUserPassword <AzSTenantAdminPassword> `
                          -CloudAdminUserName <AzSCloudAdminUser> `
                          -CloudAdminUserPassword <AzSCloudAdminPassword>
    ```

    **Paramètres**

    | Paramètre | Description |
    | --- | --- |
    | VaaSUserId | Votre ID d’utilisateur VaaS. |
    | VaaSUserPassword | Votre mot de passe VaaS. |
    | VaaSAccountTenantId | Votre identificateur global unique de locataire VaaS GUID. |
    | VaaSSolutionName | Le nom de la solution VaaS sous laquelle le test s’exécutera. |
    | VaaSTestPassName | Le nom du workflow de passe de test VaaS à créer. |
    | VaaSTestCategories | `Integration`, `Functional` ou. `Reliability`. Si vous utilisez plusieurs valeurs, séparez-les avec une virgule.  |
    | ServiceAdminUserName | Votre compte d’administration de service Azure Stack.  |
    | ServiceAdminPassword | Votre mot de passe de service Azure Stack.  |
    | TenantAdminUserName | L’administrateur du locataire principal.  |
    | TenantAdminPassword | Le mot de passe du locataire principal.  |
    | CloudAdminUserName | Le nom d’utilisateur de l’administrateur de cloud.  |
    | CloudAdminPassword | Le mot de passe de l’utilisateur administrateur de cloud.  |

4. Passez en revue les résultats de votre test. Pour plus d’options, consultez [Surveiller et gérer les tests dans le portail VaaS](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur PowerShell sur Azure Stack, révisez les derniers modules.

> [!div class="nextstepaction"]
> [Module Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0)
