---
title: Tester la définition d’interface utilisateur pour les applications managées Azure | Microsoft Docs
description: Décrit comment tester l’expérience utilisateur de création de votre application gérée Azure via le portail.
services: managed-applications
documentationcenter: na
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2018
ms.author: tomfitz
ms.openlocfilehash: c88bdce64e88f8639da2c4ebb01f4594fccff8a0
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2018
ms.locfileid: "42747086"
---
# <a name="test-azure-portal-interface-for-your-managed-application"></a>Tester l’interface de portail Azure pour votre application managée
Après la [création du fichier createUiDefinition.json](create-uidefinition-overview.md) pour votre application gérée Azure, vous devez tester l’expérience utilisateur. Pour simplifier le test, utilisez un script qui charge votre fichier dans le portail. Vous n’avez pas besoin de déployer votre application managée.

## <a name="prerequisites"></a>Prérequis

* Un fichier **createUiDefinition.json**. Si vous n’avez pas ce fichier, copiez l’[exemple de fichier](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json) et enregistrez-le en local.

* Un abonnement Azure. Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="download-test-script"></a>Télécharger le script de test

Pour tester votre interface dans le portail, copiez un des scripts suivants sur votre ordinateur local :

* [Script PowerShell side-load](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Script Azure CLI side-load](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

## <a name="run-script"></a>Exécuter le script

Pour afficher votre fichier d’interface dans le portail, exécutez le script téléchargé. Le script crée un compte de stockage dans votre abonnement Azure et charge votre fichier createUiDefinition.json dans le compte de stockage. Le compte de stockage est créé quand vous exécutez le script pour la première fois, ou si le compte de stockage a été supprimé. Si le compte de stockage existe déjà dans votre abonnement Azure, le script le réutilise. Le script ouvre le portail et charge votre fichier à partir du compte de stockage.

Indiquez un emplacement pour le compte de stockage et spécifiez le dossier contenant votre fichier createUiDefinition.json.

Pour PowerShell, utilisez la commande suivante :

```powershell
.\SideLoad-CreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Pour l’interface de ligne de commande Azure, consultez :

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Si votre fichier createUiDefinition.json se trouve dans le même dossier que le script et que vous avez déjà créé le compte de stockage, vous n’avez pas besoin de fournir ces paramètres.

Pour PowerShell, utilisez la commande suivante :

```powershell
.\SideLoad-CreateUIDefinition.ps1
```

Pour l’interface de ligne de commande Azure, consultez :

```azurecli
./sideload-createuidef.sh
```

## <a name="test-your-interface"></a>Tester votre interface

Le script ouvre un nouvel onglet dans votre navigateur. Il affiche le portail avec votre interface de création de l’application managée.

![Afficher le portail](./media/test-createuidefinition/view-portal.png)

Avant de remplir les champs, ouvrez les outils de développement web dans votre navigateur. La **Console** affiche des messages importants sur votre interface.

![Sélectionner la console](./media/test-createuidefinition/select-console.png)

Si votre définition d’interface comporte une erreur, vous voyez la description dans la console.

![Afficher l’erreur](./media/test-createuidefinition/show-error.png)

Donnez des valeurs aux champs. Lorsque vous avez terminé, vous voyez les valeurs qui sont transmises au modèle.

![Afficher les valeurs](./media/test-createuidefinition/show-json.png)

Vous pouvez utiliser ces valeurs en tant que fichier de paramètres pour tester votre modèle de déploiement.

## <a name="troubleshooting-the-interface"></a>Résolution des problèmes liés à l’interface

Voici certaines erreurs courantes que vous pouvez voir :

* Le portail ne charge pas votre interface. À la place, il affiche une icône représentant un nuage avec une larme. En règle générale, cette icône apparaît quand votre fichier contient une erreur de syntaxe. Ouvrez le fichier dans Visual Studio Code (ou un autre éditeur JSON doté de la fonctionnalité de validation de schéma) et recherchez les erreurs de syntaxe.

* Le portail se bloque à l’écran récapitulatif. En règle générale, cette interruption se produit quand la section de sortie contient un bogue. Par exemple, vous avez référencé un contrôle inexistant.

* Un paramètre dans la sortie est vide. Le paramètre peut faire référence à une propriété inexistante. Par exemple, la référence au contrôle est valide, mais pas la référence à la propriété.

## <a name="test-your-solution-files"></a>Tester vos fichiers de solution

Maintenant que vous avez vérifié que votre interface de portail fonctionne comme prévu, il est temps de vérifier que votre fichier createUiDefinition est correctement intégré à votre fichier mainTemplate.json. Vous pouvez exécuter un script de validation test pour tester le contenu de vos fichiers de solution, y compris le fichier createUiDefinition. Le script valide la syntaxe JSON, vérifie les expressions d’expression régulière sur les champs de texte et permet de s’assurer que les valeurs de sortie de l’interface du portail correspondent aux paramètres de votre modèle. Pour plus d’informations sur l’exécution de ce script, consultez [Run static validation checks for templates](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests) (Effectuer les vérifications de validation statiques des modèles).

## <a name="next-steps"></a>Étapes suivantes

Après avoir validé votre interface du portail, découvrez comment [rendre disponible votre application gérée Azure dans la Place de marché](publish-marketplace-app.md).
