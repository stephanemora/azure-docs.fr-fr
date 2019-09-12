---
title: Détection des solutions de protection des points de terminaison et évaluation de l'intégrité dans Azure Security Center | Microsoft Docs
description: Découvrez comment les solutions de protection de point de terminaison sont détectées et identifiées comme saines.
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
ms.date: 08/08/2019
ms.author: v-mohabe
ms.openlocfilehash: a5cd0f88173abb65a120aa305206505af51d9f9e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861375"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Évaluation de la protection de point de terminaison et recommandations dans Azure Security Center

Dans Azure Security Center, l'évaluation de la protection des points de terminaison permet de détecter et d'évaluer l'intégrité des versions des solutions de protection des points de terminaison [prises en charge](https://docs.microsoft.com/azure/security-center/security-center-os-coverage). Cette rubrique décrit les scénarios qui génèrent les deux recommandations suivantes en lien avec les solutions de protection des points de terminaison fournies par Azure Security Center.

* **Installer les solutions de protection de point de terminaison sur votre machine virtuelle**
* **Résoudre les problèmes d'intégrité de la protection du point de terminaison sur vos machines**

## <a name="windows-defender"></a>Windows Defender

* La recommandation **« Installer des solutions de protection de point de terminaison sur une machine virtuelle »** est générée lorsque [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) s’exécute et renvoie **AMServiceEnabled : False**

* La recommandation **« Résoudre les problèmes d'intégrité de la protection de point de terminaison sur vos machines »** est générée lorsque [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) s’exécute et qu'une des deux situations suivantes se présente :

  * Au moins une des propriétés suivantes est False :

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * Si une, voire les deux propriétés suivantes sont supérieures ou égales à 7.

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Protection du point de terminaison Microsoft System Center

* La recommandation **« Installer des solutions de protection de point de terminaison sur la machine virtuelle »** est générée lors de l’importation de **SCEPMpModule (« $env : Program Files\Microsoft sécurité Client\MpProvider\MpProvider.psd1 »)** et que l'exécution de **Get-MProtComputerStatus** de indique **AMServiceEnabled = false**

* La recommandation **« Résoudre les problèmes d'intégrité de la protection de point de terminaison sur vos machines  »** est générée lorsque **Get-MprotComputerStatus** s’exécute et qu'une, voire les deux situations suivantes se présentent :

    * Au moins une des propriétés suivantes est False :

       **AMServiceEnabled**
    
       **AntispywareEnabled**
    
       **RealTimeProtectionEnabled**
    
       **BehaviorMonitorEnabled**
    
       **IoavProtectionEnabled**
    
       **OnAccessProtectionEnabled**
          
    * Si une, voire les deux mises à jour de signature sont supérieures ou égales à 7. 

       **AntispywareSignatureAge**
    
       **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* La recommandation **« Installer les solutions de protection de point de terminaison sur la machine virtuelle »** est générée si une ou plusieurs des vérifications suivantes ne sont pas satisfaites :
    * **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent** existe
    * **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** existe
    * Le fichier **dsq_query.cmd** se trouve dans le dossier d’installation
    * L'exécution de **dsa_query.cmd** indique **Component.AM.mode : on - Trend Micro Deep Security Agent détecté**

## <a name="symantec-endpoint-protection"></a>Protection de point de terminaison Symantec
La recommandation **« Installer les solutions de protection de point de terminaison sur la machine virtuelle »** est générée si une des vérifications suivantes n'est pas satisfaite :

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Ou

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

La recommandation **« Résoudre les problèmes d'intégrité de la protection de point de terminaison sur vos machines »** est générée si une des vérifications suivantes n'est pas satisfaite :  

* Vérifier la version de Symantec > = 12 :  Emplacement du registre : **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Value "PRODUCTVERSION"**

* Vérifier l'état de la protection en temps réel : **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* Vérifier l’état de la mise à jour de la signature : **HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 days**

