---
title: Résoudre les problèmes liés à l’Analyse des changements d’application - Azure Monitor
description: Découvrez comment résoudre les problèmes liés à l’Analyse des changements d’application.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 7200978ce31a47f7bd0d023cecf359b10a1c96de
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520753"
---
# <a name="troubleshoot-application-change-analysis-preview"></a>Résoudre les problèmes liés à l’Analyse des changements d’application (préversion)

## <a name="having-trouble-registering-microsoft-change-analysis-resource-provider-from-change-history-tab"></a>Problèmes d’inscription du fournisseur de ressources Microsoft. ChangeAnalysis à partir de l’onglet Historique des modifications

Si vous affichez l’historique des modifications pour la première fois après son intégration à Analyse des changements d’application, vous verrez qu’il inscrit automatiquement un fournisseur de ressources **Microsoft.ChangeAnalysis**. Dans de rares cas, cela peut échouer pour les raisons suivantes :

- **Vous ne disposez pas d’autorisations suffisantes pour inscrire le fournisseur de ressources Microsoft.ChangeAnalysis**. Ce message d’erreur signifie que votre rôle dans l’abonnement actuel n’est pas associé à l’étendue **Microsoft.Support/register/action**. Cela peut se produire si vous n’êtes pas le propriétaire d’un abonnement et que vous disposez d’autorisations d’accès partagé par le biais d’un collègue (autrement dit, vous pouvez afficher l’accès à un groupe de ressources). Pour résoudre ce problème, vous pouvez contacter le propriétaire de votre abonnement pour inscrire le fournisseur de ressources **Microsoft.ChangeAnalysis**. Vous pouvez le faire dans le portail Azure via **Abonnements | Fournisseurs de ressources**, en recherchant ```Microsoft.ChangeAnalysis``` et en l’inscrivant dans l’interface utilisateur ou via Azure PowerShell ou Azure CLI.

    Inscrire le fournisseur de ressources par le biais de PowerShell :
    ```PowerShell
    # Register resource provider
    Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
    ```

- **Échec de l’inscription du fournisseur de ressources Microsoft.ChangeAnalysis**. Ce message signifie que l’opération a échoué immédiatement quand l’interface utilisateur a envoyé la demande d’inscription du fournisseur de ressources, et que ce problème n’est pas lié à un problème d’autorisation. Il peut s’agir d’un problème de connectivité à Internet temporaire. Essayez d’actualiser la page et de vérifier votre connexion Internet. Si l’erreur persiste, contactez changeanalysishelp@microsoft.com

- **Cela prend plus de temps que prévu.** Ce message signifie que l’inscription prend plus de 2 minutes. Cela est inhabituel, mais cela ne signifie pas nécessairement qu’une erreur s’est produite. Vous pouvez accéder à **Abonnements | Fournisseur de ressources** pour vérifier l’état d’inscription du fournisseur de ressources **Microsoft.ChangeAnalysis**. Vous pouvez essayer d’utiliser l’interface utilisateur pour annuler l’inscription, procéder à une nouvelle inscription ou actualiser pour voir si cela vous aide. Si le problème persiste, contactez changeanalysishelp@microsoft.com pour obtenir de l’aide.
    ![Résolution des problèmes d’inscription de RP trop longue](./media/change-analysis/troubleshoot-registration-taking-too-long.png)

![Capture d’écran de l’outil Diagnostiquer et résoudre les problèmes d’une machine virtuelle avec Outils de dépannage sélectionné.](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Capture d’écran de la vignette de l’outil de dépannage Analyser les changements récents d’une machine virtuelle.](./media/change-analysis/analyze-recent-changes.png)

## <a name="azure-lighthouse-subscription-is-not-supported"></a>L’abonnement Azure Lighthouse n’est pas pris en charge

