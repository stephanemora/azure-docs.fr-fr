---
title: Comment utiliser le mappage d’UserType avec la synchronisation cloud Azure AD Connect
description: Cet article explique comment utiliser le mappage d’attribut UserType avec la synchronisation cloud.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/04/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 14bbeb7339b3af41b24b25aed2332ac8367e99ae
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108776437"
---
# <a name="map-usertype-with-cloud-sync"></a>Mapper UserType avec la synchronisation cloud

La synchronisation cloud prend en charge la synchronisation de l’attribut UserType pour les objets utilisateur. 

Par défaut, l’attribut UserType n’est pas activé pour la synchronisation, car il n’existe aucun attribut UserType correspondant dans l’Active Directory local. Vous devez ajouter manuellement ce mappage pour la synchronisation. Auparavant, prenez connaissance de ce comportement, appliqué par Azure AD :

- Azure AD accepte seulement deux valeurs pour l’attribut UserType : Membre et Invité.
- Si l’attribut UserType n’est pas mappé dans la synchronisation cloud, il est défini sur Membre pour les utilisateurs Azure AD créés via la synchronisation d’annuaires.

Avant d’ajouter un mappage à l’attribut UserType, vous devez déterminer comment il sera dérivé d’Active Directory en local. Voici les approches les plus courantes :

 - Vous pouvez désigner un attribut AD local inutilisé (par exemple, extensionAttribute1) à utiliser en tant qu’attribut source. Il doit être de type chaîne, avoir une seule valeur et contenir la valeur Membre ou Invité.
 - Si vous choisissez cette approche, vous devez vous assurer que l’attribut désigné est rempli avec la valeur correcte pour tous les objets utilisateur existants dans Active Directory local qui sont synchronisés avec Azure AD avant d’activer la synchronisation de l’attribut UserType.

## <a name="to-add-the-usertype-mapping"></a>Pour ajouter le mappage d’UserType
Pour ajouter le mappage d’UserType, procédez comme suit.

 1. Dans le portail Azure, sélectionnez **Azure Active Directory**.
 2. Sélectionnez ensuite **Azure AD Connect**.
 3. Sélectionnez **Gérer la synchronisation cloud**.
 4. Sous **Configuration**, sélectionnez votre configuration.
 5. Sous **Gérer les attributs**, sélectionnez **Cliquez pour modifier les mappages**.
  ![Modifier les mappages d’attributs](media/how-to-map-usertype/usertype-1.png) 

 6. Cliquez sur **Ajouter un mappage d’attributs**.
    ![Ajouter un mappage d’attribut](media/how-to-map-usertype/usertype-2.png) 
7. Sélectionnez le type de mappage. Vous pouvez effectuer le mappage de l’une des trois façons suivantes :
 - mappage direct (c’est-à-dire à partir d’un attribut AD)
 - an expression (IIF(InStr([userPrincipalName], "@partners") > 0,"Guest","Member"))
 - constante (autrement dit, créer tous les objets utilisateur en tant qu’Invité).
  ![Ajouter UserType](media/how-to-map-usertype/usertype-3.png) 
8. Dans la liste déroulante Attribut cible, sélectionnez UserType.
9. Cliquez sur le bouton **Appliquer** au bas de la page. Cela créera un mappage pour l’attribut Azure AD UserType.

## <a name="next-steps"></a>Étapes suivantes 

- [Écriture d’expressions pour les mappages d’attributs dans Azure Active Directory](reference-expressions.md)
- [Configuration de la synchronisation cloud](how-to-configure.md)
