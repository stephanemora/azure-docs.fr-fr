---
title: Utiliser l’outil de validation Azure Stack | Microsoft Docs
description: Comment collecter des fichiers journaux de diagnostics dans Azure Stack
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 12/03/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 82a691c0e0b6280a168605d56ee628d81f10823f
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245648"
---
# <a name="validate-azure-stack-system-state"></a>Valider l’état du système Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

En tant qu’opérateur Azure Stack, il est essentiel que vous puissiez connaître l’intégrité et l’état de votre système à la demande. L’outil de validation Azure Stack (**Test-AzureStack**) est une applet de commande PowerShell qui vous permet d’exécuter une série de tests sur votre système pour identifier les éventuelles défaillances. Il vous sera généralement demandé d’exécuter cet outil par le biais du [point de terminaison privilégié (PEP)](azure-stack-privileged-endpoint.md) quand vous contacterez les services de support technique (CSS) Microsoft pour signaler un problème. Avec toutes les informations sur l’état et l’intégrité du système à portée de main, les services CSS peuvent collecter et analyser des journaux détaillés, se concentrer sur la zone où l’erreur s’est produite et collaborer avec vous afin de résoudre le problème.

## <a name="running-the-validation-tool-and-accessing-results"></a>Exécution de l’outil de validation et accès aux résultats

Comme indiqué précédemment, l’outil de validation est exécuté par le biais du point de terminaison privilégié. Chaque test retourne un état **PASS/FAIL** dans la fenêtre PowerShell. De plus, un rapport HTML détaillé est créé, auquel vous pouvez accéder ultérieurement pendant la [collecte des journaux](azure-stack-diagnostics.md). Voici une brève présentation du processus de test de validation de bout en bout : 

1. Accédez au point de terminaison privilégié. Exécutez les commandes suivantes afin d’établir une session de point de terminaison privilégié :

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > Pour accéder au point de terminaison privilégié sur un ordinateur hôte du Kit ASDK, utilisez AzS-ERCS01 pour -ComputerName.

