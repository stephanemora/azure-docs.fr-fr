---
title: Valider les packages de fabricant d’ordinateurs OEM dans le service Validation en tant que service pour Azure Stack | Microsoft Docs
description: Découvrez comment vérifier les packages de fabricant d’ordinateurs OEM avec le service Validation en tant que service.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: a08f439780e0080d8da2cde1531e1580dbdad14f
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234458"
---
# <a name="validate-oem-packages"></a>Valider les packages OEM

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Vous pouvez tester un nouveau package OEM lorsqu’une modification a été apportée au microprogramme ou aux pilotes d’une solution ayant fait l’objet d’une validation. Une fois que votre package a réussi le test, il est signé par Microsoft. Votre test doit contenir le package d’extension OEM mis à jour avec les pilotes et le microprogramme qui ont réussi les tests de certification PCS (Partner Certification Services) et du logo Windows Server.

Tous les tests s’achèvent dans les 24 heures avec le résultat **succeeded**. Si l’un des tests présente le résultat **failed**, signalez un bogue dans [Microsoft Collaborate](https://aka.ms/collaborate) et informez-en Microsoft en envoyant un e-mail à l’adresse [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com).

## <a name="get-your-oem-package-signed"></a>Obtenir votre package OEM signé

1. Vérifiez que la mise à jour mensuelle en vigueur a été appliquée. Pour connaître la dernière version, recherchez la version la plus récente dans [Documentation pour opérateur Azure Stack > Vue d’ensemble > Notes de publication](https://docs.microsoft.com/azure/azure-stack/).

    Les mises à jour logicielles Microsoft pour Azure Stack sont désignées conformément à une convention d’affectation de noms ; par exemple, 1803 indique que la mise à jour date de mars 2018. Pour plus d’informations sur la stratégie de mise à jour, la cadence des mises à jour et les notes de publication concernant Azure Stack, consultez l’article [Stratégie de maintenance Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-servicing-policy).

1. Vérifiez l’état d’intégrité du système en exécutant **Test-AzureStack** comme décrit dans l’article [Exécuter un test de validation pour Azure Stack]. Corrigez les problèmes signalés par les avertissements et les erreurs avant de lancer le moindre test.

2. Dans le [portail de validation](https://azurestackvalidation.com), sélectionnez une solution existante. Si vous n’avez pas ajouté votre solution, consultez la section [Ajouter une nouvelle solution](azure-stack-vaas-validate-solution-new.md#add-a-new-solution).

3. Sélectionnez **Démarrer** sur la vignette **Package Validations** (Validations de package) pour démarrer un nouveau flux de travail.

    ![Validations de package](media/image3.png)

4.  Fournissez une chaîne de connexion des diagnostics. Pour obtenir les instructions correspondantes, consultez l’article [Configurer un compte de stockage](azure-stack-vaas-set-up-account.md).

    Vous devez spécifier un package d’extension OEM chaque fois que vous exécutez une validation de package. Spécifiez le package OEM qui a été installé sur la solution au moment du déploiement d’Azure Stack. Pour obtenir les instructions correspondantes, consultez la section [Create an Azure storage blob to store logs](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs) (Créer un blob de stockage Azure pour stocker les journaux).

    Vous devez utiliser un fichier JSON contenant les variables d’environnement pour terminer le remplissage des champs obligatoires de l’exécution afin d’éviter les erreurs de saisie de données. Pour obtenir les instructions correspondantes, consultez l’article expliquant comment [obtenir un fichier de configuration dans un déploiement Azure Stack](azure-stack-vaas-parameters.md).

5. Exécutez les tests.

6. Une fois que tous les tests ont été effectués avec succès, envoyez le nom de votre validation de solution et de package à l’adresse [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) pour demander la signature du package.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur le service [Validation en tant que service pour Azure Stack](https://docs.microsoft.com/azure/azure-stack/partner).
