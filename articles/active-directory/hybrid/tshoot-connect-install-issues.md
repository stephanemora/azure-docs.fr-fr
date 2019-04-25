---
title: Résoudre les problèmes d’installation d’Azure AD Connect | Microsoft Docs
description: Cette rubrique explique comment résoudre les problèmes d’installation d’Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e077127681f8bd7b650ab22f2d036efd7f9733ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60454789"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Résolution de problèmes : Problèmes d’installation Azure AD Connect

## <a name="recommended-steps"></a>**Étapes recommandées**
Vérifiez quel [type d’installation d’Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) vous convient. Si vous respectez les critères d’installation rapide, nous vous recommandons vivement d’opter pour une telle installation. L’installation expresse vous offre le minimum d’options nécessaires pour accomplir l’installation. Le risque de rencontrer des problèmes est donc réduit. 

Toutefois, si vous ne réunissez pas les critères d’installation rapide et devez effectuer une installation personnalisée, voici quelques-unes des meilleures pratiques à suivre pour éviter les problèmes courants. Par souci de simplicité, seules les options sélectives sont mentionnées ici :

* Vérifiez que vous êtes un administrateur de la machine sur laquelle vous installez AAD Connect. Connectez-vous à la machine avec les mêmes informations d’identification d’administrateur.

* Conservez les valeurs par défaut pour toutes les options sur la page suivante, à l’exception de l’option « Utiliser un SQL Server existant » si vous souhaitez effectivement utiliser un SQL Server existant. Pour plus de détails sur l’utilisation des options d’installation personnalisée, voir [ici](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom). 

    ![Utiliser un SQL Server existant](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* Dans la page suivante, choisissez l’option « Créer un compte AD » afin d’éviter tout problème d’autorisation avec un compte existant.

    ![Compte de forêt AD](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Problèmes courants**

* [Problèmes de connectivité avec Active Directory local](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools).

* [Problèmes de connectivité avec Azure Active Directory en ligne](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity).

* [Problèmes d’autorisation avec Active Directory local](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account).

## <a name="recommended-documents"></a>**Documents recommandés**
* [Conditions préalables pour Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [Sélectionner le type d’installation à utiliser pour Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [Prise en main d’Azure AD Connect à l’aide de paramètres express](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Installation personnalisée d’Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect : Effectuer une mise à niveau vers la dernière version](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version).
* [Azure AD Connect : qu’est-ce qu’un serveur intermédiaire ?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [Présentation du module PowerShell ADConnectivityTool](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>Étapes suivantes
- [Synchronisation Azure AD Connect](how-to-connect-sync-whatis.md).
- [Qu’est-ce que l’identité hybride ?](whatis-hybrid-identity.md).





