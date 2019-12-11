---
title: 'Agent de provisionnement cloud Azure AD Connect : Mise à niveau automatique | Microsoft Docs'
description: Cette rubrique décrit la fonctionnalité de mise à niveau automatique intégrée dans l’agent de provisionnement cloud Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 193804064fbf6d1abb2ce06df1e923ec709ef6de
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795516"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Agent de provisionnement cloud Azure AD Connect : Mise à jour automatique

Grâce à la fonctionnalité de **mise à niveau automatique**, il n’a jamais été aussi simple de s’assurer que votre installation de l’agent de provisionnement cloud Azure AD Connect est toujours à jour. Cette fonctionnalité est activée par défaut, et ne peut pas être désactivée.

L’agent est installé ici :  **« Program files\Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe »**

Vous pouvez vérifier votre version en cliquant avec le bouton droit sur le fichier exécutable, puis en sélectionnant les propriétés, et les détails.

![Version du fichier de l’agent](media/how-to-automatic-upgrade/agent1.png)

Le programme de mise à jour de l’agent est installé ici :  **« Program files\Azure AD Connect Provisioning Agent Updater\AzureADConnectAgentUpdater.exe »**

Vous pouvez vérifier votre version en cliquant avec le bouton droit sur le fichier exécutable, puis en sélectionnant les propriétés, et les détails.

![Version du programme de mise à jour de l’agent](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstalling-the-agent"></a>Désinstallation de l’agent
Pour supprimer l’agent, accédez à **Désinstaller ou modifier un programme** et désinstallez les éléments suivants :

- Programme de mise à jour de l'agent Microsoft Azure AD Connect
- Agent d'approvisionnement Microsoft Azure AD Connect
- Package Agent d'approvisionnement Microsoft Azure AD Connect

![Suppression de l’agent](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Étapes suivantes 

- [Présentation du provisionnement](what-is-provisioning.md)
- [Présentation du provisionnement cloud Azure AD Connect](what-is-cloud-provisioning.md)

