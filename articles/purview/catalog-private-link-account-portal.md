---
title: Se connecter de manière privée et sécurisée à votre compte Purview
description: Cet article décrit la façon dont vous pouvez configurer un point de terminaison privé pour vous connecter à votre compte Purview à partir d’un réseau restreint.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/18/2021
ms.openlocfilehash: 26b98ad9c4c042c1e6bf60889625a4d23090f6f5
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122535409"
---
# <a name="connect-privately-and-securely-to-your-purview-account"></a>Se connecter de manière privée et sécurisée à votre compte Purview
Dans ce guide, vous allez apprendre à déployer des points de terminaison privés pour votre compte Purview pour vous permettre de vous connecter à celui-ci à partir de réseaux virtuels et de réseaux privés. Pour atteindre cet objectif, vous devez déployer des points de terminaison de _compte_, _portail_ et _ingestion_ privés pour votre compte Azure Purview.

Le point de terminaison privé du _compte_ Azure Purview est utilisé pour ajouter une couche de sécurité supplémentaire en permettant des scénarios où seuls les appels client provenant du réseau virtuel sont autorisés à accéder au compte Azure Purview. Ce point de terminaison privé est également un prérequis pour le point de terminaison privé du portail.

Le point de terminaison privé du _portail_ Azure Purview doit permettre la connectivité à Azure Purview Studio au moyen d’un réseau privé.

   :::image type="content" source="media/catalog-private-link/purview-private-link-account-portal.png" alt-text="Diagramme qui affiche l’architecture d’Azure Purview et Private Link.":::

Pour en savoir plus sur le service Azure Private Link, consultez [Liens privés et points de terminaison privés](../private-link/private-endpoint-overview.md).

## <a name="deployment-checklist"></a>Liste de vérification de déploiement
À l’aide de l’une des options de déploiement expliquées dans ce guide, vous pouvez déployer un nouveau compte Azure Purview avec des points de terminaison privés de _compte_ et de _portail_, ou vous pouvez choisir de déployer ces points de terminaison privés pour un compte Azure Purview existant :

1. Choisissez un réseau virtuel Azure approprié et un sous-réseau pour déployer des points de terminaison privés Azure Purview. Sélectionnez l’une des options suivantes :
   - Déployez un [réseau virtuel](../virtual-network/quick-create-portal.md) dans votre abonnement Azure.
   - Localisez un réseau virtuel Azure existant et un sous-réseau dans votre abonnement Azure.
  
