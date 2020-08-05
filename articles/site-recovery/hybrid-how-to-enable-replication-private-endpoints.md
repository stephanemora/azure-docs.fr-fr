---
title: Activer la réplication pour les machines locales avec des points de terminaison privés dans Azure Site Recovery
description: Cet article explique comment configurer la réplication pour les machines locales avec des points de terminaison privés à l’aide de Site Recovery.
author: mayurigupta13
ms.author: mayg
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.openlocfilehash: c91c92a18570f569b6364b646e6fdf7bf534802f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87094300"
---
# <a name="replicate-on-premises-machines-with-private-endpoints"></a>Répliquer des machines locales avec des points de terminaison privés

Azure Site Recovery vous permet d’utiliser des points de terminaison privés [Azure Private Link](../private-link/private-endpoint-overview.md) pour la réplication de vos machines locales vers un réseau virtuel dans Azure. L’accès des points de terminaison privés à un coffre de récupération est pris en charge pour les régions suivantes :

- Azure Commercial : USA Centre Sud, USA Ouest 2, USA Est
- Azure Government : US Gov Virginie, US Gov Arizona, US Gov Texas, US DoD Est, US DoD Centre

Cet article fournit des instructions pour effectuer les étapes suivantes :

- Créer un coffre Recovery Services Sauvegarde Azure pour protéger vos machines
- Activer une identité managée pour le coffre et accorder les autorisations nécessaires pour accéder aux comptes de stockage client afin de répliquer le trafic depuis l’environnement local vers les localisations Azure cibles. L’accès de l’identité managée au stockage est nécessaire lors de la configuration de l’accès Azure Private Link au coffre.
- Apporter les modifications DNS nécessaires pour les points de terminaison privés
- Créer et approuver des points de terminaison privés pour un coffre à l’intérieur d’un réseau virtuel
- Créer des points de terminaison privés pour les comptes de stockage. Vous pouvez continuer à autoriser un accès public ou via un pare-feu au stockage en fonction des besoins. La création d’un point de terminaison privé pour l’accès au stockage n’est pas obligatoire pour Azure Site Recovery.
  
Vous trouverez ci-dessous une architecture de référence montrant dans quelle mesure le workflow de réplication change pour la reprise d’activité hybride avec des points de terminaison privés. Les points de terminaison privés ne peuvent pas être créés sur votre réseau local. Pour pouvoir utiliser des liaisons privées, vous devez créer un réseau virtuel Azure (appelé « réseau de contournement » dans cet article), établir une connectivité privée entre le réseau local et le réseau de contournement, puis créer des points de terminaison privés dans le réseau de contournement. Le choix de la connectivité privée vous appartient.

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/architecture.png" alt-text="Architecture de référence pour Site Recovery avec des points de terminaison privés":::

## <a name="prerequisites-and-caveats"></a>Prérequis et mises en garde

