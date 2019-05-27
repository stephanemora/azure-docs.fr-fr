---
title: Accès Juste à temps à la machine virtuelle dans Azure Security Center | Microsoft Docs
description: Ce document montre comment l’accès juste-à-temps à la machine virtuelle dans Azure Security Center peut vous aider à contrôler l’accès à vos machines virtuelles Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/28/2019
ms.author: v-mohabe
ms.openlocfilehash: b07a89491343220c5c3411b5fc525f9b43f54e30
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968479"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Gérer l’accès juste-à-temps à la machine virtuelle

L’accès juste-à-temps (JIT) à la machine virtuelle peut être utilisé pour verrouiller le trafic entrant vers vos machines virtuelles Azure, réduire l’exposition aux attaques et faciliter la connexion aux machines virtuelles si nécessaire.

> [!NOTE]
> La fonctionnalité juste-à-temps est disponible pour le niveau Standard de Security Center.  Consultez [Tarification](security-center-pricing.md) pour en savoir plus sur les niveaux tarifaires de Security Center.
>
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="attack-scenario"></a>Scénario d’attaque

Les attaques par force brute ciblent généralement les ports de gestion cible pour accéder à une machine virtuelle. S’il réussit, un attaquant peut prendre le contrôle sur la machine virtuelle et créer une brèche dans votre environnement.

