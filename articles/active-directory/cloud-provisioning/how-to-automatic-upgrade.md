---
title: 'Agent de provisionnement cloud Azure AD Connect : Mise à niveau automatique | Microsoft Docs'
description: Cet article décrit la fonctionnalité de mise à niveau automatique intégrée dans l’agent de provisionnement cloud Azure AD Connect.
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
ms.openlocfilehash: f09b2fc685881aa8a7bd87b6a855c657af9ef43d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190311"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Agent de provisionnement cloud Azure AD Connect : Mise à jour automatique

La fonctionnalité de mise à niveau automatique vous permet de facilement vous assurer que votre installation de l’agent d'approvisionnement cloud Azure Active Directory (Azure AD) Connect est toujours à jour.

L’agent est installé ici : « Program files\Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe »

Pour vérifier votre version, cliquez avec le bouton droit sur le fichier exécutable et sélectionnez les propriétés, puis les détails.

![Version du fichier de l’agent](media/how-to-automatic-upgrade/agent1.png)

Le programme de mise à jour de l’agent est installé ici : « Program files\Azure AD Connect Provisioning Agent Updater\AzureADConnectAgentUpdater.exe »

Pour vérifier votre version, cliquez avec le bouton droit sur le fichier exécutable et sélectionnez les propriétés, puis les détails.

![Version du programme de mise à jour de l’agent](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>Désinstaller l’agent
Pour supprimer l’agent, accédez à **Désinstaller ou modifier un programme** et désinstallez les éléments suivants :

- **Programme de mise à jour de l'agent Microsoft Azure AD Connect**
- **Agent d'approvisionnement Microsoft Azure AD Connect**
- **Package Agent d'approvisionnement Microsoft Azure AD Connect**

![Suppression de l’agent](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Étapes suivantes 

- [Présentation du provisionnement](what-is-provisioning.md)
- [Présentation du provisionnement cloud Azure AD Connect](what-is-cloud-provisioning.md)

