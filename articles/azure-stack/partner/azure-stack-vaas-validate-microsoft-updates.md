---
title: Valider les mises à jour logicielles de Microsoft dans le service Validation en tant que service pour Azure Stack | Microsoft Docs
description: Découvrez comment valider les mises à jour logicielles de Microsoft avec le service Validation en tant que service.
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
ms.openlocfilehash: 6ef8c0486a694ac44c53375b24893812b10343e4
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158481"
---
# <a name="validate-software-updates-from-microsoft"></a>Valider les mises à jour logicielles issues de Microsoft

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft publie régulièrement des mises à jour du logiciel Azure Stack. Avant d’être rendues publiques, ces mises à jour sont fournies aux partenaires qui participent à la conception des offres Azure Stack pour permettre à ces derniers de valider les mises à jour pour leurs solutions et de faire part de leurs commentaires à Microsoft.

## <a name="test-an-existing-solution"></a>Tester une solution existante

1. Connectez-vous au [portail de validation](https://azurestackvalidation.com).

2. Sélectionnez une solution existante dans laquelle la mise à jour de Microsoft a été déployée et sélectionnez **Démarrer** sur la vignette **Package Validations** (Validations de package).

    ![Validations de package](media/image3.png)

3. Entrez le nom de la validation.

4. Spécifiez l’URL du package OEM qui a été installé sur la solution au moment du déploiement. Utilisez l’URL du package stocké sur le service Blob Azure. Pour plus d’informations, consultez la section [Create an Azure storage blob to store logs](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs) (Créer un blob de stockage Azure pour stocker les journaux).

5. Sélectionnez **Charger** pour ajouter votre fichier de configuration du déploiement. Pour plus d’informations sur le chargement de votre fichier de configuration du déploiement, consultez l’article [Valider une nouvelle solution Azure Stack](azure-stack-vaas-validate-solution-new.md).

6. Vous devez ensuite personnaliser le fichier de configuration du déploiement avec le fichier de paramètres d’environnement approprié. Pour plus d’informations, consultez la section [Environment parameters](azure-stack-vaas-parameters.md#environment-parameters) (Paramètres d’environnement).

    > [!Note]   
    > Vous pouvez personnaliser davantage le fichier de configuration du déploiement en ajoutant des paramètres de test cpmmuns. Pour plus d’informations, consultez l’article [Workflow common parameters for Azure Stack validation as a service](azure-stack-vaas-parameters.md) (Paramètres de flux de travail cpmmuns pour le service Validation en tant que service pour Azure Stack).

7. Le nom d’utilisateur et le mot de passe de l’utilisateur du locataire, de l’administrateur du service et de l’administrateur du cloud doivent être entrés manuellement.

8. Fournissez l’URL du blob Stockage Azure pour stocker les journaux de diagnostic. Pour plus d’informations, consultez la section [Create an Azure storage blob to store logs](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs) (Créer un blob de stockage Azure pour stocker les journaux).

    > [!Note]  
    > Vous pouvez entrer des balises descriptives pour libeller le flux de travail.

10. Sélectionnez **Envoyer** pour enregistrer le flux de travail.

Le flux de travail de la solution s’exécute pendant environ 24 heures. Ajoutez un lien d’accès ou des instructions concernant la planification des tests. Effacez les données de l’outil.

Pour plus d’informations sur la surveillance de la progression de l’exécution d’une validation, consultez l’article [Surveiller un test](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur la [Validation en tant que service Azure Stack](https://docs.microsoft.com/azure/azure-stack/partner).
