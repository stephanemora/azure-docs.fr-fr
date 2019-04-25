---
title: Flux de travail de l’Architecture de machine virtuelle Azure Windows | Microsoft Docs
description: Cet article présente les processus de flux de travail lorsque vous déployez un service.
services: cloud-services
documentationcenter: ''
author: genlin
manager: Willchen
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 7c8459a6694663a49203b6ec21a760d3e6bd60c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60480743"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Flux de travail d’Architecture de machine virtuelle Windows Azure classique 
Cet article fournit une vue d’ensemble des processus de flux de travail qui se produisent lorsque vous déployez ou mettez à jour une ressource Azure comme une machine virtuelle. 

> [!NOTE]
>Azure a deux modèles de déploiement différents pour créer et utiliser des ressources : Resource Manager et classique. Cet article traite du modèle de déploiement classique.

Le diagramme suivant présente l’architecture de ressources Azure.

![Flux de travail Azure](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>Principes fondamentaux du flux de travail
   
**R**. RDFE / FFE est le chemin d’accès de communication à partir de l’utilisateur à l’infrastructure. RDFE (Front-End de RedDog) est l’API exposée publiquement qui est le serveur frontal vers le portail de gestion et l’API de gestion des services tels que Visual Studio, MMC du service Azure et ainsi de suite.  Toutes les demandes à partir de l’utilisateur passent par RDFE. FFE (Fabric Front-End) est la couche qui se traduit par des demandes de RDFE dans les commandes de fabric. Toutes les demandes de RDFE passent par le FFE atteindre des contrôleurs de l’infrastructure.

**B**. Le contrôleur de tissu est responsable de la maintenance et la surveillance de toutes les ressources dans le centre de données. Il communique avec les agents hôtes de fabric sur l’envoi d’informations du système d’exploitation telles que la version du système d’exploitation invité, package de service, configuration du service et l’état du service d’infrastructure.

**C**. L’Agent hôte se trouve sur l’hôte OSsystem et est responsable de la configuration de système d’exploitation invité et communiquer avec l’Agent invité (WindowsAzureGuestAgent) afin de mettre à jour le rôle vers un état but et faire par pulsations d’inventaire vérifie auprès de l’agent invité. Si l’Agent hôte ne reçoit pas de réponse de pulsation pendant 10 minutes, l’Agent hôte redémarre le système d’exploitation invité.

**C2**. WaAppAgent est responsable de l’installation, la configuration et la mise à jour WindowsAzureGuestAgent.exe.

**D**.  WindowsAzureGuestAgent est responsable de ce qui suit :

1. Configuration du système d’exploitation invité, notamment les pare-feu, ACL, LocalStorage ressources, package de service et la configuration et les certificats. Configurer le SID du compte d’utilisateur le rôle sous lequel s’exécutera.
2. Communiquer l’état du rôle à l’infrastructure.
3. Démarrage WaHostBootstrapper et en le surveillant pour vous assurer que le rôle est dans l’état de l’objectif.

**E**. WaHostBootstrapper est responsable de :

1. Lecture de la configuration de rôle, et le démarrage de toutes les tâches appropriées et les processus pour configurer et exécuter le rôle.
2. Surveillance de tous ses processus enfants.
3. Déclencher l’événement StatusCheck sur le processus hôte de rôle.

**F**. IISConfigurator s’exécute si le rôle est configuré comme rôle web IIS complet (il ne sera pas exécutée pour les rôles HWC 1.2 de kit de développement logiciel). Il est chargé de :

1. Démarrage des services IIS standards
2. Configuration du module de réécriture dans la configuration web
3. Configurer le pool d’applications pour le rôle configuré dans le modèle de service
4. Configurer la journalisation IIS pour pointer vers le dossier DiagnosticStore LocalStorage
5. Configuration des autorisations et les listes ACL
6. Le site Web se trouve dans % roleroot%:\sitesroot\0 et le pool d’applications sur cet emplacement pour exécuter IIS. 

**G**. Tâches de démarrage sont définies par le modèle de rôle et démarrées par WaHostBootstrapper. Tâches de démarrage peuvent être configurées pour exécuter de façon asynchrone en arrière-plan, et le programme d’amorçage hôte démarre la tâche de démarrage et avant de passer à d’autres tâches de démarrage. Tâches de démarrage peuvent également être configurés pour s’exécuter en mode Simple (par défaut) dans lequel le programme d’amorçage hôte attendra la tâche de démarrage à la fin de l’exécution et retourne un code de sortie de réussite (0) avant de continuer à la tâche de démarrage suivante.

**H**. Ces tâches font partie du Kit de développement et sont définies comme des plug-ins dans la définition du rôle du service (.csdef). Quand il est développé dans les tâches de démarrage, le **DiagnosticsAgent** et **RemoteAccessAgent** sont uniques dans la mesure où ils chaque définissent deux tâches de démarrage, un standard et celui où se trouve un **/blockStartup** paramètre. La tâche de démarrage normal est définie comme une tâche de démarrage en arrière-plan afin qu’il peut s’exécuter en arrière-plan pendant que le rôle lui-même est en cours d’exécution. Le **/blockStartup** tâche de démarrage est définie comme une tâche de démarrage Simple afin que WaHostBootstrapper attend que sa fermeture avant de continuer. Le **/blockStartup** tâche attend la tâche régulière terminer son initialisation, et il se termine, puis autorise le programme d’amorçage hôte continuer. Cela afin que l’accès RDP et diagnostic peuvent être configurés avant le début du processus de rôle (cette opération est effectuée via la tâche /blockStartup). Cela permet également l’accès RDP à poursuivre son exécution après que le programme d’amorçage de l’hôte a terminé les tâches de démarrage (cette opération est effectuée via la tâche normale) et diagnostic.

**I**. WaWorkerHost est le processus hôte standard pour les rôles de travail normal. Ce processus hôte héberge des DLL et le code de point d’entrée, tels que OnStart et exécuter tous les rôles.

**J**. WaWebHost est le processus hôte standard pour les rôles web s’ils sont configurés pour utiliser le Kit de développement logiciel compatible 1.2 hébergeable (Hostable Web Core). Les rôles peuvent activer le mode HWC en supprimant l’élément à partir de la définition de service (.csdef). Dans ce mode, code et les DLL de tous les service s’exécutent à partir du processus WaWebHost. IIS (w3wp) n’est pas utilisé et il n’y a aucune AppPools configuré dans le Gestionnaire des services Internet, car il est hébergé par IIS à l’intérieur de WaWebHost.exe.

**K**. WaIISHost est le processus hôte pour le code de point d’entrée rôle pour les rôles web qui utilisent IIS complet. Ce processus charge le premier fichier DLL qui se trouve qui utilise le **RoleEntryPoint** classe et exécute le code à partir de cette classe (OnStart, exécution, OnStop). N’importe quel **RoleEnvironment** (par exemple, StatusCheck et a été modifié) qui sont créés dans la classe RoleEntryPoint sont déclenchés dans ce processus.

**L**. W3wp est le processus de travail IIS standard qui est utilisé si le rôle est configuré pour utiliser la version complète d’IIS. Cette commande exécute le pool d’applications qui est configuré à partir de IISConfigurator. Déclenchement d’éventuels événements RoleEnvironment (par exemple, StatusCheck et a été modifié) qui sont créés ici dans ce processus. Notez que RoleEnvironment événements sont déclenchés dans les deux emplacements (WaIISHost et w3wp.exe) si vous vous abonnez à des événements dans les deux processus.

## <a name="workflow-processes"></a>Processus de workflow

1. Un utilisateur effectue une demande, telles que le téléchargement de fichiers .cspkg et .cscfg, indiquant une ressource pour arrêter ou apporter une modification de configuration, et ainsi de suite. Cela est possible via le portail Azure ou un outil qui utilise l’API de gestion des services, tels que la fonctionnalité de publication de Visual Studio. Cette demande est transmise à RDFE pour effectuer toutes les relatives à l’abonnement fonctionnent et ensuite communiquent la demande à FFE. Le reste de ces étapes de flux de travail sont pour déployer un nouveau package et le démarrer.
2. FFE recherche le pool d’ordinateurs approprié (basé sur les commentaires des clients par exemple, en tant que groupe d’affinités ou emplacement géographique plus entrée à partir de l’infrastructure, telles que la disponibilité de l’ordinateur) et communique avec le contrôleur de structure maître dans ce pool d’ordinateurs.
3. Le contrôleur de structure détecte un ordinateur hôte qui a de cœurs de processeur disponibles (ou version supérieure crée un nouvel hôte). Le package de service et la configuration est copié sur l’hôte et le contrôleur de structure communique avec l’agent hôte sur la système d’exploitation pour déployer le package de l’hôte (configurer les adresses IP dynamiques, ports, système d’exploitation invité et ainsi de suite).
4. L’agent hôte démarre le système d’exploitation invité et communique avec l’agent invité (WindowsAzureGuestAgent). L’hôte envoie des pulsations pour l’invité pour vous assurer que le rôle travaille à son état d’objectif.
5. WindowsAzureGuestAgent configure l’invité du système d’exploitation (pare-feu, ACL, LocalStorage et ainsi de suite), copie d’un nouveau fichier de configuration XML vers c:\Config, puis démarre le processus WaHostBootstrapper.
6. Pour les rôles web IIS complet WaHostBootstrapper démarre IISConfigurator et lui indique de supprimer n’importe quel AppPools existant pour le rôle web à partir d’IIS.
7. WaHostBootstrapper lit le **démarrage** tâches à partir de E:\RoleModel.xml et commence à exécuter des tâches de démarrage. WaHostBootstrapper attend jusqu'à ce que toutes les tâches de démarrage Simple ont terminé et a retourné un message « succès ».
8. Pour les rôles web IIS complet WaHostBootstrapper indique IISConfigurator pour configurer le pool d’applications IIS et pointe le site à `E:\Sitesroot\<index>`, où `<index>` est un index de base 0 dans le nombre de <Sites> éléments définis pour le service.
9. WaHostBootstrapper démarre le processus hôte en fonction du type de rôle :
    1. **Rôle de travail**: WaWorkerHost.exe est démarré. WaHostBootstrapper exécute la méthode OnStart(). Après son retour, WaHostBootstrapper commence à exécuter la méthode Run() et puis simultanément marque le rôle en tant que prêt et les place dans la rotation d’équilibrage de charge (si InputEndpoints sont définis). WaHostBootsrapper entre ensuite dans une boucle de vérification de l’état du rôle.
    1. **Rôle Web de SDK 1.2 HWC**: WaWebHost est démarré. WaHostBootstrapper exécute la méthode OnStart(). Après son retour, WaHostBootstrapper commence à exécuter la méthode Run() et marque ensuite simultanément le rôle en tant que prêt et les place dans la rotation d’équilibrage de charge. WaWebHost émet une demande de mise en route (/do.rd_runtime_init GET). Toutes les requêtes web sont envoyées à WaWebHost.exe. WaHostBootsrapper entre ensuite dans une boucle de vérification de l’état du rôle.
    1. **Total de rôle Web IIS**: aIISHost est démarré. WaHostBootstrapper exécute la méthode OnStart(). Après son retour, elle commence à exécuter la méthode Run() et marque ensuite simultanément le rôle en tant que prêt et les place dans la rotation d’équilibrage de charge. WaHostBootsrapper entre ensuite dans une boucle de vérification de l’état du rôle.
10. Les demandes web entrantes vers une version complète d’IIS web déclencheurs de rôle IIS pour démarrer le processus W3WP et répondre à la demande, le même comme il le ferait dans un environnement d’IIS en local.

## <a name="log-file-locations"></a>Emplacements des fichiers journaux

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
Ce journal contient des modifications au service, y compris les nouvelles configurations démarre et s’arrête. Si le service ne change pas, attendez-vous à voir moins longs de temps dans ce fichier journal.
- C:\Logs\WaAppAgent.Log.  
Ce journal contient des mises à jour de l’état et les notifications de pulsation et est de mise à jour toutes les 2 à 3 secondes.  Ce journal contient une vue de l’historique de l’état de l’instance et vous indique quand l’instance n’était pas dans l’état prêt.
 
**WaHostBootstrapper**

C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log
 
**WaWebHost**

C:\Resources\Directory\<guid>.<role>\WaWebHost.log
 
**WaIISHost**

C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log
 
**IISConfigurator**

C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log
 
**Journaux d’activité IIS**

C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1
 
**Journaux d’événements Windows**

D:\Windows\System32\Winevt\Logs
 



