---
title: Mappage d’attributs dans la synchronisation cloud Azure AD Connect
description: Cet article explique comment utiliser la fonctionnalité de synchronisation cloud d’Azure AD Connect pour mapper des attributs.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdb043374cf6252da3929c8f0cda6c0a4be558b7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555208"
---
# <a name="attribute-mapping-in-azure-ad-connect-cloud-sync"></a>Mappage d’attributs dans la synchronisation cloud Azure AD Connect

Vous pouvez utiliser la fonctionnalité de synchronisation cloud d’Azure Active Directory (Azure AD) Connect pour mapper les attributs de vos objets utilisateur ou groupe locaux sur deux des objets Azure AD. Cette fonctionnalité a été ajoutée à la configuration de la synchronisation cloud.

Vous pouvez personnaliser (modifier, supprimer ou créer) les mappages d’attributs par défaut en fonction de vos besoins métier. Pour obtenir la liste des attributs qui sont synchronisés, consultez [Attributs synchronisés avec Azure Active Directory](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md).

## <a name="understand-types-of-attribute-mapping"></a>Comprendre les types de mappage d’attributs
Avec le mappage des attributs, vous pouvez contrôler la façon dont les attributs sont renseignés dans Azure AD. Azure AD prend en charge quatre types de mappage :

- **Direct** : l’attribut cible est renseigné avec la valeur d’un attribut de l’objet lié dans Active Directory.
- **Constant** : l’attribut cible est renseigné avec une chaîne que vous spécifiez.
- **Expression** : l’attribut cible est renseigné en fonction du résultat d’une expression de type script. Pour plus d’informations, consultez [Écriture d’expressions pour les mappages d’attributs dans Azure Active Directory](reference-expressions.md).
- **Aucun** : l’attribut cible reste inchangé. Toutefois, si l’attribut cible est vide, il est renseigné avec la valeur par défaut que vous spécifiez.

En plus de ces types de base, les mappages d’attributs personnalisés prennent en charge l’affectation de valeur *par défaut* facultative. L’affectation d’une valeur par défaut garantit qu’un attribut cible sera renseigné avec une valeur si Azure AD ou l’objet cible n’ont pas encore de valeur. La configuration la plus courante consiste à laisser ce champ vide.

## <a name="understand-properties-of-attribute-mapping"></a>Comprendre les propriétés du mappage d’attributs

Outre cette propriété de type, les mappages d’attributs prennent en charge les attributs suivants :

- **Attribut source** : attribut utilisateur du système source (exemple, Active Directory).
- **Attribut cible** : attribut utilisateur dans le système cible (exemple : Azure Active Directory).
- **Valeur par défaut si Null (facultatif)**  : la valeur qui sera passée au système cible si l’attribut source est Null. Cette valeur est provisionnée uniquement lors de la création d’un utilisateur. Elle n’est pas provisionnée lorsque vous mettez à jour un utilisateur existant.  
- **Appliquer ce mappage** :
  - **Toujours** : appliquez ce mappage lors de la création de l’utilisateur et lors de sa mise à jour.
  - **Lors de la création uniquement** : appliquez ce mappage uniquement aux actions de création d’utilisateur.

> [!NOTE]
> Cet article explique comment utiliser le portail Azure pour mapper des attributs.  Pour plus d’informations sur l’utilisation de Microsoft Graph, consultez [Transformations](how-to-transformation.md).

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

7. Sélectionnez le type de mappage. Pour cet exemple, nous utilisons **Expression**.
8. Entrez l’expression dans la zone. Pour cet exemple, nous utilisons `Replace([mail], "@contoso.com", , ,"", ,)`.
9. Entrez l’attribut cible. Pour cet exemple, nous utilisons **ExtensionAttribute15**.
10. Sélectionnez à quel moment vous souhaitez appliquer ce mappage, puis sélectionnez **Appliquer**.

    ![Capture d’écran montrant les champs renseignés pour la création d’un mappage d’attributs.](media/how-to-attribute-mapping/mapping-2a.png)

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
- [Attributs synchronisés avec Azure Active Directory](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)