* Vérifier l’état de l’analyse complète : **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 days**

* Rechercher le numéro de version de signature pour Symantec 12 : **Registry Paths+ "CurrentVersion\SharedDefs" -Value "SRTSP"** 

* Chemin d’accès à la version de signature pour Symantec 14 : **Registry Paths+ "CurrentVersion\SharedDefs\SDSDefs" -Value "SRTSP"**

Chemins d’accès au Registre :

* **"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;**
* **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>Protection de point de terminaison McAfee pour Windows

La recommandation **« Installer les solutions de protection de point de terminaison sur la machine virtuelle »** est générée si les vérifications suivantes ne sont pas satisfaites :

* **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** exists

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

La recommandation **« Résoudre les problèmes d'intégrité de la protection de point de terminaison sur vos machines »** est générée si les vérifications suivantes ne sont pas satisfaites :

* Version McAfee : **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* Rechercher la version de signature : **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "dwContentMajorVersion"**

* Rechercher la date signature : **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "szContentCreationDate" >= 7 days**

* Rechercher la date d'analyser : **HKLM:\Software\McAfee\Endpoint\AV\ODS -Value "LastFullScanOdsRunTime" >= 7 days**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>McAfee Endpoint Security for Linux Threat Prevention 

La recommandation **Installer les solutions de protection de point de terminaison sur la machine virtuelle** est générée si au moins une des vérifications suivantes n’est pas satisfaite :  

- Le fichier **/opt/isec/ens/threatprevention/bin/isecav** se ferme 

- La sortie **"/opt/isec/ens/threatprevention/bin/isecav --version"** est : **McAfee name = McAfee Endpoint Security for Linux Threat Prevention and McAfee version >= 10**

La recommandation **Résoudre les problèmes d'intégrité de la protection de point de terminaison sur vos machines** est générée si au moins une des vérifications suivantes n'est pas satisfaite :

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** retourne **Quick scan, Full scan** et les deux analyses <= 7 jours

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** retourne **DAT and engine Update time** et les deux <= 7 jours

- **"/opt/isec/ens/threatprevention/bin/isecav --getoasconfig --summary"** retourne l’état **On Access Scan**

## <a name="sophos-antivirus-for-linux"></a>Sophos Anti-Virus pour Linux 

La recommandation **Installer les solutions de protection de point de terminaison sur la machine virtuelle** est générée si au moins une des vérifications suivantes n’est pas satisfaite :

- Le fichier **/opt/sophos-av/bin/savdstatus** se ferme ou recherche l'emplacement personnalisé **"readlink $(which savscan)"**

- **"/opt/sophos-av/bin/savdstatus --version"** retourne **Sophos name = Sophos Anti-Virus and Sophos version >= 9**

La recommandation **Résoudre les problèmes d'intégrité de la protection de point de terminaison sur vos machines** est générée si au moins une des vérifications suivantes n'est pas satisfaite :

- **"/opt/sophos-av/bin/savlog --maxage=7 | grep -i "Scheduled scan .\* completed" | tail -1"** , retourne une valeur   

- **"/opt/sophos-av/bin/savlog --maxage=7 | grep "scan finished"** | tail -1", retourne une valeur   

- **"/opt/sophos-av/bin/savdstatus --lastupdate"** retourne lastUpdate qui doit être <= 7 jours 

- **"/opt/sophos-av/bin/savdstatus -v"** est égal à **"On-access scanning is running"** 

- **"/opt/sophos-av/bin/savconfig get LiveProtection"** retourne activé  

## <a name="troubleshoot-and-support"></a>Dépannage et support technique

### <a name="troubleshoot"></a>Résolution des problèmes

Les journaux de l'extension Microsoft Antimalware à l'emplacement :  
**%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Or PaaSAntimalware)\1.5.5.x(version#)\CommandExecution.log**

### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/support/faq/).