Pour réduire l’exposition aux attaques par force brute, vous pouvez limiter la durée d’ouverture d’un port. Les ports de gestion n’ont pas besoin d’être toujours ouverts. Ils doivent uniquement être ouverts lorsque vous êtes connecté à la machine virtuelle, par exemple pour effectuer des tâches de maintenance ou de gestion. Quand la fonctionnalité juste-à-temps est activée, Security Center utilise des règles de [groupe de sécurité réseau](../virtual-network/security-overview.md#security-rules) (NSG) qui limitent l’accès aux ports de gestion pour qu’ils ne soient pas la cible d’attaquants.

![Scénario juste-à-temps](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Fonctionnement de l’accès JIT

Lorsque la fonctionnalité juste-à-temps est activée, Security Center verrouille le trafic entrant vers vos machines virtuelles Azure en créant une règle de groupe de sécurité réseau (NSG). Vous sélectionnez les ports de la machine virtuelle pour lesquels le trafic entrant sera verrouillé. Ces ports sont contrôlés par la solution juste-à-temps.

Quand un utilisateur demande l’accès à une machine virtuelle, Security Center vérifie que cet utilisateur dispose des autorisations [Contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/role-assignments-portal.md) qui lui permettent de demander l’accès à une machine virtuelle. Si la demande est approuvée, Security Center configure automatiquement les Groupes de sécurité réseau (NSG) afin d’autoriser le trafic entrant vers les ports sélectionnés et vers les adresses IP source demandées, pendant la durée spécifiée. Après expiration du délai, Security Center restaure les groupes de sécurité réseau à leur état précédent. Ces connexions déjà établies ne sont cependant pas interrompues.

> [!NOTE]
> L’accès juste-à-temps à la machine virtuelle Security Center prend en charge uniquement les machines virtuelles déployées par le biais d’Azure Resource Manager. Pour en savoir plus sur les modèles de déploiement de type Classic et Resource Manager, consultez [Déploiement Azure Resource Manager et déploiement Classic](../azure-resource-manager/resource-manager-deployment-model.md).
>
>

Vous pouvez accéder à JIT via :
- [Accès JIT dans Azure Security Center](#jit-asc)
- [Accès JIT dans un panneau de machine virtuelle Azure](#jit-vm)

## Accès JIT dans Azure Security Center<a name="jit-asc"></a>

1. Ouvrez le tableau de bord **Security Center**.

2. Dans le volet gauche, sélectionnez **Accès Juste à temps à la machine virtuelle**.

![Vignette Accès juste-à-temps à la machine virtuelle](./media/security-center-just-in-time/just-in-time.png)

La fenêtre **Accès juste-à-temps à la machine virtuelle** s’ouvre.

![Vignette Accès juste-à-temps à la machine virtuelle](./media/security-center-just-in-time/just-in-time-access.png)

La section **Accès juste-à-temps à la machine virtuelle** fournit des informations sur l’état de vos machines virtuelles :

- **Configuré** : machines virtuelles configurées pour prendre en charge l’accès juste-à-temps à la machine virtuelle. Les données présentées concernent la semaine qui vient de s’écouler et incluent, pour chaque machine virtuelle le nombre de requêtes approuvées, la date et l’heure du dernier accès, mais aussi le dernier utilisateur.
- **Recommandé** : machines virtuelles qui peuvent prendre en charge l’accès juste-à-temps à la machine virtuelle, mais qui n’ont pas été configurées dans cette optique. Nous vous recommandons d’activer le contrôle d’accès juste-à-temps à la machine virtuelle pour ces machines virtuelles. Consultez [Configuration d’une stratégie d’accès juste-à-temps](#jit-config).
- **Aucune recommandation** : voici les raisons pour lesquelles une machine virtuelle peut ne pas être recommandée :
  - Groupe de sécurité réseau manquant : la solution juste-à-temps nécessite la présence d’un groupe de sécurité réseau.
  - Machine virtuelle classique : l’accès juste-à-temps à la machine virtuelle Security Center prend en charge uniquement les machines virtuelles déployées par le biais d’Azure Resource Manager. Le déploiement classique n’est pas pris en charge par la solution juste-à-temps.
  - Autre : catégorie d’une machine virtuelle si la solution juste-à-temps est désactivée dans la stratégie de sécurité de l’abonnement ou du groupe de ressources, ou si la machine virtuelle ne dispose pas d’une adresse IP publique ni d’un groupe de sécurité réseau.

### Configuration d’une stratégie d’accès JIT<a name="jit-config"></a>

Pour sélectionner les machines virtuelles que vous souhaitez activer :

1. Sous **Accès juste-à-temps à la machine virtuelle**, sélectionnez l’onglet **Recommandé**.

   ![Activer l’accès juste-à-temps](./media/security-center-just-in-time/enable-just-in-time-access.png)

2. Sous **MACHINE VIRTUELLE**, sélectionnez les machines virtuelles à activer. Une coche est alors placée en regard des machines virtuelles concernées.
3. Sélectionnez **Enable JIT on VMs** (Activer JIT sur les machines virtuelles).
   1. Ce panneau affiche les ports par défaut recommandés par Azure Security Center :
      - 22 - SSH
      - 3389 - RDP
      - 5985 - WinRM 
      - 5986 - WinRM
   2. Vous pouvez également configurer des ports personnalisés. Pour ce faire, sélectionnez **Ajouter**. 
   3. Dans la section **Ajouter une configuration de port**, pour chaque port que vous choisissez de configurer (par défaut et personnalisé), vous pouvez personnaliser les paramètres suivants :
      - **Type de protocole** : le protocole qui est autorisé sur ce port lorsqu’une demande est approuvée.
      - **Adresses IP source autorisées** : les plages d’adresses IP qui sont autorisées sur ce port lorsqu’une demande est approuvée.
      - **Durée de demande maximale** : la fenêtre de temps maximale pendant laquelle un port spécifique peut être ouvert.

4. Sélectionnez **Enregistrer**.


> [!NOTE]
>Lors de la machine virtuelle l’accès JIT est activé pour une machine virtuelle, Azure Security Center crée des règles de « refuser tout le trafic entrant » pour les ports sélectionnés dans les groupes de sécurité réseau associés. Si d’autres règles avaient été créés pour les ports sélectionnés, les règles existantes sont prioritaires sur les nouvelles règles de « refuser tout le trafic entrant ». S’il n’y a aucune règle existant sur les ports sélectionnés, puis les nouvelles règles de « refuser tout le trafic entrant » prioritaires supérieure dans les groupes de sécurité réseau.
>

### <a name="request-jit-access-to-a-vm"></a>Demander l’accès JIT à une machine virtuelle

Pour demander l’accès à une machine virtuelle :
1.  Sous **Accès juste-à-temps à la machine virtuelle**, sélectionnez l’onglet **Configuré**.
2.  Sous **Machines virtuelles**, sélectionnez les machines virtuelles pour lesquelles vous souhaitez activer l’accès juste-à-temps.
3.  Sélectionnez **Demander l’accès**. 
  ![demander l’accès à une machine virtuelle](./media/security-center-just-in-time/request-access-to-a-vm.png)
4.  Sous **Demander l’accès**, configurez pour chaque machine virtuelle les ports que vous voulez ouvrir, les adresses IP sources sur lesquelles le port est ouvert, ainsi que la fenêtre de temps pendant laquelle le port est ouvert. La demande d’accès ne sera possible que pour les ports configurés dans la stratégie juste-à-temps. Chaque port dispose d’un délai maximal autorisé issu de la stratégie juste-à-temps.
5.  Sélectionnez **Ports ouverts**.

> [!NOTE]
> Si un utilisateur demande l’accès alors qu’il se trouve derrière un proxy, l’option **Mon IP** risque de ne pas fonctionner. Il peut se révéler nécessaire de définir la plage complète d’adresses IP de l’organisation.

### <a name="editing-a-jit-access-policy"></a>Modification d’une stratégie d’accès JIT

Vous pouvez modifier la stratégie juste-à-temps d’une machine virtuelle en ajoutant et en configurant un port à protéger pour cette machine virtuelle, ou en modifiant tout autre paramètre lié à un port déjà protégé.

Pour modifier une stratégie juste-à-temps existante d’une machine virtuelle :
1. Dans l’onglet **Configuré**, sous **Machines virtuelles**, sélectionnez la machine virtuelle à laquelle ajouter un port en cliquant sur les trois points qui se trouvent sur la ligne de cette machine virtuelle. 
2. Sélectionnez **Modifier**.
3. Sous **JIT VM access configuration** (Configuration de l’accès juste-à-temps à la machine virtuelle), vous pouvez soit modifier les paramètres existants d’un port déjà protégé, soit ajouter un nouveau port personnalisé. Pour plus d’informations, consultez [Configuration d’une stratégie d’accès juste-à-temps](#jit-config). 
  ![accès JIT à la machine virtuelle](./media/security-center-just-in-time/edit-policy.png)

## Accès JIT dans un panneau de machine virtuelle Azure<a name="jit-vm"></a>

Pour votre commodité, vous pouvez vous connecter à une machine virtuelle à l’aide de JIT, directement à partir du panneau de machine virtuelle dans Azure.

### <a name="configuring-a-just-in-time-access-policy"></a>Configuration d’une stratégie d’accès juste-à-temps 
Pour faciliter le déploiement de l’accès juste-à-temps entre vos machines virtuelles, vous pouvez configurer une machine virtuelle pour autoriser uniquement l’accès juste-à-temps directement à partir de la machine virtuelle.

1. Dans le portail Azure, sélectionnez **Machines virtuelles**.
2. Cliquez sur la machine virtuelle pour laquelle vous voulez limiter l’accès juste-à-temps.
3. Dans le menu, cliquez sur **Configuration**.
4. Sous **Accès juste-à-temps**, cliquez sur **Activer une stratégie juste-à-temps**. 

Cela permet un accès juste-à-temps pour la machine virtuelle en utilisant les paramètres suivants :

- Serveurs Windows :
    - Port RDP 3389
    - 3 heures d’accès autorisé maximum
    - Les adresses IP sources autorisées est définie sur Any
- Serveurs Linux :
    - Port SSH 22
    - 3 heures d’accès autorisé maximum
    - Les adresses IP sources autorisées est définie sur Any
     
Si l’accès juste-à-temps est déjà activé sur une machine virtuelle, vous pouvez le voir quand vous accédez à sa page de configuration, et vous pouvez utiliser le lien pour ouvrir la stratégie dans Azure Security Center afin d’afficher et de changer les paramètres.

![jit config in vm](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="requesting-jit-access-to-a-vm"></a>Demande d’accès JIT à une machine virtuelle

Dans le portail Azure, lorsque vous essayez de vous connecter à une machine virtuelle, Azure vérifie que vous disposez d’une stratégie d’accès juste-à-temps configurée sur cette machine virtuelle.

- Si vous ne disposez pas d’accès JIT configuré sur une machine virtuelle, vous êtes invité à configurer une stratégie JIT.

  ![invite jit](./media/security-center-just-in-time/jit-prompt.png)

- Si vous ne disposez pas d’une stratégie JIT configurée sur la machine virtuelle, vous pouvez cliquer sur **Demander l’accès** pour vous permettre d’avoir accès conformément à la stratégie JIT définie pour la machine virtuelle. L’accès est demandé avec les paramètres par défaut suivants :
    - **adresse IP source**: 'Any' (*) (ne peut pas être modifié)
    - **intervalle de temps**: 3 heures (ne peut pas être modifié)
    - **le numéro de port** RDP le port 3389 pour Windows / port 22 pour Linux (vous pouvez modifier le numéro de port dans le **se connecter à la machine virtuelle** boîte de dialogue.)


  >![Demander l’accès jit](./media/security-center-just-in-time/jit-request-access.png)

## <a name="auditing-jit-access-activity"></a>Audit de l’activité d’accès JIT

Vous pouvez obtenir des informations sur les activités des machines virtuelles à l’aide de la recherche dans les journaux. Pour consulter les journaux d’activité :

1. Sous **Accès juste-à-temps à la machine virtuelle**, sélectionnez l’onglet **Configuré**.
2. Sous **Machines virtuelles**, sélectionnez la machine virtuelle dont vous souhaitez consulter les informations en cliquant sur l’ellipse qui se trouve sur la ligne de cette machine virtuelle. Un menu s’ouvre.
3. Sélectionnez **Journal d’activité** dans le menu. Cette opération ouvre **Journal d’activité**.

   ![Sélectionner un journal d’activité](./media/security-center-just-in-time/select-activity-log.png)

   **Journal d’activité** fournit une vue filtrée des opérations précédentes pour cette machine virtuelle, ainsi que l’abonnement, la date et l’heure.

Vous pouvez télécharger les informations du journal en sélectionnant **Cliquez ici pour télécharger tous les éléments au format CSV**.

Modifiez les filtres et sélectionnez **Appliquer** pour créer une recherche et un journal.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Autorisations nécessaires pour configurer et utiliser JIT

Vous devez définir ces privilèges pour permettre à un utilisateur de configurer ou de modifier une stratégie JIT pour une machine virtuelle.

Assignez ces *actions* au rôle : 
- Sur l’étendue d’un abonnement ou d’un groupe de ressources qui est associé à la machine virtuelle :
  - Microsoft.Security/locations/jitNetworkAccessPolicies/write
- Sur l’étendue d’un abonnement, d’un groupe de ressources ou d’une machine virtuelle :
  - Microsoft.Compute/virtualMachines/write 

Définissez ces privilèges pour permettre à un utilisateur de demander un accès JIT à une machine virtuelle : Assignez ces *actions* à l’utilisateur :
- Sur l’étendue d’un abonnement ou d’un groupe de ressources qui est associé à la machine virtuelle :
  - Microsoft.Security/locations/{the_location_of_the_VM}/jitNetworkAccessPolicies/ initiate/action
- Sur l’étendue d’un abonnement, d’un groupe de ressources ou d’une machine virtuelle :
  - Microsoft.Compute/virtualMachines/read



## <a name="use-jit-programmatically"></a>Utiliser JIT par programmation
Vous pouvez configurer et utiliser la fonctionnalité juste-à-temps via les API REST et via PowerShell.

### <a name="using-just-in-time-vm-access-via-rest-apis"></a>Utilisation de l’accès aux machines virtuelles juste-à-temps via des API REST

La fonctionnalité d’accès aux machines virtuelles juste-à-temps peut être utilisée via l’API Azure Security Center. Vous pouvez obtenir des informations sur les machines virtuelles configurées, en ajouter de nouvelles, demander l’accès à une machine virtuelle ou effectuer d’autres actions via cette API. Pour plus d’informations sur l’API REST juste-à-temps, consultez [Jit Network Access Policies](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies).

### <a name="using-jit-vm-access-via-powershell"></a>Utilisation de l’accès juste-à-temps aux machines virtuelles via PowerShell 

Pour utiliser la solution d’accès juste-à-temps aux machines virtuelles via PowerShell, utilisez les cmdlets officiels d’Azure Security Center PowerShell, et plus spécifiquement `Set-AzJitNetworkAccessPolicy`.

L’exemple suivant définit une stratégie d’accès juste-à-temps aux machines virtuelles pour une machine virtuelle spécifique et définit les éléments suivants :
1.  Fermez les ports 22 et 3389.
2.  Définissez une fenêtre de temps maximale de 3 heures pour chaque afin de pouvoir être ouverts pour chaque demande approuvée.
3.  Cela permet à l’utilisateur demandant un accès de contrôler les adresses IP source et cela lui permet d’établir une session avec succès après une demande d’accès juste-à-temps approuvée.

Pour ce faire, exécutez la commande suivante dans PowerShell :

1.  Attribuez une variable qui conserve la stratégie d’accès juste-à-temps à une machine virtuelle pour une machine virtuelle :

        $JitPolicy = (@{
         id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})

2.  Insérez la stratégie d’accès juste-à-temps à une machine virtuelle d’une machine virtuelle dans le tableau :
    
        $JitPolicyArr=@($JitPolicy)

3.  Configurez la stratégie d’accès juste-à-temps à une machine virtuelle de la machine virtuelle sélectionnée :
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

#### <a name="requesting-access-to-a-vm"></a>Demande d’accès à une machine virtuelle

Dans l’exemple suivant, vous pouvez voir une demande d’accès juste-à-temps à une machine virtuelle pour une machine virtuelle spécifique. Dans cette dernière, l’ouverture du port 22 est requise pour une adresse IP et une durée spécifiques :

Exécutez la commande suivante dans PowerShell :
1.  Configurez les propriétés de la demande d’accès à une machine virtuelle

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  Insérez les paramètres de la demande d’accès à la machine virtuelle dans un tableau :

        $JitPolicyArr=@($JitPolicyVm1)
3.  Envoyez la demande d’accès (utilisez l’ID de ressource obtenu à l’étape 1)

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Pour plus d’informations, consultez la documentation sur la cmdlet PowerShell.


## <a name="next-steps"></a>Étapes suivantes
Cet article vous a fait découvrir en quoi l’accès juste-à-temps à la machine virtuelle dans Security Center peut vous aider à contrôler l’accès à vos machines virtuelles Azure.

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
- [Gestion des recommandations de sécurité](security-center-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
- [Surveillance de l’intégrité de la sécurité](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
- [Gestion et résolution des alertes de sécurité](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
- [Surveillance des solutions de partenaire](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaires.
- [FAQ Security Center](security-center-faq.md) : découvrez les réponses aux questions les plus souvent posées à propos de l’utilisation de ce service.
- [Blog sur la sécurité Azure](https://blogs.msdn.microsoft.com/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.

