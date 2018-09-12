---
title: Valider une nouvelle solution Azure Stack | Microsoft Docs
description: Découvrez comment valider une nouvelle solution Azure Stack avec le service Validation en tant que service.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 1e908a8cf5576ce3bc3d58d1ef6f29d596ebc58b
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158175"
---
# <a name="validate-a-new-azure-stack-solution"></a>Valider une nouvelle solution Azure Stack

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Découvrez comment utiliser le flux de travail de validation de solution pour certifier les nouvelles solutions Azure Stack.

Une solution Azure Stack est une nomenclature matérielle qui a été convenue conjointement avec Microsoft et a satisfait aux exigences de certification du logo Windows Server. Vous pouvez également utiliser le flux de travail de validation de solution lorsqu’une modification apportée à la nomenclature matérielle entraîne la classification d’une solution comme *nouvelle* solution. Si vous avez des questions concernant les aspects qui donneraient lieu à une **nouvelle** solution ou à la **recertification** d’une solution, contactez l’adresse [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com).

Pour certifier votre solution, exécutez le flux de travail deux fois. Exécutez-le une première fois pour la configuration *minimale* prise en charge. Exécutez-le une seconde fois pour la configuration *maximale*. Microsoft certifie la solution si les deux configurations réussissent tous les tests.

Ce guide de démarrage rapide vous explique comment ajouter votre solution et exécuter des tests.

## <a name="add-a-new-solution"></a>Ajouter une nouvelle solution

1. Connectez-vous au [portail de validation](https://azurestackvalidation.com).
2. Sélectionnez **Nouvelle solution**.
3. Entrez un nom pour la solution, puis sélectionnez **Enregistrer**.

## <a name="create-a-solution-validation-workflow"></a>Créer un flux de travail de validation de solution

1. Sélectionnez le nom de la solution.
2. Sélectionnez **Gérer** sur la vignette **Validations de solution**.

    ![Validations de solution](media/image2.png)

## <a name="create-a-solution-workflow"></a>Créer un flux de travail de solution

1. Sélectionnez **Validation de nouvelle solution**.
2. Tapez le nom de la validation.
3. Sélectionnez **Minimum** ou **Maximum**.  
    - **Minimum**  
    La solution est configurée avec le nombre minimal de nœuds pris en charge.  
    - **Maximum**  
    La solution est configurée avec le nombre maximal de nœuds pris en charge.
4. Sélectionnez **Charger**, puis ajoutez votre fichier de configuration du déploiement. Il s’agit d’une étape facultative. Vous pouvez également ajouter vos paramètres de test en exécutant la procédure décrite à la section suivante.

    > [!note]  
    > Vous pouvez créer votre fichier de configuration en ajoutant les paramètres dans les sections des paramètres d’environnement et des paramètres de test communs de l’interface. Vous pouvez récupérer le fichier généré par le service à partir du déploiement Azure Stack que vous êtes en train de valider. Pour obtenir les instructions correspondantes, consultez l’article [Workflow common parameters for Azure Stack validation as a service](azure-stack-vaas-parameters.md) (Paramètres de flux de travail communs pour le service Validation en tant que service pour Azure Stack).

5. Ajoutez vos paramètres d’environnement. Pour plus d’informations, consultez la section [Ajouter des paramètres d’environnement](#add-environmental-parameters).
6. Ajoutez vos paramètres de test communs. Pour plus d’informations, consultez la section [Ajouter des paramètres de test communs](#add-common-test-parameters).

    Selon la définition du test, le test peut vous obliger à entrer une valeur indépendamment des paramètres communs, ou vous autoriser à remplacer la valeur du paramètre commun.

7. Cliquez sur **Envoyer** pour planifier le test.

## <a name="add-environmental-parameters"></a>Ajouter des paramètres d’environnement

Ajoutez les paramètres d’environnement suivants :

| Informations de passe de test | Obligatoire | Description |
| --- | --- | --- | --- |
| Build Azure Stack | Obligatoire | Numéro de build Azure Stack (par exemple 20170501.1). Cette valeur doit être un numéro de build ou une version Azure Stack valides, par exemple 1.0.170330.9. |
| ID client | Obligatoire | ID du locataire Active Directory. Il doit s’agir d’un identificateur global unique (par exemple ECA23256-6BA0-4F27-8E4D-AFB02F088363) |
| Région | Obligatoire | Région du déploiement Azure Stack. |
| Point de terminaison Resource Manager du locataire | Obligatoire | Point de terminaison pour les opérations Azure Resource Manager du locataire (par exemple https://management.loc-ext.domain.com) |
| Point de terminaison Resource Manager de l’administrateur | Non requis | Point de terminaison pour les opérations Azure Resource Manager du locataire (par exemple https://management.loc-ext.domain.com) |
| FQDN externe | Non requis | Nom de domaine complet externe utilisé comme suffixe pour les points de terminaison. (par exemple local.azurestack.external ou redmond.contoso.com). |
| Nombre de nœuds | Obligatoire | Nombre de nœuds de votre solution. |

## <a name="add-common-test-parameters"></a>Ajouter des paramètres de test communs

Ajoutez les paramètres de test communs suivants :

| Informations de passe de test | Obligatoire | Description |
| --- | --- | --- |
| Nom d'utilisateur du locataire | Obligatoire | Nom d’utilisateur du locataire (par exemple tenant@contoso.onmicrosoft.com) |
| Mot de passe du locataire | Obligatoire | Mot de passe du locataire. |
| Nom d’utilisateur de l’administrateur de service | Non requis | Nom d’utilisateur du locataire (par exemple tenant@contoso.onmicrosoft.com) |
| Mot de passe de l’administrateur de service | Non requis | Nom d’utilisateur de l’administrateur du service (par exemple serviceadmin@contoso.onmicrosoft.com) |
| Nom d’utilisateur de l’administrateur de cloud | Non requis | Compte d’administrateur de domaine Azure Stack (par exemple, contoso\cloudadmin). |
| Mot de passe d’administrateur de cloud | Non requis | |
|  Chaîne de connexion des diagnostics | Non requis | URI de signature d’accès partagé (SAP) d’un compte de stockage Azure dans lequel les journaux de diagnostic seront copiés pendant l’exécution des tests. Consultez la section [Créer un blob de stockage Azure pour stocker les journaux](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs). <br><br>La valeur du paramètre commun **Chaîne de connexion des diagnostics** est stockée par le service et fournie lors de la planification à tous les tests du flux de travail qui utilisent ce paramètre. Lorsque l’URL SAP doit arriver à expiration dans les 30 jours, vous êtes invité à indiquer une nouvelle URL SAP sur la page des paramètres communs. |
| Balise - Nom | Non requis |  Vous pouvez entrer des balises descriptives pour libeller le flux de travail. Il s’agit du nom de la balise. |
| Balise - Valeur | Non requis | Vous pouvez entrer des balises descriptives pour libeller le flux de travail. Il s’agit de la valeur de la balise. |

## <a name="next-steps"></a>Étapes suivantes

- [Replanifiez ou annulez un test](azure-stack-vaas-monitor-test.md#reschedule-a-test).
- Apprenez-en davantage sur le service [Validation en tant que service pour Azure Stack](https://docs.microsoft.com/azure/azure-stack/partner).