2. Une fois que vous êtes dans le point de terminaison privilégié, exécutez : 

   ```powershell
   Test-AzureStack
   ```

   Pour plus d’informations, consultez les sections [Considérations relatives aux paramètres](azure-stack-diagnostic-test.md#parameter-considerations) et [Exemples d’utilisation](azure-stack-diagnostic-test.md#use-case-examples).

3. Si un test retourne **FAIL**, exécutez :

   ```powershell
   Get-AzureStackLog -FilterByRole SeedRing -OutputSharePath "<path>" -OutputShareCredential $cred
   ```

   L’applet de commande recueille des journaux générés par Test-AzureStack. Pour plus d’informations sur les journaux de diagnostic, voir [Outils de diagnostics Azure Stack](azure-stack-diagnostics.md). Vous ne devez pas recueillir de journaux ni contacter les services de support technique si les tests retournent **WARN**.

4. Si les services de support technique vous ont demandé d’exécuter l’outil de validation, le représentant du support technique vous demandera de lui fournir les journaux que vous avez recueillis afin de poursuivre la résolution de votre problème.

## <a name="tests-available"></a>Tests disponibles

L’outil de validation vous permet d’exécuter une série de tests au niveau du système et des scénarios cloud de base. Avec ces insights sur l’état actuel, vous pouvez identifier les problèmes affectant votre système.

### <a name="cloud-infrastructure-tests"></a>Tests d’infrastructure cloud

Ces tests à faible impact sont exécutés au niveau de l’infrastructure et fournissent des informations sur les différents composants et fonctions du système. Actuellement, les tests sont regroupés dans les catégories suivantes :

| Catégorie de test                                        | Argument pour -Include et -Ignore |
| :--------------------------------------------------- | :-------------------------------- |
| Résumé des alertes Azure Stack                            | AzsAlertSummary                   |
| Résumé de l’accessibilité du partage de sauvegarde Azure Stack       | AzsBackupShareAccessibility       |
| Résumé du plan de contrôle Azure Stack                    | AzsControlPlane                   |
| Résumé Defender Azure Stack                         | AzsDefenderSummary                |
| Résumé du microprogramme de l’infrastructure d’hébergement Azure Stack  | AzsHostingInfraFWSummary          |
| Résumé de l’infrastructure d’hébergement cloud Azure Stack     | AzsHostingInfraSummary            |
| Utilisation de l’infrastructure d’hébergement cloud Azure Stack | AzsHostingInfraUtilization        |
| Capacité de l’infrastructure Azure Stack                  | AzsInfraCapacity                  |
| Performances de l’infrastructure Azure Stack               | AzsInfraPerformance               |
| Résumé des rôles d’infrastructure Azure Stack              | AzsInfraRoleSummary               |
| Résumé de mise à jour Azure Stack                           | AzsInfraUpdateSummary             |
| Résumé de l’API et du portail Azure Stack                   | AzsPortalAPISummary               |
| Événements de machine virtuelle d’unité d’échelle Azure Stack                     | AzsScaleUnitEvents                |
| Ressources de machine virtuelle d’unité d’échelle Azure Stack                  | AzsScaleUnitResources             |
| Résumé des validations SDN Azure                   | AzsSDNValidation                  |
| Résumé des rôles Azure Stack Service Fabric              | AzsSFRoleSummary                  |
| Résumé BMC Azure Stack                              | AzsStampBMCSummary                |
| Résumé des services de stockage Azure Stack                 | AzsStorageSvcsSummary             |
| Résumé de magasin SQL Azure Stack                        | AzsStoreSummary                   |
| Résumé de la sélection élective de machine virtuelle Azure Stack                     | AzsVmPlacement                    |

### <a name="cloud-scenario-tests"></a>Tests de scénarios cloud

En plus des tests d’infrastructure ci-dessus, vous pouvez également exécuter des tests de scénarios cloud pour vérifier les fonctionnalités entre les composants d’infrastructure. Des informations d’identification d’administrateur du cloud sont nécessaires pour exécuter ces tests, car ceux-ci impliquent un déploiement de ressources. 
    > [!NOTE]
    >
    > Currently you cannot run cloud scenario tests using Active Directory Federated Services (AD FS) credentials. 

Les scénarios cloud suivants sont testés par l’outil de validation :
- Création de groupe de ressources   
- Création de plan              
- Création d’offre            
- Création de compte de stockage   
- Création de machine virtuelle 
- Opération de stockage d’objet blob   
- Opération de stockage de file d’attente  
- Opération de stockage de table  

## <a name="parameter-considerations"></a>Considérations relatives aux paramètres

- Vous pouvez utiliser le paramètre **List** pour afficher toutes les catégories de tests disponibles.

- Vous pouvez utiliser les paramètres **Include** et **Ignore** pour inclure ou exclure des catégories de tests. Pour plus d’informations sur le raccourci à utiliser avec ces arguments, consultez la section [Tests disponibles](azure-stack-diagnostic-test.md#tests-available).

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- Une machine virtuelle de locataire est déployée dans le cadre de l’un des tests de scénario cloud. Vous pouvez utiliser **DoNotDeployTenantVm** pour désactiver cette option. 

- Vous devez fournir le paramètre **ServiceAdminCredential** pour exécuter des tests de scénarios cloud, comme décrit dans la section [Exemples d’utilisation](azure-stack-diagnostic-test.md#use-case-examples).

- **BackupSharePath** et **BackupShareCredential** sont utilisés quand vous testez des paramètres de sauvegarde d’infrastructure comme indiqué dans la section [Exemples d’utilisation](azure-stack-diagnostic-test.md#use-case-examples).

- L’outil de validation prend également en charge des paramètres PowerShell courants : Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable et OutVariable. Pour plus d’informations, voir [About Common Parameters](https://go.microsoft.com/fwlink/?LinkID=113216) (À propos des paramètres courants).  

## <a name="use-case-examples"></a>Exemples d’utilisation

### <a name="run-validation-without-cloud-scenarios"></a>Exécuter la validation sans scénarios cloud

Exécutez l’outil de validation sans le paramètre **ServiceAdminCredential** pour ignorer l’exécution des tests de scénarios cloud : 

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred
Test-AzureStack
```

### <a name="run-validation-with-cloud-scenarios"></a>Exécuter la validation avec scénarios cloud

Quand vous spécifiez le paramètre **ServiceAdminCredentials**, l’outil de validation exécute les tests de scénarios cloud par défaut : 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" 
```

Si vous souhaitez exécuter UNIQUEMENT des scénarios cloud sans exécuter le reste des tests, vous pouvez utiliser le paramètre **Include** : 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" -Include AzsScenarios   
```

Vous devez taper le nom d’utilisateur de l’administrateur du cloud au format UPN : serviceadmin@contoso.onmicrosoft.com (Azure AD). Lorsque vous y êtes invité, saisissez le mot de passe du compte d’administrateur de cloud.

### <a name="run-validation-tool-to-test-system-readiness-before-installing-update-or-hotfix"></a>Exécuter l’outil de validation pour tester la disponibilité du système avant d’installer une mise à jour ou un correctif logiciel

Avant de commencer l’installation d’une mise à jour ou d’un correctif, vous devez exécuter l’outil de validation pour vérifier l’état de votre système Azure Stack :

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
```

### <a name="run-validation-tool-to-test-infrastructure-backup-settings"></a>Exécuter l’outil de validation pour tester les paramètres de sauvegarde d’infrastructure

*Avant* de configurer la sauvegarde de l’infrastructure, vous pouvez tester le chemin et les informations d’authentification du partage de sauvegarde à l’aide du test **AzsBackupShareAccessibility** : 

  ```powershell
  New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
  ```

*Après* avoir configuré la sauvegarde, vous pouvez exécuter **AzsBackupShareAccessibility** pour vérifier que le partage est accessible à partir du système ERCS :

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility
  ```

Pour tester les nouvelles informations d’identification avec le partage de sauvegarde configuré, exécutez : 

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential "<PSCredential for backup share>"
  ```



## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les outils de diagnostic Azure Stack et l’enregistrement des problèmes, consultez [Outils de diagnostic Azure Stack](azure-stack-diagnostics.md).

Pour plus d’informations sur la résolution des problèmes, consultez [Résolution des problèmes de Microsoft Azure Stack](azure-stack-troubleshooting.md).