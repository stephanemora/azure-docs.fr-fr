---
title: Procédure de personnalisation d’une règle de synchronisation dans Azure AD Connect | Microsoft Docs
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
ms.topic: how-to
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75300824d94ecbd77f426539ce99da6caaa2e27a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85359991"
---
# <a name="how-to-customize-a-synchronization-rule"></a>Procédure de personnalisation d’une règle de synchronisation

## <a name="recommended-steps"></a>**Étapes recommandées**

Vous pouvez utiliser l’éditeur des règles de synchronisation pour modifier ou créer une nouvelle règle de synchronisation. Vous devez être un utilisateur avancé pour apporter des modifications aux règles de synchronisation. Toute modification incorrecte peut entraîner la suppression d’objets dans votre répertoire cible. Veuillez lire les [Documents recommandés](#recommended-documents) pour devenir expert dans les règles de synchronisation. Pour modifier une règle de synchronisation, procédez comme suit :

* Exécutez l’éditeur de synchronisation à partir d’un menu d’application dans le poste de travail comme indiqué ci-après :

    ![Menu de l’éditeur de règles de synchronisation](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* Pour personnaliser une règle de synchronisation par défaut, clonez la règle existante en cliquant sur le bouton « Modifier» dans l’éditeur des règles de synchronisation, ce qui crée une copie de la règle par défaut standard et la désactive. Enregistrez la règle clonée avec une priorité inférieure à 100.  La priorité détermine quelle règle l’emporte (valeur numérique inférieure) dans le cadre d’une résolution de conflit de flux d’attributs.

    ![Éditeur de règles de synchronisation](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* Lorsque vous modifiez un attribut spécifique, l’idéal serait de ne conserver que l’attribut modificateur dans la règle clonée.  Activez ensuite la règle par défaut pour que l’attribut modifié émane de la règle clonée et que les autres attributs soient choisis dans la règle standard par défaut. 

* Veuillez noter que dans le cas où la valeur calculée de l’attribut modifié est NULL dans votre règle clonée et n’est pas NULL dans la règle standard par défaut, la valeur non NULL l’emporte et remplace la valeur NULL. Si vous ne voulez pas qu’une valeur NULL soit remplacée par une valeur non NULL, assignez AuthoritativeNull dans votre règle clonée.

* Pour modifier une **règle de trafic sortant**, modifiez le filtre correspondant dans l’éditeur de règles de synchronisation.

## <a name="recommended-documents"></a>**Documents recommandés**
* [Synchronisation Azure AD Connect : concepts techniques](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Synchronisation Azure AD Connect : comprendre l’architecture](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Synchronisation Azure AD Connect : présentation du provisionnement déclaratif](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Synchronisation Azure AD Connect : comprendre les expressions de provisionnement déclaratif](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Synchronisation Azure AD Connect : comprendre la configuration par défaut](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Synchronisation Azure AD Connect : Comprendre les utilisateurs, les groupes et les contacts](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Synchronisation Azure AD Connect : attributs de clichés instantanés](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Étapes suivantes
- [Synchronisation Azure AD Connect](how-to-connect-sync-whatis.md).
- [Qu’est-ce que l’identité hybride ?](whatis-hybrid-identity.md).
