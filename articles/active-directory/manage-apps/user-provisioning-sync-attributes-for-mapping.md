---
title: Synchroniser les attributs à Azure AD pour le mappage | Microsoft Docs
description: Découvrez comment synchroniser les attributs de votre annuaire local Active Directory à Azure AD. Lors de la configuration des utilisateurs pour les applications SaaS, utilisez la fonctionnalité d’extension de répertoire pour ajouter des attributs de source qui ne sont pas synchronisés par défaut.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mimart
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9460bc924ea662646360d1a3f5087949a39a03f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806115"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>L’attribut d’Active Directory en local à Azure AD pour l’approvisionnement d’une application de synchronisation

Lors de la personnalisation des mappages d’attributs pour l’approvisionnement, vous constaterez peut-être que l’attribut que vous souhaitez mapper n’apparaît pas dans le **attribut Source** liste. Cet article vous montre comment ajouter l’attribut manquant en synchronisant à partir de votre réseau local Active Directory (AD) à Azure Active Directory (Azure AD).

Azure AD doit contenir toutes les données requises pour créer un profil utilisateur lors de l’approvisionnement des comptes utilisateur d’Azure AD vers une application SaaS. Dans certains cas, pour rendre les données disponibles vous pouvez doivent synchroniser les attributs à partir de votre réseau local Active Directory à Azure AD. Azure AD Connect synchronise automatiquement certains attributs pour Azure AD, mais pas tous les attributs. En outre, certains attributs (par exemple, SAMAccountName) sont synchronisées par défaut ne peuvent pas être exposés via l’API Graph Azure AD. Dans ce cas, vous pouvez utiliser la fonctionnalité d’extension de répertoire Azure AD Connect pour synchroniser l’attribut à Azure AD. De cette façon, l’attribut sera visible pour l’API Graph Azure AD et le service d’approvisionnement AD Azure.

Si les données que vous avez besoin pour l’approvisionnement sont dans Active Directory, mais n’est pas disponibles pour l’approvisionnement pour les raisons décrites ci-dessus, procédez comme suit.
 
## <a name="sync-an-attribute"></a>Synchroniser un attribut 

1. Ouvrir l’Assistant Azure AD Connect, choisissez des tâches, puis **personnaliser les options de synchronisation**.

   ![Azure Active Directory Connect de page Assistant Tâches supplémentaires](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Connectez-vous en tant qu’administrateur Global Azure AD. 

3. Sur le **fonctionnalités facultatives** page, sélectionnez **synchronisation attribut extensions d’annuaire**.
 
   ![Azure Active Directory Connect de page Assistant fonctionnalités facultatives](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Sélectionnez les attributs que vous souhaitez étendre à Azure AD.
   > [!NOTE]
   > La recherche sous **attributs disponibles** respecte la casse.

   ![Azure Active Directory Connect répertoire extensions sélection page de l’Assistant](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Terminez l’Assistant Azure AD Connect et autoriser un cycle de synchronisation complète pour s’exécuter. Lorsque le cycle est terminé, le schéma est étendu et les nouvelles valeurs sont synchronisés entre votre réseau local AD et Azure AD.
 
6. Dans le portail Azure, alors que vous êtes [modification des mappages d’attributs utilisateur](customize-application-attributes.md), le **attribut Source** liste contient maintenant l’attribut ajouté au format `<attributename> (extension_<appID>_<attributename>)`. Sélectionnez l’attribut et le mapper à l’application cible pour l’approvisionnement.

   ![Azure Active Directory Connect répertoire extensions sélection page de l’Assistant](media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> Possibilité de configurer les attributs de référence à partir d’AD local, tel que **managedby** ou **DN/DistinguishedName**, n’est pas pris en charge dès aujourd'hui. Vous pouvez demander cette fonctionnalité sur [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="next-steps"></a>Étapes suivantes

* [Définir des noms qui figurent dans l’étendue de l’approvisionnement](define-conditional-rules-for-provisioning-user-accounts.md)
