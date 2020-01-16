---
title: Détection des menaces pour les machines virtuelles et les serveurs dans Azure Security Center | Microsoft Docs
description: Cette rubrique présente les alertes de machine virtuelle et de serveur disponibles dans Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: b101fd30ddbdbf0f8fdf6e02394cb10b9af5f4b0
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666361"
---
# <a name="threat-detection-for-vms-and-servers-in-azure-security-center"></a>Détection des menaces pour les machines virtuelles et les serveurs dans Azure Security Center

Cette rubrique présente les différents types de méthodes de détection et d’alertes disponibles pour les serveurs et les machines virtuelles avec les systèmes d’exploitation suivants. Pour obtenir la liste des versions prises en charge, consultez [Plateformes et fonctionnalités prises en charge par Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows <a name="windows-machines"></a>

Azure Security Center s’intègre aux services Azure pour superviser et protéger vos machines Windows. Security Center présente les alertes et les suggestions de correction de l’ensemble de ces services dans un format facile à utiliser.

* **Microsoft Defender ATP** <a name="windows-atp"></a> - Security Center étend ses plateformes de protection de charge de travail cloud en s’intégrant à Microsoft Defender ATP (Advanced Threat Protection). Vous bénéficiez ainsi de fonctionnalités complètes de détection et de réponse des points de terminaison (EDR).

    > [!NOTE]
    > Le capteur Microsoft Defender ATP est automatiquement activé sur les serveurs Windows qui utilisent Security Center.

    Quand Microsoft Defender ATP détecte une menace, il déclenche une alerte. L’alerte s’affiche dans le tableau de bord de Security Center. À partir du tableau de bord, vous pouvez accéder à la console Microsoft Defender ATP et effectuer un examen détaillé pour découvrir l’étendue de l’attaque. Pour plus d’informations sur Microsoft Defender ATP, consultez [Intégrer des serveurs au service Microsoft Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* **Analyse de vidage sur incident** <a name="windows-dump"></a> - Lorsque le logiciel se bloque, un vidage sur incident capture une partie de la mémoire au moment de l’incident.

    Un plantage peut avoir été provoqué par un programme malveillant ou contenir un programme malveillant. Pour éviter d’être détectées par les produits de sécurité, différentes formes de programmes malveillants utilisent une attaque sans fichier, ce qui évite l’écriture sur disque ou le chiffrement de composants logiciels écrits sur le disque. Ce type d’attaque est difficile à détecter à l’aide des approches traditionnelles basées sur le disque.

    Toutefois, il est possible de détecter ce type d’attaque à l’aide de l’analyse de la mémoire. En analysant la mémoire dans le vidage sur incident, Security Center peut détecter les techniques utilisées par l’attaque. Par exemple, l’attaque peut tenter d’exploiter des vulnérabilités présentes dans le logiciel, d’accéder à des données confidentielles et de persister subrepticement sur une machine compromise. Security Center détecte toutes ces techniques avec un impact minime sur les performances des ordinateurs hôtes.

    Pour obtenir la liste des alertes d’analyse de vidage sur incident, consultez la [table de référence des alertes](alerts-reference.md#alerts-crashdump).

* **Détection des attaques sans fichier** <a name="windows-fileless"></a> - Les attaques sans fichier ciblant des points de terminaison sont courantes. Pour éviter d’être détectées, les attaques sans fichier injectent des charges utiles malveillantes en mémoire. Les charges utiles des attaquants sont conservées dans la mémoire des processus compromis et effectuent un large éventail d’activités malveillantes.

    Avec la détection des attaques sans fichier, les techniques d’investigation automatique de la mémoire identifient les comportements, les techniques et les kits de ressources des attaques sans fichier. Cette solution analyse régulièrement votre machine au moment de l’exécution et extrait des insights directement de la mémoire des processus critiques de sécurité.

    Elle trouve des preuves de l’exploitation, de l’injection de code et de l’exécution de charges utiles malveillantes. La détection des attaques sans fichier génère des alertes de sécurité détaillées qui accélèrent le tri des alertes, la corrélation et le temps de réponse en aval. Cette approche complète les solutions EDR basées sur les événements, en offrant une couverture de détection plus large.

    Pour obtenir la liste des alertes de détection des attaques sans fichier, consultez la [table de référence des alertes](alerts-reference.md#alerts-filelessattackdetect).

> [!NOTE]
> Vous pouvez simuler des alertes Windows en téléchargeant le document [Azure Security Center Playbook: Alertes de sécurité](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).

## Linux <a name="linux-machines"></a>

Security Center collecte les enregistrements d’audit à partir des machines Linux à l’aide d’**auditd**, l’un des frameworks d’audit Linux les plus courants. auditd se trouve dans le noyau mainline. 

* **Intégration à Microsoft Monitoring Agent (MMA) et aux alertes auditd Linux** <a name="linux-auditd"></a> - Le système auditd se compose d’un sous-système au niveau du noyau, qui est responsable de la supervision des appels système. Il filtre les appels par un ensemble de règles donné et écrit les messages pour ces derniers dans un socket. Security Center intègre les fonctionnalités du package auditd à Microsoft Monitoring Agent (MMA). Cette intégration permet la collecte des événements auditd dans toutes les distributions Linux prises en charge, sans prérequis.  

    Les enregistrements auditd sont collectés, enrichis et agrégés dans les événements à l’aide de l’agent MMA Linux. Security Center enrichit continuellement l’analytique, qui utilise les signaux Linux pour détecter les comportements malveillants sur les machines Linux locales ou dans le cloud. À l’image des fonctionnalités Windows, cette analytique englobe les processus suspects, les tentatives de connexion douteuses, le chargement de modules de noyau et diverses autres activités. Ces activités peuvent signaler qu’une machine fait l’objet d’une attaque ou qu’elle a subi une violation de la sécurité.  

    Voici plusieurs exemples d’analytique qui couvrent les différentes phases du cycle de vie des attaques.

    Pour obtenir la liste des alertes Linux, consultez la [table de référence des alertes](alerts-reference.md#alerts-linux).

> [!NOTE]
> Vous pouvez simuler des alertes Linux en téléchargeant le document [Azure Security Center Playbook : Détections Linux](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).

 
 ## <a name="next-steps"></a>Étapes suivantes

Pour obtenir plus d’informations et des exemples sur la détection par Security Center, consultez :

* [Comment Azure Security Center automatise la détection des cyberattaques](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Comment Azure Security Center détecte les vulnérabilités à l’aide d’outils d’administration](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)
* [Tirer parti d’Azure Security Center pour détecter les attaques de machines Linux compromises](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Azure Security Center peut détecter les vulnérabilités émergentes dans Linux](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)