- La prise en charge des liaisons privées est activée pour l’infrastructure Site Recovery avec les **versions 9.35** et ultérieures.
- Les points de terminaison privés ne peuvent être créés que pour les nouveaux coffres Recovery Services qui ne contiennent pas d’éléments. Ainsi, les points de terminaison privés **doivent être créés avant l’ajout d’éléments au coffre**. Passez en revue la structure des tarifs des [points de terminaison privés](https://azure.microsoft.com/pricing/details/private-link/).
- Quand un point de terminaison privé est créé pour un coffre, ce dernier est verrouillé et **n’est pas accessible à partir de réseaux autres que ceux qui ont des points de terminaison privés**.
- Azure Active Directory ne prend pas en charge les points de terminaison privés. Ainsi, les adresses IP et les noms de domaine complets nécessaires pour qu’Azure Active Directory fonctionne dans une région doivent se voir autoriser un accès sortant à partir du réseau virtuel Azure sécurisé. Vous pouvez également utiliser l’étiquette de groupe de sécurité réseau « Azure Active Directory » et des étiquettes de Pare-feu Azure pour autoriser l’accès à Azure Active Directory, le cas échéant.
- **Cinq adresses IP sont nécessaires** dans le réseau de contournement où vous créez votre point de terminaison privé. Quand vous créez un point de terminaison privé pour le coffre, Site Recovery crée cinq liaisons privées pour accéder à ses microservices.
- **Une adresse IP supplémentaire est nécessaire** dans le réseau de contournement pour la connectivité des points de terminaison privés à un compte de stockage de cache. Le choix de la méthode de connectivité, par exemple Internet ou [ExpressRoute](../expressroute/index.yml), entre votre environnement local et votre point de terminaison de compte de stockage vous appartient. L’établissement d’une liaison privée est facultatif. Les points de terminaison privés pour le stockage peuvent uniquement être créés sur un compte de stockage de type v2 universel. Passez en revue la structure des tarifs du [transfert de données sur GPv2](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

 ## <a name="creating-and-using-private-endpoints-for-site-recovery"></a>Création et utilisation de points de terminaison privés pour Site Recovery

 Cette section décrit les étapes de création et d’utilisation des points de terminaison privés pour le Azure Site Recovery à l’intérieur de vos réseaux virtuels.

> [!NOTE]
> Nous vous recommandons de suivre ces étapes dans l’ordre indiqué. Si vous ne le faites pas, le coffre rendu risque de ne pas pouvoir utiliser des points de terminaison privés, ce qui vous obligerait à recommencer la procédure avec un nouveau coffre.

## <a name="create-a-recovery-services-vault"></a>Créez un coffre Recovery Services.

Un coffre Recovery Services est une entité qui contient les informations de réplication des machines et qui est utilisée pour déclencher des opérations de Site Recovery. Pour savoir comment créer un coffre Recovery Services dans la région Azure où vous souhaitez effectuer un basculement en cas de sinistre, consultez [Créer un coffre Recovery Services](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault).

## <a name="enable-the-managed-identity-for-the-vault"></a>Activer l’identité managée pour le coffre

Une [identité managée](../active-directory/managed-identities-azure-resources/overview.md) autorise le coffre à accéder aux comptes de stockage du client. Site Recovery doit accéder au stockage cible et aux comptes de stockage de cache/journal en fonction de l’exigence du scénario. L’accès de l’identité managée est essentiel quand vous utilisez le service de liaisons privées pour le coffre.

1. Accédez à votre coffre Recovery Services. Sélectionnez **Identité** sous _Paramètres_.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Montre le portail Azure et la page Recovery Services":::

1. Cliquez sur le bouton bascule sous **État** pour le définir sur la valeur _Activé_, puis sélectionnez **Enregistrer**.

1. Un **ID d’objet** est généré, indiquant que le coffre est maintenant inscrit auprès d’Azure Active Directory.

## <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Créer des points de terminaison privés pour le coffre Recovery Services

Vous aurez besoin d’un point de terminaison privé pour le coffre dans le réseau de contournement pour la protection des machines dans le réseau source local. Créez le point de terminaison privé à l’aide du centre Azure Private Link dans le portail ou par le biais d’[Azure PowerShell](../private-link/create-private-endpoint-powershell.md).

1. Depuis la barre de recherche du portail Azure, recherchez et sélectionnez « Liaison privée ». Vous accédez ainsi au Centre de liaisons privées.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Montre la recherche du Centre de liaisons privées dans le portail Azure":::

1. Dans la barre de navigation de gauche, sélectionnez **points de terminaison privés**. Une fois dans le volet Points de terminaison privés, sélectionnez **\+Ajouter** pour démarrer le processus de création d’un point de terminaison privé pour votre coffre.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="Montre la création d’un point de terminaison privé dans le Centre de liaisons privées":::

1. Une fois dans l’expérience de création d’un point de terminaison privé, vous devez entrer des informations requises pour créer la connexion de votre point de terminaison privé.

   1. **Paramètres de base**: Entrez les informations de base de vos points de terminaison privés. La région doit être la même que celle du réseau de contournement.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Montre l’onglet Général, les détails du projet, l’abonnement et d’autres champs associés pour la création d’un point de terminaison privé dans le portail Azure":::

   1. **Ressource** : Dans cet onglet, vous devez mentionner la ressource PaaS (plateforme en tant que service) pour laquelle vous souhaitez créer votre connexion. Sélectionnez _Microsoft.RecoveryServices/vaults_ à partir du **Type de ressource** pour l’abonnement sélectionné. Ensuite, choisissez le nom de votre coffre Recovery Services en guise de **Ressource** et définissez _Azure Site Recovery_ comme **Sous-ressource cible**.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Montre l’onglet Ressource ainsi que les champs Type de ressource, Ressource et Sous-ressource cible pour la liaison à un point de terminaison privé dans le portail Azure":::

   1. **Configuration** : Dans Configuration, spécifiez le réseau de contournement et le sous-réseau où vous souhaitez que le point de terminaison privé soit créé. Activez l’intégration à la zone DNS privée en sélectionnant **Oui**.
      Choisissez une zone DNS déjà créée ou créez-en une. La sélection de l’option **Oui** associe automatiquement la zone au réseau de contournement et ajoute les enregistrements DNS nécessaires à la résolution DNS des adresses IP et des noms de domaine complets créés pour le point de terminaison privé.

      Veillez à créer une zone DNS pour chaque nouveau point de terminaison privé qui se connecte au même coffre. Si vous choisissez une zone DNS privée existante, les enregistrements CNAME précédents sont remplacés. Reportez-vous à [Guide des points de terminaisons privés](../private-link/private-endpoint-overview.md#private-endpoint-properties) avant de continuer.

      Si votre environnement a un modèle hub-and-spoke, vous n’avez besoin que d’un seul point de terminaison privé et d’une seule zone DNS privée pour l’ensemble de l’installation, car l’appairage est déjà activé pour tous vos réseaux virtuels. Pour plus d’informations, consultez [Intégration DNS avec des points de terminaison privés](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server).

      Pour créer manuellement la zone DNS privée, suivez les étapes décrites dans [Créer des zones DNS privées et ajouter des enregistrements DNS manuellement](#create-private-dns-zones-and-add-dns-records-manually).

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="Montre l’onglet Configuration avec les champs de mise en réseau et d’intégration DNS pour la configuration d’un point de terminaison privé dans le portail Azure":::

   1. **Étiquettes** : Si vous le souhaitez, vous pouvez ajouter des étiquettes à votre point de terminaison privé.

   1. **Vérifier \+ créer** : Une fois la validation terminée, sélectionnez **Créer** pour créer le point de terminaison privé.

Une fois le point de terminaison privé créé, cinq noms de domaine complets sont ajoutés à celui-ci. Ces liaisons permettent aux machines du réseau local d’obtenir un accès via le réseau de contournement à tous les microservices Site Recovery requis dans le contexte du coffre. Un même point de terminaison privé peut être utilisé pour la protection de n’importe quelle machine Azure dans le réseau de contournement et tous les réseaux appairés.

Les cinq noms de domaine sont mis en forme selon le modèle suivant :

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

## <a name="approve-private-endpoints-for-site-recovery"></a>Approuver des points de terminaison privés pour Site Recovery

Si l’utilisateur qui crée le point de terminaison privé est également le propriétaire du coffre Recovery Services, le point de terminaison privé créé est approuvé automatiquement en quelques minutes. Dans le cas contraire, le propriétaire du coffre doit approuver le point de terminaison privé avant que vous ne l’utilisiez. Pour approuver ou rejeter une connexion de point de terminaison privée demandée, accédez à **Connexions des points de terminaison privés** sous « Paramètres » dans la page du coffre de récupération.

Vous pouvez accéder à la ressource de point de terminaison privé pour passer en revue l’état de la connexion avant de continuer.

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="Montre la page Connexions des points de terminaison privés du coffre et la liste des connexions dans le portail Azure":::

## <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>(Facultatif) Créer des points de terminaison privés pour le compte de stockage de cache

Un point de terminaison privé vers le stockage Azure peut être utilisé. La création de points de terminaison privés pour l’accès au stockage est _facultative_ pour la réplication Azure Site Recovery. Quand vous créez un point de terminaison privé pour le stockage, vous avez besoin d’un point de terminaison privé pour le compte de stockage de cache/journal dans votre réseau virtuel de contournement.

> [!NOTE]
> Le point de terminaison privé pour le stockage peut uniquement être créé sur un compte de stockage **v2 universel**. Pour plus d’informations sur les tarifs, consultez les [prix des objets blob de pages Standard](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Suivez les [instructions pour la création d’un stockage privé](../private-link/create-private-endpoint-storage-portal.md#create-your-private-endpoint) afin de créer un compte de stockage avec un point de terminaison privé. Veillez à sélectionner **Oui** pour l’intégration à la zone DNS privée. Sélectionnez une zone DNS déjà créée ou créez-en une.

## <a name="grant-required-permissions-to-the-vault"></a>Accorder les autorisations nécessaires au coffre

Selon votre configuration, vous pouvez avoir besoin d’un ou de plusieurs comptes de stockage dans la région Azure cible. Ensuite, accordez les autorisations d’identité managée pour tous les comptes de stockage de cache/journal requis par Site Recovery. Dans ce cas, vous devez créer les comptes de stockage requis à l’avance.

Avant d’activer la réplication des machines virtuelles, vous devez accorder à l’identité managée du coffre les autorisations de rôle suivantes en fonction du type de compte de stockage :

- Comptes de stockage basés sur Resource Manager (type Standard) :
  - [Contributeur](../role-based-access-control/built-in-roles.md#contributor)
  - [Contributeur aux données Blob du stockage](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Comptes de stockage basés sur Resource Manager (type Premium) :
  - [Contributeur](../role-based-access-control/built-in-roles.md#contributor)
  - [Propriétaire des données Blob du stockage](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Comptes de stockage Classic :
  - [Contributeur de compte de stockage classique](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Rôle de service d’opérateur de clé de compte de stockage classique](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

Les étapes suivantes expliquent comment ajouter une attribution de rôle à vos comptes de stockage, une à la fois :

1. Accédez au compte de stockage et à **Contrôle d’accès (IAM)** sur le côté gauche de la page.

1. Une fois dans **Contrôle d’accès (IAM)** , dans la zone « Ajouter une attribution de rôle », sélectionnez **Ajouter**.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="Montre la page Contrôle d’accès (IAM) sur un compte de stockage et le bouton « Ajouter une attribution de rôle » dans le portail Azure":::

1. Dans la page latérale « Ajouter une attribution de rôle », choisissez le rôle parmi la liste plus haut dans la liste déroulante **Rôle**. Entrez le **nom** du coffre, puis sélectionnez **Enregistrer**.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="Montre la page Contrôle d’accès (IAM) sur un compte de stockage et les options permettant de sélectionner un rôle et le principal auquel le rôle doit être accordé dans le portail Azure":::

En plus de ces autorisations, les services de confiance MS doivent se voir accorder l’accès. Accédez à « Pare-feux et réseaux virtuels » et cochez la case « Autoriser les services Microsoft approuvés à accéder à ce compte de stockage » dans **Exceptions**.

## <a name="protect-your-virtual-machines"></a>Protéger vos machines virtuelles

Une fois toutes les configurations ci-dessus terminées, passez à la configuration de votre infrastructure locale.

- [Déployer le serveur de configuration pour VMware et les machines physiques](./vmware-azure-deploy-configuration-server.md)
- OU [Configurer l’environnement Hyper-V pour la réplication](./hyper-v-azure-tutorial.md#set-up-the-source-environment)

Une fois l’installation terminée, activez la réplication pour vos machines sources. Assurez-vous que la configuration de l’infrastructure est effectuée uniquement après que les points de terminaison privés du coffre ont été créés dans le réseau de contournement.

## <a name="create-private-dns-zones-and-add-dns-records-manually"></a>Créer des zones DNS privées et ajouter des enregistrements DNS manuellement

Si vous n’avez pas sélectionné l’option d’intégration à la zone DNS privée au moment de la création d’un point de terminaison privé pour le coffre, suivez les étapes de cette section.

Créez une zone DNS privée afin de permettre au fournisseur Site Recovery (pour les machines Hyper-V) ou au serveur de processus (pour les machines VMware/physiques) de résoudre les noms de domaine complets de liaison privée en adresses IP privées.

1. Créer une zone DNS privée

   1. Dans la barre de recherche **Tous les services**, recherchez « Zone DNS privée », puis, dans la liste déroulante, sélectionnez « Zones DNS privées ».

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Montre la recherche de « zone DNS privée » dans la page des nouvelles ressources du portail Azure":::

   1. Une fois dans la page « Zones DNS privées », sélectionnez le bouton **\+Ajouter** pour commencer à créer une zone.

   1. Dans la page « Créer une zone DNS privée », entrez les informations requises. Entrez `privatelink.siterecovery.windowsazure.com` comme nom de la zone DNS privée. Vous pouvez choisir n’importe quel groupe de ressources et n’importe quel abonnement pour la créer.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="Montre l’onglet Général de la page Créer une zone DNS privée et les détails de projet associés dans le portail Azure":::

   1. Passez à l’onglet **Vérifier \+ créer** pour vérifier et créer la zone DNS.

1. Lier la zone DNS privée à votre réseau virtuel

   La zone DNS privée créée ci-dessus doit maintenant être liée au réseau de contournement.

   1. Accédez à la zone DNS privée que vous avez créée à l’étape précédente, puis, sur le côté gauche de la page, accédez à **Liens de réseau virtuel**. Ensuite, sélectionnez le bouton **\+Ajouter**.

   1. Renseignez les champs obligatoires. Vous devez renseigner les champs **Abonnement** et **Réseau virtuel** avec les paramètres correspondants du réseau de contournement. Ne modifiez pas les autres champs.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="Montre la page permettant d’ajouter un lien de réseau virtuel avec le nom du lien, l’abonnement et le réseau virtuel associé dans le portail Azure":::

1. Ajoutez des enregistrements DNS.

   Une fois que vous avez créé la zone DNS privée requise et le point de terminaison privé, vous devez ajouter des enregistrements DNS à votre zone DNS.

   > [!NOTE]
   > Si vous utilisez une zone DNS privée personnalisée, assurez-vous que des entrées similaires sont créées, comme indiqué ci-dessous.

   Cette étape nécessite que vous créiez des entrées dans votre zone de DNS privée pour chaque nom de domaine complet dans votre point de terminaison privé.

   1. Accédez à votre zone DNS privée, puis, sur le côté gauche de la page, accédez à la section **Vue d’ensemble**. Cliquez ensuite sur **\+Jeu d’enregistrements** pour commencer à ajouter des enregistrements.

   1. Dans la page « Ajouter un jeu d’enregistrements » qui s’ouvre, ajoutez une entrée pour chaque nom de domaine complet et adresse IP privée en tant qu’enregistrement de type _A_. La liste des noms de domaine complets et des adresses IP peut être obtenue à partir de la page « Point de terminaison privé » dans **Vue d’ensemble**. Comme indiqué dans l’exemple ci-dessous, le premier nom de domaine complet du point de terminaison privé est ajouté au jeu d’enregistrements dans la zone DNS privée.

      Ces noms de domaine complets correspondent au modèle : `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="Montre la page permettant d’ajouter un enregistrement DNS de type A pour le nom de domaine complet au point de terminaison privé dans le portail Azure":::

## <a name="next-steps"></a>Étapes suivantes

Des points de terminaison privés étant activés pour la réplication de vos machines virtuelles, consultez les pages ci-dessous pour obtenir des informations supplémentaires et connexes :

- [Déployer un serveur de configuration local](./vmware-azure-deploy-configuration-server.md)
- [Configurer la reprise d’activité après sinistre de machines virtuelles Hyper-V locales vers Azure](./hyper-v-azure-tutorial.md)