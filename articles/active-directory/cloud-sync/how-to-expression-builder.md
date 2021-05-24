---
title: Guide pratique pour utiliser le générateur d’expressions avec la synchronisation cloud Azure AD Connect
description: Cet article explique comment utiliser le générateur d’expressions avec la synchronisation cloud.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e169c2cbabf9e9e37bb93b1e3ee26080105d501e
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108776446"
---
# <a name="expression-builder-with-cloud-sync"></a>Générateur d’expressions avec la synchronisation cloud
Le générateur d’expressions est un nouveau panneau dans Azure situé sous Synchronisation cloud. Il facilite la création des expressions complexes et vous permet de tester ces expressions avant de les appliquer dans votre environnement de synchronisation cloud.

## <a name="use-the-expression-builder"></a>Utiliser le générateur d’expressions
Pour accéder au générateur d’expressions, effectuez les étapes suivantes.

 1. Dans le portail Azure, sélectionnez **Azure Active Directory**.
 2. Sélectionnez ensuite **Azure AD Connect**.
 3. Sélectionnez **Gérer la synchronisation cloud**.
 4. Sous **Configuration**, sélectionnez votre configuration.
 5. Sous **Gérer les attributs**, sélectionnez **Cliquez pour modifier les mappages**.
 6. Dans le panneau **Modifier les mappages d’attributs**, cliquez sur **Ajouter un mappage d’attributs**.
 7. Sous **Type de mappage**, sélectionnez **Expression**.
 8. Sélectionnez **Tester le générateur d’expressions (préversion)** .
 ![Utiliser le générateur d’expressions](media/how-to-expression-builder/expression-1.png)

## <a name="build-an-expression"></a>Générer une expression
Cette section vous permet d’utiliser la liste déroulante pour sélectionner une fonction dans une liste de fonctions prises en charge.  Elle présente ensuite différents champs supplémentaires à remplir en fonction de la fonction sélectionnée.  Une fois que vous avez sélectionné **Appliquer l’expression**, la syntaxe s’affiche dans la zone d’**entrée de l’expression**.

Par exemple, si vous sélectionnez **Replace** (Remplacer) dans la liste déroulante, des zones supplémentaires s’affichent.  La syntaxe de la fonction est présentée dans la zone bleu clair.  Les zones affichées correspondent à la syntaxe de la fonction que vous avez sélectionnée.  La fonction Replace se comporte différemment selon les paramètres fournis.  Pour notre exemple, nous utiliserons :

- Lorsque les valeurs oldValue et replacementValue sont fournies :
    - Remplace toutes les occurrences d’oldValue dans la source par  replacementValue.

Pour plus d’informations, consultez [Replace](reference-expressions.md#replace)

La première chose que nous devons faire est de sélectionner l’attribut qui est la source de la fonction Replace. Dans notre exemple, nous avons sélectionné l’attribut **mail**. 

Ensuite, nous spécifions la valeur de l’objet oldValue.  Ici, oldValue a la valeur **@fabrikam.com** .  Enfin, dans la zone replacementValue, nous spécifions la valeur **@contoso.com** .

Ainsi, notre expression remplace l’attribut mail des objets User ayant la valeur @fabrikam.com par la valeur @contoso.com.  Si nous cliquons sur le bouton **Ajouter l’expression**, nous voyons la syntaxe affichée dans la zone d’**entrée de l’expression**.


>[!NOTE]
>Veillez à indiquer les valeurs pour oldValue et replacementValue dans les zones qui correspondent à la syntaxe affichée quand vous avez sélectionné la fonction Replace.

Pour plus d’informations sur les expressions prises en charge, consultez [Écriture d’expressions pour les mappages d’attributs dans Azure Active Directory](reference-expressions.md).

### <a name="information-on-expression-builder-input-boxes"></a>Informations sur les zones d’entrée du générateur d’expressions
Selon la fonction que vous avez sélectionnée, les zones présentées par le générateur d’expressions acceptent des valeurs différentes.  Par exemple, la fonction JOIN accepte des chaînes ou la valeur qui est associée à un attribut donné.  Par exemple, nous pouvons utiliser la valeur contenue dans la valeur d’attribut de [givenName] et la joindre à la valeur de chaîne « @contoso.com » pour créer une adresse e-mail.

  ![Valeurs de zone d’entrée](media/how-to-expression-builder/expression-8.png)

Pour plus d’informations sur les valeurs possibles et l’écriture d’expressions, consultez [Écriture d’expressions pour les mappages d’attributs dans Azure Active Directory](reference-expressions.md).

## <a name="test-an-expression"></a>Tester une expression
Dans cette section, vous pouvez tester vos expressions.  Dans la liste déroulante, sélectionnez l’attribut **mail**.  Spécifiez **@fabrikam.com** comme valeur et cliquez ensuite sur **Tester l’expression**.  

Vous voyez alors la valeur **@contoso.com** affichée dans la zone de **sortie de l’expression**.

 ![Tester votre expression](media/how-to-expression-builder/expression-4.png)

## <a name="deploy-the-expression"></a>Déployer l’expression
Une fois que vous avez terminé l’expression, cliquez simplement sur le bouton **Appliquer l’expression**.
![Ajouter votre expression](media/how-to-expression-builder/expression-5.png)

Cette opération ajoute l’expression à la configuration de l’agent.
![Configuration de l’agent](media/how-to-expression-builder/expression-6.png)

## <a name="setting-a-null-value-on-an-expression"></a>Définition d’une valeur NULL sur une expression
Pour définir une valeur d’attribut à la valeur NULL.  Vous pouvez utiliser une expression avec la valeur `""`.  Ainsi, la valeur NULL sera passée à l’attribut cible (target).

![Valeur NULL](media/how-to-expression-builder/expression-7.png)



## <a name="next-steps"></a>Étapes suivantes 

- [Écriture d’expressions pour les mappages d’attributs dans Azure Active Directory](reference-expressions.md)
- [Configuration de la synchronisation cloud](how-to-configure.md)
