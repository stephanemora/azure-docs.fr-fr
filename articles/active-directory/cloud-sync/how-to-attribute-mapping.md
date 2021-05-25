---
title: Mappage d’attributs dans la synchronisation cloud Azure AD Connect
description: Cet article explique comment utiliser la fonctionnalité de synchronisation cloud d’Azure AD Connect pour mapper des attributs.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/30/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d097260dc7e84804df30895babd5977a191e293d
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108737936"
---
# <a name="attribute-mapping-in-azure-ad-connect-cloud-sync"></a>Mappage d’attributs dans la synchronisation cloud Azure AD Connect

Vous pouvez utiliser la fonctionnalité de synchronisation cloud d’Azure Active Directory (Azure AD) Connect pour mapper les attributs de vos objets utilisateur ou groupe locaux sur deux des objets Azure AD. Cette fonctionnalité a été ajoutée à la configuration de la synchronisation cloud.

Vous pouvez personnaliser (modifier, supprimer ou créer) les mappages d’attributs par défaut en fonction de vos besoins métier. Pour obtenir la liste des attributs qui sont synchronisés, consultez [Attributs synchronisés avec Azure Active Directory](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md).

> [!NOTE]
> Cet article explique comment utiliser le portail Azure pour mapper des attributs.  Pour plus d’informations sur l’utilisation de Microsoft Graph, consultez [Transformations](how-to-transformation.md).

## <a name="understand-types-of-attribute-mapping"></a>Comprendre les types de mappage d’attributs
Avec le mappage des attributs, vous pouvez contrôler la façon dont les attributs sont renseignés dans Azure AD. Azure AD prend en charge quatre types de mappage :

|Type de mappage|Description|
|-----|-----|
|**Direct**|L’attribut cible est renseigné avec la valeur d’un attribut de l’objet lié dans Active Directory.|
|**Constant**|L’attribut cible est renseigné avec une chaîne que vous spécifiez.|
|**Expression**|L’attribut cible est renseigné en fonction du résultat d’une expression de type script. Pour plus d’informations, consultez [Générateur d’expressions](how-to-expression-builder.md) et [Écriture d’expressions pour les mappages d’attributs dans Azure Active Directory](reference-expressions.md).|
|**Aucun**|L’attribut cible reste inchangé. Toutefois, si l’attribut cible est vide, il est renseigné avec la valeur par défaut que vous spécifiez.|

En plus de ces types de base, les mappages d’attributs personnalisés prennent en charge l’affectation de valeur *par défaut* facultative. L’affectation d’une valeur par défaut garantit qu’un attribut cible sera renseigné avec une valeur si Azure AD ou l’objet cible n’ont pas encore de valeur. La configuration la plus courante consiste à laisser ce champ vide.

## <a name="schema-updates-and-mappings"></a>Mises à jour du schéma et mappages
La synchronisation cloud met occasionnellement à jour le schéma et la liste des attributs par défaut qui sont [synchronisés](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-attributes-synchronized?context=/azure/active-directory/cloud-provisioning/context/cp-context).  Ces mappages d’attributs par défaut sont disponibles pour les nouvelles installations, mais ne sont pas automatiquement ajoutés aux installations existantes.  Pour ajouter ces mappages, vous pouvez suivre les étapes ci-dessous.


  1. Cliquez sur « Ajouter un mappage d’attributs ».
  2. Sélectionnez la liste déroulante Attribut cible
  3. Vous devez voir les nouveaux attributs sont disponibles ici.

La liste suivante répertorie les nouveaux mappages qui ont été ajoutés.

Attribut ajouté | Type de mappage | Ajouté avec la version de l’agent
| ----- | -----| -----|
|preferredDatalocation|Direct|1.1.359.0|
|EmployeeNumber|Direct|1.1.359.0|
|UserType|Direct|1.1.359.0|

Pour plus d’informations sur la façon de mapper UserType, consultez [Mapper UserType avec la synchronisation cloud](how-to-map-usertype.md).

## <a name="understand-properties-of-attribute-mappings"></a>Comprendre les propriétés des mappages d’attributs

Outre cette propriété de type, les mappages d’attributs prennent en charge certains attributs.  Ces attributs dépendent du type de mappage que vous avez sélectionné.  Les sections suivantes décrivent les mappages d’attributs pris en charge pour chacun des types individuels

### <a name="direct-mapping-attributes"></a>Attributs de mappage direct
Les attributs pris en charge par un mappage direct sont les suivants :

- **Attribut source** : attribut utilisateur du système source (exemple, Active Directory).
- **Attribut cible** : attribut utilisateur dans le système cible (exemple : Azure Active Directory).
- **Valeur par défaut si Null (facultatif)**  : la valeur qui sera passée au système cible si l’attribut source est Null. Cette valeur est provisionnée uniquement lors de la création d’un utilisateur. Elle n’est pas provisionnée lorsque vous mettez à jour un utilisateur existant.  
- **Appliquer ce mappage** :
  - **Toujours** : appliquez ce mappage lors de la création de l’utilisateur et lors de sa mise à jour.
  - **Lors de la création uniquement** : appliquez ce mappage uniquement aux actions de création d’utilisateur.

 ![Capture d’écran de mappage direct](media/how-to-attribute-mapping/mapping-7.png)

### <a name="constant-mapping-attributes"></a>Attributs de mappage de constante
Les attributs pris en charge par un mappage de constante sont les suivants :

