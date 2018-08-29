---
title: Paramètres de test de la validation en tant que service Azure Stack | Microsoft Docs
description: Rubrique de référence sur les paramètres du fichier de configuration et de la passe de test, nécessaires à la validation en tant que service Azure Stack.
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
ms.openlocfilehash: 65cae4d10a4683207474008a18bac39751ce8e96
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234531"
---
# <a name="test-parameters-for-validation-as-a-service-azure-stack"></a>Paramètres de test de la validation en tant que service Azure Stack

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Avant d’exécuter une suite de tests à partir de la validation en tant que service (VaaS), sélectionnez une solution et créez une passe de test.

- Connectez-vous avec vos informations d’identification de locataire VaaS inscrit.
- Créer une solution (en sélectionnant **Ajouter une Solution**) ou sélectionnez-en une existante.
- Sélectionnez le bouton **Démarrer** à partir de la vignette de flux de travail **Passes de Test**.

> [!Note]  
> Créez une entrée de solution pour chaque configuration de matériel/groupe de machines unique que vous voulez valider, et une nouvelle passe de test pour chaque déploiement de build sur ce groupe de machines.

Vous devez entrer les paramètres nécessaires à l’exécution des tests dans la page **Informations de passe de test**. Fournissez ces paramètres au démarrage d’une nouvelle passe de test ou lors de l’exécution d’une validation. La plupart des paramètres ont les mêmes valeurs que celles que vous avez fournies lors du déploiement d’Azure Stack.

Vous pouvez indiquer les valeurs listées manuellement dans le [tableau des paramètres de test](#test-parameters), ou charger le fichier de configuration de déploiement qui contient les informations du tampon Azure Stack dans son intégralité. Une fois le fichier chargé, le portail récupère les valeurs à partir de celui-ci.

> [!Note]  
> Vous pouvez rechercher et entrer les valeurs des paramètres de test en trouvant le fichier de configuration et en le chargeant sur le portail.

## <a name="export-the-test-parameters-using-powershell"></a>Exporter les paramètres de test au moyen de PowerShell

1. Connectez-vous à la machine DVM ou à n’importe quelle machine ayant accès à l’environnement Azure Stack.
2. Ouvrez une fenêtre Windows PowerShell avec des privilèges élevés et exécutez la commande suivante :

    ````PowerShell  
        $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation'
        ConvertTo-Json $params > stampinfoproperties.json
    ````

3. Chargez le fichier **stampinfoproperties.json** sur le portail VaaS.

## <a name="find-the-test-parameters-in-the-configuration-file"></a>Rechercher les paramètres de test dans le fichier de configuration

Vous pouvez également récupérer les valeurs des paramètres de test en ouvrant le **fichier de configuration** ECE. Ce fichier se trouve à l’emplacement suivant sur la machine DVM :  
`C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787`

## <a name="test-parameters"></a>Paramètres de test 

| Paramètre    | Description |
|-------------|-----------------|
| Build Azure Stack                      | Build Azure Stack ou numéro de la version qui a été déployée, par exemple 1.0.170330.0 | 
| ID client                              | Locataire Azure Active Directory spécifié au cours du déploiement Azure Stack. Recherchez **AADTenant** dans le fichier de configuration et sélectionnez la valeur **GUID** dans la balise `Id`. | 
| Région                                 | Région de déploiement Azure Stack. Recherchez **Region** dans le fichier de configuration. | 
| Resource Manager du locataire                | Point de terminaison Azure Resource Manager de locataire, par exemple `https://management.<ExternalFqdn>` | 
| Resource Manager de l’administrateur                 | Point de terminaison Azure Resource Manager d’administrateur. par exemple, `https://adminmanagement.<ExternalFqdn>` | 
| FQDN externe                          | Nom de domaine complet (FQDN) externe de l’environnement. Recherchez **ExternalFqdn** dans le fichier de configuration. | 
| Utilisateur locataire                            | Administrateur du locataire Azure Active Directory qui était déjà provisionné ou qui doit être provisionné par l’administrateur de service dans l’annuaire Azure AD. Pour plus d’informations sur le provisionnement du compte de locataire, consultez [Ajouter un nouveau compte de locataire Azure Stack dans Azure Active Directory](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-new-user-aad). Cette valeur est utilisée par le test pour effectuer des opérations au niveau du locataire, telles que le déploiement de modèles pour provisionner des ressources (machines virtuelles, comptes de stockage etc.) et exécuter des charges de travail. | 
| Utilisateur administrateur de service             | Administrateur Azure Active Directory du locataire Azure AD spécifié au cours du déploiement Azure Stack. Recherchez **AADTenant** dans le fichier de configuration et sélectionnez la valeur dans la balise `UniqueName` du fichier de configuration. | 

## <a name="checks-before-starting-the-tests"></a>Vérifications avant de commencer les tests

Les tests effectuent des opérations à distance. La machine qui exécute les tests doit avoir accès aux points de terminaison Azure Stack. Sinon, le test ne fonctionne pas. Si vous utilisez VaaS sur l’agent local, utilisez la machine sur laquelle l’agent s’exécutera. Vous pouvez vérifier que votre machine a accès aux points Azure Stack en effectuant les vérifications suivantes.

1. Vérifiez que l’URI de base est accessible. Ouvrez une invite de commandes ou l’interpréteur de commandes Bash, et exécutez la commande suivante :

    ```bash  
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Ouvrez un navigateur web et accédez à `https://adminportal.<EXTERNALFQDN>` afin de vérifier que le portail MAS (Microsoft Azure Stack) est accessible.

3. Connectez-vous à l’aide des valeurs du mot de passe et du nom d’administrateur de service Azure AD fournies lors de la création de la passe de test.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur la [Validation en tant que service Azure Stack](https://docs.microsoft.com/azure/azure-stack/partner).
