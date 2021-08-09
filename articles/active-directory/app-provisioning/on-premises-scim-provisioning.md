---
title: Approvisionnement d’applications locales Azure AD vers des applications prenant en charge SCIM
description: Cet article explique comment procéder à l’approvisionnement d’applications locales vers des applications prenant en charge SCIM.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 05/28/2021
ms.author: billmath
ms.reviewer: arvinh
ms.openlocfilehash: bf0b13bf1e2de31d15599bff2aa8750396f021ac
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570035"
---
# <a name="azure-ad-on-premises-application-provisioning-to-scim-enabled-apps"></a>Approvisionnement d’applications locales Azure AD vers des applications prenant en charge SCIM

>[!IMPORTANT]
> Actuellement, la préversion d’approvisionnement local n’est disponible que sur invitation. Vous pouvez demander l’accès à cette fonctionnalité [ici](https://aka.ms/onpremprovisioningpublicpreviewaccess). Nous allons ouvrir la préversion à un plus grand nombre de clients et de connecteurs au cours des prochains mois, car nous préparons la disponibilité générale.

Le service d’approvisionnement Azure AD prend en charge un client [SCIM 2.0](https://techcommunity.microsoft.com/t5/identity-standards-blog/provisioning-with-scim-getting-started/ba-p/880010) qui peut être utilisé pour approvisionner automatiquement des utilisateurs dans des applications locales ou dans le cloud. Ce document explique comment vous pouvez utiliser le service d’approvisionnement Azure AD pour approvisionner des utilisateurs dans une application locale compatible avec SCIM. Si vous envisagez d’approvisionner des utilisateurs dans des applications locales sans SCIM, telles qu’un annuaire LDAP non-AD ou SQL DB, cliquez ici (lien vers un nouveau document que nous allons créer). Si vous envisagez d’approvisionner des utilisateurs dans des applications cloud, telles que DropBox, Atlassian, etc., passez en revue les [tutoriels](../../active-directory/saas-apps/tutorial-list.md) spécifiques à l’application. 

![architecture](./media/on-premises-scim-provisioning/scim-4.png)


## <a name="pre-requisites"></a>Conditions préalables
- Locataire Azure AD avec Azure AD Premium P1 ou Premium P2 (ou EMS E3 ou E5). 
    [!INCLUDE [active-directory-p1-license.md](../../../includes/active-directory-p1-license.md)]
- Rôle Administrateur pour l’installation de l’agent.  Il s’agit d’un effort unique et cela doit être un compte Azure qui est un administrateur hybride ou un administrateur général. 
- Rôle Administrateur pour la configuration de l’application dans le cloud (administrateur d’application, administrateur d’application cloud, administrateur général, rôle personnalisé avec autorisations)

## <a name="steps-for-on-premises-app-provisioning-to-scim-enabled-apps"></a>Procédure d’approvisionnement d’applications locales vers des applications prenant en charge SCIM
Suivez la procédure ci-dessous pour approvisionner les applications compatibles avec SCIM. 

 1. Ajoutez l’application « Approvisionnement SCIM basé sur un agent » à partir de la [galerie](../../active-directory/manage-apps/add-application-portal.md).
 2. Accédez à votre application > Approvisionnement > Télécharger l’agent d’approvisionnement.
 3. Cliquez sur la connectivité locale et téléchargez l’agent d’approvisionnement.
 4. Copiez l’agent sur la machine virtuelle ou le serveur sur lequel votre point de terminaison SCIM est hébergé.
 5. Ouvrez le programme d’installation de l’agent d’approvisionnement, acceptez les conditions d’utilisation du service, puis cliquez sur installer.
 6. Ouvrez l’Assistant de l’agent d’approvisionnement et sélectionnez l’approvisionnement local lorsque vous êtes invité à indiquer l’extension que vous souhaitez activer.
 7. Fournissez les informations d’identification Administrateur Azure AD lorsque vous êtes invité à autoriser (administrateur hybride ou administrateur général requis).
 8. Cliquez sur confirmer pour valider le succès de l’installation.
 9. Revenez à votre application > connectivité locale.
 10. Dans la liste déroulante, sélectionnez l’agent que vous avez installé, puis cliquez sur attribuer un agent.
 11. Patientez 10 minutes ou redémarrez le service Agent d’approvisionnement Azure AD Connect sur votre serveur/machine virtuelle.
 12. Indiquez l’URL de votre point de terminaison SCIM dans le champ URL du locataire (par exemple, Https://localhost:8585/scim).
     ![attribuer un agent](./media/on-premises-scim-provisioning/scim-2.png)
 13. Cliquez sur tester la connexion et enregistrez les informations d’identification.
 14. Configurez les [mappages d’attributs](customize-application-attributes.md) ou les règles d’[étendue](define-conditional-rules-for-provisioning-user-accounts.md) requis pour votre application.  
 15. Ajoutez des utilisateurs à l’étendue en [attribuant des utilisateurs et des groupes](../../active-directory/manage-apps/add-application-portal-assign-users.md) à l’application.
 16. Testez l’approvisionnement de quelques utilisateurs [à la demande](provision-on-demand.md).
 17. Ajoutez des utilisateurs supplémentaires dans l’étendue en les attribuant à votre application.
 18. Accédez au panneau d’approvisionnement, puis cliquez sur démarrer l’approvisionnement.
 19. Analyse à l’aide des [journaux d’approvisionnement](../../active-directory/reports-monitoring/concept-provisioning-logs.md).
 

## <a name="things-to-be-aware-of"></a>Éléments à prendre en considération
* Assurez-vous que votre implémentation [SCIM](https://techcommunity.microsoft.com/t5/identity-standards-blog/provisioning-with-scim-getting-started/ba-p/880010) répond aux [exigences du SCIM Azure AD](use-scim-to-provision-users-and-groups.md).
  * Azure AD offre un [code de référence](https://github.com/AzureAD/SCIMReferenceCode/wiki) open source que les développeurs peuvent utiliser pour démarrer leur implémentation SCIM (le code est tel quel)
* Prenez en charge le point de terminaison /schemaDiscovery pour diminuer la configuration requise dans le Portail Azure. 

## <a name="next-steps"></a>Étapes suivantes

- [Approvisionnement d’applications](user-provisioning.md)
- [Installation de l’Hôte de connecteur ECMA Azure AD](on-premises-ecma-install.md)
- [Configuration de l’Hôte de connecteur ECMA Azure AD](on-premises-ecma-configure.md)
- [Connecteur SQL générique](on-premises-sql-connector-configure.md)
- [Tutoriel : Connecteur SQL générique pour l’Hôte de connecteur ECMA](tutorial-ecma-sql-connector.md)
