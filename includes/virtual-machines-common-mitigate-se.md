---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/14/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: be8c3d3be4410d15ba132a24a417e7a7b0418352
ms.sourcegitcommit: 3675daec6c6efa3f2d2bf65279e36ca06ecefb41
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65620269"
---
**Dernière mise à jour du document**: 14 mai 2019 10:00 AM PST.

La divulgation d’une [nouvelle classe de vulnérabilités de processeur](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) appelées attaques par canal latéral de l’exécution spéculative a généré des questions de la part de clients recherchant plus d’explications.  

Microsoft a déployé des solutions d’atténuation des risques sur l’ensemble de ses services cloud. L’infrastructure qui exécute Azure et isole les différentes charges de travail des clients est protégée. Cela signifie qu’un éventuel attaquant utilisant la même infrastructure ne peut pas attaquer votre application à l’aide de ces vulnérabilités.

Azure utilise autant que possible la [maintenance avec préservation de la mémoire](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-not-requiring-a-reboot) afin de réduire l’impact sur le client et d’éliminer la nécessité de redémarrages. Azure continuera à utiliser ces méthodes lors des mises à jour à l’échelle du système sur l’hôte et à protéger nos clients.

Plus d’informations sur l’intégration de la sécurité dans chaque aspect d’Azure sont disponibles sur le site de [documentation sur la sécurité Azure](https://docs.microsoft.com/azure/security/). 

> [!NOTE] 
> Depuis la première publication de ce document, plusieurs variantes de cette classe de vulnérabilités ont été divulguées. Microsoft poursuit ses efforts d’investissement dans la protection de nos clients et de conseils. Cette page sera mise à jour à mesure que nous publierons d’autres correctifs. 
> 
> Sur le 14 mai 2019, [Intel divulguée](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00233.html) un nouvel ensemble de vulnérabilité de canal côté l’exécution spéculative appelé échantillonnage des données micro architecturale (MDS consultez les conseils de sécurité Microsoft [ADV190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)), qui ont été affectés plusieurs Exposures : 
> - CVE-2018-11091 - micro architecturale les données d’échantillonnage de mémoire Uncacheable (MDSUM)
> - CVE-2018-12126 - micro architecturale Store tampon des données d’échantillonnage (MSBDS) 
> - CVE-2018-12127 - données de charge utilise le Port micro architecturale d’échantillonnage (MLPDS)
> - CVE-2018-12130 - données de mémoire tampon de remplissage de micro architecturale d’échantillonnage (MFBDS)
>
> Cette vulnérabilité affecte les processeurs Intel® Core® et Intel® Xeon®.  Microsoft Azure a publié des mises à jour du système d’exploitation et est de déploiement nouveau microcode, comme il est mis à disposition par Intel, tout au long de notre parc pour protéger nos clients par rapport à ces nouvelles vulnérabilités.   Azure travaille en étroite collaboration avec Intel pour tester et valider le nouveau microcode avant sa sortie officielle sur la plateforme. 
>
> **Les clients qui sont en cours d’exécution non approuvés de code au sein de leur machine virtuelle** devez prendre des mesures pour protéger contre ces vulnérabilités en lecture ci-dessous pour obtenir des conseils supplémentaires sur toutes les vulnérabilités par canal latéral l’exécution spéculative (Microsoft avis ADV [180002 du](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018), et [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)).
>
> Autres clients doivent évaluer ces vulnérabilités à partir d’une défense en profondeur et prendre en compte les implications en matière de sécurité et les performances de leur configuration choisie.



## <a name="keeping-your-operating-systems-up-to-date"></a>Mise à jour permanente de vos systèmes d’exploitation

Alors qu’une mise à jour du système d’exploitation n’est pas nécessaire pour isoler vos applications s’exécutant sur Azure d’autres clients Azure, il est toujours judicieux de maintenir vos logiciels à jour. Les derniers correctifs cumulatifs de sécurité pour Windows contiennent des atténuations des risques pour plusieurs vulnérabilités de canal côté exécution spéculative. De même, les distributions Linux ont mis à disposition plusieurs mises à jour pour résoudre ces vulnérabilités. Voici nos actions recommandées pour mettre à jour votre système d’exploitation :

| Offre | Action recommandée  |
|----------|---------------------|
| Azure Cloud Services  | Activez la [mise à jour automatique](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) ou vérifiez que vous exécutez le dernier système d’exploitation invité. |
| Machines virtuelles Linux Azure | Installez les mises à jour de votre fournisseur de système d’exploitation. Pour plus d’informations, consultez [Linux](#linux) plus loin dans ce document. |
| Machines virtuelles Windows Azure  | Installez le dernier correctif cumulatif de sécurité.
| Autres services PaaS Azure | Aucune action n’est requise de la part des clients utilisant ces services. Azure maintient automatiquement à jour les versions du système d’exploitation. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Conseils supplémentaires si vous exécutez du code non approuvé 

Les clients qui autorisent les utilisateurs non approuvés à exécuter du code arbitraire peuvent souhaiter implémenter des fonctionnalités de sécurité supplémentaires dans leurs machines virtuelles Azure ou services cloud. Ces fonctionnalités protègent contre les vecteurs de divulgation intra-processus décrits par plusieurs vulnérabilités d’exécution spéculative.

Exemples de scénarios dans lesquels des fonctionnalités de sécurité supplémentaires sont recommandées :

- Vous autorisez l’exécution de code non approuvé dans votre machine virtuelle.  
    - *Par exemple, vous autorisez un de vos clients à charger un fichier binaire ou un script que vous exécutez ensuite dans votre application*. 
- Vous autorisez des utilisateurs qui ne sont pas approuvés à se connecter à votre machine virtuelle à l’aide de comptes à privilèges faibles.   
    - *Par exemple, vous autorisez un utilisateur à privilèges faibles de vous connecter à l’une de vos machines virtuelles à l’aide du Bureau à distance ou SSH*.  
- Vous autorisez des utilisateurs non approuvés à accéder à des machines virtuelles implémentées par le biais de la virtualisation imbriquée.  
    - *Par exemple, vous contrôlez l’hôte Hyper-V mais vous allouez les machines virtuelles à des utilisateurs non approuvés*. 

Les clients qui n’implémentent pas un scénario impliquant du code non approuvé ne doivent pas nécessairement activer ces fonctionnalités de sécurité supplémentaires. 

## <a name="enabling-additional-security"></a>Activer la sécurité supplémentaire 

Vous pouvez activer les fonctionnalités de sécurité supplémentaires à l’intérieur de votre machine virtuelle ou un Service Cloud si vous exécutez le code non fiable. En parallèle, vérifiez que votre système d’exploitation est à jour pour activer les fonctionnalités de sécurité à l’intérieur de votre machine virtuelle ou un Service Cloud

### <a name="windows"></a>Windows 

Votre système d’exploitation cible doit être à jour pour pouvoir activer ces fonctionnalités de sécurité supplémentaires. Bien que de nombreuses atténuations des risques de canal côté exécution spéculative soient activées par défaut, les fonctionnalités supplémentaires décrites ici doivent être activées manuellement et peuvent affecter les performances. 


**Étape 1 : Désactiver l’hyperthreading sur la machine virtuelle** : les clients qui exécutent du code non fiable sur une machine virtuelle devra désactiver l’hyperthreading ou le déplacer vers une taille de machine virtuelle non hyperthreaded de hyperthreaded. Pour vérifier si votre machine virtuelle a l’hyperthreading est activé, consultez le script à l’aide de la ligne de commande de Windows à partir de la machine virtuelle ci-dessous.

Type `wmic` pour ouvrir l’interface interactive. Puis tapez ci-dessous pour afficher la quantité de physique et logique processeurs sur la machine virtuelle.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Si le nombre de processeurs logiques est supérieur à processeurs physiques (cœurs), l’hyperthreading est activé.  Si vous exécutez une machine virtuelle hyperthreaded, veuillez [contactez le Support Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) pour obtenir l’hyperthreading désactivé.  Une fois que l’hyperthreading est désactivé, **prise en charge nécessite un redémarrage complet de la machine virtuelle**. 


**Étape 2** : En parallèle à l’étape 1, suivez les instructions de [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) pour vérifier des protections sont activées à l’aide de la [SpeculationControl](https://aka.ms/SpeculationControlPS) module PowerShell.

> [!NOTE]
> Si vous avez déjà téléchargé ce module, vous devez installer la dernière version.
>


La sortie du script PowerShell doit avoir le dessous des valeurs à valider activé protections contre ces vulnérabilités :

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
```

Si la sortie indique `MDS mitigation is enabled: False`, veuillez [contactez le Support Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) pour les options d’atténuation disponibles.



**Étape 3** : Pour activer la prise en charge de la branche cible d’Injection (RTC) du système d’exploitation et de noyau virtuels adresse occultation (KVAS), suivez les instructions de [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) de protection à l’aide de la `Session Manager` clés de Registre. Un redémarrage est nécessaire.


**Étape 4** : Pour les déploiements qui utilisent [virtualisation imbriquée](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (D3 et E3 uniquement) : Ces instructions s’appliquent à l’intérieur de la machine virtuelle que vous utilisez comme un hôte Hyper-V.

1.  Suivez les instructions de [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) de protection à l’aide de la `MinVmVersionForCpuBasedMitigations` clés de Registre.
2.  Définissez le type de planificateur hyperviseur sur `Core` en suivant les instructions [ici](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types).


### <a name="linux"></a>Linux

<a name="linux"></a>L’activation de l’ensemble de fonctionnalités de sécurité supplémentaires nécessite que le système d’exploitation cible soit entièrement à jour. Certaines atténuations des risques sont activées par défaut. La section suivante décrit les fonctionnalités qui sont désactivées par défaut et/ou qui dépendent de la prise en charge matérielle (microcode). L’activation de ces fonctionnalités peut affecter les performances. Consultez la documentation du fournisseur de votre système d’exploitation pour obtenir des instructions


**Étape 1 : Désactiver l’hyperthreading sur la machine virtuelle** : les clients qui exécutent du code non fiable sur une machine virtuelle devra désactiver l’hyperthreading ou le déplacer vers une machine virtuelle non hyperthreaded de hyperthreaded.  Pour vérifier si vous exécutez une machine virtuelle hyperthreaded, exécutez le `lspcu` commande dans la VM Linux. 

Si `Thread(s) per core = 2`, puis l’hyperthreading est activé. 

Si `Thread(s) per core = 1`, puis l’hyperthreading a été désactivée. 

 
Exemple de sortie pour une machine virtuelle avec l’hyperthreading est activé : 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0,2,4,6
Off-line CPU(s) list:  1,3,5,7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

Si vous exécutez une machine virtuelle hyperthreaded, veuillez [contactez le Support Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) pour obtenir l’hyperthreading désactivé.  Remarque : Une fois que l’hyperthreading est désactivé, **prise en charge nécessite un redémarrage complet de la machine virtuelle**.


**Étape 2** : Pour limiter la possibilité des ci-dessous les vulnérabilités par canal latéral l’exécution spéculative, reportez-vous à la documentation de votre fournisseur de système d’exploitation :   
 
- [Redhat et CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 

## <a name="next-steps"></a>Étapes suivantes

Cet article fournit des conseils pour le ci-dessous attaques côté canal de l’exécution spéculative qui affectent le nombre de processeurs moderne :

[Spectre Meltdown](https://portal.msrc.microsoft.com/security-guidance/advisory/ADV180002):
- CVE-2017-5715 - Injection de cible de branche (RTC)  
- CVE-2017-5754 - Kernel Page Table Isolation (KPTI)
- CVE-2018-3639 – Store spéculative contournement (KPTI) 
 
[L1 Erreur de Terminal (L1TF)](https://portal.msrc.microsoft.com/security-guidance/advisory/ADV180018):
- CVE-2018-3615 - Intel Software Guard Extensions (SGX Intel)
- CVE-2018-3620 - systèmes d’exploitation (OS) et Mode de gestion de système (SMM)
- CVE-2018-3646 – a un impact sur Virtual Machine Manager (VMM)

[Échantillonnage des données de micro architecturale](https://portal.msrc.microsoft.com/security-guidance/advisory/ADV190013): 
- CVE-2018-11091 - micro architecturale les données d’échantillonnage de mémoire Uncacheable (MDSUM)
- CVE-2018-12126 - micro architecturale Store tampon des données d’échantillonnage (MSBDS)
- CVE-2018-12127 - données de charge utilise le Port micro architecturale d’échantillonnage (MLPDS)
- CVE-2018-12130 - données de mémoire tampon de remplissage de micro architecturale d’échantillonnage (MFBDS)








