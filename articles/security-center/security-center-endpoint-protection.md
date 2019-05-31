---
title: Point de terminaison protection solutions découverte et l’intégrité évaluation dans Azure Security Center | Microsoft Docs
description: Comment les solutions de protection de point de terminaison sont découverts et identifiées comme étant sains.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2019
ms.author: v-mohabe
ms.openlocfilehash: b17e5f16b988bfa562b00bc6f5b9dfd34be4ca43
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247963"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Évaluation de protection de point de terminaison et des recommandations dans Azure Security Center

Évaluation de protection de point de terminaison et des recommandations dans Azure Security Center détecte et fournit une évaluation d’intégrité de [pris en charge](https://docs.microsoft.com/azure/security-center/security-center-os-coverage#supported-platforms-for-windows-computers-and-vms) des versions de solutions de protection de point de terminaison. Cette rubrique explique les scénarios qui génèrent deux recommandations suivantes pour les solutions de protection de point de terminaison par Azure Security Center.

* **Installe des solutions de protection de point de terminaison sur votre machine virtuelle**
* **Résoudre les problèmes d’intégrité endpoint protection sur vos machines**

## <a name="windows-defender"></a>Windows Defender

* Le **« Installer les solutions de protection de point de terminaison sur l’ordinateur virtuel »** recommandation est générée lorsque [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) s’exécute et le résultat est **AMServiceEnabled : False**

* Le **« Résoudre les problèmes d’intégrité endpoint protection sur vos machines »** recommandation est générée lorsque [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) s’exécute et une des deux opérations suivantes se produit :

  * Au moins une des propriétés suivantes a la valeur false :

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * Si un ou les deux propriétés suivantes est supérieur ou égal à 7.

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Protection de point de terminaison de Microsoft System Center

* Le **« Installer les solutions de protection de point de terminaison sur l’ordinateur virtuel »** recommandation est générée lors de l’importation **SCEPMpModule (« $env : Program Files\Microsoft sécurité Client\MpProvider\MpProvider.psd1 »)** et en cours d’exécution **Get-MProtComputerStatus** de résultats avec **AMServiceEnabled = false**

* Le **« Résoudre les problèmes d’intégrité endpoint protection sur vos machines »** recommandation est générée lorsque **Get-MprotComputerStatus** s’exécute et une des deux opérations suivantes se produit :

    * Au moins une des propriétés suivantes a la valeur false :

       **AMServiceEnabled**
    
       **AntispywareEnabled**
    
       **RealTimeProtectionEnabled**
    
       **BehaviorMonitorEnabled**
    
       **IoavProtectionEnabled**
    
       **OnAccessProtectionEnabled**
          
    * Si une ou les deux mises à jour de Signature suivant est supérieure ou égale à 7. 

       **AntispywareSignatureAge**
    
       **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* Le **« Installer les solutions de protection de point de terminaison sur l’ordinateur virtuel »** recommandation est générée si un ou plusieurs vérifications suivantes ne sont pas remplies :
    * **Agent de sécurité HKLM:\SOFTWARE\TrendMicro\Deep** existe
    * **HKLM:\SOFTWARE\TrendMicro\Deep sécurité Agent\InstallationFolder** existe
    * Le **dsq_query.cmd** fichier se trouve dans le dossier d’Installation
    * En cours d’exécution **dsa_query.cmd** de résultats avec **Component.AM.mode : on - Trend Micro Deep Security Agent détecté**

## <a name="symantec-endpoint-protection"></a>Protection de point de terminaison Symantec
Le **« Installer les solutions de protection de point de terminaison sur l’ordinateur virtuel »** recommandation est générée si un des contrôles suivants ne sont pas remplie :

* **Protection\CurrentVersion\PRODUCTNAME de point de terminaison HKLM:\Software\Symantec\Symantec = « Symantec Endpoint Protection »**

* **Point de terminaison de HKLM:\Software\Symantec\Symantec Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Ou

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **Point de terminaison de HKLM:\Software\Wow6432Node\Symantec\Symantec Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Le **« Résoudre les problèmes d’intégrité endpoint protection sur vos machines »** recommandation est générée si un des contrôles suivants ne sont pas remplie :  

* Vérifier la Version de Symantec > = 12 :  Emplacement du Registre : **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Value "PRODUCTVERSION"**

* Vérifiez l’état de Protection en temps réel : **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* Vérifiez l’état de mise à jour de Signature : **Point de terminaison HKLM\Software\Symantec\Symantec Protection\CurrentVersion\public-opstate\LatestVirusDefsDate < = 7 jours**

* Vérifiez l’état de l’analyse complète : **Point de terminaison HKLM:\Software\Symantec\Symantec Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime < = 7 jours**

* Recherchez le numéro de version de signature chemin d’accès à la version de signature Symantec 12 : **Registry Paths+ "CurrentVersion\SharedDefs" -Value "SRTSP"** 

* Chemin d’accès à la version de signature de Symantec 14 : **Registry Paths+ "CurrentVersion\SharedDefs\SDSDefs" -Value "SRTSP"**

Chemins d’accès du Registre :

**"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;** 
 **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee endpoint protection pour Windows

Le **« Installer les solutions de protection de point de terminaison sur l’ordinateur virtuel »** recommandation est générée si les vérifications suivantes ne sont pas remplies :

* **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** existe

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

Le **« Résoudre les problèmes d’intégrité endpoint protection sur vos machines »** recommandation est générée si les vérifications suivantes ne sont pas remplies :

* McAfee, Version : **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* Recherchez la Version de la Signature : **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "dwContentMajorVersion"**

* Rechercher la date de la Signature : **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "szContentCreationDate" >= 7 days**

* Rechercher la date de l’analyse : **HKLM:\Software\McAfee\Endpoint\AV\ODS -Value "LastFullScanOdsRunTime" >= 7 days**

## <a name="troubleshoot-and-support"></a>Dépannage et support technique

### <a name="troubleshoot"></a>Résolution des problèmes

Journaux de l’extension Microsoft Antimalware sont disponibles ici :  
**%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Or PaaSAntimalware)\1.5.5.x(version#)\CommandExecution.log**

### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Ou bien, vous pouvez signaler un incident de support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/support/faq/).
