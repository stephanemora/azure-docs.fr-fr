---
title: Utiliser Application Gateway pour protéger vos applications web sur Azure VMware Solution
description: Configurez Azure Application Gateway pour exposer en toute sécurité vos applications web s’exécutant sur Azure VMware Solution.
ms.topic: how-to
ms.date: 07/31/2020
ms.openlocfilehash: b63b7348419500504cb45917d741cc17e2b8a622
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951449"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Utiliser Application Gateway pour protéger vos applications web sur Azure VMware Solution

[Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) est un équilibreur de charge de trafic web de couche 7 qui vous permet de gérer le trafic vers vos applications web. Il offre de nombreuses fonctionnalités telles que l’affinité de session basée sur des cookies, le routage basé sur l’URL et le pare-feu d’applications web (WAF) (pour obtenir la liste complète des fonctionnalités, consultez [Fonctionnalités Azure Application Gateway](../application-gateway/features.md)). Elle est proposée dans deux versions, v1 et v2. Toutes deux ont été testées avec des applications web s’exécutant sur Azure VMware Solution.

Cet article aborde un scénario courant utilisant Application Gateway devant une batterie de serveurs web avec un ensemble de configurations et recommandations pour protéger une application web s’exécutant sur Azure VMware Solution. 

## <a name="topology"></a>Topologie
Comme illustré dans la figure suivante, Application Gateway peut être utilisé pour protéger des machines virtuelles Azure IaaS, des groupes de machines virtuelles identiques Azure ou des serveurs locaux. Application Gateway traite des machines virtuelles Azure VMware Solution comme des serveurs locaux.

![Application Gateway protège les machines virtuelles de la solution VMware Azure.](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-protects.png)

> [!IMPORTANT]
> Azure Application Gateway est actuellement la seule méthode prise en charge pour exposer des applications web s’exécutant sur des machines virtuelles Azure VMware Solution.

Le diagramme suivant illustre le scénario de test utilisé pour valider Application Gateway avec des applications web Azure VMware Solution.

![Intégration d’Application Gateway avec Azure VMware Solution exécutant des applications web.](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-avs-scenario.png)

L’instance Application Gateway est déployée sur le hub dans un sous-réseau dédié. Comme elle a une adresse IP publique Azure, l’activation de la protection DDoS Standard pour le réseau virtuel est recommandée. Le serveur web est hébergé sur un cloud privé Azure VMware Solution derrière des routeurs NSX T0 et T1. Azure VMware Solution utilise le [Service Global Reach d’ExpressRoute](../expressroute/expressroute-global-reach.md) pour activer la communication avec le hub et les systèmes locaux.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif.
- Cloud privé Azure VMware Solution déployé et en cours d’exécution.

## <a name="deployment-and-configuration"></a>Déploiement et configuration

1. Dans le portail Azure, recherchez **Application Gateway**, puis sélectionnez **Créer une passerelle Application Gateway**.

2. Fournissez les informations de base, comme dans l’illustration suivante, puis sélectionnez **Suivant : Serveurs frontaux>** . 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/create-app-gateway.png" alt-text="Création d’une passerelle Application Gateway":::

3. Choisissez le type d’adresse IP frontale. Pour l’option Publique, choisissez une adresse IP publique ou créez-en une. Sélectionnez **Suivant : Serveurs principaux>** .

    > [!NOTE]
    > Seules les références (SKU) Standard et de pare-feu d’applications web (WAF) sont prises en charge pour des serveurs frontaux privés.

4. Ensuite, ajoutez un pool principal décrivant un ensemble d’instances faisant partie de l’application ou du service (dans ce cas, des machines virtuelles s’exécutant sur une infrastructure Azure VMware Solution). Fournissez les détails des serveurs web qui s’exécutent sur le cloud privé Azure VMware Solution, puis sélectionnez **Ajouter**. Ensuite, sélectionnez **Suivant : Configuration>** .

1. Sous l’onglet **Configuration**, sélectionnez **Ajouter une règle d’acheminement**.

6. Sous l’onglet **Écouteur**, entrez les détails de l’écouteur. Si le protocole HTTPS est sélectionné, un certificat doit être fourni, soit à partir d’un fichier PFX, soit à partir d’un certificat existant à d’Azure Key Vault. 

7. Sélectionnez l’onglet **Cibles de back-end**, puis le pool principal créé précédemment. Pour le champ **Paramètres HTTP**, sélectionnez **Ajouter nouveau**.

8. Configurez les paramètres HTTP. Sélectionnez **Ajouter**.

9. Si vous souhaitez configurer des règles basées sur le chemin d’accès, sélectionnez **Ajouter plusieurs cibles pour créer une règle basée sur le chemin**. 

10. Ajoutez une règle basée sur un chemin d’accès, et sélectionnez **Ajouter**. Répétez cette opération pour ajouter d’autres règles basées sur un chemin d’accès. 

11. Lorsque vous avez fini d’ajouter des règles basées sur un chemin d’accès, sélectionnez de nouveau **Ajouter**. Ensuite, sélectionnez **Suivant : Balises>** . 

12. Ajoutez les balises souhaitées. Sélectionnez **Suivant : Vérifier + créer**.

13. Une validation est exécutée sur votre passerelle Application Gateway. En cas de réussite, sélectionnez **Créer** pour opérer le déploiement.

## <a name="configuration-examples"></a>Exemples de configuration

Dans cette section, vous allez apprendre à configurer Application Gateway avec des machines virtuelles Azure VMware Solution comme pools principaux pour les cas d’utilisation suivants : 

