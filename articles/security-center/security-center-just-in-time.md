---
title: Accès Juste à temps à la machine virtuelle dans Azure Security Center | Microsoft Docs
description: Ce document montre comment l’accès juste-à-temps à la machine virtuelle dans Azure Security Center peut vous aider à contrôler l’accès à vos machines virtuelles Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 4b2b388fb736997010a6cbbdf93b23b77c7ef3a3
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603978"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Sécuriser vos ports de gestion avec un accès juste-à-temps

Si vous êtes sur le niveau tarifaire standard de Security Center (voir la [tarification](/azure/security-center/security-center-pricing)), vous pouvez verrouiller le trafic entrant sur vos machines virtuelles Azure avec un accès juste-à-temps (JIT) aux machines virtuelles. Cela réduit l’exposition aux attaques tout en offrant un accès facile pour la connexion aux machines virtuelles en cas de besoin.

> [!NOTE]
> L’accès juste-à-temps à la machine virtuelle Security Center prend en charge uniquement les machines virtuelles déployées par le biais d’Azure Resource Manager. Pour en savoir plus sur les modèles de déploiement de type Classic et Resource Manager, consultez [Déploiement Azure Resource Manager et déploiement Classic](../azure-resource-manager/management/deployment-models.md).

[!INCLUDE [security-center-jit-description](../../includes/security-center-jit-description.md)]

## <a name="configure-jit-on-a-vm"></a>Configurer une stratégie juste-à-temps sur une machine virtuelle

Trois méthodes s’offrent à vous pour configurer une stratégie juste-à-temps sur une machine virtuelle :

