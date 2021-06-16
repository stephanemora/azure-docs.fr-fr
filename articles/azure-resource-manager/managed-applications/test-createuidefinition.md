---
title: Tester le fichier de définition de l’interface utilisateur
description: Décrit comment tester l’expérience utilisateur de création de votre application gérée Azure via le portail.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/04/2021
ms.author: tomfitz
ms.openlocfilehash: 55a966c17f3bcd51f354198e36e03071efd4834d
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111951479"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Tester votre interface de portail pour Applications managées Azure

Après la [création du fichier createUiDefinition.json](create-uidefinition-overview.md) pour votre application managée, vous devez tester l’expérience utilisateur. Pour simplifier le test, utilisez un environnement de bac à sable qui charge votre fichier dans le portail. Vous n’avez pas besoin de déployer votre application managée. Le bac à sable présente votre interface utilisateur dans l’expérience portail actuelle et en plein écran. Le bac à sable constitue la méthode recommandée pour afficher un aperçu de l’interface.

## <a name="prerequisites"></a>Prérequis

* Un fichier **createUiDefinition.json**. Si vous n’avez pas ce fichier, copiez l’[exemple de fichier](https://github.com/Azure/azure-quickstart-templates/blob/master/demos/100-marketplace-sample/createUiDefinition.json).

* Un abonnement Azure. Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="use-sandbox"></a>Utiliser le bac à sable

1. Ouvrez [Créer un bac à sable (sandbox) de définition d’interface utilisateur](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade).

   ![Afficher le bac à sable](./media/test-createuidefinition/show-sandbox.png)

1. Remplacez la définition vide par le contenu de votre fichier createUiDefinition.json. Sélectionnez **Aperçu**.

   ![Sélectionner l’aperçu](./media/test-createuidefinition/select-preview.png)

1. Le formulaire que vous avez créé s’affiche. Vous pouvez parcourir l’expérience utilisateur et renseigner les valeurs.

   ![Afficher le formulaire](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Dépannage

Si votre formulaire ne s’affiche après la sélection de l’option **Aperçu**, cela signifie peut-être que vous avez fait une erreur de syntaxe. Recherchez l’indicateur rouge dans la barre de défilement sur la droite et accédez à ce dernier.

![Afficher une erreur de syntaxe](./media/test-createuidefinition/show-syntax-error.png)

Si votre formulaire ne s’affiche pas, et si à la place vous voyez une icône représentant un nuage avec une larme, cela signifie que votre formulaire comporte une erreur, comme une propriété manquante. Ouvrez Web Developer Tools dans votre navigateur. La **Console** affiche des messages importants sur votre interface.

![Afficher l’erreur](./media/test-createuidefinition/show-error.png)

## <a name="test-your-solution-files"></a>Tester vos fichiers de solution

Maintenant que vous avez vérifié que votre interface de portail fonctionne comme prévu, il est temps de vérifier que votre fichier createUiDefinition est correctement intégré à votre fichier mainTemplate.json. Vous pouvez exécuter un script de validation test pour tester le contenu de vos fichiers de solution, y compris le fichier createUiDefinition. Le script valide la syntaxe JSON, vérifie les expressions d’expression régulière sur les champs de texte et permet de s’assurer que les valeurs de sortie de l’interface du portail correspondent aux paramètres de votre modèle. Pour plus d’informations sur l’exécution de ce script, consultez [Run static validation checks for templates](https://aka.ms/arm-ttk) (Effectuer les vérifications de validation statiques des modèles).

## <a name="next-steps"></a>Étapes suivantes

Après avoir validé votre interface du portail, découvrez comment [rendre disponible votre application gérée Azure dans la Place de marché](../../marketplace/azure-app-offer-setup.md).