- [Hébergement de plusieurs sites](#hosting-multiple-sites)
- [Routage par URL](#routing-by-url)

### <a name="hosting-multiple-sites"></a>Hébergement de plusieurs sites

Vous pouvez utiliser le portail Azure pour configurer l’hébergement de plusieurs sites web quand vous créez une passerelle Application Gateway. Dans ce tutoriel, vous allez définir des pools d’adresses principaux à l’aide de machines virtuelles s’exécutant sur un cloud privé Azure VMware Solution sur une passerelle Application Gateway existante. La passerelle Application Gateway fait partie d’un réseau virtuel hub, comme décrit dans [Intégrer Azure VMware Solution dans une architecture en étoile](concepts-hub-and-spoke.md). Ce tutoriel part du principe que vous avez plusieurs domaines et utilise les exemples de www.contoso.com et www.fabrikam.com.

1. Créez les machines virtuelles. Sur le cloud privé Azure VMware Solution, créez deux pools de machines virtuelles. L’une représentera Contoso et l’autre Fabrikam. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs.png" alt-text="Création d’une passerelle Application Gateway":::

    Pour illustrer ce tutoriel, nous avons utilisé Windows Server 2016 avec le rôle Internet Information Services (IIS) installé. Une fois les machines virtuelles installées, exécutez les commandes PowerShell suivantes pour configurer IIS sur chaque machine virtuelle. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. Ajouter les pools principaux Dans une instance Application Gateway existante, sélectionnez **Pools principaux** dans le menu à gauche, sélectionnez **Ajouter**, puis entrez les détails des nouveaux pools. Sélectionnez **Ajouter** dans le volet droit.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-02.png" alt-text="Création d’une passerelle Application Gateway" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-02.png":::

3. Dans la section **Écouteurs**, créez un écouteur pour chaque site web. Entrez les détails de chaque écouteur, puis sélectionnez **Ajouter**.

4. Dans le volet de navigation à gauche, sélectionnez **Paramètres HTTP**, puis choisissez **Ajouter** dans le volet gauche. Renseignez les détails pour créer un paramètre HTTP, puis sélectionnez **Enregistrer**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-03.png" alt-text="Création d’une passerelle Application Gateway" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-03.png":::

5. Créez les règles dans la section **Règles** du menu à gauche. Associez chaque règle à l’écouteur correspondant. Sélectionnez **Ajouter**.

6. Configurez le pool principal et les paramètres HTTP correspondants. Sélectionnez **Ajouter**.

7. Testez la connexion. Ouvrez votre navigateur préféré et accédez aux différents sites web hébergés dans votre environnement Azure VMware Solution, par exemple http://www.fabrikam.com.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-07.png" alt-text="Création d’une passerelle Application Gateway":::

### <a name="routing-by-url"></a>Routage par URL

Vous pouvez utiliser Azure Application Gateway pour configurer des règles d’acheminement basées sur le chemin d’accès de l’URL. Dans ce tutoriel, vous allez définir des pools d’adresses principaux à l’aide de machines virtuelles s’exécutant sur un cloud privé Azure VMware Solution sur une passerelle Application Gateway existante. La passerelle Application Gateway fait partie d’un réseau virtuel hub, comme décrit dans la [documentation relative à l’intégration native d’Azure VMware Solution](concepts-hub-and-spoke.md). Il permet ensuite de définir des règles d’acheminement qui font en sorte que le trafic web arrive sur les serveurs voulus dans les pools.

1. Créez les machines virtuelles. Dans le cloud privé Azure VMware Solution, créez un pool de machines virtuelles pour représenter la batterie de serveurs web. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs.png" alt-text="Création d’une passerelle Application Gateway"
    Add-Content -Path C:\inetpub\wwwroot\video\test.htm -Value $($env:computername)
    ```

2. Ajouter les pools principaux Vous devrez ajouter trois nouveaux pools principaux dans une instance Application Gateway existante. Sélectionnez **Pools principaux** dans le menu de gauche. Sélectionnez **Ajouter** et entrez les détails du premier pool, **contoso-web**. Ajoutez une machine virtuelle en tant que cible. Sélectionnez **Ajouter**. Répétez ce processus pour **contoso-images** et **contoso-video**, en ajoutant une machine virtuelle unique à chaque cible. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-02.png" alt-text="Création d’une passerelle Application Gateway" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-02.png":::

3. Dans la section **Écouteurs**, créez un écouteur de type De base utilisant le port 8080.

4. Dans le volet de navigation à gauche, sélectionnez **Paramètres HTTP**, puis choisissez **Ajouter** dans le volet gauche. Renseignez les détails pour créer un paramètre HTTP, puis sélectionnez **Enregistrer**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-04.png" alt-text="Création d’une passerelle Application Gateway":::

5. Créez les règles dans la section **Règles** du menu à gauche. Associez chaque règle à l’écouteur créé précédemment. Configurez ensuite le pool principal principal et les paramètres HTTP. Sélectionnez **Ajouter**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-07.png" alt-text="Création d’une passerelle Application Gateway":::

6. Testez la configuration. Accédez à la passerelle d’application sur le Portail Azure, puis, dans la section **Vue d’ensemble**, copiez l’adresse IP publique. Ouvrez ensuite une nouvelle fenêtre de navigateur et entrez l’URL `http://<app-gw-ip-address>:8080`. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-08.png" alt-text="Création d’une passerelle Application Gateway":::

    Définissez l’URL sur `http://<app-gw-ip-address>:8080/images/test.htm`.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-09.png" alt-text="Création d’une passerelle Application Gateway":::

    Définissez de nouveau l’URL sur `http://<app-gw-ip-address>:8080/video/test.htm`.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-10.png" alt-text="Création d’une passerelle Application Gateway":::

## <a name="next-steps"></a>Étapes suivantes

Pour d’autres exemples de configuration, consultez la [documentation d’Azure Application Gateway](../application-gateway/index.yml).