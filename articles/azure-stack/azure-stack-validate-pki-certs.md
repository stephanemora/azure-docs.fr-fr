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
ms.date: 04/11/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: cd917165804314f6ee4ee006e3f29263d8d4b4c5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
---
# <a name="validate-azure-stack-pki-certificates"></a>Valider des certificats PKI Azure Stack

L’outil Azure Stack Readiness Checker décrit dans cet article est accessible [à partir de PowerShell Gallery](https://aka.ms/AzsReadinessChecker). Vous pouvez utiliser cet outil pour vérifier que les [certificats pour infrastructure à clé publique (PKI) générés](azure-stack-get-pki-certs.md) sont adaptés au prédéploiement. Vous devez valider les certificats en gardant suffisamment de temps pour tester et réémettre les certificats si nécessaire.

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

> [!IMPORTANT]  
> Le certificat PKI est un fichier PFX et le mot de passe doit être considéré comme une information sensible.

## <a name="prerequisites"></a>Prérequis


Votre système doit respecter la configuration requise ci-après afin de permettre la validation des certificats PKI pour un déploiement Azure Stack :

- Outil Microsoft Azure Stack Readiness Checker
- Certificats SSL exportés conformément aux [instructions de préparation](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 ou Windows Server 2016

## <a name="perform-certificate-validation"></a>Valider les certificats

Pour préparer et valider les certificats PKI Azure Stack, procédez comme suit :

1. Installez AzsReadinessChecker à partir d’une invite PowerShell (5.1 ou version ultérieure) en exécutant l’applet de commande suivante :

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker 
    ````

2. Créez la structure de répertoires de certificat. Dans l’exemple ci-après, vous pouvez remplacer `<c:\certificates>` par un nouveau chemin d’accès de répertoire de votre choix.

    ````PowerShell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','ADFS','Admin Portal','ARM Admin','ARM Public','Graph','KeyVault','KeyVaultInternal','Public Portal' 

    $destination = 'c:\certificates' 

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}  
    ````

 - Placez vos certificats dans les répertoires appropriés créés à l’étape précédente. Par exemple :   
    - c:\certificates\ACSBlob\CustomerCertificate.pfx 
    - c:\certificates\Certs\Admin Portal\CustomerCertificate.pfx 
    - c:\certificates\Certs\ARM Admin\CustomerCertificate.pfx 
    - Etc. 

3. Dans la fenêtre PowerShell, exécutez les commandes suivantes :

    ````PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ````

4. Examinez la sortie pour vérifier que tous les certificats ont réussi les tests. Par exemple : 

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
    AzsReadinessChecker Report location (for OEM): C:\AzsReadinessChecker\AzsReadinessReport.json
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

## <a name="using-validated-certificates"></a>Utilisation des certificats validés

Une fois que vos certificats ont été validés par l’outil AzsReadinessChecker, vous êtes prêt à les utiliser dans votre déploiement Azure Stack ou pour la rotation des secrets Azure Stack. 

 - Pour le déploiement, transférez en toute sécurité vos certificats à votre ingénieur de déploiement pour lui permettre de les copier sur l’hôte de déploiement, comme spécifié dans la [documentation relative aux exigences de certificat pour infrastructure à clé publique Azure Stack](azure-stack-pki-certs.md).
 - Pour la rotation des secrets, vous pouvez utiliser les certificats afin de mettre à jour les anciens certificats pour les points de terminaison d’infrastructure publique de votre environnement Azure Stack en suivant les instructions de la [documentation concernant la rotation des secrets Azure Stack](azure-stack-rotate-secrets.md).

## <a name="next-steps"></a>Étapes suivantes

[Intégration des identités au centre de données](azure-stack-integrate-identity.md)
