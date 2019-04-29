---
title: Résoudre les problèmes de restauration automatique sur l’infrastructure locale pendant la reprise d’activité de machines virtuelles VMware sur Azure avec Azure Site Recovery | Microsoft Docs
description: Cet article explique comment résoudre les problèmes de restauration automatique et de reprotection pendant la reprise d’activité de machines virtuelles VMware sur Azure avec Azure Site Recovery.
author: vDonGlover
manager: JarrettRenshaw
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: v-doglov
ms.openlocfilehash: c598c5e238458c010500579c5371622b85e71de0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565189"
---
# <a name="troubleshoot-vcenter-discovery-failures"></a>Résoudre les problèmes d’échec de découverte vCenter

Cet article vous aide à résoudre les problèmes qui surviennent en raison d’échecs de découverte de VMware vCenter.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Valeurs non numériques dans la propriété maxSnapShots

Sur les versions antérieures 9.20, vCenter déconnecte lorsqu’il récupère une valeur non numérique pour la propriété `snapshot.maxSnapShots` propriété sur une machine virtuelle.

Ce problème est identifié par l’ID d’erreur 95126.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
Pour résoudre le problème :

- Identifiez la machine virtuelle et définissez la valeur sur une valeur numérique (machine virtuelle modifier les paramètres dans vCenter).

Ou

- Mettre à niveau votre serveur de configuration vers 9.20 ou version ultérieure.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Problèmes de configuration de proxy pour la connectivité de vCenter

vCenter découverte respecte les paramètres de proxy système par défaut configurées par l’utilisateur du système. Le service DRA respecte les paramètres de proxy fournis par l’utilisateur pendant l’installation du serveur de configuration à l’aide du programme d’installation du programme d’installation unifiée ou le modèle de fichier OVA. 

En règle générale, le proxy est utilisé pour communiquer avec les réseaux publics ; telles que la communication avec Azure. Si le proxy est configuré et vCenter est dans un environnement local, il sera en mesure de communiquer avec DRA.

Les situations suivantes se produisent lorsque vous rencontrez ce problème :

- Le serveur vCenter \<vCenter > n’est pas accessible en raison de l’erreur : Le serveur distant a retourné une erreur : Serveur (503) non disponible
- Le serveur vCenter \<vCenter > n’est pas accessible en raison de l’erreur : Le serveur distant a retourné une erreur : Impossible de se connecter au serveur distant.
- Connexion impossible au serveur vCenter/ESXi.

Pour résoudre le problème :

Téléchargez le [outil PsExec](https://aka.ms/PsExec). 

Utilisez l’outil PsExec pour accéder au contexte d’utilisateur système et déterminer si l’adresse proxy est configuré. Vous pouvez ensuite ajouter vCenter à la liste de contournement utilisant les procédures suivantes.

Pour la configuration du proxy de découverte :

1. IE ouvert dans le contexte d’utilisateur système à l’aide de l’outil PsExec.
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. Modifiez les paramètres de proxy dans Internet Explorer pour contourner l’adresse IP de vCenter.
3. Redémarrez le service tmanssvc.

Pour la configuration du proxy DRA :

1. Ouvrez une invite de commandes et ouvrez le dossier fournisseur Microsoft Azure Site Recovery.
 
    **Fournisseur de CD C:\Program Files\Microsoft Azure Site Recovery**

3. À partir de l’invite de commandes, exécutez la commande suivante.
   
   **DRCONFIGURATOR. EXE / configurer /AddBypassUrls [adresse IP/FQDN de vCenter Server fourni au moment de l’ajouter vCenter]**

4. Redémarrez le service du fournisseur DRA.

## <a name="next-steps"></a>Étapes suivantes

[Gérer le serveur de configuration pour la récupération d’urgence de VMware VM](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
