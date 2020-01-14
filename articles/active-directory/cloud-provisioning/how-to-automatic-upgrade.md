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
ms.openlocfilehash: d3786386f75f4b85fe89562254eab63471de6cb2
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75549395"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Agent de provisionnement cloud Azure AD Connect : Mise à jour automatique

La fonctionnalité de mise à niveau automatique vous permet de facilement vous assurer que votre installation de l’agent d'approvisionnement cloud Azure Active Directory (Azure AD) Connect est toujours à jour. Cette fonctionnalité est activée par défaut, et ne peut pas être désactivée.

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

