---
title: Désinstaller Azure AD Connect
description: Ce document explique comment désinstaller Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5eb537a70c69745c8067ffb71cfb895a0875945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96934130"
---
# <a name="uninstall-azure-ad-connect"></a>Désinstaller Azure AD Connect

Ce document explique comment désinstaller Azure AD Connect.

## <a name="uninstall-azure-ad-connect-from-the-server"></a>Désinstallation d’Azure AD Connect du serveur
La première chose à faire est de supprimer Azure AD Connect du serveur sur lequel il s’exécute.  Utiliser les étapes suivantes :

 1. Sur le serveur sur lequel s’exécute Azure AD Connect, accédez à **Panneau de configuration**.
 2. Cliquez sur **Désinstaller un programme**
 ![Désinstaller un programme](media/how-to-connect-uninstall/uninstall-1.png)</br>
 
 3. Sélectionnez ensuite **Azure AD Connect**.
 ![Sélection d’Azure AD Connect](media/how-to-connect-uninstall/uninstall-2.png)</br>
 
 4. Cliquez sur **Oui** dans l’invite pour confirmer.
 5. L’écran Azure AD Connect s’affiche.  Cliquez sur **Supprimer**.
 ![Remove](media/how-to-connect-uninstall/uninstall-3.png)</br>
 
 6. Une fois cette action terminée, cliquez sur **Quitter**.
 7. ![Quitter](media/how-to-connect-uninstall/uninstall-4.png)</br>
 
 8. Dans le **Panneau de configuration**, cliquez sur **Actualiser**. Tous les composants doivent avoir été supprimés.


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](whatis-hybrid-identity.md).
- [Installer Azure AD Connect à l’aide d’une base de données ADSync existante](how-to-connect-install-existing-database.md)
- [Installer Azure AD Connect à l’aide d’autorisations administrateur déléguées SQL](how-to-connect-install-sql-delegation.md)

