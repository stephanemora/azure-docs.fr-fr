---
title: Utiliser le mappage de UserType avec la synchronisation cloud Azure AD Connect
description: Cet article explique comment mapper l’attribut UserType avec la synchronisation cloud.
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
ms.openlocfilehash: 7b06381472549fd7d9b3f3b5dcd222fcd96f4f15
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113506202"
---
# <a name="map-usertype-with-cloud-sync"></a>Mapper UserType avec la synchronisation cloud

La synchronisation cloud prend en charge la synchronisation de l’attribut **UserType** pour les objets utilisateur.

Par défaut, l’attribut **UserType** n’est pas activé pour la synchronisation, car il n’existe aucun attribut **UserType** correspondant dans l’Active Directory local. Vous devez ajouter manuellement ce mappage pour la synchronisation. Avant d’accomplir cette étape, vous devez prendre note du comportement suivant appliqué par Azure Active Directory (Azure AD) :

- Azure AD n’accepte que deux valeurs pour l’attribut **UserType** : Membre et Invité.
- Si l’attribut **UserType** n’est pas mappé dans la synchronisation cloud, il est défini sur **Membre** pour les utilisateurs Azure AD créés via la synchronisation d’annuaires.

Avant d’ajouter un mappage à l’attribut **UserType**, vous devez déterminer comment il sera dérivé d’Active Directory local. Les approches suivantes sont les plus courantes :

 - Vous pouvez désigner un attribut Active Directory local inutilisé, comme extensionAttribute1, à utiliser en tant qu’attribut source. Il doit être du type chaîne, avoir une seule valeur et contenir la valeur Membre ou Invité.
 - Si vous choisissez cette approche, vous devez vous assurer que l’attribut désigné est rempli avec la bonne valeur pour tous les objets utilisateur existants dans Active Directory local qui sont synchronisés avec Azure AD avant d’activer la synchronisation de l’attribut **UserType**.

## <a name="add-the-usertype-mapping"></a>Ajouter le mappage de UserType
Pour ajouter le mappage de **UserType** :

 1. Dans le portail Azure, sélectionnez **Azure Active Directory**.
 1. Sélectionnez ensuite **Azure AD Connect**.
 1. Sélectionnez **Gérer la synchronisation cloud**.
 1. Sous **Configuration**, sélectionnez votre configuration.
 1. Sous **Gérer les attributs**, sélectionnez **Cliquez pour modifier les mappages**.
 
    ![Capture d’écran montrant la modification des mappages d’attributs.](media/how-to-map-usertype/usertype-1.png) 

 1. Sélectionnez **Ajouter un mappage d’attributs**.
 
    ![Capture d’écran montrant l’ajout d’un nouveau mappage d’attributs.](media/how-to-map-usertype/usertype-2.png) 
1. Sélectionnez le type de mappage. Vous pouvez effectuer le mappage de l’une des trois façons suivantes :
   - Mappage direct, par exemple, à partir d’un attribut Active Directory
   - Expression, comme IIF(InStr([userPrincipalName], "@partners") > 0,"Guest","Member")
   - Constante, par exemple, attribuer Invité à tous les objets utilisateur
 
     ![Capture d’écran montrant l’ajout d’un attribut UserType.](media/how-to-map-usertype/usertype-3.png)

1. Dans la liste déroulante **Attribut cible**, sélectionnez **UserType**.
1. Sélectionnez **Appliquer** en bas de la page pour créer le mappage de l’attribut Azure AD **UserType**.

## <a name="next-steps"></a>Étapes suivantes 

- [Écriture d’expressions pour les mappages d’attributs dans Azure Active Directory](reference-expressions.md)
- [Configuration de la synchronisation cloud](how-to-configure.md)
