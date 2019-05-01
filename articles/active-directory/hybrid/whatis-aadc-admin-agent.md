---
title: Quel est l’Agent Azure AD Connect Admin - Azure AD Connect | Microsoft Docs
description: Décrit les outils utilisés pour synchroniser et superviser votre environnement local avec Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ab3fff4294b4cda3d1554ef2761d3f4acaca35
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64687248"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Présentation de l’agent d’administration Azure AD Connect 
L’Agent Azure AD Connect Administration est un nouveau composant d’Azure Active Directory Connect qui peut être installé sur un serveur d’Azure Active Directory Connect. Il est utilisé pour collecter des données spécifiques à partir de votre environnement Active Directory qui permet de prendre en charge d’ingénieur pour résoudre les problèmes lorsque vous ouvrez une demande de support Microsoft. 

>[!NOTE]
>L’agent d’administration n’est pas installé et activé par défaut.  Vous devez installer l’agent afin de collecter des données afin de faciliter les cas de support.

Lors de l’installation, l’Agent Azure AD Connect Administration attend des demandes spécifiques pour les données à partir d’Azure Active Directory, obtient les données demandées à partir de l’environnement de synchronisation et l’envoie à Azure Active Directory, où il est présenté à la Microsoft prennent en charge ingénieur. 

Les informations que l’Agent Azure AD Connect Administration récupère à partir de votre environnement ne sont pas stockées en aucune façon : il ne s’affiche à l’ingénieur du support Microsoft pour les aider à l’examen et résolution des problèmes d’Azure Active Directory Connect cas de support liées au problème que vous avez ouvert l’Agent Azure AD Connect Administration n’est pas installé par défaut sur le serveur Azure AD Connect. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Installer l’Agent Azure AD Connect Administration sur le serveur Azure AD Connect 
Les binaires de l’Agent Azure AD Connect Administration sont placés dans le serveur AAD Connect. Pour installer l’agent, procédez comme suit : 



1. Ouvrez powershell en mode administrateur 
2. Accédez au répertoire où l’application est trouve cd « C:\Program Files\Microsoft Azure Connect\SetupFiles Active au répertoire » 
3. Exécutez l’application AADConnectAdminAgentSetup.exe 
 
Lorsque vous y êtes invité, entrez vos informations d’identification administrateur général de Azure AD. 

>[!NOTE]
>Il existe un problème connu où vous serez invité vos informations d’identification plusieurs fois. Une solution est prévue dans la prochaine version.

Une fois que l’agent est installé, vous verrez deux nouveaux programmes dans la liste « Ajout/Suppression de programmes » dans le panneau de votre serveur : 

![Agent d’administration](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Les données dans mon service de synchronisation sont indiquées à l’ingénieur du service Microsoft ? 
Lorsque vous ouvrez un cas de prise en charge de l’ingénieur de Support Microsoft pouvez le voir, pour un utilisateur donné, les données pertinentes dans Active Directory, l’espace de connecteur Active Directory dans le serveur Azure Active Directory Connect, l’espace de connecteur Azure Active Directory dans Azure Active Directory se connecter le serveur et le métaverse dans le serveur Azure Active Directory Connect. 

L’ingénieur du Support Microsoft ne peut pas modifier des données dans votre système et ne peut pas voir les mots de passe. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Que se passe-t-il si je ne veux pas l’ingénieur du support Microsoft pour accéder à mes données ? 
Une fois que l’agent est installé, si vous ne souhaitez pas que l’ingénieur du service Microsoft pour accéder à vos données pour un appel de la prise en charge, vous pouvez désactiver la fonctionnalité en modifiant le fichier de configuration de service, comme décrit ci-dessous : 

1.  Ouvrez **C:\Program Files\Microsoft Azure AD se connecter Administration Agent\AzureADConnectAdministrationAgentService.exe.config** dans le bloc-notes.
2.  Désactiver **UserDataEnabled** définition comme indiqué ci-dessous. Si **UserDataEnabled** paramètre existe et est défini sur true, puis affectez-lui la valeur false. Si le paramètre n’existe pas, puis ajoutez le paramètre comme indiqué ci-dessous.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.  Enregistrez le fichier de configuration.
4.  Redémarrez le service de l’Agent Azure AD Connect Administration comme indiqué ci-dessous

![Agent d’administration](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](whatis-hybrid-identity.md).
