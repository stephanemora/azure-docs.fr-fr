---
title: Configurer Azure RBAC pour le service FHIR-API de santé Azure
description: Cet article explique comment configurer Azure RBAC pour FHIR.
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 09/10/2021
ms.author: zxue
ms.openlocfilehash: ff2a488a7ed8a693f23b533242748eed400bd802
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124782492"
---
# <a name="configure-azure-rbac-for-the-fhir-service"></a>Configurer Azure RBAC pour le service FHIR

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Dans cet article, vous allez apprendre à utiliser le [contrôle d’accès en fonction du rôle (RBAC) Azure](../../role-based-access-control/index.yml) pour attribuer l’accès au plan de données des API de santé. Un RBAC Azure est la méthode recommandée pour l’attribution de l’accès au plan de données lorsque les utilisateurs du plan de données sont gérés dans le locataire Azure Active Directory associé à votre abonnement Azure. 

## <a name="assign-roles"></a>Attribuer des rôles

Pour accorder à des utilisateurs, des principaux de service ou des groupes l’accès au plan de données FHIR, sélectionnez le service FHIR dans la Portail Azure. Sélectionnez **contrôle d’accès (IAM)**, puis sélectionnez l’onglet **attributions de rôles** . Sélectionnez **+ Ajouter**, puis ajouter une **attribution de rôle**.
 
Si l’option attribution de rôle est grisée, demandez à votre administrateur d’abonnement Azure de vous accorder les autorisations pour l’abonnement ou le groupe de ressources, par exemple, « administrateur de l’accès utilisateur ». Pour plus d’informations sur les rôles intégrés Azure, consultez [rôles intégrés Azure](../../role-based-access-control/built-in-roles.md).

[![Attribution de rôle de contrôle d’accès. ](media/rbac/role-assignment.png) ](media/rbac/role-assignment.png#lightbox)

Dans la sélection de rôle, recherchez l’un des rôles intégrés pour le plan de données FHIR, par exemple, « FHIR Data Contributor ». Vous pouvez choisir d’autres rôles ci-dessous.

* **Lecteur de données FHIR**: peut lire (et Rechercher) des données FHIR.
* **FHIR Data Writer**: peut lire, écrire et supprimer de manière réversible des données FHIR.
* **Exportateur de données FHIR**: peut lire et exporter des données (opérateur $Export).
* **FHIR Data Contributor**: peut effectuer toutes les opérations du plan de données.
* **Convertisseur de données FHIR**: peut utiliser le convertisseur pour effectuer la conversion de données

Dans la section **Sélectionner** , tapez le nom de l’inscription de l’application cliente. Si le nom est trouvé, le nom de l’application est indiqué. Sélectionnez le nom de l’application, puis sélectionnez **Enregistrer**. 

Si l’application cliente est introuvable, vérifiez l’inscription de votre application pour vous assurer que le nom est correct. Assurez-vous que l’application cliente est créée dans le même locataire que le service FHIR dans les API de santé Azure (par le biais du service FHIR) est déployé dans.


[![Sélectionnez attribution de rôle. ](media/rbac/select-role-assignment.png) ](media/rbac/select-role-assignment.png#lightbox)

Vous pouvez vérifier l’attribution de rôle en sélectionnant l’onglet **attributions de rôles** dans l’option de menu **contrôle d’accès (IAM)** .
 

> [!NOTE]
> La propagation de l’attribution de rôle peut prendre quelques minutes dans le système. Si vous ne pouvez pas accéder au service FHIR dans votre application ou d’autres outils de test, vous pouvez patienter quelques minutes. Vérifiez également que vous avez accordé les autorisations user_impersonation aux API Azure Healthcare dans l’inscription de votre application.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à affecter des rôles Azure pour le plan de données FHIR. Pour savoir comment accéder au service FHIR à l’aide de la publication, consultez

>[!div class="nextstepaction"]
>[Accéder au service FHIR avec le poster](../use-postman.md)