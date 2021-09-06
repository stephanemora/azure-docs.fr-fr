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
ms.openlocfilehash: dea990e1075e8a0714959f42fdef38286a120912
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449587"
---
# <a name="installation-of-the-azure-ad-ecma-connector-host"></a>Installation de l’Hôte de connecteur ECMA Azure AD

>[!IMPORTANT]
> Actuellement, la préversion du provisionnement local n’est disponible que sur invitation. Pour demander l’accès à la fonctionnalité, utilisez le [formulaire de demande d’accès](https://aka.ms/onpremprovisioningpublicpreviewaccess). Nous allons ouvrir la préversion à un plus grand nombre de clients et de connecteurs au cours des prochains mois, car nous préparons la disponibilité générale.

L’hôte de connecteur ECMA Azure Active Directory (Azure AD) est inclus dans le package de l’agent d’approvisionnement Azure AD Connect. L’agent d’approvisionnement et l’hôte de connecteur ECMA Azure AD sont deux services Windows distincts. Ils sont installés à l’aide d’un programme d’installation, qui est déployé sur le même ordinateur.

Ce flux vous guide tout au long du processus d’installation et de configuration de l’Hôte connecteur ECMA Azure AD.

 ![Diagramme montrant le flux d’installation.](./media/on-premises-ecma-install/flow-1.png)

Pour plus d’informations sur l’installation et la configuration, consultez :

   - [Conditions préalables requises pour l’Hôte de connecteur ECMA Azure AD](on-premises-ecma-prerequisites.md)
   - [Configurer l’Hôte de connecteur ECMA Azure AD et l’agent d’approvisionnement](on-premises-ecma-configure.md)
   - [Configuration du connecteur SQL générique pour l’Hôte de connecteur ECMA Azure AD](on-premises-sql-connector-configure.md)

## <a name="download-and-install-the-azure-ad-connect-provisioning-agent-package"></a>Télécharger et installer le package de l’agent d’approvisionnement Azure AD Connect

 1. Connectez-vous au portail Azure.
 1. Accédez à **Applications d’entreprise** > **Ajouter une application**.
 1. Recherchez l’application **Approvisionnement local** et ajoutez-la à l’image de votre locataire.
 1. Accédez au volet **Approvisionnement**.
 1. Sélectionnez **Connectivité locale**.
 1. Téléchargez le programme d’installation de l’agent.
 1. Exécutez le programme d’installation d’approvisionnement Azure AD Connect **AADConnectProvisioningAgentSetup.msi**.
 1. Sur l’écran **Package Agent d’approvisionnement Microsoft Azure AD Connect**, acceptez les termes du contrat de licence et sélectionnez **Installer**.
 
    ![Écran Package Agent d’approvisionnement Microsoft Azure AD Connect.](media/on-premises-ecma-install/install-1.png)</br>
 1. Une fois cette opération terminée, l’Assistant Configuration démarre. Sélectionnez **Suivant**.
 
    ![Capture d’écran montrant l’écran d’accueil.](media/on-premises-ecma-install/install-2.png)</br>

 1. Sur l’écran **Sélectionner une extension**, sélectionnez **Approvisionnement d’applications locales (Azure AD à l’application)** . Sélectionnez **Suivant**. 
 
    ![Capture d’écran montrant la sélection d’extension.](media/on-premises-ecma-install/install-3.png)</br>
 1. Utilisez votre compte d’administrateur général pour vous connecter à Azure AD.
 
     ![Capture d’écran montrant la connexion à Azure.](media/on-premises-ecma-install/install-4.png)</br>
 1. Sur l’écran **Agent configuration**, sélectionnez **Confirm**.
 
     ![Capture d’écran montrant la confirmation de l’installation.](media/on-premises-ecma-install/install-5.png)</br>
 1. Une fois l’installation terminée, un message s’affiche au bas de l’Assistant. Sélectionnez **Quitter**.
 
     ![Capture d’écran montrant la fin.](media/on-premises-ecma-install/install-6.png)</br>
 

Maintenant que le package de l’agent a été correctement installé, vous devez configurer l’hôte de connecteur ECMA Azure AD et créer ou importer des connecteurs.
 
## <a name="next-steps"></a>Étapes suivantes

- [Conditions préalables requises pour l’Hôte de connecteur ECMA Azure AD](on-premises-ecma-prerequisites.md)
- [Configuration de l’hôte du connecteur Azure AD ECMA](on-premises-ecma-configure.md)
- [Connecteur SQL générique](on-premises-sql-connector-configure.md)
