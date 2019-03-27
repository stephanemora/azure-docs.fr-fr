---
title: Quel est l’Agent Azure AD Connect Admin - Azure AD Connect | Microsoft Docs
description: Décrit les outils utilisés pour synchroniser et superviser votre environnement local avec Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17b99b7b03898ad72b4d9c7cc2ba9154855475ef
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488283"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Qu’est l’Agent Azure AD Connect administrateur ? 
L’Agent Azure AD Connect Administration est un nouveau composant d’Azure Active Directory Connect qui est installé sur un serveur d’Azure Active Directory Connect. Il est utilisé pour collecter des données spécifiques à partir de votre environnement Active Directory qui permet de prendre en charge d’ingénieur pour résoudre les problèmes lorsque vous ouvrez une demande de support Microsoft.

Lors de l’installation, l’Agent Azure AD Connect Administration attend des demandes spécifiques pour les données à partir d’Azure Active Directory, obtient les données demandées à partir de l’environnement de synchronisation et l’envoie à Azure Active Directory, où il est présenté à la Microsoft prennent en charge ingénieur.

Les informations que l’Agent Azure AD Connect Administration récupère à partir de votre environnement ne sont pas stockées en aucune façon : il ne s’affiche à l’ingénieur du support Microsoft pour les aider à l’examen et résolution des problèmes d’Azure Active Directory Connect cas de support liées au problème que vous avez ouvert

## <a name="how-is-the-azure-ad-connect-admin-agent-installed-on-the-azure-ad-connect-server"></a>Comment l’Agent Azure AD Connect Admin est installé sur le serveur Azure AD Connect ? 
Une fois que l’Agent d’administration est installé, vous verrez deux nouveaux programmes dans la liste « Ajout/Suppression de programmes » dans le panneau de votre serveur : 

![Agent d’administration](media/whatis-aadc-admin-agent/adminagent1.png)

Ne supprimez pas ni ne retirer installer les programmes tels qu’ils sont un élément essentiel de votre installation Azure AD Connect.

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Les données dans mon service de synchronisation sont indiquées à l’ingénieur du service Microsoft ?
Lorsque vous ouvrez une demande de support, l’ingénieur de Support Microsoft pouvez le voir, pour un utilisateur donné, les données pertinentes dans Active Directory, l’espace de connecteur Active Directory dans le serveur Azure Active Directory Connect, l’espace de connecteur Azure Active Directory dans Azure Active Directory se connecter le serveur et le métaverse dans le serveur Azure Active Directory Connect.

L’ingénieur du Support Microsoft ne peut pas modifier des données dans votre système et ne peut pas voir les mots de passe.

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Que se passe-t-il si je ne veux pas l’ingénieur du support Microsoft pour accéder à mes données ? 
 
Si vous ne souhaitez pas que l’ingénieur du service Microsoft pour accéder à vos données pour un appel de prise en charge que vous pouvez indiquer cela lorsque vous ouvrez une prise en charge appeler dans le portail : 

  1.    Ouvrez **C:\Program Files\Microsoft Azure AD se connecter Administration Agent\AzureADConnectAdministrationAgentService.exe.config** dans le bloc-notes.
  2.    Désactiver **UserDataEnabled** définition comme indiqué ci-dessous. Si **UserDataEnabled** paramètre existe et est défini sur true, puis affectez-lui la valeur false. Si le paramètre n’existe pas, puis ajoutez le paramètre comme indiqué ci-dessous.    
  `
 <appSettings>
   <add key="TraceFilename" value="ADAdministrationAgent.log" />
   <add key="UserDataEnabled" value="false" />
  </appSettings>
  `
  3.    Enregistrez le fichier de configuration.
  4.    Redémarrez le service de l’Agent Azure AD Connect Administration comme indiqué ci-dessous

![Agent d’administration](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](whatis-hybrid-identity.md).