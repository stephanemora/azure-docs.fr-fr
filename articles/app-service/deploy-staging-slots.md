---
title: Configurer des environnements intermédiaires pour les applications web dans Azure App Service | Microsoft Docs
description: Découvrez comment utiliser la publication intermédiaire pour les applications web dans Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: jpconnoc
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2019
ms.author: cephalin
ms.openlocfilehash: 544ef8947f3a593071cabea018c722db96ab1475
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59266203"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Configurer des environnements intermédiaires dans Azure App Service
<a name="Overview"></a>

> [!NOTE]
> Ce guide pratique montre comment gérer les emplacements à l’aide d’une nouvelle page de gestion en préversion. Les clients habitués à la page de gestion existante peuvent continuer à utiliser la page de gestion des emplacements existante, comme avant. 
>

Lorsque vous déployez votre application web, votre application web Linux, votre backend mobile ou votre API dans [App Service](https://go.microsoft.com/fwlink/?LinkId=529714), vous pouvez cibler un autre emplacement de déploiement que l’emplacement de production par défaut lorsque vous exécutez le niveau de plan **Standard**, **Premium** ou **Isolé** d’App Service. Les emplacements de déploiement sont en fait des applications dynamiques pourvues de leur propre nom d’hôte. Les éléments de contenu et de configuration des applications peuvent être échangés entre deux emplacements de déploiement, y compris l’emplacement de production. Le déploiement de votre application sur un emplacement hors production présente les avantages suivants :

* Vous pouvez valider les modifications d’une application dans un emplacement de déploiement intermédiaire avant de l’échanger avec l’emplacement de production.
* Déployer d’abord une application dans un emplacement et la basculer ensuite en production permet de vous assurer que toutes les instances de l’emplacement sont initialisées avant d’être basculées en production. Cela permet d’éliminer les temps d’arrêt lors du déploiement de l’application. La redirection du trafic est transparente et aucune requête n’est abandonnée du fait d’opérations de permutation. Ce flux de travail peut être entièrement automatisé en configurant l’[Échange automatique](#Auto-Swap) lorsqu’aucune validation n’est nécessaire avant l’échange.
* Après basculement, la précédente application de production se retrouve dans l’emplacement de l’application précédemment intermédiaire. Si les modifications permutées en production ne vous conviennent pas, vous pouvez effectuer la même permutation afin de récupérer immédiatement le contenu du précédent site qui vous plaisait.

Chaque niveau de plan App Service prend en charge un nombre différent d’emplacements de déploiement. Pour connaître le nombre d’emplacements pris en charge par le plan de votre application, consultez [Limites App Service](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). Pour mettre votre application à l’échelle vers un niveau différent, le niveau cible doit prendre en charge le nombre d’emplacements déjà utilisés par votre application. Par exemple, si votre application possède plus de cinq emplacements, vous ne pouvez pas effectuer de scale-down vers le niveau **Standard**, car **ce niveau** ne prend en charge que cinq emplacements de déploiement.

<a name="Add"></a>

## <a name="add-slot"></a>Ajouter un emplacement
Pour que vous puissiez activer plusieurs emplacements de déploiement, l’application doit s’exécuter au niveau **Standard**, **Premium** ou **Isolé**.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la [page de ressources](../azure-resource-manager/manage-resources-portal.md#manage-resources) de votre application.

2. Dans le volet de navigation gauche, choisissez l’option **Emplacements de déploiement (préversion)**, puis cliquez sur **Ajouter un emplacement**.
   
    ![Add a new deployment slot](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Si l’application ne s’exécute pas au niveau **Standard**, **Premium** ou **Isolé**, vous recevez un message précisant les niveaux pris en charge pour l’activation de la publication intermédiaire. À ce stade, vous pouvez sélectionner **Mettre à niveau** et accéder à l’onglet **Mettre à l’échelle** de votre application avant de continuer.
   > 

3. Dans la boîte de dialogue **Ajouter un emplacement**, nommez l’emplacement, puis indiquez si vous souhaitez cloner la configuration de l’application à partir d’un autre emplacement de déploiement. Cliquez sur **Ajouter** pour continuer.
   
    ![Source de configuration](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Vous pouvez cloner la configuration à partir d’un emplacement existant. Parmi les paramètres pouvant être clonés figurent des paramètres de l’application, des chaînes de connexion, des versions du framework de langage, des sockets web, la version HTTP et le nombre de bits de la plateforme.

4. Une fois l’emplacement ajouté, cliquez sur **Fermer** pour fermer la boîte de dialogue. Le nouvel emplacement est désormais affiché dans la page **Emplacements de déploiement (préversion)**. Par défaut, l’option **% de trafic** est définie sur 0 pour le nouvel emplacement, avec tout le trafic client acheminé vers l’emplacement de production.

5. Cliquez sur le nouvel emplacement de déploiement pour ouvrir la page des ressources de cet emplacement.
   
    ![Titre de l'emplacement de déploiement](./media/web-sites-staged-publishing/StagingTitle.png)

    L’emplacement intermédiaire dispose d’une page de gestion, comme n’importe quelle application App Service. Vous pouvez modifier la configuration de l’emplacement. Le nom de l’emplacement est indiqué en haut de la page, pour vous rappeler que c’est l’emplacement de déploiement qui est affiché.

6. Cliquez sur l’URL de l’application dans la page des ressources de l’emplacement. L’emplacement de déploiement possède son propre nom d’hôte ; il s’agit d’une application dynamique. Pour limiter l’accès public à l’emplacement de déploiement, consultez [Restrictions d’adresse IP avec Azure App Service](app-service-ip-restrictions.md).

Le nouvel emplacement de déploiement n’a aucun contenu, même si vous clonez les paramètres à partir d’un autre emplacement. Par exemple, vous pouvez [publier sur cet emplacement avec Git](app-service-deploy-local-git.md). Vous pouvez effectuer un déploiement sur l’emplacement à partir d’une autre branche du dépôt, ou d’un dépôt différent. 

<a name="AboutConfiguration"></a>

## <a name="which-settings-are-swapped"></a>Quels sont les paramètres échangés ?
Lorsque vous clonez la configuration depuis un autre emplacement de déploiement, celle-ci est modifiable. Par ailleurs, au cours d’un échange, certains éléments de configuration suivent le contenu (éléments non propres à un emplacement) tandis que d’autres restent dans le même emplacement après l’échange (éléments propres à un emplacement). Les listes suivantes représentent les paramètres qui évoluent lorsque vous échangez les emplacements.

**Paramètres échangés**:

* Paramètres généraux, par exemple versions d’infrastructure, 32/64 bits, sockets web
* Paramètres d’application (peuvent être configurés pour respecter un emplacement)
* Chaînes de connexion (peuvent être configurées pour respecter un emplacement)
* Mappages de gestionnaires
* Paramètres de surveillance et de diagnostics
* Certificats publics
* Contenu WebJobs
* Connexions hybrides *
* Intégration au réseau virtuel *
* Points de terminaison de service *
* Azure CDN *

Fonctionnalités marquées avec un * vont être apportées rattachés à l’emplacement. 

**Paramètres non échangés** :

* Points de terminaison de publication
* Noms de domaine personnalisés
* Certificats privés et liaisons SSL
* Paramètres de mise à l'échelle
* Planificateurs WebJobs
* Restrictions d’adresse IP
* Always On
* Paramètres de protocole (HTTP**S**, version TLS, les certificats clients)
* Paramètres du journal de diagnostic
* CORS

<!-- VNET and hybrid connections not yet sticky to slot -->

Pour lier un paramètre d’application ou une chaîne de connexion à un emplacement particulier (non permuté), accédez à la page **Paramètres de l’application** de cet emplacement, puis cochez la case **Paramètre d’emplacement** correspondant aux éléments de configuration à lier à cet emplacement. Le fait de marquer un élément de configuration comme propre à un emplacement indique à App Service qu’il n’est pas permutable. 

![Paramètre de l’emplacement](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Permuter deux emplacements 
Vous pouvez permuter des emplacements de déploiement dans la page **Emplacements de déploiement (préversion)** de votre application. 

Vous pouvez également permuter des emplacements depuis les pages **Vue d’ensemble** et **Emplacements de déploiement**, mais l’ancienne expérience d’interface vous est actuellement proposée. Ce guide montre comment utiliser la nouvelle interface utilisateur dans la page **Emplacements de déploiement (préversion)**.

> [!IMPORTANT]
> Avant de permuter une application depuis un emplacement de déploiement sur un emplacement de production, assurez-vous que tous les paramètres sont configurés exactement comme vous le souhaitez dans la cible de l’échange.
> 
> 

Pour échanger les emplacements de déploiement, suivez ces étapes :

1. Accédez à la page **Emplacements de déploiement (préversion)** de votre application et cliquez sur **Permuter**.
   
    ![Bouton Swap](./media/web-sites-staged-publishing/SwapButtonBar.png)

    La boîte de dialogue **Échanger** regroupe les paramètres des emplacements source et cible sélectionnés qui vont être permutés.

2. Sélectionnez les emplacements **Source** et **Cible** souhaités. En général, la cible correspond à l’emplacement de production. Cliquez également sur les onglets **Modifications sources** et **Modifications cibles** pour vérifier que les changements de configuration à opérer correspondent à ce que vous attendez. Lorsque vous avez terminé, vous pouvez permuter les emplacements immédiatement en cliquant sur **Permuter**.

    ![Échange effectué](./media/web-sites-staged-publishing/SwapImmediately.png)

    Pour voir comment votre emplacement cible s’exécuterait avec les nouveaux paramètres, avant que l’échange soit réellement effectué, ne cliquez pas sur **Permuter**, mais suivez les instructions dans [Échange avec aperçu](#Multi-Phase).

3. Lorsque vous avez terminé, fermez la boîte de dialogue en cliquant sur **Fermer**.

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Échange avec aperçu (échange multiphase)

> [!NOTE]
> L’échange avec aperçu n’est pas pris en charge dans les applications web sur Linux.

Avant de passer la production en emplacement cible, contrôlez l’exécution de l’application avec les paramètres permutés, préalablement à l’échange. L’emplacement source est également initialisé avant la fin de l’échange, ce qui est aussi souhaitable pour les applications stratégiques.

Lorsque vous effectuez un échange avec aperçu, App Service effectue les opérations suivantes lorsque vous démarrez l’échange :

- Maintien de l’emplacement cible inchangé, de manière à ne pas influer sur la charge de travail existante dans cet emplacement (de production, par exemple).
- Application des éléments de configuration de l’emplacement cible à l’emplacement source, dont les chaînes de connexion spécifiques de l’emplacement et les paramètres de l’application.
- Redémarrage des processus Worker dans l’emplacement source à l’aide de ces éléments de configuration. Vous pouvez accéder à l’emplacement source et voir l’exécution de l’application avec les changements de configuration.

Si vous procédez à l’échange dans une étape distincte, App Service déplace l’emplacement source initialisée dans l’emplacement cible, et l’emplacement cible dans l’emplacement source. Si vous annulez l’échange, App Service réapplique les éléments de configuration de l’emplacement source à l’emplacement source.

Pour opérer un échange avec aperçu, suivez ces étapes.

1. Procédez comme indiqué dans [Échanger des emplacements de déploiement](#Swap), mais sélectionnez **Effectuer l’échange avec aperçu**.

    ![Échange avec aperçu](./media/web-sites-staged-publishing/SwapWithPreview.png)

    La boîte de dialogue montre comment la configuration de l’emplacement source est modifiée dans la phase 1, et comment les emplacements source et cible sont modifiés dans la phase 2.

2. Lorsque vous êtes prêt à démarrer l’échange, cliquez sur **Démarrer l’échange**.

    Lorsque la phase 1 est terminée, vous en êtes averti dans la boîte de dialogue. Affichez l’aperçu de l’échange dans l’emplacement source en accédant à `https://<app_name>-<source-slot-name>.azurewebsites.net`. 

3. Lorsque vous êtes prêt à effectuer l’échange en attente, sélectionnez **Terminer l’échange** dans **Action d’échange** et cliquez sur **Terminer l’échange**.

    Pour annuler un échange en attente, sélectionnez plutôt **Annuler l’échange** et cliquez sur **Annuler l’échange**.

4. Lorsque vous avez terminé, fermez la boîte de dialogue en cliquant sur **Fermer**.

Pour automatiser un échange multiphase, consultez Automatiser avec PowerShell.

<a name="Rollback"></a>

## <a name="roll-back-swap"></a>Restaurer l’échange
Si des erreurs se produisent dans l’emplacement cible (par exemple, l’emplacement de production) après une permutation d’emplacements, rétablissez ces deux emplacements comme ils étaient avant l’opération, en les intervertissant immédiatement.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Configurer l’échange automatique

> [!NOTE]
> L’échange automatique n’est pas pris en charge dans les applications web sur Linux.

L’échange automatique simplifie les scénarios DevOps impliquant un déploiement de l’application en continu, sans démarrage à froid ni temps d’arrêt pour les clients finals de l’application. Lorsque l’échange automatique d’un emplacement s’effectue en production, chaque fois que vous envoyez (push) des modifications de votre code à cet emplacement, App Service échange automatiquement l’application en production après son initialisation dans l’emplacement source.

   > [!NOTE]
   > Avant de configurer l’échange automatique pour l’emplacement de production, envisagez tout d’abord de tester l’échange automatique sur un emplacement cible hors production.
   > 

Pour configurer la fonction Échange automatique, procédez comme suit :

1. Accédez à la page des ressources de votre application. Sélectionnez **Emplacements de déploiement (préversion)** > *\<emplacement source souhaité >* > **Paramètres de l’application**.
   
2. Dans **Échange automatique**, sélectionnez **Activé**, ainsi que l’emplacement cible souhaité dans **Emplacement d’échange automatique**, puis cliquez sur **Enregistrer** dans la barre de commandes. 
   
    ![](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Exécutez un push de code sur l’emplacement source. L’échange automatique se produit peu après, et la mise à jour est appliquée dans l’URL de votre emplacement cible.

<a name="Warm-up"></a>

## <a name="custom-warm-up"></a>Personnaliser l’initialisation
Quand vous utilisez [Échange automatique](#Auto-Swap), certaines applications peuvent nécessiter quelques actions préparatoires personnalisées avant l’échange. L’élément de configuration `applicationInitialization` du fichier web.config vous permet de spécifier les actions d’initialisation personnalisées à exécuter. L’opération d’échange attend la fin de cette mise en route personnalisée pour procéder à la permutation avec l’emplacement cible. Voici un exemple de fragment web.config.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Pour plus d’informations sur la personnalisation de la `applicationInitialization` élément, consultez [échecs échange d’emplacement de déploiement les plus courants et comment les résoudre](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

Vous pouvez également personnaliser le comportement d'initialisation en utilisant un ou plusieurs [paramètres d'application](web-sites-configure.md) suivants :

- `WEBSITE_SWAP_WARMUP_PING_PATH`: chemin d’accès pour effectuer un test ping afin d'initialiser votre site. Ajoutez ce paramètre d’application en spécifiant un chemin d’accès personnalisé qui commence par une barre oblique comme valeur. Par exemple : `/statuscheck`. La valeur par défaut est `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Codes de réponse HTTP valides pour l’opération d'initialisation. Ajoutez ce paramètre d’application avec une liste séparée par des virgules de codes HTTP. Par exemple : `200,202`. Si le code d’état renvoyé ne figure pas dans la liste, les opérations d'initialisation et de basculement sont arrêtées. Par défaut, tous les codes de réponse sont valides.

## <a name="monitor-swap"></a>Superviser l’échange

Si l’exécution de l’opération d’échange prend beaucoup de temps, vous pouvez obtenir des informations sur cette opération dans le [journal d’activité](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Dans la page des ressources de votre application sur le portail, sélectionnez **Journal d’activité** dans le volet de navigation gauche.

Une opération d’échange s’affiche dans la requête de journal en tant que `Swap Web App Slots`. Vous pouvez la développer et sélectionner l’une des sous-opérations ou erreurs afin d’afficher le contenu en détail.

## <a name="route-traffic"></a>Acheminer le trafic

Par défaut, toutes les requêtes clientes vers les URL de production de l’application (`http://<app_name>.azurewebsites.net`) sont acheminées vers l’emplacement de production. Vous pouvez acheminer une partie du trafic vers un autre emplacement. Cette fonctionnalité est utile si vous avez besoin d’un retour d’expérience utilisateur pour une nouvelle mise à jour, mais que vous n’êtes pas prêt à la publier en production.

### <a name="route-production-traffic-automatically"></a>Acheminer le trafic de production automatiquement

Pour router le trafic de production automatiquement, suivez ces étapes :

1. Accédez à la page des ressources de votre application et sélectionnez **Emplacements de déploiement (préversion)**.

2. Dans la colonne **% de trafic** de l’emplacement vers lequel vous souhaitez acheminer le trafic, spécifiez un pourcentage (compris entre 0 et 100) pour représenter la quantité totale de trafic à diriger. Cliquez sur **Enregistrer**.

    ![](./media/web-sites-staged-publishing/RouteTraffic.png)

Une fois le paramètre enregistré, le pourcentage de clients spécifié est acheminé de manière aléatoire vers l’emplacement hors production. 

Lorsqu’un client est automatiquement acheminé vers un emplacement particulier, il est « épinglé » à cet emplacement pendant la durée de cette session cliente. Dans le navigateur client, vous pouvez voir à quel emplacement votre session est épinglée en examinant le cookie `x-ms-routing-name` dans les en-têtes HTTP. Une requête qui est acheminée vers l’emplacement « intermédiaire » contient le cookie `x-ms-routing-name=staging`. Une requête qui est acheminée vers l’emplacement de production a le cookie `x-ms-routing-name=self`.

### <a name="route-production-traffic-manually"></a>Acheminer le trafic de production manuellement

Parallèlement au routage automatique du trafic, App Service peut acheminer les requêtes vers un emplacement particulier. Cela est utile lorsque vous souhaitez que vos utilisateurs puissent choisir d’accepter ou de refuser votre application bêta. Pour router le trafic de production manuellement, vous utilisez le paramètre de requête `x-ms-routing-name`.

Par exemple, pour permettre aux utilisateurs de refuser votre application bêta, vous pouvez placer ce lien sur votre page web :

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

La chaîne `x-ms-routing-name=self` spécifie l’emplacement de production. Dès lors que le navigateur client accède au lien, non seulement il est redirigé vers l’emplacement de production, mais chaque requête ultérieure possède le cookie `x-ms-routing-name=self` qui épingle la session à l’emplacement de production.

Pour permettre aux utilisateurs d’accepter votre application bêta, définissez le même paramètre de requête pour le nom de l’emplacement hors production, par exemple :

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

<a name="Delete"></a>

## <a name="delete-slot"></a>Supprimer l’emplacement

Accédez à la page des ressources de votre application. Sélectionnez **Emplacements de déploiement (préversion)** > *\<emplacement à supprimer >* > **Vue d’ensemble**. Cliquez sur **Supprimer** dans la barre de commandes.  

![Supprimer un emplacement de déploiement](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatiser avec PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell est un module qui fournit des applets de commande pour gérer Azure via Windows PowerShell, notamment la prise en charge de la gestion des emplacements de déploiement des applications dans Azure App Service.

Pour plus d’informations sur l’installation et la configuration d’Azure PowerShell et sur l’authentification d’Azure PowerShell avec votre abonnement Azure, consultez la page [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).  

- - -
### <a name="create-web-app"></a>Créer une application web
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-slot"></a>Créer un emplacement
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>Initialiser un échange avec aperçu (échange multiphase) et appliquer la configuration de l’emplacement de destination à l’emplacement source
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>Annuler un échange en attente (échange avec aperçu) et restaurer la configuration de l’emplacement source
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>Échanger des emplacements de déploiement
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Surveiller les événements d’échange dans le journal d’activité
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

- - -
### <a name="delete-slot"></a>Supprimer l’emplacement
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-cli"></a>Automatiser avec CLI

Pour connaître les commandes de l’interface [Azure CLI](https://github.com/Azure/azure-cli) pour les emplacements de déploiement, consultez [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="next-steps"></a>Étapes suivantes
[Bloquer l’accès à des emplacements hors production](app-service-ip-restrictions.md)
