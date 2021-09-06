---
title: Utiliser le générateur d’expressions avec la synchronisation cloud Azure AD Connect
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
ms.openlocfilehash: 6ef8a039b61a0dd787c65ec66a3acb48a09bc1b2
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113506580"
---
# <a name="expression-builder-with-cloud-sync"></a>Générateur d’expressions avec la synchronisation cloud
Le générateur d’expressions est une nouvelle fonction dans Azure située sous synchronisation cloud. Il vous aide à créer des expressions complexes. Vous pouvez l’utiliser pour tester ces expressions avant de les appliquer à votre environnement de synchronisation cloud.

## <a name="use-the-expression-builder"></a>Utiliser le générateur d’expressions
Pour accéder au générateur d’expressions :

 1. Dans le portail Azure, sélectionnez **Azure Active Directory**.
 1. Sélectionnez ensuite **Azure AD Connect**.
 1. Sélectionnez **Gérer la synchronisation cloud**.
 1. Sous **Configuration**, sélectionnez votre configuration.
 1. Sous **Gérer les attributs**, sélectionnez **Cliquez pour modifier les mappages**.
 1. Dans le panneau **Modifier les mappages d’attributs**, sélectionnez **Ajouter un mappage d’attributs**.
 1. Sous **Type de mappage**, sélectionnez **Expression**.
 1. Sélectionnez **Tester le générateur d’expressions (préversion)** .
 
    ![Capture d’écran montrant l’utilisation du générateur d’expressions.](media/how-to-expression-builder/expression-1.png)

## <a name="build-an-expression"></a>Générer une expression
Dans cette section, vous allez utiliser la liste déroulante pour sélectionner une fonction parmi celles prises en charge. Ensuite, vous allez remplir d’autres zones, selon la fonction sélectionnée. Après avoir sélectionné **Appliquer l’expression**, la syntaxe s’affiche dans la zone d’**entrée de l’expression**.

Par exemple, si vous sélectionnez **Replace** (Remplacer) dans la liste déroulante, d’autres zones s’affichent. La syntaxe de la fonction est présentée dans la zone bleu clair. Les zones affichées correspondent à la syntaxe de la fonction que vous avez sélectionnée. La fonction Replace se comporte différemment selon les paramètres fournis.

Pour cet exemple, quand les valeurs **oldValue** et **replacementValue** sont données, toutes les occurrences de **oldValue** sont remplacées dans la source par **replacementValue**.

Pour plus d’informations, consultez [Replace](reference-expressions.md#replace).

La première chose que vous devez faire est de sélectionner l’attribut qui est la source de la fonction Replace. Dans cet exemple, l’attribut **mail** est sélectionné.

Ensuite, localisez la zone **oldValue** et entrez **@fabrikam.com** . Enfin, dans la zone de **replacementValue**, introduisez la valeur **@contoso.com** .

L’expression remplace l’attribut mail des objets User ayant la valeur @fabrikam.com par la valeur @contoso.com. Lorsque vous sélectionnez **Ajouter l’expression**, vous pouvez voir la syntaxe affichée dans la zone d’**entrée de l’expression**.

>[!NOTE]
>Veillez à indiquer les valeurs pour **oldValue** et **replacementValue** dans les zones qui correspondent à la syntaxe affichée quand vous avez sélectionné la fonction **Replace**.

Pour plus d’informations sur les expressions prises en charge, consultez [Écriture d’expressions pour les mappages d’attributs dans Azure Active Directory](reference-expressions.md).

### <a name="information-on-expression-builder-input-boxes"></a>Informations sur les zones d’entrée du générateur d’expressions
Selon la fonction que vous avez sélectionnée, les zones présentées par le générateur d’expressions acceptent des valeurs différentes. Par exemple, la fonction JOIN accepte des chaînes ou la valeur qui est associée à un attribut donné. Par exemple, nous pouvons utiliser la valeur contenue dans la valeur d’attribut de **[givenName]** et la joindre à la valeur de chaîne **@contoso.com** pour créer une adresse e-mail.

  ![Capture d’écran montrant les valeurs de zone d’entrée.](media/how-to-expression-builder/expression-8.png)

Pour plus d’informations sur les valeurs possibles et l’écriture d’expressions, consultez [Écriture d’expressions pour les mappages d’attributs dans Azure Active Directory](reference-expressions.md).

## <a name="test-an-expression"></a>Tester une expression
Dans cette section, vous pouvez tester vos expressions. Dans la liste déroulante, sélectionnez l’attribut **mail**. Spécifiez **@fabrikam.com** comme valeur et sélectionnez **Tester l’expression**.

La valeur **@contoso.com** apparaît dans la zone **Afficher les résultats de l’expression**.

 ![Capture d’écran montrant le test de votre expression.](media/how-to-expression-builder/expression-4.png)

## <a name="deploy-the-expression"></a>Déployer l’expression
Une fois que l’expression vous convient, sélectionnez **Appliquer l’expression**.

![Capture d’écran montrant l’ajout de votre expression.](media/how-to-expression-builder/expression-5.png)

Cette action ajoute l’expression à la configuration de l’agent.

![Capture d’écran illustrant la configuration de l’agent.](media/how-to-expression-builder/expression-6.png)

## <a name="set-a-null-value-on-an-expression"></a>Définir une valeur NULL sur une expression
Pour définir la valeur d’un attribut sur NULL, utilisez une expression comprenant la valeur `""`. Ainsi, l’expression passe la valeur NULL à l’attribut cible (target).

![Capture d’écran montrant une valeur NULL.](media/how-to-expression-builder/expression-7.png)


## <a name="next-steps"></a>Étapes suivantes 

- [Écriture d’expressions pour les mappages d’attributs dans Azure Active Directory](reference-expressions.md)
- [Configuration de la synchronisation cloud](how-to-configure.md)