2. Définissez une [méthode de résolution de noms DNS](./catalog-private-link-name-resolution.md#deployment-options) appropriée afin que le compte Azure Purview et le portail web puissent être accessibles par le biais d’adresses IP privées. Vous pouvez utiliser une des options suivantes :
   - Déployez de nouvelles zones Azure DNS à l’aide des étapes décrites plus loin dans ce guide.
   - Ajoutez les enregistrements DNS requis aux zones Azure DNS existantes à l’aide des étapes décrites plus loin dans ce guide.
   - Après avoir effectué les étapes de ce guide, ajoutez manuellement les enregistrements A DNS requis dans vos serveurs DNS existants.
3. Déployez un [nouveau compte Purview](#option-1---deploy-a-new-azure-purview-account-with-account-and-portal-private-endpoints) avec des points de terminaison privés de compte et de portail, ou déployez des points de terminaison privés de compte et de portail pour un [compte Purview existant](#option-2---enable-account-and-portal-private-endpoint-on-existing-azure-purview-accounts).
4. [Activez l’accès à Azure Active Directory](#enable-access-to-azure-active-directory) si votre réseau privé a des règles de groupe de sécurité réseau définies sur refuser pour l’ensemble du trafic Internet public.
5. À la fin de ce guide, ajustez les configurations DNS si nécessaire.
6. Validez votre réseau et la résolution de noms de l’ordinateur de gestion sur Azure Purview. 

## <a name="option-1---deploy-a-new-azure-purview-account-with-_account_-and-_portal_-private-endpoints"></a>Option 1 : déployer un nouveau compte Azure Purview avec le point de terminaison privé de _compte_ et de _portail_

1. Accédez au [Portail Azure](https://portal.azure.com), puis accédez à la page **Comptes Purview**. Sélectionnez **+ Créer** afin de créer un compte Azure Purview.

2. Entrez les informations de base, puis, sous l’onglet **Mise en réseau**, définissez la méthode de connectivité sur **Point de terminaison privé**. Définissez le point de terminaison privé sur **Compte et portail uniquement**.

3. Sous **Compte et portail**, sélectionnez **+ Ajouter** afin d’ajouter un point de terminaison privé pour votre compte Azure Purview.

   :::image type="content" source="media/catalog-private-link/purview-pe-scenario-1-1.png" alt-text="Capture d’écran montrant la création d’un point de terminaison privé pour les sélections de page de portail et de compte.":::

4. Sur la page **Créer un point de terminaison privé**, pour la **sous-ressource Purview**, choisissez votre emplacement, fournissez un nom pour le point de terminaison privé du _compte_ et sélectionnez **compte**. Sous **mise en réseau**, sélectionnez votre réseau virtuel et votre sous-réseau, puis, si vous le souhaitez, sélectionnez **Intégrer à une zone DNS privé** pour créer une zone DNS privé Azure. 
   
   :::image type="content" source="media/catalog-private-link/purview-pe-scenario-1-2.png" alt-text="Capture d’écran montrant la page créer un point de terminaison privé de compte.":::


   > [!NOTE]
   > Vous pouvez également utiliser vos zones Azure DNS privé existantes ou créer des enregistrements DNS dans vos serveurs DNS manuellement par la suite. Pour plus d’informations, consultez [Configurer la résolution de noms DNS pour les points de terminaison privés](./catalog-private-link-name-resolution.md).

5. Sélectionnez **OK**.
   
6. Dans l’Assistant de **création d’un compte Purview**, sélectionnez à nouveau **+Ajouter** pour ajouter un point de terminaison privé de _portail_.
     
7. Sur la page **Créer un point de terminaison privé**, pour la **sous-ressource Purview**, choisissez votre emplacement, fournissez un nom pour le point de terminaison privé du _portail_ et sélectionnez **portail**. Sous **mise en réseau**, sélectionnez votre réseau virtuel et votre sous-réseau, puis, si vous le souhaitez, sélectionnez **Intégrer à une zone DNS privé** pour créer une zone DNS privé Azure. 
   
   > [!NOTE]
   > Vous pouvez également utiliser vos zones Azure DNS privé existantes ou créer des enregistrements DNS dans vos serveurs DNS manuellement par la suite. Pour plus d’informations, consultez [Configurer la résolution de noms DNS pour les points de terminaison privés](./catalog-private-link-name-resolution.md).

8.  Sélectionnez **OK**.
   
   :::image type="content" source="media/catalog-private-link/purview-pe-scenario-1-3.png" alt-text="Capture d’écran montrant la page de révision de la création d’un point de terminaison privé.":::

9.  Sélectionnez **Vérifier + créer**. Sur la page **Revoir + créer**, Azure valide votre configuration.

10. Quand le message « Validation réussie » s’affiche, sélectionnez **Créer**.

## <a name="option-2---enable-_account_-and-_portal_-private-endpoint-on-existing-azure-purview-accounts"></a>Option 2 : activer les points de terminaison privé de _compte_ et de _portail_ sur les comptes Azure Purview existants

Il existe deux façons d’ajouter des points de terminaison privés de _compte_ et de _portail_ Azure Purview pour un compte Azure Purview existant :

- Utilisez le Portail Azure (compte Azure Purview).
- Utilisez le Centre Private Link.

### <a name="use-the-azure-portal-azure-purview-account"></a>Utilisez le Portail Azure (compte Azure Purview).

1. Accédez au [Portail Azure](https://portal.azure.com), puis cliquez sur votre compte Azure Purview, et sous **Paramètres**, sélectionnez **Mise en réseau**, puis sélectionnez **Connexions de point de terminaison privé**.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Capture d’écran montrant comment créer un point de terminaison privé de compte.":::

2. Sélectionnez **+ Point de terminaison privé** pour créer un point de terminaison privé.

3. Entrez les informations de base.

4. Sous l’onglet **Ressource**, pour **Type de ressource**, sélectionnez **Microsoft.Purview/accounts**.

5. Pour **Ressource**, sélectionnez le compte Azure Purview et pour **Sous-ressource cible**, sélectionnez **compte**.

6. Sous l’onglet **Configuration**, sélectionnez le réseau virtuel et, si vous le souhaitez, sélectionnez Zone DNS privé Azure pour créer une zone Azure DNS.
   
   > [!NOTE]
   > Pour la configuration DNS, vous pouvez également utiliser vos zones DNS privé Azure existantes à partir de la liste déroulante ou ajouter manuellement les enregistrements DNS requis à vos serveurs DNS ultérieurement. Pour plus d’informations, consultez [Configurer la résolution de noms DNS pour les points de terminaison privés](./catalog-private-link-name-resolution.md).

7. Accédez à la page de résumé, puis sélectionnez **Créer** pour créer le point de terminaison privé du portail.
   
8. Suivez les mêmes étapes lorsque vous sélectionnez le **portail** pour la **Sous-ressource cible**.

### <a name="use-the-private-link-center"></a>Utiliser Centre Private Link

1. Accédez au [portail Azure](https://portal.azure.com).

1. Dans la barre de recherche en haut de la page, recherchez **liaison privée** et accédez au panneau **Private Link** en cliquant sur la première option.

1. Sélectionnez **+ Ajouter** et renseignez les détails de base.

   :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="Capture d’écran montrant la création de points de terminaison privés à partir du Centre Private Link.":::

1. Pour **Ressource**, sélectionnez le compte Azure Purview déjà créé. Pour **Sous-ressource cible**, sélectionnez **compte**.

1. Sous l’onglet **Configuration**, sélectionnez le réseau virtuel et la zone DNS privée. Accédez à la page de résumé, puis sélectionnez **Créer** pour créer le point de terminaison privé du compte.

> [!NOTE]
> Suivez les mêmes étapes lorsque vous sélectionnez le **portail** pour la **Sous-ressource cible**.

## <a name="enable-access-to-azure-active-directory"></a>Activer l’accès à Azure Active Directory

> [!NOTE]
> Si votre machine virtuelle, votre passerelle VPN ou votre passerelle Peering de réseau virtuel dispose d’un accès Internet public, elle peut accéder au Portail Azure Purview et au compte Azure Purview activé avec des points de terminaison privés. Pour cette raison, vous n’êtes pas obligé de suivre le reste des instructions. Si votre réseau privé a des règles de groupe de sécurité réseau définies pour refuser tout trafic Internet public, vous allez devoir ajouter des règles pour activer l’accès à Azure Active Directory (Azure AD). Pour ce faire, suivez les instructions.

Les instructions permettent d’accéder à Azure Purview en toute sécurité à partir d’une machine virtuelle Azure. Vous devez suivre des étapes similaires si vous utilisez un VPN ou d’autres passerelles de Peering de réseaux virtuels.

1. Accédez à votre machine virtuelle dans le Portail Azure, puis, sous **Paramètres**, sélectionnez **Mise en réseau**. Sélectionnez ensuite **Règles de port de sortie**, **Ajouter une règle de port de sortie**.

   :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="Capture d’écran montrant l’ajout d’une règle de trafic sortant.":::

1. Dans le volet **Ajouter une règle de sécurité de trafic sortant** :

   1. Sous **Destination**, sélectionnez **Étiquette de service**.
   1. Sous **Étiquette de service de destination**, sélectionnez **AzureActiveDirectory**.
   1. Sous **Plages de port de destination**, sélectionnez *.
   1. Sous **Action**, sélectionnez **Autoriser**.
   1. Sous **Priorité**, la valeur doit être supérieure à la règle qui a refusé tout le trafic Internet.
   
   Créez la règle.

   :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="Capture d’écran montrant l’ajout des détails d’une règle de trafic sortant.":::

1. Suivez les mêmes étapes pour créer une autre règle afin d’autoriser l’étiquette de service **AzureResourceManager**. Si vous avez besoin d’accéder au Portail Azure, vous pouvez également ajouter une règle pour l’étiquette de service **AzurePortal**.

1. Connectez-vous à la machine virtuelle et ouvrez le navigateur. Accédez à la console du navigateur en sélectionnant Ctrl+Maj+J et basculez vers l’onglet réseau pour surveiller les requêtes réseau. Entrez web.purview.azure.com dans la zone URL, puis essayez de vous connecter à l’aide de vos informations d’identification Azure AD. Il est probable que la connexion échoue et, sous l’onglet **Réseau** dans la console, vous pouvez voir Azure AD tenter d’accéder à aadcdn.msauth.net mais être bloqué.

   :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="Capture d’écran montrant les détails de l’échec de la connexion.":::

1. Dans ce cas, ouvrez une invite de commandes sur la machine virtuelle, effectuez un test ping sur aadcdn.msauth.net, récupérez son adresse IP, puis ajoutez une règle de port de sortie pour l’adresse IP dans les règles de sécurité réseau de la machine virtuelle. Définissez la **Destination** sur **Adresses IP** et définissez les **Adresses IP de destination** sur l’adresse IP aadcdn. À cause d’Azure Load Balancer et d’Azure Traffic Manager, l’adresse IP du réseau de distribution de contenu d’Azure AD peut être dynamique. Une fois que vous avez obtenu son adresse IP, il est préférable de l’ajouter dans le fichier hôte de la machine virtuelle pour forcer le navigateur à visiter cette adresse IP afin d’obtenir le réseau de distribution de contenu Azure AD.

   :::image type="content" source="media/catalog-private-link/ping.png" alt-text="Capture d’écran montrant le test Ping.":::

   :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="Capture d’écran montrant la règle du réseau de distribution de contenu Azure AD.":::

1. Une fois la nouvelle règle créée, revenez à la machine virtuelle et essayez de vous connecter à l’aide de vos informations d’identification Azure AD. Si la connexion réussit, le portail Purview est prêt à être utilisé. Toutefois, dans certains cas, Azure AD effectue une redirection vers d’autres domaines pour se connecter en fonction du type de compte d’un client. Par exemple, pour un compte live.com, Azure AD effectue une redirection vers live.com pour se connecter, puis ces requêtes sont à nouveau bloquées. Pour les comptes d’employés Microsoft, Azure AD accède à msft.sts.microsoft.com pour obtenir des informations de connexion.

   Vérifiez les demandes de mise en réseau sous l’onglet de **Mise en réseau** du navigateur pour afficher les demandes du domaine qui sont bloquées, recommencez l’étape précédente pour obtenir son adresse IP, puis ajoutez des règles de port de sortie dans le groupe de sécurité réseau afin d’autoriser les demandes pour cette adresse IP. Si possible, ajoutez l’URL et l’adresse IP au fichier hôte de la machine virtuelle pour corriger la résolution DNS. Si vous connaissez les plages exactes d’adresses IP du domaine de connexion, vous pouvez également les ajouter directement dans les règles de mise en réseau.

1. Votre connexion Azure AD doit maintenant réussir. Le portail Azure Purview va se charger correctement, mais la liste de tous les comptes Azure Purview ne s’affichera pas, car il ne peut accéder qu’à un compte Azure Purview spécifique. Entrez `web.purview.azure.com/resource/{PurviewAccountName}` pour accéder directement au compte Azure Purview pour lequel vous avez correctement configuré un point de terminaison privé.

## <a name="next-steps"></a>Étapes suivantes

-  [Vérifier la résolution des points de terminaison privés](./catalog-private-link-name-resolution.md)
-  [Gérer des sources de données dans Azure Purview](./manage-data-sources.md)
-  [Résolution des problèmes de configuration de point de terminaison privé pour votre compte Azure Purview](./catalog-private-link-troubleshoot.md)
