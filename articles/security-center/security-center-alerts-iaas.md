---
title: Détection des menaces pour les machines virtuelles et les serveurs dans Azure Security Center | Microsoft Docs
description: Cette rubrique présente les alertes de machine virtuelle et de serveur disponibles dans Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 055b578c1d976b7f85c65ab4e028f9d609861cd4
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013326"
---
# <a name="threat-detection-for-vms-and-servers-in-azure-security-center"></a>Détection des menaces pour les machines virtuelles et les serveurs dans Azure Security Center

Cette rubrique présente les différents types de méthodes de détection et d’alertes disponibles pour les serveurs et les machines virtuelles avec les systèmes d’exploitation suivants. Pour obtenir la liste des versions prises en charge, consultez [Plateformes et fonctionnalités prises en charge par Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows <a name="windows-machines"></a>

Azure Security Center s’intègre aux services Azure pour superviser et protéger vos machines Windows. Security Center présente les alertes et les suggestions de correction de l’ensemble de ces services dans un format facile à utiliser.

### Windows Server Defender ATP <a nanme="windows-atp"></a>

Security Center étend ses plateformes de protection de charge de travail cloud en s’intégrant à Windows Server Defender Advanced Threat Protection (ATP). Vous bénéficiez ainsi de fonctionnalités complètes de détection et de réponse des points de terminaison (EDR).

> [!NOTE]
> Le capteur Windows Server Defender ATP est automatiquement activé sur les serveurs Windows qui utilisent Security Center.

Quand Windows Server Defender ATP détecte une menace, il déclenche une alerte. L’alerte s’affiche dans le tableau de bord de Security Center. À partir du tableau de bord, vous pouvez accéder à la console Windows Defender ATP et effectuer un examen détaillé pour découvrir l’étendue de l’attaque. Pour plus d’informations sur Windows Server Defender ATP, consultez [Intégrer des serveurs au service Windows Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

### Analyse de vidage sur incident <a nanme="windows-dump"></a>

Lorsque le logiciel se bloque, un vidage sur incident capture une partie de la mémoire au moment de l’incident.

Un plantage peut avoir été provoqué par un programme malveillant ou contenir un programme malveillant. Pour éviter d’être détectées par les produits de sécurité, différentes formes de programmes malveillants utilisent une attaque sans fichier, ce qui évite l’écriture sur disque ou le chiffrement de composants logiciels écrits sur le disque. Ce type d’attaque est difficile à détecter à l’aide des approches traditionnelles basées sur le disque.

Toutefois, il est possible de détecter ce type d’attaque à l’aide de l’analyse de la mémoire. En analysant la mémoire dans le vidage sur incident, Security Center peut détecter les techniques utilisées par l’attaque. Par exemple, l’attaque peut tenter d’exploiter des vulnérabilités présentes dans le logiciel, d’accéder à des données confidentielles et de persister subrepticement sur une machine compromise. Security Center détecte toutes ces techniques avec un impact minime sur les performances des ordinateurs hôtes.

> [!div class="mx-tableFixed"]

|Alerte|Description|
|---|---|
|**Injection de code découverte**|Une injection de code est l’insertion de modules exécutables dans des processus ou threads en cours d’exécution. Un programme malveillant utilise cette technique pour accéder aux données, tout en se camouflant pour ne pas être trouvé et supprimé. <br/>Cette alerte indique qu’un module injecté est présent dans l’image mémoire. Pour distinguer les modules injectés malveillants et non malveillants, Azure Security Center vérifie si le module injecté est conforme à un profil de comportement suspect.|
|**Segment de code suspect découvert**|Indique qu’un segment de code a été alloué à l’aide de méthodes non standard, comme l’injection par réflexion et le remplacement de processus (« process hollowing »). L’alerte présente d’autres caractéristiques du segment de code qui a été traité pour fournir un contexte relatif aux fonctionnalités et aux comportements du segment de code indiqué.|
|**Shellcode découvert**|Un shellcode est la charge utile exécutée après qu’un programme malveillant a exploité une vulnérabilité logicielle.<br/>Cette alerte indique que l’analyse de vidage sur plantage a détecté du code exécutable présentant un comportement souvent associé à des charges utiles malveillantes. Bien que des logiciels non malveillants puissent aussi présenter ce comportement, il n’est pas typique des pratiques de développement logiciel standard.|

### Détection des attaques sans fichier <a nanme="windows-fileless"></a>

Les attaques sans fichier ciblant des points de terminaison sont courantes. Pour éviter d’être détectées, les attaques sans fichier injectent des charges utiles malveillantes en mémoire. Les charges utiles des attaquants sont conservées dans la mémoire des processus compromis et effectuent un large éventail d’activités malveillantes.

Avec la détection des attaques sans fichier, les techniques d’investigation automatique de la mémoire identifient les comportements, les techniques et les kits de ressources des attaques sans fichier. Cette solution analyse régulièrement votre machine au moment de l’exécution et extrait des insights directement de la mémoire des processus critiques de sécurité.

Elle trouve des preuves de l’exploitation, de l’injection de code et de l’exécution de charges utiles malveillantes. La détection des attaques sans fichier génère des alertes de sécurité détaillées qui accélèrent le tri des alertes, la corrélation et le temps de réponse en aval. Cette approche complète les solutions EDR basées sur les événements, en offrant une couverture de détection plus large.

> [!NOTE]
> Vous pouvez simuler des alertes Windows en téléchargeant le document [Azure Security Center Playbook: Alertes de sécurité](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).

> [!div class="mx-tableFixed"]

|Alerte|Description|
|---|---|
|**Technique d’attaque sans fichier détectée**|La mémoire du processus spécifié contient un kit de ressources des attaques sans fichier : Meterpreter. Les kits de ressources des attaques sans fichier ne sont généralement pas présents sur le système de fichiers, ce qui rend difficile la détection par un logiciel antivirus traditionnel.|

### <a name="further-reading"></a>Pour aller plus loin

Pour obtenir plus d’informations et des exemples sur la détection par Security Center, consultez :

* [Comment Azure Security Center automatise la détection des cyberattaques](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Comment Azure Security Center détecte les vulnérabilités à l’aide d’outils d’administration](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

## Linux <a name="linux-machines"></a>

Security Center collecte les enregistrements d’audit à partir des machines Linux à l’aide d’**auditd**, l’un des frameworks d’audit Linux les plus courants. auditd se trouve dans le noyau mainline. 

### Alertes d’auditd Linux et intégration à Microsoft Monitoring Agent (MMA) <a name="linux-auditd"></a>

Le système auditd se compose d’un sous-système au niveau du noyau, qui est responsable de la supervision des appels système. Il filtre les appels par un ensemble de règles donné et écrit les messages pour ces derniers dans un socket. Security Center intègre les fonctionnalités du package auditd à Microsoft Monitoring Agent (MMA). Cette intégration permet la collecte des événements auditd dans toutes les distributions Linux prises en charge, sans prérequis.  

Les enregistrements auditd sont collectés, enrichis et agrégés dans les événements à l’aide de l’agent MMA Linux. Security Center enrichit continuellement l’analytique, qui utilise les signaux Linux pour détecter les comportements malveillants sur les machines Linux locales ou dans le cloud. À l’image des fonctionnalités Windows, cette analytique englobe les processus suspects, les tentatives de connexion douteuses, le chargement de modules de noyau et diverses autres activités. Ces activités peuvent signaler qu’une machine fait l’objet d’une attaque ou qu’elle a subi une violation de la sécurité.  

Voici plusieurs exemples d’analytique qui couvrent les différentes phases du cycle de vie des attaques.

> [!div class="mx-tableFixed"]

|Alerte|Description|
|---|---|
|**Détection d’un processus accédant de façon inhabituelle au fichier de clés autorisées SSH**|Un fichier de clés autorisées SSH a fait l’objet d’un accès d’une façon rappelant les campagnes de programmes malveillants connus. Cet accès peut indiquer qu’un attaquant tente d’obtenir un accès persistant à une machine.|
|**Tentative de persistance détectée**|L’analyse des données de l’hôte a détecté qu’un script de démarrage pour le mode mono-utilisateur a été installé. <br/>Dans la mesure où il est rare qu’un processus légitime doive s’exécuter dans ce mode, cela peut indiquer qu’un attaquant a ajouté un processus malveillant à chaque niveau d’exécution pour garantir la persistance.|
|**Détection d’une manipulation des tâches planifiées**|L’analyse des données de l’hôte a détecté une manipulation éventuelle des tâches planifiées. Les attaquants ajoutent souvent des tâches planifiées aux machines qu’ils ont compromises pour obtenir la persistance.|
|**Modification suspecte de l’horodatage des fichiers**|L’analyse des données de l’hôte a détecté une modification suspecte de l’horodatage. Les attaquants copient souvent les horodatages de fichiers légitimes existants dans de nouveaux outils pour empêcher la détection de ces fichiers supprimés.|
|**Ajout d’un nouvel utilisateur au groupe sudoers**|L’analyse des données de l’hôte a détecté qu’un utilisateur a été ajouté au groupe sudoers, ce qui permet à ses membres d’exécuter des commandes avec des privilèges élevés.|
|**Exploitation probable de la vulnérabilité DynoRoot dans le client dhcp**|L’analyse des données de l’hôte a détecté l’exécution d’une commande inhabituelle, avec un processus parent du script dhclient.|
|**Détection d’un module de noyau suspect**|L’analyse des données de l’hôte a détecté qu’un fichier objet partagé est chargé en tant que module de noyau. Il peut s’agir d’une activité légitime, ou d’une indication qu’une de vos machines a été compromise.|
|**Détection d’un processus associé au minage de devises numériques**|L’analyse des données de l’hôte a détecté l’exécution d’un processus qui est normalement associé au minage de devises numériques.|
|**Réacheminement potentiel d’un port vers une adresse IP externe**|L’analyse des données de l’hôte a détecté le lancement d’un réacheminement de port vers une adresse IP externe.|

> [!NOTE]
> Vous pouvez simuler des alertes Windows en téléchargeant le document [Azure Security Center Playbook: Détections Linux](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).


Pour plus d'informations, consultez les pages suivantes :  

* [Tirer parti d’Azure Security Center pour détecter les attaques de machines Linux compromises](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)

* [Azure Security Center peut détecter les vulnérabilités émergentes dans Linux](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

 