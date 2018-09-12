---
title: Générer des certificats pour infrastructure à clé publique Azure Stack pour le déploiement de systèmes intégrés Azure Stack | Microsoft Docs
description: Décrit le processus de déploiement de certificat pour infrastructure à clé publique Azure Stack pour des systèmes intégrés Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 698e044aea6bbd78847cb209160c1fa6b2edcdbf
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2018
ms.locfileid: "44023417"
---
# <a name="azure-stack-certificates-signing-request-generation"></a>Génération de CSR Azure Stack

L’outil Azure Stack Readiness Checker décrit dans cet article est disponible [à partir de PowerShell Gallery](https://aka.ms/AzsReadinessChecker). L’outil crée des requêtes de signature de certificat (CSR) appropriées pour un déploiement Azure Stack. Les certificats doivent être demandés, générés et validés avec suffisamment de temps pour les tester avant le déploiement.

L’outil Azure Stack Readiness Checker (AzsReadinessChecker) exécute les requêtes de certificat suivantes :

 - **Requêtes de certificat standard**  
    Requête conforme à [Générer des certificats d’infrastructure à clé publique pour le déploiement d’Azure Stack](azure-stack-get-pki-certs.md).
 - **Plateforme en tant que service (PaaS)**  
    Vous pouvez également demander des noms PaaS aux certificats comme spécifié dans [Exigences de certificat pour infrastructure à clé publique Azure Stack - Certificats PaaS facultatifs](azure-stack-pki-certs.md#optional-paas-certificates).



## <a name="prerequisites"></a>Prérequis

Votre système doit respecter la configuration requise suivante avant de générer les CSR pour les certificats PKI pour un déploiement Azure Stack :

 - Outil Microsoft Azure Stack Readiness Checker
 - Attributs de certificat :
    - Nom de la région
    - Nom de domaine pleinement qualifié externe (FQDN)
    - Objet
 - Windows 10 ou Windows Server 2016
 
  > [!NOTE]
  > Après avoir récupéré vos certificats de sauvegarde auprès de votre autorité de certification, vous devez effectuer les étapes de [Préparer des certificats PKI Azure Stack](azure-stack-prepare-pki-certs.md) sur le même système !

## <a name="generate-certificate-signing-requests"></a>Générer la ou les requêtes de signature de certificat

Suivez ces étapes pour préparer et valider les certificats PKI Azure Stack : 

1.  Installez AzsReadinessChecker à partir d’une invite PowerShell (5.1 ou version ultérieure) en exécutant l’applet de commande suivante :

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ````

2.  Déclarez **l’objet** en tant que dictionnaire trié. Par exemple :  

    ````PowerShell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"} 
    ````
    > [!note]  
    > Si un nom commun (CN) est fourni, il sera remplacé par le premier nom DNS de la requête de certificat.

3.  Déclarez un répertoire de sortie qui existe déjà. Par exemple : 

    ````PowerShell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ````
4.  Déclarer l’identification du système

    Azure Active Directory

    ```PowerShell
    $IdentitySystem = "AAD"
    ````

    Services de fédération Active Directory (AD FS)

    ```PowerShell
    $IdentitySystem = "ADFS"
    ````

5. Déclarez le **nom de région** et un **FQDN externe** pour le déploiement Azure Stack.

    ```PowerShell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ````

    > [!note]  
    > `<regionName>.<externalFQDN>` constitue la base sur laquelle tous les noms DNS externes dans Azure Stack sont créés. Dans cet exemple, le portail serait `portal.east.azurestack.contoso.com`.  

6. Pour générer des requêtes de signature de certificat pour chaque nom DNS :

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ````

    Pour inclure des Services PaaS, spécifiez le commutateur ```-IncludePaaS```

7. Méthode alternative pour les environnements Dev/Test : pour générer une requête de certificat unique avec plusieurs autres noms d’objets, ajoutez le paramètre **-RequestType SingleCSR** et la valeur (**non** recommandé pour les environnements de production) :

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ````

    Pour inclure des Services PaaS, spécifiez le commutateur ```-IncludePaaS```
    
8. Passez en revue la sortie :

    ````PowerShell  
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Request Generation

    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com*dn2=*.adminhosting.east.azurestack.contoso.com@dns=*.hosting.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Finished Certificate Request Generation

    AzsReadinessChecker Log location: C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\1.1803.405.3\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

9.  Envoyez le fichier **.REQ** généré à votre autorité de certification (interne ou publique).  Le répertoire de sortie de **Start-AzsReadinessChecker** contient les CSR nécessaires à envoyer à une autorité de certification.  Il contient également un répertoire enfant contenant le ou les fichiers INF utilisés pendant la génération de requête de certificat, en tant que référence. Assurez-vous que votre autorité de certification génère des certificats à l’aide de votre requête générée qui est conforme aux [exigences PKI d’Azure Stack](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Étapes suivantes

[Préparer des certificats PKI Azure Stack](azure-stack-prepare-pki-certs.md)
