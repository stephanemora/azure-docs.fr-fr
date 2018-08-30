---
title: Valider des certificats pour infrastructure à clé publique Azure Stack pour le déploiement de systèmes intégrés Azure Stack | Microsoft Docs
description: Explique comment valider les certificats pour infrastructure à clé publique Azure Stack pour des systèmes intégrés Azure Stack. Couvre l’utilisation de l’outil de vérification de certificats d’Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: e381d2ed3c6a972d776dd31f311fcebe2e35823a
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42917081"
---
# <a name="validate-azure-stack-pki-certificates"></a>Valider des certificats PKI Azure Stack

L’outil Azure Stack Readiness Checker décrit dans cet article est disponible [à partir de PowerShell Gallery](https://aka.ms/AzsReadinessChecker). Vous pouvez utiliser cet outil pour vérifier que les [certificats pour infrastructure à clé publique (PKI) générés](azure-stack-get-pki-certs.md) sont adaptés au prédéploiement. Vous devez valider les certificats en gardant suffisamment de temps pour tester et réémettre les certificats si nécessaire.

L’outil Readiness Checker effectue les validations de certificat suivantes :

- **Lecture PFX**  
    Vérifie la validité du fichier PFX et l’exactitude du mot de passe et vous avertit si les informations publiques ne sont pas protégées par le mot de passe. 
- **Algorithme de signature**  
    Vérifie que l’algorithme de signature n’est pas SHA1 (Secure Hash Algorithm 1).
- **Clé privée**  
    Vérifie que la clé privée est présente et qu’elle est exportée avec l’attribut d’ordinateur local. 
- **Chaîne d’approbation**  
    Vérifie que la chaîne d’approbation est intacte, y compris pour les certificats auto-signés.
- **Noms DNS**  
    Vérifie que le réseau SAN contient les noms DNS appropriés pour chaque point de terminaison ou si un caractère générique de prise en charge est présent.
- **Syntaxe de la clé**  
    Vérifie que la syntaxe de la clé contient une signature numérique et un chiffrement de clé, et que la syntaxe avancée de la clé contient l’authentification du serveur et l’authentification du client.
- **Taille de la clé**  
    Vérifie que la taille de clé est au moins égale à 2 048 octets.
- **Ordre de la chaîne**  
    Vérifie l’ordre des autres certificats afin de garantir que l’ordre est correct.
- **Autres certificats**  
    Assurez-vous qu’aucun autre certificat n’a été packagé dans le PFX, à part le certificat feuille pertinent et sa chaîne.
- **Aucun profil**  
    Vérifie qu’un nouvel utilisateur peut charger les données PFX sans profil utilisateur chargé, en simulant le comportement des comptes de service administré de groupe pendant la maintenance du certificat.

> [!IMPORTANT]  
> Le certificat PKI est un fichier PFX et le mot de passe doit être considéré comme une information sensible.

## <a name="prerequisites"></a>Prérequis

Votre système doit respecter la configuration requise ci-après afin de permettre la validation des certificats PKI pour un déploiement Azure Stack :

- Outil Microsoft Azure Stack Readiness Checker
- Certificats SSL exportés conformément aux [instructions de préparation](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 ou Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>Effectuer la validation principale des certificats de service

Pour préparer et valider les certificats PKI Azure Stack en vue du déploiement et de la rotation des clés, effectuez les étapes suivantes :

1. Installez **AzsReadinessChecker** à partir d’une invite PowerShell (5.1 ou version ultérieure) en exécutant l’applet de commande suivante :

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ````

2. Créez la structure de répertoires de certificat. Dans l’exemple ci-après, vous pouvez remplacer `<c:\certificates>` par un nouveau chemin d’accès de répertoire de votre choix.

    ````PowerShell  
    New-Item C:\Certificates -ItemType Directory
    
    $directories = 'ACSBlob','ACSQueue','ACSTable','ADFS','Admin Portal','ARM Admin','ARM Public','Graph','KeyVault','KeyVaultInternal','Public Portal'
    
    $destination = 'c:\certificates'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ````
    
    > [!Note]  
    > AD FS et Graph sont obligatoires si vous utilisez AD FS comme système d’identité.
    
     - Placez vos certificats dans les répertoires appropriés créés à l’étape précédente. Par exemple :   
        - `c:\certificates\ACSBlob\CustomerCertificate.pfx`
        - `c:\certificates\Certs\Admin Portal\CustomerCertificate.pfx`
        - `c:\certificates\Certs\ARM Admin\CustomerCertificate.pfx`

3. Dans la fenêtre PowerShell, changez les valeurs de **RegionName** et **FQDN** en fonction de l’environnement Azure Stack et exécutez la commande suivante :

    ````PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD 

    ````

4. Vérifiez que la sortie et tous les certificats réussissent tous les tests. Par exemple : 

    ````PowerShell
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Validation

    Starting Azure Stack Certificate Validation 1.1803.405.3
    Testing: ARM Public\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: ACSBlob\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location: 
    C:\AzsReadinessChecker\AzsReadinessReport.json
    AzsReadinessChecker Completed
    ````

### <a name="known-issues"></a>Problèmes connus

**Symptôme** : les tests sont ignorés.

**Cause** : AzsReadinessChecker ignore certains tests si aucune dépendance n’est détectée :

 - Si la chaîne d’approbation échoue, les autres certificats sont ignorés.

    ````PowerShell  
    Testing: ACSBlob\singlewildcard.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: Fail
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: Skipped
    Details:
    The certificate records '*.east.azurestack.contoso.com' do not contain a record that is valid for '*.blob.east.azurestack.contoso.com'. Please refer to the documentation for how to create the required certificate file.
    The Other Certificates check was skipped because Cert Chain and/or DNS Names failed. Follow the guidance to remediate those issues and recheck. 
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location (for OEM): C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

**Résolution** : suivez les instructions de l’outil dans la section des détails sous chaque ensemble de tests de chacun des certificats.

## <a name="perform-platform-as-a-service-certificate-validation"></a>Effectuer une validation de certificats Platform as a service

Utilisez ces étapes pour préparer et valider les certificats PKI Azure Stack pour les certificats PaaS (Platform as a service), si des déploiements SQL/MySQL ou App Services sont planifiés.

1.  Installez **AzsReadinessChecker** à partir d’une invite PowerShell (5.1 ou version ultérieure) en exécutant l’applet de commande suivante :

    ````PowerShell  
      Install-Module Microsoft.AzureStack.ReadinessChecker -force
    ````

2.  Créez une table de hachage imbriquée qui contient les chemins et mot de passe de chaque certificat PaaS à valider. Dans la fenêtre PowerShell, exécutez les commandes suivantes :

    ```PowerShell
        $PaaSCertificates = @{
        'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        }
    ```

3.  Changez les valeurs de **RegionName** et **FQDN** en fonction de votre environnement Azure Stack pour démarrer la validation. Exécutez ensuite la commande suivante :

    ```PowerShell
    Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com 
    ```
4.  Vérifiez que la sortie et tous les certificats réussissent tous les tests.

    ```PowerShell
    AzsReadinessChecker v1.1805.425.2 started
    Starting PaaS Certificate Validation
    
    Starting Azure Stack Certificate Validation 1.0 
    Testing: PaaSCerts\wildcard.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\api.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\wildcard.dbadapter.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\sso.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
    ```

## <a name="using-validated-certificates"></a>Utilisation des certificats validés

Une fois que vos certificats ont été validés par l’outil AzsReadinessChecker, vous êtes prêt à les utiliser dans votre déploiement Azure Stack ou pour la rotation des secrets Azure Stack. 

 - Pour le déploiement, transférez en toute sécurité vos certificats à votre ingénieur de déploiement pour lui permettre de les copier sur l’hôte de déploiement, comme spécifié dans la [documentation relative aux exigences de certificat pour infrastructure à clé publique Azure Stack](azure-stack-pki-certs.md).
 - Pour la rotation des secrets, vous pouvez utiliser les certificats afin de mettre à jour les anciens certificats pour les points de terminaison d’infrastructure publique de votre environnement Azure Stack en suivant les instructions de la [documentation concernant la rotation des secrets Azure Stack](azure-stack-rotate-secrets.md).
 - Pour les services PaaS, vous pouvez utiliser les certificats afin d’installer des fournisseurs de ressources SQL, MySQL et App Services dans Azure Stack en suivant la documentation [Vue d’ensemble de l’offre de services dans Azure Stack](azure-stack-offer-services-overview.md).

## <a name="next-steps"></a>Étapes suivantes

[Intégration des identités au centre de données](azure-stack-integrate-identity.md)