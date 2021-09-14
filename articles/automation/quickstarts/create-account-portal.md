---
title: Démarrage rapide - Créer un compte Azure Automation en utilisant le portail
description: Ce guide de démarrage rapide vous aide à créer un compte Azure Automation en utilisant le portail.
services: automation
ms.date: 09/07/2021
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 5d73da041b5ecb5dd2b7089ee898b5bffbe4f512
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123542992"
---
# <a name="quickstart-create-an-automation-account-using-the-azure-portal"></a>Démarrage rapide : Créer un compte Automation en utilisant le portail Azure

Vous pouvez créer un [compte Azure Automation](../automation-security-overview.md) en utilisant le portail Azure, une interface utilisateur basée sur un navigateur permettant d’accéder à un certain nombre de ressources. Un compte Automation peut gérer les ressources sur l’ensemble des régions et abonnements pour un locataire donné. Ce guide de démarrage rapide vous guide tout au long de la création d’un compte Automation.

## <a name="prerequisites"></a>Prérequis

Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-automation-account"></a>Créer un compte Automation

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans le menu du haut, sélectionnez **+ Créer une ressource**.

1. Sous Catégories**, sélectionnez **Informatique et outils de gestion**, puis **Automation**.

   :::image type="content" source="./media/create-account-portal/automation-account-portal.png" alt-text="Recherche de comptes Automation dans le portail.":::

1. Dans la page **Ajouter un compte Automation**, entrez les informations suivantes :

   | Propriété | Description |
   |---|---|
   |Name| Entrez un nom unique pour l’emplacement et le groupe de ressources. Il est possible que les noms de comptes Automation qui ont été supprimés ne soient pas immédiatement disponibles. Vous ne pouvez pas changer le nom du compte une fois qu’il a été entré dans l’interface utilisateur. |
   |Abonnement| Dans la liste déroulante, sélectionnez l’abonnement Azure pour le compte.|
   |Resource group|Dans la liste déroulante, sélectionnez votre groupe de ressources existant ou **Créer**.|
   |Emplacement| Dans la liste déroulante, sélectionnez un emplacement pour le compte. Pour obtenir une liste mise à jour des emplacements sur lesquels vous pouvez déployer un compte Automation, consultez [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all).|
   |Créer un compte d’identification Azure| Sélectionnez **Non**.  Un compte d’identification Azure dans le compte Automation est utile pour l’authentification auprès d’Azure. Toutefois, les identités managées dans Automation sont désormais disponibles. Les [identités managées](../../active-directory/managed-identities-azure-resources/overview.md) fournissent une identité utilisée par les applications lorsqu’elles se connectent à des ressources qui prennent en charge l’authentification Azure Active Directory (Azure AD). |

   :::image type="content" source="./media/create-account-portal/add-automation-account-portal.png" alt-text="Champs requis pour l’ajout du compte Automation":::

1. Sélectionnez **Créer** pour démarrer le déploiement du compte Automation. La création se termine dans environ une minute.

1. Vous recevez une notification une fois le déploiement terminé. Sélectionnez **Atteindre la ressource** dans la notification pour ouvrir la page **Compte Automation**.

1. Passez en revue votre nouveau compte Automation.

   :::image type="content" source="./media/create-account-portal/automation-account-overview.png" alt-text="Page Vue d’ensemble du compte Automation":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne prévoyez pas de continuer à utiliser le compte Automation, sélectionnez **Supprimer** dans la page **Vue d’ensemble**, puis sélectionnez **Oui** quand vous y êtes invité.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un compte Automation. Pour utiliser des identités managées avec votre compte Automation, passez au guide de démarrage rapide suivant :

> [!div class="nextstepaction"]
> [Démarrage rapide - Activer les identités managées](enable-managed-identity.md)