- **Échec de l’interrogation du fournisseur de ressources Microsoft.ChangeAnalysis** avec le message *L’abonnement à Azure Lighthouse n’est pas pris en charge, les modifications ne sont disponibles que dans le locataire d’accueil de l’abonnement*. Il existe actuellement une limite pour le fournisseur de ressources Analyse des changements pour s’inscrire par le biais d’un abonnement à Azure Lighthouse pour les utilisateurs qui ne sont pas dans le locataire d’accueil. Nous mettons tout en œuvre pour corriger cette limitation. Si ce problème vous bloque, il existe une solution de contournement qui implique la création d’un principal de service et l’attribution explicite du rôle pour autoriser l’accès.  Pour en savoir plus, contactez changeanalysishelp@microsoft.com.

## <a name="an-error-occurred-while-getting-changes-please-refresh-this-page-or-come-back-later-to-view-changes"></a>Une erreur s’est produite lors de la récupération des modifications. Actualisez cette page ou revenez plus tard pour afficher les modifications

Il s’agit du message d’erreur général présenté par le service Analyse des changements d’application lorsque les modifications n’ont pas pu être chargées. Voici quelques causes connues :

- Erreur de connectivité Internet à partir de l’appareil client
- Service Analyse des changements d’application temporairement indisponible, actualiser la page après quelques minutes corrige généralement ce problème. Si l’erreur persiste, contactez changeanalysishelp@micorosoft.com

## <a name="you-dont-have-enough-permissions-to-view-some-changes-contact-your-azure-subscription-administrator"></a>Vous n’avez pas les autorisations suffisantes pour afficher des modifications. Contactez votre administrateur d’abonnement Azure

Il s’agit du message d’erreur général non autorisé, expliquant que l’utilisateur actuel ne dispose pas des autorisations suffisantes pour afficher les modifications. Au moins un accès lecteur est requis sur la ressource pour afficher les modifications d’infrastructure retournées par Azure Resource Graph et Azure Resource Manager. Pour les modifications de fichier dans l’application web dans l’invité et les modifications de configuration, un rôle de contributeur au moins est nécessaire.

## <a name="failed-to-register-microsoftchangeanalysis-resource-provider"></a>Échec de l’inscription du fournisseur de ressources Microsoft.ChangeAnalysis

Ce message signifie que l’opération a échoué immédiatement quand l’interface utilisateur a envoyé la demande d’inscription du fournisseur de ressources, et que ce problème n’est pas lié à un problème d’autorisation. Il peut s’agir d’un problème de connectivité à Internet temporaire. Essayez d’actualiser la page et de vérifier votre connexion Internet. Si l’erreur persiste, contactez changeanalysishelp@microsoft.com

## <a name="you-dont-have-enough-permissions-to-register-microsoftchangeanalysis-resource-provider-contact-your-azure-subscription-administrator"></a>Vous ne disposez pas d’autorisations suffisantes pour inscrire le fournisseur de ressources Microsoft.ChangeAnalysis. Contactez votre administrateur d’abonnement Azure

Ce message d’erreur signifie que votre rôle dans l’abonnement actuel n’est pas associé à l’étendue **Microsoft.Support/register/action**. Cela peut se produire si vous n’êtes pas le propriétaire d’un abonnement et que vous disposez d’autorisations d’accès partagé par le biais d’un collègue (autrement dit, vous pouvez afficher l’accès à un groupe de ressources). Pour résoudre ce problème, vous pouvez contacter le propriétaire de votre abonnement pour inscrire le fournisseur de ressources **Microsoft.ChangeAnalysis**. Vous pouvez le faire dans le portail Azure via **Abonnements | Fournisseurs de ressources**, en recherchant ```Microsoft.ChangeAnalysis``` et en l’inscrivant dans l’interface utilisateur ou via Azure PowerShell ou Azure CLI.

Inscrire le fournisseur de ressources par le biais de PowerShell :

```PowerShell
# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
```

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur [Azure Resource Graph](../../governance/resource-graph/overview.md), qui vous aide à exécuter l’analyse des changements.