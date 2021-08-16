---
title: Installation de l’Hôte de connecteur ECMA Azure AD
description: Cet article explique la procédure d’installation de l’Hôte de connecteur ECMA Azure AD.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/28/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e12ce0d979b323f10104d345f86b77618e094ba6
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570373"
---
# <a name="installation-of-the-azure-ad-ecma-connector-host"></a>Installation de l’Hôte de connecteur ECMA Azure AD

>[!IMPORTANT]
> Actuellement, la préversion de l’approvisionnement local n’est disponible que sur invitation. Vous pouvez demander l’accès à cette fonctionnalité [ici](https://aka.ms/onpremprovisioningpublicpreviewaccess). Nous allons ouvrir la préversion à un plus grand nombre de clients et de connecteurs au cours des prochains mois, car nous préparons la disponibilité générale.

L’Hôte de connecteur ECMA Azure AD est inclus et intégré au package de l’agent d’approvisionnement Azure AD Connect.  L’agent d’approvisionnement et l’Hôte de connecteur ECMA Azure AD sont deux services Windows distincts installés à l’aide d’un programme d’installation, déployés sur le même ordinateur. 

L’installation et la configuration de l’Hôte de connecteur ECMA Azure AD sont un processus.  Utilisez le flux ci-dessous pour vous guider tout au long du processus.

 ![Flux d’installation](./media/on-premises-ecma-install/flow-1.png)  

Pour plus d’informations sur l’installation et la configuration, consultez :
   - [Conditions préalables requises pour l’Hôte de connecteur ECMA Azure AD](on-premises-ecma-prerequisites.md)
   - [Configurer l’Hôte de connecteur ECMA Azure AD et l’agent d’approvisionnement](on-premises-ecma-configure.md)
    - [Configuration du connecteur SQL générique pour l’Hôte de connecteur ECMA Azure AD](on-premises-sql-connector-configure.md)


## <a name="download-and-install-the-azure-ad-connect-provisioning-agent-package"></a>Télécharger et installer le package de l’agent d’approvisionnement Azure AD Connect

 1. Se connecter au portail Azure
 2. Accédez à Applications d’entreprise > Ajouter une application
 3. Recherchez l’application « Approvisionnement local » et ajoutez-la à l’image de votre locataire
 4. Accédez au volet d’approvisionnement
 5. Cliquez sur Connectivité locale
 6.  Téléchargez le programme d’installation de l’agent
 7. Exécutez le programme d’installation du provisionnement Azure AD Connect AADConnectProvisioningAgentSetup.msi.
 8. Dans l’écran **Package Agent d’approvisionnement Microsoft Azure AD Connect**, acceptez les termes du contrat de licence et sélectionnez **Installer**.
   ![Écran Package Agent d’approvisionnement Microsoft Azure AD Connect](media/on-premises-ecma-install/install-1.png)</br>
 9. Une fois cette opération terminée, l’Assistant Configuration démarre. Cliquez sur **Suivant**.
   ![Écran d’accueil](media/on-premises-ecma-install/install-2.png)</br>
 10. Dans l’écran **Sélectionner une extension**, sélectionnez **Approvisionnement d’applications locales (Azure AD à l’application)** , puis cliquez sur **Suivant**. 
   ![Sélectionnez une extension](media/on-premises-ecma-install/install-3.png)</br>
 12. Utilisez votre compte d’administrateur général et connectez-vous à Azure AD.
     ![Connexion à Azure](media/on-premises-ecma-install/install-4.png)</br>
 13.  Dans l’écran **Configuration de l'agent**, cliquez sur **Confirmer**.
     ![Confirmez l'installation](media/on-premises-ecma-install/install-5.png)</br>
 14.  Une fois l’installation terminée, un message s’affiche au bas de l’Assistant.  Cliquez sur **Terminer**.
     ![Cliquez sur Terminer](media/on-premises-ecma-install/install-6.png)</br>
 15. Cliquez sur **Fermer**.

Maintenant que le package de l’agent a été correctement installé, vous devez configurer l’Hôte de connecteur ECMA Azure AD et créer ou importer des connecteurs.  
## <a name="next-steps"></a>Étapes suivantes


- [Conditions préalables requises pour l’Hôte de connecteur ECMA Azure AD](on-premises-ecma-prerequisites.md)
- [Configuration de l’Hôte de connecteur ECMA Azure AD](on-premises-ecma-configure.md)
- [Connecteur SQL générique](on-premises-sql-connector-configure.md)
