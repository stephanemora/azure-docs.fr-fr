---
title: 'Didacticiel : Configurer des ressources pour la validation en tant que service | Microsoft Docs'
description: Dans ce didacticiel, apprenez à configurer des ressources pour la validation en tant que service.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 460edc7f092764cce3584c81371db4801fbd69bc
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55240010"
---
# <a name="tutorial-set-up-resources-for-validation-as-a-service"></a>Tutoriel : Configurer des ressources pour la validation en tant que service

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Vous devez créer une solution. Une solution de validation en tant que service (VaaS) est une solution Azure Stack associée à une nomenclature matérielle particulière. Vous allez utiliser la solution pour vérifier si votre matériel peut prendre en charge l’exécution d’Azure Stack. Suivez ce didacticiel pour vous préparer à utiliser le service avec votre solution.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Se préparer à utiliser VaaS en configurant l’instance Azure Active Directory (Azure AD).
> * Créez un compte de stockage.

## <a name="configure-an-azure-ad-tenant"></a>Configurer un locataire Azure AD

Vous avez besoin d’un locataire Azure AD pour vous authentifier et vous inscrire auprès de VaaS. Les fonctionnalités de contrôle d’accès en fonction du rôle (RBAC) du locataire seront utilisées par le partenaire pour gérer les personnes pouvant utiliser VaaS dans son organisation.

Inscrivez l’annuaire du locataire Azure AD de votre organisation (et non l’annuaire du locataire Azure AD utilisé pour Azure Stack) et établissez une stratégie pour gérer les comptes d’utilisateur qu’il contient. Pour plus d’informations, consultez [Gérer votre répertoire Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-administer).

### <a name="create-a-tenant"></a>Créer un client

Créez un locataire spécifiquement pour VaaS en utilisant un nom descriptif, par exemple `ContosoVaaS@onmicrosoft.com`.

1. Créez un locataire Azure Active Directory dans le [portail Azure](https://portal.azure.com) ou utilisez un locataire existant. <!-- For instructions on creating new Azure AD tenants, see [Get started with Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). -->

2. Ajoutez au locataire des membres de votre organisation. Ces utilisateurs auront la responsabilité d’utiliser le service pour afficher ou planifier des tests. Une fois que vous avez terminé l’inscription, vous devez définir les niveaux d’accès des utilisateurs.
 
    Autorisez les utilisateurs de votre locataire à exécuter des actions dans VaaS en leur attribuant l’un des rôles suivants :

    | Nom du rôle | Description |
    |---------------------|------------------------------------------|
    | Propriétaire | A un accès total à toutes les ressources. |
    | Lecteur | Peut afficher toutes les ressources, mais ne peut ni en créer, ni les gérer. |
    | Contributeur du test | Peut créer et gérer les ressources de test. |

    Pour affecter de rôles dans l’application **Azure Stack Validation Service** :

    1. Connectez-vous au [Portail Azure](https://portal.azure.com).
    2. Sélectionnez **Tous les Services** > **Azure Active Directory** dans la section **Identité**.
    3. Sélectionnez **Applications d’entreprise** > **application Azure Stack Validation Service**.
    4. Sélectionnez **Utilisateurs et groupes**. Le panneau **Azure Stack Validation services - Utilisateurs et groupes** répertorie les utilisateurs ayant l’autorisation d’utiliser l’application.
    5. Sélectionnez **+ Ajouter un utilisateur** pour ajouter un utilisateur à partir de votre locataire et lui attribuer un rôle.
   
    Si vous souhaitez isoler des ressources et actions VaaS entre différents groupes au sein d’une organisation, vous pouvez créer plusieurs annuaires de locataire Azure AD.

### <a name="register-your-tenant"></a>Inscrire votre locataire

Ce processus autorise votre locataire dans l’application Azure AD **Azure Stack Validation Service**.

1. Envoyez les informations suivantes sur le locataire à Microsoft à l’adresse [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com).

    | Données | Description |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | Nom de l’organisation | Nom officiel de l’organisation. |
    | Nom du répertoire du locataire Azure AD | Nom de l’annuaire du locataire Azure AD en cours d’inscription. |
    | ID de l’annuaire du locataire Azure AD | GUID de l’annuaire du locataire AD Azure associé à l’annuaire. Pour plus d’informations sur la façon de trouver votre ID d’annuaire de locataire Azure AD, consultez [Obtenir l’ID de locataire](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id). |

2. Attendez la confirmation de l’équipe de validation Azure Stack pour vérifier que votre locataire peut utiliser le portail VaaS.

### <a name="consent-to-the-vaas-application"></a>Donner son consentement à l’application VaaS

En tant qu’administrateur Azure AD, attribuez à l’application VaaS Azure AD les autorisations requises pour le compte de votre locataire :

1. Utilisez les informations d’identification d’administrateur général pour connecter le locataire au [portail VaaS](https://azurestackvalidation.com/). 

2. Sélectionnez **My Account**.

3 Acceptez les conditions du contrat pour continuer lorsque vous êtes invité à accorder à VaaS les autorisations Azure AD indiquées.

## <a name="create-an-azure-storage-account"></a>Création d'un compte Azure Storage

Pendant l’exécution du test, VaaS génère des journaux de diagnostic vers un compte de stockage Azure. En plus des journaux de test, le compte de stockage peut également servir à charger les packages d’extension OEM pour le workflow de validation de la solution.

Le compte de stockage Azure est hébergé dans le cloud public Azure, et non sur votre environnement Azure Stack.

1. Dans le portail Azure, sélectionnez **Tous les services** > **Stockage** > **Comptes de stockage**. Dans le panneau **Comptes de stockage**, sélectionnez **Ajouter**.

2. Sélectionnez l’abonnement dans lequel créer le compte de stockage.

3. Sous **Groupe de ressources**, sélectionnez **Créer**. Entrez le nom de votre nouveau groupe de ressources.

4. Entrez un nom pour votre compte de stockage. Le nom que vous choisissez doit :
    - Être unique dans Azure
    - Contenir entre 3 et 24 caractères
    - Ne contenir que des chiffres et des lettres minuscules

5. Sélectionnez la région **USA Ouest** pour votre compte de stockage.

    Afin d’éviter que des frais de mise en réseau ne vous soient facturés pour le stockage des journaux, le compte de stockage Azure peut être configuré pour utiliser uniquement la région **USA Ouest**. Les fonctions de réplication des données et de niveau d’accès chaud ne sont pas nécessaires pour ces données. L’activation de ces fonctionnalités augmentera considérablement les coûts.

6. Conservez les paramètres aux valeurs par défaut ,à l’exception de **Type de compte** :

    - Le champ **Modèle de déploiement** est défini par défaut sur **Resource Manager**.
    - Le champ **Performances** est défini par défaut sur **Standard**.
    - Dans le champ **Type de compte**, sélectionnez **Stockage Blob**.
    - Le champ **Réplication** est défini par défaut sur **Stockage localement redondant (LRS)**.
    - Le champ **Niveau d’accès** est défini par défaut sur **Chaud**.

7. Cliquez sur **Vérifier + créer** pour passer en revue vos paramètres de compte de stockage et créer le compte.

## <a name="next-steps"></a>Étapes suivantes

Si votre environnement n’autorise pas les connexions entrantes, suivez le didacticiel sur le déploiement de l’agent local pour exécuter un test sur votre matériel.

> [!div class="nextstepaction"]
> [Déployer l’agent local](azure-stack-vaas-local-agent.md)
