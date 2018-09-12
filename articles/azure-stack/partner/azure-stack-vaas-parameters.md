---
title: Paramètres de flux de travail communs dans la validation en tant que service Azure Stack| Microsoft Docs
description: Paramètres de flux de travail communs dans la validation en tant que service Azure Stack| Microsoft Docs
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: c50e4b5c9eb81c9386e2cb0db96a88de70dcb9e9
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44157801"
---
# <a name="workflow-common-parameters-for-azure-stack-validation-as-a-service"></a>Paramètres de flux de travail communs dans la validation en tant que service Azure Stack| Microsoft Docs

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Les paramètres communs comprennent des valeurs, telles que les variables d’environnement et les informations d’identification utilisateur, qui sont exigées par tous les tests de la validation en tant que service (VaaS). Vous définissez ces valeurs au niveau du flux de travail. Vous enregistrez les valeurs lorsque vous créez ou modifiez un flux de travail. Au moment de la planification, le flux de travail charge les valeurs pour le test. 

## <a name="environment-parameters"></a>Paramètres d’environnement

Les paramètres d’environnement décrivent l’environnement Azure Stack testé. Ces valeurs doivent être fournies en générant et en chargeant votre fichier de configuration de tampon `&lt;link&gt;. [How to get the stamp info link].`

| Nom du paramètre | Obligatoire | type | Description |
|----------------------------------|----------|------|---------------------------------------------------------------------------------------------------------------------------------|
| Build Azure Stack | Obligatoire |  | Numéro de build du déploiement Azure Stack (par exemple, 1.0.170330.9) |
| Version OEM | Oui |  | Numéro de version du package OEM utilisé au cours du déploiement Azure Stack. |
| Signature OEM | Oui |  | Signature du package OEM utilisé au cours du déploiement Azure Stack. |
| ID de locataire AAD | Obligatoire |  | GUID de locataire Azure Active Directory spécifié au cours du déploiement Azure Stack.|
| Région | Obligatoire |  | Région de déploiement Azure Stack. |
| Point de terminaison Resource Manager du locataire | Obligatoire |  | Point de terminaison pour les opérations Azure Resource Manager de locataire (par exemple, https://management.<ExternalFqdn>) |
| Point de terminaison Resource Manager de l’administrateur | Oui |  | Point de terminaison pour les opérations Azure Resource Manager de locataire (par exemple, https://adminmanagement.<ExternalFqdn>) |
| FQDN externe | Oui |  | Nom de domaine complet externe utilisé comme suffixe pour les points de terminaison. (par exemple, local.azurestack.external ou redmond.contoso.com). |
| Nombre de nœuds | Oui |  | Nombre de nœuds dans le déploiement. |

## <a name="test-parameters"></a>Paramètres de test

Les paramètres de test communs comportent des informations sensibles qui ne peuvent pas stockées dans des fichiers de configuration, et qui doivent être fournies manuellement.

| Nom du paramètre | Obligatoire | type | Description |
|--------------------------------|------------------------------------------------------------------------------|------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nom d'utilisateur du locataire | Obligatoire |  | Administrateur du locataire Azure Active Directory qui était déjà provisionné ou qui doit être provisionné par l’administrateur de service dans l’annuaire AAD. Pour plus d’informations sur le provisionnement du compte de locataire, consultez [Bien démarrer avec Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). Cette valeur est utilisée par le test pour effectuer des opérations au niveau du locataire, telles que le déploiement de modèles pour provisionner des ressources (machines virtuelles, comptes de stockage etc.) et exécuter des charges de travail. Cette valeur est utilisée par le test pour effectuer des opérations au niveau du locataire, telles que le déploiement de modèles pour provisionner des ressources (machines virtuelles, comptes de stockage etc.) et exécuter des charges de travail. |
| Mot de passe du locataire | Obligatoire |  | Mot de passe de l’utilisateur locataire. |
| Nom d’utilisateur de l’administrateur de service | Obligatoire : validation de solution, validation de package<br>Facultatif: Passe de test |  | Administrateur Azure Active Directory du locataire AAD spécifié au cours du déploiement Azure Stack. |
| Mot de passe de l’administrateur de service | Obligatoire : validation de solution, validation de package<br>Facultatif: Passe de test |  | Mot de passe de l’utilisateur administrateur de service. |
| Nom d’utilisateur de l’administrateur de cloud | Obligatoire |  | Compte d’administrateur de domaine Azure Stack (par exemple, contoso\cloudadmin). Recherchez « User Role="CloudAdmin" » dans le fichier de configuration et sélectionnez la valeur dans la balise UserName du fichier de configuration. |
| Mot de passe d’administrateur de cloud | Obligatoire |  | Mot de passe de l’utilisateur administrateur de cloud. |
| Chaîne de connexion des diagnostics | Obligatoire |  | URI de signature d’accès partagé (SAP) d’un compte de stockage Azure dans lequel les journaux de diagnostic sont copiés pendant l’exécution des tests. Les instructions pour la génération de l’URI SAP se trouvent dans [Configurer un compte de stockage d’objets blob](azure-stack-vaas-set-up-account.md). |


## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur la [Validation en tant que service Azure Stack](https://docs.microsoft.com/azure/azure-stack/partner).