- **Valeur de constante** : la valeur que vous souhaitez appliquer à l’attribut cible.
- **Attribut cible** : attribut utilisateur dans le système cible (exemple : Azure Active Directory).
- **Appliquer ce mappage** :
  - **Toujours** : appliquez ce mappage lors de la création de l’utilisateur et lors de sa mise à jour.
  - **Lors de la création uniquement** : appliquez ce mappage uniquement aux actions de création d’utilisateur.

 ![Capture d’écran de la constante](media/how-to-attribute-mapping/mapping-9.png)

### <a name="expression-mapping-attributes"></a>Attributs de mappage d’expression
Voici les attributs pris en charge par un mappage d’expression :

- **Expression** : il s’agit de l’expression qui va être appliquée à l’attribut cible.  Pour plus d’informations, consultez [Générateur d’expressions](how-to-expression-builder.md) et [Écriture d’expressions pour les mappages d’attributs dans Azure Active Directory](reference-expressions.md).
-  **Valeur par défaut si Null (facultatif)**  : la valeur qui sera passée au système cible si l’attribut source est Null. Cette valeur est provisionnée uniquement lors de la création d’un utilisateur. Elle n’est pas provisionnée lorsque vous mettez à jour un utilisateur existant. 
- **Attribut cible** : attribut utilisateur dans le système cible (exemple : Azure Active Directory).
 
- **Appliquer ce mappage** :
  - **Toujours** : appliquez ce mappage lors de la création de l’utilisateur et lors de sa mise à jour.
  - **Lors de la création uniquement** : appliquez ce mappage uniquement aux actions de création d’utilisateur.

 ![Capture d’écran de l’expression](media/how-to-attribute-mapping/mapping-10.png)

## <a name="add-an-attribute-mapping"></a>Ajouter un mappage d’attributs

Pour utiliser les nouvelles fonctionnalités, effectuez les étapes suivantes :

1.  Dans le portail Azure, sélectionnez **Azure Active Directory**.
2.  Sélectionnez ensuite **Azure AD Connect**.
3.  Sélectionnez **Gérer la synchronisation cloud**.

    ![Capture d’écran montrant le lien permettant de gérer la synchronisation cloud.](media/how-to-install/install-6.png)

4. Sous **Configuration**, sélectionnez votre configuration.
5. Sélectionnez **Cliquez pour modifier les mappages**.  Ce lien permet d’ouvrir la page **Mappages d’attributs**.

    ![Capture d’écran montrant le lien permettant d’ajouter des attributs.](media/how-to-attribute-mapping/mapping-6.png)

6.  Sélectionnez **Ajouter un attribut**.

    ![Capture d’écran montrant le bouton permettant d’ajouter un attribut, ainsi que des listes d’attributs et des types de mappages.](media/how-to-attribute-mapping/mapping-1.png)

7. Sélectionnez le type de mappage. Il peut s'agir de l'une des ressources suivantes :
     - **Direct** : l’attribut cible est renseigné avec la valeur d’un attribut de l’objet lié dans Active Directory.
     - **Constant** : l’attribut cible est renseigné avec une chaîne que vous spécifiez.
     - **Expression** : l’attribut cible est renseigné en fonction du résultat d’une expression de type script. 
     - **Aucun** : l’attribut cible reste inchangé. 
 
     Pour plus d’informations, consultez [Comprendre les types d’attributs](#understand-types-of-attribute-mapping) ci-dessus.
8. Selon ce que vous avez sélectionné à l’étape précédente, différentes options sont disponibles.  Pour plus d’informations sur ces attributs, consultez la section [Comprendre les propriétés des mappages d’attributs](#understand-properties-of-attribute-mappings) ci-dessus.
9. Sélectionnez à quel moment vous souhaitez appliquer ce mappage, puis sélectionnez **Appliquer**.
11. Quand vous retournez dans l’écran **Mappages d’attributs**, vous devez voir votre nouveau mappage d’attributs.  
12. Sélectionnez **Enregistrer le schéma**.

    ![Capture d’écran montrant le bouton Enregistrer le schéma.](media/how-to-attribute-mapping/mapping-3.png)

## <a name="test-your-attribute-mapping"></a>Tester votre mappage d’attributs

Pour tester votre mappage d’attributs, vous pouvez utiliser le [provisionnement à la demande](how-to-on-demand-provision.md) : 

1. Dans le portail Azure, sélectionnez **Azure Active Directory**.
2. Sélectionnez ensuite **Azure AD Connect**.
3. Sélectionnez **Gérer le provisionnement**.
4. Sous **Configuration**, sélectionnez votre configuration.
5. Sous **Valider**, sélectionnez le bouton **Provisionner un utilisateur**. 
6. Dans l’écran **Provisionner à la demande**, entrez le nom unique d’un utilisateur ou d’un groupe, puis sélectionnez le bouton **Provisionner**. 

   L’écran indique que le provisionnement est en cours.

   ![Capture d’écran montrant que le provisionnement est en cours.](media/how-to-attribute-mapping/mapping-4.png)

8. Une fois le provisionnement terminé, un écran de confirmation s’affiche avec quatre coches vertes. 

   Sous **Exécuter l’action**, sélectionnez **Afficher les détails**. À droite, vous devriez voir le nouvel attribut synchronisé et l’expression appliquée.

   ![Capture d’écran indiquant la réussite de l’opération et les informations d’exportation.](media/how-to-attribute-mapping/mapping-5.png)






## <a name="next-steps"></a>Étapes suivantes

- [Qu’est-ce que la synchronisation cloud Azure AD Connect ?](what-is-cloud-sync.md)
- [Écriture d’expressions pour les mappages d’attributs](reference-expressions.md)
- [Comment utiliser le générateur d’expressions avec la synchronisation cloud](how-to-expression-builder.md)
- [Attributs synchronisés avec Azure Active Directory](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)
