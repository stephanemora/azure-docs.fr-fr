---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 08/14/2018
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 4c5b4c5eacd4be751004af551e3753a61873c7a7
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551520"
---
**Dernière mise à jour du document**: 14 août 2018 10:00 AM PST.

La divulgation d’une [nouvelle classe de vulnérabilités de processeur](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) appelées attaques par canal latéral de l’exécution spéculative a généré des questions de la part de clients recherchant plus d’explications.  

Microsoft a déployé des solutions d’atténuation des risques sur l’ensemble de ses services cloud. L’infrastructure qui exécute Azure et isole les différentes charges de travail des clients est protégée. Cela signifie qu’un éventuel attaquant utilisant la même infrastructure ne peut pas attaquer votre application à l’aide de ces vulnérabilités.

Azure utilise autant que possible la [maintenance avec préservation de la mémoire](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-not-requiring-a-reboot) afin de réduire l’impact sur le client et d’éliminer la nécessité de redémarrages. Azure continuera à utiliser ces méthodes lors des mises à jour à l’échelle du système sur l’hôte et à protéger nos clients.

Plus d’informations sur l’intégration de la sécurité dans chaque aspect d’Azure sont disponibles sur le site de [documentation sur la sécurité Azure](https://docs.microsoft.com/azure/security/). 

> [!NOTE] 
> Depuis la première publication de ce document, plusieurs variantes de cette classe de vulnérabilités ont été divulguées. Microsoft poursuit ses efforts d’investissement dans la protection de nos clients et de conseils. Cette page sera mise à jour à mesure que nous publierons d’autres correctifs. 
> 
> Le 14 août 2018, le secteur a révélé une nouvelle vulnérabilité de canal côté exécution spéculative appelée [L1 Terminal Fault](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180018) (L1TF) qui s’est vue attribuer plusieurs CVE ([CVE-2018-3615, CVE-2018-3620 et CVE-2018-3646](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00161.html)). Cette vulnérabilité affecte les processeurs Intel® Core® et Intel® Xeon®. Microsoft a déployé des atténuations des risques sur nos services cloud qui renforcent l’isolation entre les clients. Lisez ci-dessous pour obtenir des conseils supplémentaires sur la protection contre les vulnérabilités L1TF et précédentes ([Spectre Variante 2 CVE-2017-5715 et Meltdown Variante 3 CVE-2017-5754](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)).
>  






## <a name="keeping-your-operating-systems-up-to-date"></a>Maintien à jour de vos systèmes d’exploitation

Alors qu’une mise à jour du système d’exploitation n’est pas nécessaire pour isoler vos applications s’exécutant sur Azure d’autres clients Azure, il est toujours judicieux de maintenir vos logiciels à jour. Les derniers correctifs cumulatifs de sécurité pour Windows contiennent des atténuations des risques pour plusieurs vulnérabilités de canal côté exécution spéculative. De même, les distributions Linux ont mis à disposition plusieurs mises à jour pour résoudre ces vulnérabilités. Vous trouverez ci-dessous nos actions recommandées pour mettre à jour votre système d’exploitation :

| Offre | Action recommandée  |
|----------|---------------------|
| Services cloud Azure  | Activez la [mise à jour automatique](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) ou vérifiez que vous exécutez le dernier système d’exploitation invité. |
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

Vous pouvez activer des fonctionnalités de sécurité supplémentaires dans votre machine virtuelle ou service cloud.

### <a name="windows"></a>Windows 

Votre système d’exploitation cible doit être à jour pour pouvoir activer ces fonctionnalités de sécurité supplémentaires. Bien que de nombreuses atténuations des risques de canal côté exécution spéculative soient activées par défaut, les fonctionnalités supplémentaires décrites ici doivent être activées manuellement et peuvent affecter les performances. 

**Étape 1** : [Contactez le Support Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) ou exposer mis à jour de microprogramme (microcode) dans vos Machines virtuelles. 

**Étape 2** : Activer la prise en charge du noyau virtuels adresse occultation (KVAS) et du système d’exploitation de l’Injection de cible de branche (RTC). Suivez les instructions de l’article [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) pour activer les protections via les clés de Registre `Session Manager`. Un redémarrage est nécessaire. 

**Étape 3** : Pour les déploiements qui utilisent [virtualisation imbriquée](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (D3 et E3 uniquement) : Ces instructions s’appliquent à l’intérieur de la machine virtuelle que vous utilisez comme un hôte Hyper-V. 

1. Suivez les instructions de l’article [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) pour activer les protections via les clés de Registre `MinVmVersionForCpuBasedMitigations`.  
 
1. Définissez le type de planificateur d’hyperviseur sur **Core** en suivant les instructions fournies [ici](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types). 

**Étape 4** : Suivez les instructions de [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) pour vérifier des protections sont activées à l’aide de la [SpeculationControl](https://aka.ms/SpeculationControlPS) module PowerShell. 

> [!NOTE]
> Si vous avez déjà téléchargé ce module, vous devez installer la dernière version.
>

Toutes les machines virtuelles doivent afficher :

```
branch target injection mitigation is enabled: True

kernel VA shadow is enabled: True  

L1TFWindowsSupportEnabled: True
```


### <a name="linux"></a>Linux

<a name="linux"></a>L’activation de l’ensemble de fonctionnalités de sécurité supplémentaires nécessite que le système d’exploitation cible soit entièrement à jour. Certaines atténuations des risques sont activées par défaut. La section suivante décrit les fonctionnalités qui sont désactivées par défaut et/ou qui dépendent de la prise en charge matérielle (microcode). L’activation de ces fonctionnalités peut affecter les performances. Consultez la documentation du fournisseur de votre système d’exploitation pour obtenir des instructions
 
**Étape 1** : [Contactez le Support Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) ou exposer mis à jour de microprogramme (microcode) dans vos Machines virtuelles.
 
**Étape 2** : Activer la prise en charge de l’Injection de cible de branche (RTC) du système d’exploitation atténuer CVE-2017-5715 (Spectre variante 2) en suivant la documentation de votre fournisseur de système d’exploitation. 
 
**Étape 3** : Activer Kernel Page Table Isolation (KPTI) pour atténuer CVE-2017-5754 (Meltdown Variant 3) en suivant la documentation de votre fournisseur de système d’exploitation. 
 
Des informations supplémentaires sont disponibles auprès du fournisseur de votre système d’exploitation :  
 
- [Redhat et CentOS](https://access.redhat.com/security/vulnerabilities/speculativeexecution) 
- [Suse](https://www.suse.com/support/kb/doc/?id=7022512) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/SpectreAndMeltdown) 


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Securing Azure customers from CPU vulnerability](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/) (Protéger les clients Azure des vulnérabilités de processeur).
