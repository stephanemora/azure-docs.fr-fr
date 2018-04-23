---
title: Gérer des profils de version des API dans Azure Stack | Microsoft Docs
description: En savoir plus sur les profils de version des API dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 8A336052-8520-41D2-AF6F-0CCE23F727B4
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 452ed1de0588b380747edaa44dd0cc3805c51392
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Gérer les profils de version des API dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Les profils d’API précisent le fournisseur de ressources Azure ainsi que la version d’API des points de terminaison Azure REST. Vous pouvez créer des clients personnalisés dans différents langages à l’aide des profils d’API. Chaque client utilise un profil d’API pour contacter le fournisseur de ressources approprié et la version d’API pour Azure Stack. 

Vous pouvez créer une application qui fonctionne avec les fournisseurs de ressources Azure sans savoir exactement quelle version des API de fournisseur de ressources est compatible avec Azure Stack. Il vous suffit d’aligner votre application sur un profil. Le kit de développement logiciel (SDK) rétablit les versions d’API appropriées.


Cette rubrique vous aide à :
 - comprendre les profils d’API pour Azure Stack,
 - comprendre l’utilisation des profils d’API pour développer vos solutions,
 - obtenir des conseils spécifiques au code.

## <a name="summary-of-api-profiles"></a>Résumé des profils d’API

- Les profils d’API représentent un ensemble de fournisseurs de ressources Azure et leurs versions d’API.
- Les profils d’API ont été créés pour les développeurs. Ils leur servent à générer des modèles utilisables sur plusieurs clouds Azure. Ils sont conçus pour répondre à vos besoins en vous offrant des interfaces compatibles et stables.
- La mise en production des profils intervient quatre fois par an.
- Les trois conventions d’appellation des profils sont les suivantes :
    - **le plus récent**  
        Versions d’API les plus récentes publiées dans Azure.
    - **yyyy-mm-dd-hybrid**  
    Version publiée tous les semestres. Elle se concentre sur la cohérence et la stabilité d’un cloud à l’autre.
    - **yyyy-mm-dd-profile**  
    Version se situant entre celle offrant une stabilité optimale et celle proposant les fonctionnalités les plus récentes.

## <a name="azure-resource-manager-api-profiles"></a>Profils d’API Azure Resource Manager

Azure Stack n’utilise pas la version d’API la plus récente proposée dans la version générale d’Azure. Au moment où vous créez votre propre solution, vous devez rechercher la version d’API pour chaque fournisseur de ressources dans Azure qui est compatible avec Azure Stack.

Plutôt que d’effectuer des recherches sur chaque fournisseur de ressources et sur chacune des versions spécifiques prises en charge par Azure Stack, vous pouvez utiliser un profil d’API. Le profil spécifie un ensemble de fournisseurs de ressources et de versions d’API. Le SDK, ou un outil intégré au SDK, vous oriente vers la version d’API cible indiquée dans le profil. Avec les profils d’API, vous pouvez spécifier une version de profil qui s’applique à tout un modèle. Puis, au moment de l’exécution, Azure Resource Manager sélectionne la version appropriée de la ressource.

Les profils d’API fonctionnent avec des outils qui utilisent Azure Resource Manager, par exemple PowerShell, Azure CLI, le code fourni dans le SDK et Microsoft Visual Studio. Les outils et les SDK peuvent utiliser des profils pour lire la version des modules et des bibliothèques à inclure au moment de créer une application.

Par exemple, si vous utilisez PowerShell pour créer un compte de stockage à l’aide du fournisseur de ressources **Microsoft.Storage** (qui prend en charge la version d’API 2016-03-30) et une machine virtuelle à l’aide du fournisseur de ressources Microsoft.Compute (qui utilise la version d’API 2015-12-01), vous devez rechercher quel module PowerShell prend en charge 2016-03-30 pour Storage et quel module prend en charge 2015-02-01 pour Compute, puis les installer. Pour vous éviter de procéder ainsi, vous pouvez utiliser un profil. Utilisez la cmdlet **Install-Profile *nom du profil***. PowerShell charge alors la bonne version des modules.

De même, lorsque vous utilisez le SDK Python pour générer une application basée sur Python, vous pouvez spécifier le profil. Le SDK charge les modules correspondant aux fournisseurs de ressources que vous avez spécifiés dans votre script.

En tant que développeur, vous pouvez ainsi vous concentrer sur l’écriture de votre solution. Au lieu de rechercher les versions d’API, le fournisseur de ressources et le type de cloud qui fonctionnent ensemble, utilisez un profil. Vous avez ainsi l’assurance que votre code fonctionne dans tous les clouds qui prennent en charge ce profil.

## <a name="api-profile-code-samples"></a>Exemples de code de profil d’API

Il existe des exemples de code pour vous aider à intégrer votre solution et votre langage par défaut à Azure Stack à l’aide des profils. Actuellement, vous pouvez trouver des conseils et des exemples pour les langages suivants :

- **PowerShell**  
Le module **AzureRM.Bootstrapper**, disponible via PowerShell Gallery, fournit les cmdlets PowerShell nécessaires pour utiliser des profils de version d’API.  
Pour plus d’informations, consultez [Utiliser des profils de version d’API pour PowerShell](azure-stack-version-profiles-powershell.md).
- **Azure CLI 2.0**  
Vous pouvez mettre à jour la configuration de votre environnement pour utiliser le profil de version d’API spécifique à Azure Stack.  
Pour plus d’informations, consultez [Utiliser les profils de version d’API pour Azure CLI 2.0](azure-stack-version-profiles-azurecli2.md).
- **GO**  
Dans le SDK GO, un profil est une combinaison de différents types de ressources dans différentes versions provenant de différents services. Les profils sont disponibles sous les profils/chemin d’accès, leur version étant au format **YYYY-MM-DD**.  
Pour plus d’informations, consultez [Utiliser des profils de version d’API pour GO](azure-stack-version-profiles-go.md).

## <a name="next-steps"></a>Étapes suivantes
* [Installer PowerShell pour Azure Stack](azure-stack-powershell-install.md)
* [Configurez l’environnement PowerShell de l’utilisateur Azure Stack.](azure-stack-powershell-configure-user.md)
* [Passer en revue les détails sur les versions d’API du fournisseur de ressources prises en charge par les profils](azure-stack-profiles-azure-resource-manager-versions.md).
