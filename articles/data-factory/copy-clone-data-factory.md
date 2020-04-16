---
title: Copier ou cloner une fabrique de données dans Azure Data Factory
description: Découvrir comment copier ou cloner une fabrique de données dans Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: 5e44bda8648fbf26487b04cf36a8fd0ec085c411
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414104"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Copier ou cloner une fabrique de données dans Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Cet article montre comment copier ou cloner une fabrique de données dans Azure Data Factory.

## <a name="use-cases-for-cloning-a-data-factory"></a>Cas d’usage pour le clonage d’une fabrique de données

Voici quelques-uns des cas dans lesquels il peut être utile de copier ou cloner une fabrique de données :

-   **Renommer des ressources**. Azure ne permet pas de renommer des ressources. Si vous souhaitez renommer une fabrique de données, vous pouvez cloner la fabrique de données avec un nom différent et ensuite supprimer la fabrique de données existantes.

-   **Débogage des modifications** lorsque les fonctionnalités de débogage ne sont pas suffisantes. Parfois, pour tester vos modifications, vous voudrez peut-être le faire dans une autre fabrique avant de les appliquer à votre fabrique principale. Dans la plupart des scénarios, vous pouvez utiliser le débogage. Toutefois, les modifications apportées aux déclencheurs, telles que le comportement de vos modifications lorsqu’un déclencheur est invoqué automatiquement ou sur une fenêtre temporelle, peuvent ne pas être facilement testables sans vérification. Dans ces cas, le clonage de la fabrique et l’application de vos modifications sont des opérations utiles. Comme Azure Data Factory facture principalement en fonction du nombre d’exécutions, la deuxième fabrique n’entraîne pas de frais supplémentaires.

## <a name="how-to-clone-a-data-factory"></a>Guide pratique de clonage d’une entité Data Factory

1. L’interface utilisateur de Data Factory dans le portail Azure vous permet d’exporter toute la charge utile de votre fabrique de données dans un modèle de Resource Manager, ainsi qu’un fichier de paramètres qui vous permet de modifier les valeurs souhaitées lorsque vous clonez votre fabrique.

1. Comme condition préalable, vous devez créer votre fabrique de données cible à partir du portail Azure.

1. Si vous avez un runtime d’intégration auto-hébergé dans votre fabrique source, vous devez le précréer avec le même nom dans la fabrique cible. Si vous souhaitez que différentes fabriques partagent le runtime d’intégration auto-hébergé, vous pouvez utiliser le modèle publié [ici](source-control.md#best-practices-for-git-integration).

1. Si vous êtes en mode GIT, chaque fois que vous publiez à partir du portail, le modèle Resource Manager de la fabrique est enregistré dans la branche adf_publish du référentiel GIT.

1. Pour d’autres scénarios, le modèle Resource Manager peut être téléchargé d’un clic sur le bouton **Exporter un modèle Resource Manager** dans le portail.

1. Après avoir téléchargé le modèle Resource Manager, vous pouvez le déployer via les méthodes de déploiement de modèle Resource Manager standards.

1. Pour des raisons de sécurité, le modèle Resource Manager généré ne contient aucune information secrète, comme les mots de passe des services liés. Par conséquent, vous devez fournir ces mots de passe comme paramètres de déploiement. Si la fourniture de paramètres n’est pas souhaitable, vous devez obtenir les chaînes de connexion et les mots de passe des services liés auprès d’Azure Key Vault.

## <a name="next-steps"></a>Étapes suivantes

Passez en revue les conseils de création d’une fabrique de données dans le portail Azure à la rubrique [Créer une fabrique de données à l’aide de l’interface utilisateur d’Azure Data Factory](quickstart-create-data-factory-portal.md).
