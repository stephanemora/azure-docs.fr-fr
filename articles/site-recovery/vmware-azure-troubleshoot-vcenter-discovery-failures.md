---
title: Résoudre les problèmes de restauration automatique sur l’infrastructure locale pendant la reprise d’activité de machines virtuelles VMware sur Azure avec Azure Site Recovery | Microsoft Docs
description: Cet article explique comment résoudre les problèmes de restauration automatique et de reprotection pendant la reprise d’activité de machines virtuelles VMware sur Azure avec Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: c27e72333618f73b67eec9b5c0c3a70239a1c0b3
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970851"
---
# <a name="troubleshoot-vcenter-discovery-failures"></a>Résoudre les problèmes d’échec de découverte vCenter

Cet article vous aide à résoudre les problèmes qui surviennent pour cause d’échecs de découverte de VMware vCenter.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Valeurs non numériques dans la propriété maxSnapShots

Dans les versions antérieures à 9.20, vCenter se déconnecte lorsqu’il récupère une valeur non numérique pour la propriété `snapshot.maxSnapShots` sur une machine virtuelle.

Ce problème est identifié par l’ID d’erreur 95126.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
Pour résoudre le problème :

- Identifiez la machine virtuelle et définissez la valeur sur une valeur numérique (dans vCenter, Machine virtuelle, Modifier les paramètres).

Ou

- Mettez à niveau votre serveur de configuration vers la version 9.20 ou ultérieure.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Problèmes de configuration de proxy pour la connectivité de vCenter

Découverte de vCenter respecte les paramètres de proxy par défaut du système, qui sont configurés par l’utilisateur système. Le service DRA respecte les paramètres de proxy fournis par l’utilisateur pendant l’installation du serveur de configuration à l’aide du programme d’installation unifiée ou du modèle OVA. 

En règle générale, le proxy est utilisé pour communiquer avec les réseaux publics, comme la communication avec Azure. Si le proxy est configuré et que vCenter se trouve dans un environnement local, il ne sera pas en mesure de communiquer avec DRA.

Les situations suivantes se produisent lorsque vous rencontrez ce problème :

- Le serveur vCenter \<vCenter> n’est pas accessible en raison de l’erreur suivante : Le serveur distant a retourné une erreur : (503) Serveur non disponible
- Le serveur vCenter \<vCenter> n’est pas accessible en raison de l’erreur suivante : Le serveur distant a retourné une erreur : Impossible de se connecter au serveur distant.
- Impossible de se connecter au serveur vCenter/ESXi.

Pour résoudre le problème :

Téléchargez l’[outil PsExec](https://aka.ms/PsExec). 

Utilisez l’outil PsExec pour accéder au contexte d’utilisateur système et déterminez si l’adresse proxy est configurée. Vous pouvez ensuite ajouter vCenter à la liste de contournement en utilisant les procédures suivantes.

Pour la configuration du proxy de découverte :

1. Ouvez IE dans le contexte d’utilisateur système à l’aide de l’outil PsExec.
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. Modifiez les paramètres de proxy dans Internet Explorer pour contourner l’adresse IP de vCenter.
3. Redémarrez le service tmanssvc.

Pour la configuration du proxy de DRA :

1. Ouvrez une invite de commandes et ouvrez le dossier Fournisseur Microsoft Azure Site Recovery.
 
    **cd C:\Program Files\Fournisseur Microsoft Azure Site Recovery**

3. Exécutez ensuite la commande suivante dans l’invite de commandes.
   
   **DRCONFIGURATOR. EXE /configure /AddBypassUrls [adresse IP/nom de domaine complet de vCenter Server fourni au moment de l’ajout de vCenter]**

4. Redémarrez le service du fournisseur DRA.

## <a name="next-steps"></a>Étapes suivantes

[Gérer le serveur de configuration pour la récupération d’urgence de machines virtuelles VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