- [Configurer un accès juste-à-temps dans Azure Security Center](#jit-asc)
- [Configurer un accès juste-à-temps dans une page de machine virtuelle Azure](#jit-vm)
- [Configurer une stratégie juste-à-temps sur une machine virtuelle par programme](#jit-program)

## <a name="configure-jit-in-azure-security-center"></a>Configurer un accès juste-à-temps dans Azure Security Center

Dans Security Center, il est possible de configurer une stratégie JIT pour demander l’accès à une machine virtuelle.

### Configurer un accès JIT sur une machine virtuelle dans Security Center <a name="jit-asc"></a>

1. Ouvrez le tableau de bord **Security Center**.

1. Dans le volet gauche, sélectionnez **Accès Juste à temps à la machine virtuelle**.

    ![Vignette Accès juste-à-temps à la machine virtuelle](./media/security-center-just-in-time/just-in-time.png)

    La fenêtre **Accès Juste à temps à la machine virtuelle** s’ouvre et affiche des informations sur l’état de vos machines virtuelles :

    - **Configuré** : machines virtuelles configurées pour prendre en charge l’accès juste-à-temps à la machine virtuelle. Les données présentées concernent la semaine qui vient de s’écouler et incluent, pour chaque machine virtuelle le nombre de requêtes approuvées, la date et l’heure du dernier accès, mais aussi le dernier utilisateur.
    - **Recommandé** : machines virtuelles qui peuvent prendre en charge l’accès juste-à-temps à la machine virtuelle, mais qui n’ont pas été configurées dans cette optique. Nous vous recommandons d’activer le contrôle d’accès juste-à-temps à la machine virtuelle pour ces machines virtuelles.
    - **Aucune recommandation** : voici les raisons pour lesquelles une machine virtuelle peut ne pas être recommandée :
      - Groupe de sécurité réseau manquant : la solution juste-à-temps nécessite la présence d’un groupe de sécurité réseau.
      - Machine virtuelle classique : l’accès juste-à-temps à la machine virtuelle Security Center prend en charge uniquement les machines virtuelles déployées par le biais d’Azure Resource Manager. Le déploiement classique n’est pas pris en charge par la solution juste-à-temps. 
      - Autre : catégorie d’une machine virtuelle si la solution juste-à-temps est désactivée dans la stratégie de sécurité de l’abonnement ou du groupe de ressources, ou si la machine virtuelle ne dispose pas d’une adresse IP publique ni d’un groupe de sécurité réseau.

1. Sélectionnez l’onglet **Recommandé**.

1. Sous **MACHINE VIRTUELLE**, cliquez sur les machines virtuelles à activer. Une coche est alors placée en regard des machines virtuelles concernées.

      ![Activer l’accès juste-à-temps](./media/security-center-just-in-time/enable-just-in-time.png)

1. Cliquez sur **Activer JIT sur les machines virtuelles**. Un volet s’ouvre et affiche les ports par défaut recommandés par Azure Security Center :
    - 22 - SSH
    - 3389 - RDP
    - 5985 - WinRM 
    - 5986 - WinRM
1. Si vous le souhaitez, vous pouvez ajouter des ports personnalisés à la liste :

      1. Cliquez sur **Add**. La fenêtre **Ajouter la configuration du port** s’ouvre.
      1. Pour chaque port que vous choisissez de configurer (par défaut et personnalisé), vous pouvez personnaliser les paramètres suivants :
            - **Type de protocole** : le protocole qui est autorisé sur ce port lorsqu’une demande est approuvée.
            - **Adresses IP source autorisées** : les plages d’adresses IP qui sont autorisées sur ce port lorsqu’une demande est approuvée.
            - **Durée de demande maximale** : la fenêtre de temps maximale pendant laquelle un port spécifique peut être ouvert.

     1. Cliquez sur **OK**.

1. Cliquez sur **Enregistrer**.

> [!NOTE]
>Lorsque l’accès juste-à-temps aux machines virtuelles est activé pour une machine virtuelle, Azure Security Center crée des règles de « refus de tout le trafic entrant » pour les ports sélectionnés dans les groupes de sécurité réseau associés et le pare-feu Azure. Si d’autres règles avaient été créées pour les ports sélectionnés, les règles existantes sont prioritaires sur les nouvelles règles de « refus de tout le trafic entrant ». S’il n’y a aucune règle sur les ports sélectionnés, alors les nouvelles règles de « refus de tout le trafic entrant » sont prioritaires sur les groupes de sécurité réseau et le pare-feu Azure.


## <a name="request-jit-access-via-security-center"></a>Demander un accès JIT dans Security Center

Pour demander l’accès à une machine virtuelle avec Security Center :

1. Sous **Accès juste-à-temps à la machine virtuelle**, sélectionnez l’onglet **Configuré**.

1. Sous **Machine virtuelle**, cliquez sur les machines virtuelles pour lesquelles vous souhaitez demander l’accès. Une coche est alors placée en regard de la machine virtuelle concernée.

    - L’icône dans la colonne **Détails de la connexion** indique si l’accès juste-à-temps est activé sur le groupe de sécurité réseau ou le pare-feu. S’il est activé sur les deux, seule l’icône de pare-feu s’affiche.

    - La colonne **Détails de la connexion** fournit les informations nécessaires pour se connecter à la machine virtuelle et à ses ports ouverts.

      ![Demander l’accès juste-à-temps](./media/security-center-just-in-time/request-just-in-time-access.png)

1. Cliquez sur **Demander l’accès**. La fenêtre **Demander l’accès** s’ouvre.

      ![Détails de l’accès juste-à-temps](./media/security-center-just-in-time/just-in-time-details.png)

1. Sous **Demander l’accès**, configurez pour chaque machine virtuelle les ports que vous voulez ouvrir, les adresses IP sources sur lesquelles le port est ouvert, ainsi que la fenêtre de temps pendant laquelle le port est ouvert. La demande d’accès ne sera possible que pour les ports configurés dans la stratégie juste-à-temps. Chaque port dispose d’un délai maximal autorisé issu de la stratégie juste-à-temps.

1. Cliquez sur **Ports ouverts**.

> [!NOTE]
> Si un utilisateur demande l’accès alors qu’il se trouve derrière un proxy, l’option **Mon IP** risque de ne pas fonctionner. Il peut se révéler nécessaire de définir la plage complète d’adresses IP de l’organisation.



## <a name="edit-a-jit-access-policy-via-security-center"></a>Modifier une stratégie d’accès JIT dans Security Center

Vous pouvez modifier la stratégie juste-à-temps d’une machine virtuelle en ajoutant et en configurant un port à protéger pour cette machine virtuelle, ou en modifiant tout autre paramètre lié à un port déjà protégé.

Pour modifier une stratégie juste-à-temps existante d’une machine virtuelle :

1. Dans l’onglet **Configuré**, sous **Machines virtuelles**, sélectionnez la machine virtuelle à laquelle ajouter un port en cliquant sur les trois points qui se trouvent sur la ligne de cette machine virtuelle. 

1. Sélectionnez **Modifier**.

1. Sous **JIT VM access configuration** (Configuration de l’accès juste-à-temps à la machine virtuelle), vous pouvez soit modifier les paramètres existants d’un port déjà protégé, soit ajouter un nouveau port personnalisé. 
  ![accès JIT à la machine virtuelle](./media/security-center-just-in-time/edit-policy.png)



## <a name="audit-jit-access-activity-in-security-center"></a>Auditer l’activité d’accès JIT dans Security Center

Vous pouvez obtenir des informations sur les activités des machines virtuelles à l’aide de la recherche dans les journaux. Pour consulter les journaux d’activité :

1. Sous **Accès juste-à-temps à la machine virtuelle**, sélectionnez l’onglet **Configuré**.
2. Sous **Machines virtuelles**, sélectionnez la machine virtuelle dont vous souhaitez consulter les informations en cliquant sur l’ellipse qui se trouve sur la ligne de cette machine virtuelle, puis sélectionnez **Journal d’activité** dans le menu. Le **journal d’activité** s’ouvre.

   ![Sélectionner un journal d’activité](./media/security-center-just-in-time/select-activity-log.png)

   **Journal d’activité** fournit une vue filtrée des opérations précédentes pour cette machine virtuelle, ainsi que l’abonnement, la date et l’heure.

Vous pouvez télécharger les informations du journal en sélectionnant **Cliquez ici pour télécharger tous les éléments au format CSV**.

Modifiez les filtres et sélectionnez **Appliquer** pour créer une recherche et un journal.



## Configurer un accès JIT sur la page d’une machine virtuelle Azure <a name="jit-vm"></a>

Dans un souci de simplicité, il est possible de se connecter à une machine virtuelle en accès JIT, directement sur la page de cette machine virtuelle dans Security Center.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-page"></a>Configurer un accès JIT sur une machine virtuelle sur la page de la machine virtuelle Azure

Pour faciliter le déploiement de l’accès juste-à-temps entre vos machines virtuelles, vous pouvez configurer une machine virtuelle pour autoriser uniquement l’accès juste-à-temps directement à partir de la machine virtuelle.

1. Dans le **portail Azure**, recherchez et sélectionnez [Machines virtuelles](https://ms.portal.azure.com). 
2. Sélectionnez la machine virtuelle pour laquelle vous voulez limiter l’accès juste-à-temps.
3. Dans le menu, sélectionnez **Configuration**.
4. Sous **Accès juste-à-temps**, sélectionnez **Activer l’accès juste-à-temps**. 

Cela permet un accès juste-à-temps pour la machine virtuelle en utilisant les paramètres suivants :

- Serveurs Windows :
    - Port RDP 3389
    - Trois heures d’accès autorisé maximum
    - L’option Adresses IP sources autorisées est définie sur Toutes
- Serveurs Linux :
    - Port SSH 22
    - Trois heures d’accès autorisé maximum
    - L’option Adresses IP sources autorisées est définie sur Toutes
     
Si l’accès juste-à-temps est déjà activé sur une machine virtuelle, vous pouvez le voir quand vous accédez à sa page de configuration, et vous pouvez utiliser le lien pour ouvrir la stratégie dans Azure Security Center afin d’afficher et de changer les paramètres.

![jit config in vm](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-an-azure-vms-page"></a>Demander un accès juste-à-temps sur une machine virtuelle via la page d’une machine virtuelle Azure

Dans le portail Azure, lorsque vous essayez de vous connecter à une machine virtuelle, Azure vérifie que vous disposez d’une stratégie d’accès juste-à-temps configurée sur cette machine virtuelle.

- Si une stratégie JIT est configurée sur la machine virtuelle, vous pouvez cliquer sur **Demander l’accès** pour accorder l’accès conformément à la stratégie JIT définie pour la machine virtuelle. 

  >![Demande d’accès juste-à-temps](./media/security-center-just-in-time/jit-request.png)

  L’accès est demandé avec les paramètres par défaut suivants :

  - **IP source** : « Toutes » (*) (cette valeur n’est pas modifiable)
  - **Intervalle de temps** : Trois heures (cette valeur n’est pas modifiable) <!--Isn't this set in the policy-->
  - **Numéro de port** : port RDP 3389 pour Windows/port 22 pour Linux (cette valeur est modifiable)

    > [!NOTE]
    > Une fois la demande approuvée pour une machine virtuelle protégée par le pare-feu Azure, Security Center fournit à l’utilisateur les informations de connexion appropriées (mappage de ports provenant de la table DNAT) à utiliser pour se connecter à la machine virtuelle.

- Si aucune stratégie JIT n’est configurée sur une machine virtuelle, vous êtes invité à configurer une stratégie JIT sur celle-ci.

  ![invite jit](./media/security-center-just-in-time/jit-prompt.png)

## Configurer une stratégie juste-à-temps sur une machine virtuelle par programme <a name="jit-program"></a>

Vous pouvez configurer et utiliser la fonctionnalité juste-à-temps via les API REST et via PowerShell.

### <a name="jit-vm-access-via-rest-apis"></a>Accès juste-à-temps aux machines virtuelles via les API REST

La fonctionnalité d’accès aux machines virtuelles juste-à-temps peut être utilisée via l’API Azure Security Center. Vous pouvez obtenir des informations sur les machines virtuelles configurées, en ajouter de nouvelles, demander l’accès à une machine virtuelle ou effectuer d’autres actions via cette API. Pour plus d’informations sur l’API REST juste-à-temps, consultez [Jit Network Access Policies](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies).

### <a name="jit-vm-access-via-powershell"></a>Accès juste-à-temps aux machines virtuelles via PowerShell

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

### <a name="request-access-to-a-vm-via-powershell"></a>Demander l’accès à une machine virtuelle via PowerShell

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

Pour plus d’informations, consultez la [documentation relative aux cmdlets PowerShell](https://docs.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-overview).


## <a name="automatic-cleanup-of-redundant-jit-rules"></a>Nettoyage automatique des règles JIT redondantes 

Chaque fois que vous mettez à jour une stratégie JIT, un outil de nettoyage s’exécute automatiquement pour vérifier la validité de votre ensemble de règles. L’outil recherche les incompatibilités entre les règles de votre stratégie et les règles du groupe de sécurité réseau. Si l’outil de nettoyage détecte une incompatibilité, il détermine la cause et, lorsque cela ne présente aucun risque, supprime les règles intégrées qui ne sont plus nécessaires. Le nettoyeur ne supprime jamais les règles que vous avez créées.

Exemples de scénarios où le nettoyeur peut supprimer une règle intégrée :

- Lorsque deux règles avec des définitions identiques existent et que l’une d’elles a une priorité plus élevée que l’autre (ce qui signifie que la règle de priorité la plus basse ne sera jamais utilisée)
- Lorsqu’une description de règle comprend le nom d’une machine virtuelle qui ne correspond pas à l’adresse IP de destination de la règle 


## <a name="next-steps"></a>Étapes suivantes

Cet article vous a fait découvrir en quoi l’accès juste-à-temps à la machine virtuelle dans Security Center peut vous aider à contrôler l’accès à vos machines virtuelles Azure.

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
- [Gestion des recommandations de sécurité](security-center-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
- [Surveillance de l’intégrité de la sécurité](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.