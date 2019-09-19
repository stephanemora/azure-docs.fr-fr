---
title: Installer une passerelle de données locale - Azure Logic Apps
description: Télécharger et installer la passerelle de données locale pour pouvoir accéder aux données locales avec Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: 657bc704e33e89b1646dffa6123a27169e6c317a
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860867"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Installer une passerelle de données locale pour Azure Logic Apps

Pour pouvoir vous connecter à des sources de données locales à partir d’Azure Logic Apps, téléchargez et installez la passerelle de données locale sur un ordinateur local. La passerelle fonctionne comme un pont qui effectue un transfert de données et de chiffrement entre des sources de données locales (pas situées sur le cloud) et vos applications logiques. Vous pouvez utiliser la même installation de passerelle pour d’autres services cloud, comme Power BI, Microsoft Flow, PowerApps et Azure Analysis Services. Pour plus d’informations sur l’utilisation de la passerelle avec ces services, consultez ces articles :

* [Passerelle de données locale Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Passerelle de données locale Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Passerelle de données locale Microsoft Flow](https://flow.microsoft.com/documentation/gateway-manage/)
* [Passerelle de données locale Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

Cet article explique comment télécharger, installer et configurer votre passerelle de données locale afin de pouvoir accéder à des sources de données locales à partir d’Azure Logic Apps. Vous pouvez également en savoir plus sur le [fonctionnement de la passerelle de données](#gateway-cloud-service) plus loin dans cette rubrique.

<a name="supported-connections"></a>

La passerelle prend en charge [des connecteurs locaux](../connectors/apis-list.md#on-premises-connectors) dans Azure Logic Apps pour les sources de données suivantes :

* BizTalk Server 2016
* Système de fichiers
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

Bien que la passerelle elle-même n’entraîne pas de frais supplémentaires, le [modèle de tarification de Logic Apps](../logic-apps/logic-apps-pricing.md) s’applique à ces connecteurs et à d’autres opérations dans Azure Logic Apps.

<a name="requirements"></a>

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

  * Vous devez utiliser le même compte Azure pour installer et administrer la passerelle. Lors de l’installation, vous utilisez ce compte Azure pour associer la passerelle sur votre ordinateur à un abonnement Azure. Plus tard, vous utilisez le même compte lors de la création d’une ressource Azure dans le portail Azure pour l’installation de votre passerelle. 

  * Vous devez vous connecter avec un compte professionnel ou scolaire, également appelé compte d’*organisation*, qui se présente comme ceci : `username@contoso.com`. Vous ne pouvez pas utiliser des comptes Azure B2B (invité) ou des comptes Microsoft personnels, comme @hotmail.com ou @outlook.com.

    > [!TIP]
    > Si vous avez souscrit une offre Office 365 sans fournir votre adresse e-mail professionnelle, votre adresse de connexion peut se présenter comme ceci : `username@domain.onmicrosoft.com`. Votre compte est stocké au sein d’un locataire dans Azure Active Directory (Azure AD). Dans la plupart des cas, le nom d’utilisateur principal (UPN) de votre compte Azure AD est identique à votre adresse e-mail.
    >
    > Pour utiliser un [abonnement standard Visual Studio](https://visualstudio.microsoft.com/vs/pricing/) associé à un compte Microsoft, commencez par [créer un locataire dans Azure AD](../active-directory/develop/quickstart-create-new-tenant.md) ou utilisez l’annuaire par défaut. Ajoutez un utilisateur avec un mot de passe au répertoire, puis donnez-lui accès à votre abonnement. 
    > Vous pourrez alors vous connecter au cours de l’installation de la passerelle avec ce nom d’utilisateur et ce mot de passe.

* Voici la configuration requise pour votre ordinateur local :

  **Configuration minimale requise**

  * .NET Framework 4.6
  * Version 64 bits de Windows 7 ou Windows Server 2008 R2 (ou version ultérieure)

  **Configuration recommandée**

  * Processeur 8 cœurs
  * 8 Go de mémoire
  * Version 64 bits de Windows Server 2012 R2 ou ultérieure
  * Stockage SSD pour la mise en file d’attente

  > [!NOTE]
  > La passerelle ne prend pas en charge Windows Server 2016 Core.

* **Considérations associées**

  * Vous pouvez uniquement installer la passerelle de données locale sur un ordinateur local, et pas sur un contrôleur de domaine. Néanmoins, il n’est pas absolument nécessaire d’installer la passerelle sur le même ordinateur que celui sur lequel se trouve votre source de données. Vous n’avez besoin que d’une seule passerelle pour toutes vos sources de données : il n’est donc pas nécessaire d’installer une passerelle pour chaque source de données.

    > [!TIP]
    > Pour réduire la latence, vous pouvez installer la passerelle le plus près possible de votre source de données, ou sur le même ordinateur, en supposant que vous disposiez des autorisations nécessaires.

  * Installez la passerelle sur un ordinateur qui se trouve sur un réseau câblé, connecté à Internet, toujours allumé et qui ne se met pas en veille. Sinon, la passerelle ne peut pas s’exécuter et les performances peuvent se dégrader sur un réseau sans fil.

  * Si vous prévoyez d’utiliser l’authentification Windows, veillez à installer la passerelle sur un ordinateur membre du même environnement Active Directory que vos sources de données.

  * La région que vous sélectionnez pour l’installation de votre passerelle est le même emplacement que celui que vous devez sélectionner quand vous créez plus tard la ressource de passerelle Azure pour votre application logique. Par défaut, cette région est le même emplacement que votre locataire Azure AD qui gère votre compte Azure. Vous pouvez cependant changer l’emplacement lors de l’installation de la passerelle.

  * La passerelle a deux modes : le mode standard et le mode personnel, qui s’applique seulement à Power BI. Vous ne pouvez pas avoir plusieurs passerelles s’exécutant dans le même mode sur le même ordinateur.

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Installer une passerelle de données

1. [Téléchargez et exécutez le programme d’installation de passerelle sur un ordinateur local](https://aka.ms/on-premises-data-gateway-installer).

1. Une fois le programme d’installation ouvert, sélectionnez **Suivant**.

   ![Introduction au programme d’installation](./media/logic-apps-gateway-install/gateway-intro-screen.png)

1. Sélectionnez **Passerelle de données locale (recommandé)** , qui est le mode standard, puis sélectionnez **Suivant**.

   ![Sélectionner le mode de passerelle](./media/logic-apps-gateway-install/select-gateway-mode.png)

1. Passez en revue la configuration minimale requise, conservez le chemin d’installation par défaut, acceptez les conditions d’utilisation, puis sélectionnez **Installer**.

   ![Passer en revue les conditions requises et accepter les conditions d’utilisation](./media/logic-apps-gateway-install/accept-terms.png)

1. Une fois la passerelle installée avec succès, fournissez l’adresse e-mail pour votre compte d’organisation, puis choisissez **Se connecter**, par exemple :

   ![Connectez-vous avec un compte professionnel ou scolaire](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   Vous êtes maintenant connecté à votre compte.

1. Sélectionnez **Inscrivez une nouvelle passerelle sur cet ordinateur** > **Suivant**. Cette étape inscrit l’installation de votre passerelle auprès du [service cloud de passerelle](#gateway-cloud-service).

   ![Inscrivez la passerelle](./media/logic-apps-gateway-install/register-gateway.png)

1. Fournissez les informations suivantes pour votre installation de passerelle :

   * Un nom de passerelle unique sur votre locataire Azure AD
   * La clé de récupération (qui doit comporter au moins huit caractères) que vous voulez utiliser
   * Confirmation de votre clé de récupération

   ![Configuration de la passerelle](./media/logic-apps-gateway-install/set-up-gateway.png)

   > [!IMPORTANT]
   > Sauvegardez votre clé de récupération dans un endroit sûr. Vous avez besoin de cette clé si vous voulez changer l’emplacement, déplacer, récupérer ou reprendre l’installation d’une passerelle.

   Notez l’option pour **Ajouter à un cluster de passerelle existant**, que vous sélectionnez quand vous installez des passerelles supplémentaires pour des [scénarios de haute disponibilité](#high-availability).

1. Vérifiez la région sélectionnée pour le service cloud de passerelle et pour [Azure Service Bus](https://azure.microsoft.com/services/service-bus/), qui est utilisée par votre installation de passerelle. Par défaut, cette région est le même emplacement que votre locataire Azure AD pour votre compte Azure.

   ![Vérification de la région](./media/logic-apps-gateway-install/check-region.png)

1. Pour accepter la région par défaut, sélectionnez **Configurer**. Cependant, si la région par défaut n’est pas celle qui est la plus proche de vous, vous pouvez changer la région.

   *Pourquoi modifier la région de votre programme d’installation de passerelle ?*

   Par exemple, pour réduire la latence, vous pouvez modifier la région de votre passerelle vers la même région que votre application logique. Ou bien, vous pouvez sélectionner la région la plus proche de votre source de données locale. Votre *ressource de passerelle sur Azure* et votre application logique peuvent se trouver dans des emplacements différents.

   1. À côté de la région actuelle, sélectionnez **Changer la région**.

      ![Modification de la région](./media/logic-apps-gateway-install/change-region.png)

   1. Sur la page suivante, ouvrez la liste **Sélectionner une région**, sélectionnez la région de votre choix, puis sélectionnez **Terminé**.

      ![Sélection d’une autre région](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Passez en revue les informations de la fenêtre de confirmation finale. Cet exemple utilise le même compte pour Logic Apps, Power BI, PowerApps et Microsoft Flow : la passerelle est donc disponible pour tous ces services. Quand vous êtes prêt, sélectionnez **Fermer**.

   ![Passerelle terminée](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. Maintenant, [créez la ressource Azure pour votre installation de passerelle](../logic-apps/logic-apps-gateway-connection.md).

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Prise en charge de la haute disponibilité

Pour éviter les points de défaillance uniques pour l’accès aux données locales, vous pouvez avoir plusieurs installations de passerelle (mode standard uniquement) avec chacune sur un ordinateur différent, et les configurer en tant que cluster ou en tant que groupe. Ainsi, si la passerelle principale n’est pas disponible, les demandes de données sont routées vers la deuxième passerelle, etc. Comme vous ne pouvez installer qu’une seule passerelle standard sur un ordinateur, vous devez installer chaque passerelle supplémentaire qui se trouve dans le cluster sur un autre ordinateur. Tous les connecteurs qui fonctionnent avec la passerelle de données locale prennent en charge la haute disponibilité. 

* Vous devez déjà disposer d’au moins une passerelle d’installation dans le même abonnement Azure servant de passerelle principale et de clé de récupération pour cette installation.

* Votre passerelle principale doit fonctionner sous la mise à jour de passerelle datant de novembre 2017 ou une version plus récente.

Une fois que vous avez configuré votre passerelle principale, quand vous accédez à installer une autre passerelle, sélectionnez **Ajouter à un cluster de passerelle existant**, sélectionnez la passerelle principale, qui est la première passerelle que vous avez installée, et spécifiez la clé de récupération pour cette passerelle. Pour plus d’informations, consultez [Clusters à haute disponibilité pour la passerelle de données locale](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Modifier l’emplacement, migrer, restaurer ou contrôler une passerelle existante

Si vous devez modifier l’emplacement de votre passerelle, déplacer votre programme d’installation de passerelle vers un nouvel ordinateur, récupérer une passerelle endommagée ou contrôler une passerelle existante, vous avez besoin de la clé de récupération qui a été fournie lors de l’installation de la passerelle.

1. Exécutez le programme d’installation de passerelle sur l’ordinateur où se trouve la passerelle existante. Si vous n’avez pas le dernier programme d’installation de passerelle, [téléchargez la dernière version de la passerelle](https://aka.ms/on-premises-data-gateway-installer).

   > [!NOTE]
   > Avant de restaurer la passerelle sur l’ordinateur où la passerelle d’origine est installée, vous devez d’abord désinstaller la passerelle sur cet ordinateur. Cette action déconnecte l’ancienne passerelle.
   > Si vous supprimez un cluster de passerelle pour un service cloud, vous ne pouvez pas restaurer ce cluster.

1. Une fois le programme d’installation ouvert, connectez-vous avec le même compte Azure que celui utilisé pour installer la passerelle.

1. Sélectionnez **Migrez, restaurez ou remplacez une passerelle existante** > **Suivant**, par exemple :

   ![Sélectionnez « Migrer, restaurer ou contrôler une passerelle existante »](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Sélectionnez parmi les clusters et les passerelles disponibles, puis entrez la clé de récupération pour la passerelle sélectionnée, par exemple :

   ![Sélectionner une passerelle](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Pour changer la région, sélectionnez **Changer la région** et sélectionnez la nouvelle région.

1. Quand vous êtes prêt, sélectionnez **Configurer** pour pouvoir terminer votre tâche.

## <a name="configure-proxy-or-firewall"></a>Configurer le proxy ou le pare-feu

Si votre environnement de travail nécessite que le trafic passe par un proxy pour accéder à Internet, cette restriction peut empêcher la passerelle de données de se connecter au service cloud de passerelle et à [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). Pour plus d’informations, consultez [Configuration des paramètres de proxy pour la passerelle de données locale](https://docs.microsoft.com/power-bi/service-gateway-proxy).

Pour vérifier si votre proxy ou votre pare-feu risque de bloquer des connexions, vérifiez que votre ordinateur peut effectivement se connecter à Internet et à Azure Service Bus. À partir d’une invite PowerShell, exécutez la commande suivante :

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> Cette commande teste uniquement la connectivité réseau et la connectivité à Azure Service Bus. La commande n’a rien à voir avec la passerelle ou le service cloud de la passerelle, qui chiffre et stocke vos informations d’identification et les détails de la passerelle. 
>
> Par ailleurs, cette commande est disponible uniquement sur Windows Server 2012 R2 ou version ultérieure, et sur Windows 8.1 ou version ultérieure. Dans les versions de système d’exploitation antérieures, vous pouvez utiliser Telnet pour tester la connectivité. Pour en savoir plus, voir [Solutions Azure Service Bus et hybrides](../service-bus-messaging/service-bus-messaging-overview.md).

Vos résultats devraient ressembler à cet exemple avec **TcpTestSucceeded** défini sur **True**:

```text
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Si **TcpTestSucceeded** n’est pas défini sur **True**, il se peut que votre passerelle soit bloquée par un pare-feu. Pour être exhaustif, remplacez les valeurs **ComputerName** et **Port** par celles répertoriées dans la section [Configuration des ports](#configure-ports) de cet article.

Le pare-feu peut également bloquer des connexions qu’Azure Service Bus tente d’établir avec des centres de données Microsoft Azure. Si ce scénario se produit, approuvez (débloquez) toutes les adresses IP de ces centres de données dans votre région. Pour ces adresses IP, vous pouvez [obtenir la liste des adresses IP Azure ici](https://www.microsoft.com/download/details.aspx?id=41653).

<a name="configure-ports"></a>

## <a name="configure-ports"></a>Configuration des ports

La passerelle crée une connexion sortante vers Azure Service Bus et communique sur les ports de sortie suivants : TCP 443 (par défaut), 5671, 5672 et 9350 à 9354. La passerelle ne nécessite pas de ports entrants. Pour en savoir plus, voir [Solutions Azure Service Bus et hybrides](../service-bus-messaging/service-bus-messaging-overview.md).

La passerelle utilise ces noms de domaine complets :

| Noms de domaine | Ports sortants | Description |
| ------------ | -------------- | ----------- |
| *.analysis.windows.net | 443 | HTTPS |
| *.core.windows.net | 443 | HTTPS |
| *.frontend.clouddatahub.net | 443 | HTTPS |
| *.login.windows.net | 443 | HTTPS |
| *.microsoftonline-p.com | 443 | Utilisé pour l’authentification en fonction de la configuration. |
| *.msftncsi.com | 443 | Permet de tester la connectivité Internet lorsque la passerelle est inaccessible par le service Power BI. |
| *.servicebus.windows.net | 443, 9350-9354 | Récepteurs du Service Bus Relay sur TCP (nécessite le port 443 pour l’acquisition du jeton Access Control) |
| *.servicebus.windows.net | 5671-5672 | Advanced Message Queuing Protocol (AMQP) |
| login.microsoftonline.com | 443 | HTTPS |
||||

Dans certains cas, les connexions Azure Service Bus s’effectuent avec l’adresse IP plutôt qu’avec le nom de domaine complet. Nous vous recommandons de débloquer les adresses IP pour votre région de données dans votre pare-feu. Afin d’autoriser l’accès pour des adresses IP plutôt que pour des domaines, vous pouvez télécharger et utiliser la [Liste des plages d’adresses IP du centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Les adresses IP dans cette liste sont en notation [Classless Inter-Domain Routing (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

### <a name="force-https-communication-with-azure-service-bus"></a>Forcer les communications HTTPS avec Azure Service Bus

Certains proxys autorisent uniquement le trafic vers les ports 80 et 443. Par défaut, la communication avec Azure Service Bus se produit sur des ports autres que 443. Vous pouvez forcer la passerelle à communiquer avec Azure Service Bus à l’aide de HTTPS au lieu de TCP direct ; toutefois, cela peut considérablement réduire les performances. Pour plus d’informations, consultez [Forcer les communications HTTPS avec Azure Service Bus](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication#force-https-communication-with-azure-service-bus).

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Compte de service Windows

Par défaut, l’installation de la passerelle sur votre ordinateur local s’exécute avec un compte de service Windows nommé « Service de passerelle de données locale ». Cependant, l’installation de la passerelle utilise le nom `NT SERVICE\PBIEgwService` pour les informations d’identification de son compte « Ouvrir une session en tant que » et dispose des autorisations « Ouvrir une session en tant que service ».

> [!NOTE]
> Votre compte de service Windows diffère du compte utilisé pour la connexion à des sources de données locales, et du compte Azure que vous utilisez quand vous vous connectez à des services cloud.

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Redémarrer la passerelle

La passerelle de données s’exécute en tant que service Windows et, comme avec n’importe quel autre service Windows, vous pouvez la démarrer et l’arrêter de plusieurs façons. Pour plus d’informations, consultez [Redémarrer une passerelle de données locale](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart).

## <a name="tenant-level-administration"></a>Administration au niveau du locataire

Pour obtenir une visibilité de toutes les passerelles de données locales dans un locataire Azure AD, les administrateurs généraux de ce locataire peuvent se connecter au [Centre d’administration Power Platform](https://powerplatform.microsoft.com) en tant qu’administrateur du locataire et sélectionner l’option **Passerelles de données**. Pour plus d’informations, consultez [Administration au niveau du locataire pour la passerelle de données locale](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin).

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Fonctionnement de la passerelle

La passerelle de données assure une communication rapide et sécurisée entre votre application logique, le service cloud de passerelle et votre source de données locale. Le service cloud de passerelle chiffre et stocke les informations d’identification de votre source de données et les détails de la passerelle. Le service achemine également les requêtes et leurs résultats entre votre application logique, la passerelle de données locale et votre source de données locale.

La passerelle fonctionne avec les pare-feux et utilise uniquement des connexions sortantes. Tout le trafic est initialisé en tant que trafic sortant de l’agent de passerelle sécurisé. La passerelle relaie les données des sources locales sur des canaux chiffrés via Azure Service Bus. Ce service bus crée un canal entre la passerelle et le service d’appel, mais ne stocke aucune donnée. Toutes les données qui transitent via la passerelle sont chiffrées.

![Architecture pour la passerelle de données locale](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

Ces étapes décrivent ce qu’il se produit lorsqu’un utilisateur dans le cloud interagit avec un élément connecté à votre source de données locale :

1. Le service cloud de passerelle crée une requête, ainsi que les informations d’identification chiffrées pour la source de données, puis envoie la requête vers la file d’attente afin que la passerelle la traite.

1. Le service cloud de passerelle analyse la requête et envoie les demandes à Azure Service Bus.

1. La passerelle de données locale interroge Azure Service Bus pour connaître les requêtes en attente.

1. La passerelle reçoit la requête, déchiffre les informations d’identification et se connecte à la source de données avec ces informations d’identification.

1. La passerelle envoie la requête à la source de données pour exécution.

1. Les résultats sont envoyés de la source de données vers la passerelle, puis au service cloud de passerelle. Le service cloud de passerelle utilise ensuite les résultats.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="general"></a>Généralités

**Q** : Ai-je besoin d’une passerelle pour les sources de données dans le cloud, par exemple Azure SQL Database ? <br/>
**R** : Non, une passerelle se connecte uniquement aux sources de données locales.

**Q** : La passerelle doit-elle être installée sur le même ordinateur que la source de données ? <br/>
**R** : Non, la passerelle se connecte à la source de données en utilisant les informations de connexion fournies. En ce sens, considérez la passerelle comme une application cliente. La passerelle doit juste être en mesure de se connecter au nom du serveur qui a été fourni.

<a name="why-azure-work-school-account"></a>

**Q** : Pourquoi dois-je utiliser un compte professionnel ou scolaire pour me connecter ? <br/>
**R** : Lorsque vous installez la passerelle de données locale, vous pouvez uniquement utiliser un compte professionnel ou scolaire. Votre compte de connexion est stocké dans un client géré par Azure Active Directory (Azure AD). En règle générale, le nom d’utilisateur principal (UPN) de votre compte Azure AD correspond à l’adresse de messagerie.

**Q** : Où mes informations d’identification sont-elles stockées ? <br/>
**R** : Les informations d’identification que vous entrez pour une source de données sont chiffrées et stockées dans le service cloud de passerelle. Les informations d’identification sont déchiffrées au niveau de la passerelle de données locale.

**Q** : Existe-t-il des exigences concernant la bande passante réseau ? <br/>
**R** : Nous vous recommandons de vérifier que votre connexion réseau offre un bon débit. Chaque environnement est différent, et la quantité de données envoyées peut affecter les résultats. Pour garantir un bon niveau de débit entre votre source de données locale et les centres de données Azure, essayez [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Pour aider à mesurer votre débit, essayez un outil externe tel qu’Azure Speed Test.

**Q** : Quelle est la latence d’exécution des requêtes adressées à une source de données à partir de la passerelle ? Quelle est la meilleure architecture ? <br/>
**R** : Pour réduire la latence du réseau, installez la passerelle le plus près possible de la source de données. Si vous pouvez installer la passerelle sur la source de données réelle, cette proximité réduit le temps de latence. En outre, tenez compte de la proximité avec les centres de données Azure. Par exemple, si votre service utilise le centre de données de l’USA Ouest, et que vous hébergez SQL Server sur une machine virtuelle Azure, alors vous pouvez également souhaiter que votre machine virtuelle Azure se situe dans l’USA Ouest. Cette proximité réduit la latence et évite des frais d’acheminement sur la machine virtuelle Azure.

**Q** : Comment les résultats sont-ils renvoyés vers le cloud ? <br/>
**R** : Les résultats sont envoyés via Azure Service Bus.

**Q** : Existe-t-il des connexions entrantes vers la passerelle à partir du cloud ? <br/>
**R** : Non, la passerelle utilise des connexions sortantes vers Azure Service Bus.

**Q** : Que se passe-t-il si je bloque les connexions sortantes ? Que dois-je ouvrir ? <br/>
**R** : Vérifiez les ports et les hôtes que la passerelle utilise.

**Q** : Comment s’appelle le service Windows réel ? <br/>
**R** : Sous l’onglet Services du Gestionnaire des tâches, le nom du service est « PBIEgwService », soit le service Power BI Enterprise Gateway Service. Dans la console Services, le nom du service est « Service de passerelle de données locale ». Le service Windows utilise « NT SERVICE\PBIEgwService » en tant que Service SID (SSID).

**Q** : Le service Windows de passerelle peut-il s’exécuter avec un compte Azure Active Directory ? <br/>
**R** : Non, le service Windows doit avoir un compte Windows valide.

### <a name="disaster-recovery"></a>Récupération d'urgence

**Q** : Quelles sont les options de reprise d’activité disponibles ? <br/>
**R** : Vous pouvez utiliser la clé de récupération pour restaurer ou déplacer une passerelle. Lorsque vous installez la passerelle, spécifiez la clé de récupération.

**Q** : Quel avantage offre la clé de récupération ? <br/>
**R** : La clé de récupération permet de migrer ou de récupérer les paramètres de votre passerelle en cas de récupération d’urgence.

## <a name="troubleshooting"></a>Résolution de problèmes

Cette section traite de certains problèmes courants que vous pouvez rencontrer lors de la configuration et de l’utilisation de la passerelle de données locale.

**Q** : Pourquoi mon installation de passerelle a-t-elle échoué ? <br/>
**R** : Ce problème peut survenir si le logiciel antivirus sur l’ordinateur cible est obsolète. Vous pouvez mettre à jour le logiciel antivirus ou le désactiver, mais uniquement lors de l’installation de la passerelle, puis vous pouvez réactiver le logiciel de nouveau.

**Q** : Pourquoi ne puis-je pas voir mon installation de la passerelle lorsque je crée la ressource de passerelle dans Azure ? <br/>
**R** : Ce problème peut se produire pour les raisons suivantes :

* L’installation de votre passerelle est déjà enregistrée et revendiquée par une autre ressource de passerelle dans Azure. Les installations de passerelle ne s’affichent pas dans la liste des instances après la création de ressources de passerelle pour elles. Pour vérifier vos inscriptions de passerelle dans le portail Azure, passez en revue toutes vos ressources Azure avec le type de **Passerelles de données locales** pour *tous* les abonnements Azure.

* L’identité Azure AD octroyée à la personne qui a installé la passerelle diffère de celle de la personne qui s’est connectée au portail Azure. Vérifiez que vous êtes connecté avec la même identité que celle qui a installé la passerelle.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**Q** : Où se situent les journaux d’activité de la passerelle ? <br/>
**R** : Voir la [section **Journaux d’activité**](#logs) plus loin dans cet article.

**Q** : Comment voir les requêtes qui sont envoyées à la source de données locale ? <br/>
**R** : Vous pouvez activer le traçage de requête qui inclut les requêtes envoyées. N’oubliez pas de rétablir la valeur d’origine du traçage des requêtes une fois les problèmes résolus. Le fait de laisser activé le traçage des requêtes contribue à augmenter la taille des journaux d’activité.

Vous pouvez également utiliser les outils de suivi des requêtes proposés par votre source de données. Par exemple, vous pouvez utiliser Extended Events ou SQL Profiler for SQL Server et Analysis Services.

### <a name="outdated-gateway-version"></a>Version de passerelle obsolète

Beaucoup de problèmes peuvent survenir si la version de la passerelle est obsolète. En règle générale, veillez toujours à utiliser la version la plus récente. Si vous n’avez pas mis à jour la passerelle depuis un mois ou plus, vous pouvez installer la dernière version de la passerelle pour vérifier si le problème se reproduit.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Error: Impossible d’ajouter l’utilisateur au groupe. (-2147463168 PBIEgwService Performance Log Users)

Cette erreur peut apparaître si vous essayez d’installer la passerelle sur un contrôleur de domaine qui n’est pas pris en charge. Veillez à déployer la passerelle sur un ordinateur qui n’est pas un contrôleur de domaine.

<a name="logs"></a>

### <a name="logs"></a>Journaux d’activité

Pour vous aider à résoudre les problèmes, démarrez toujours par la collecte et la consultation des journaux d’activité de passerelle. Vous disposez de plusieurs méthodes pour recueillir les journaux d’activité, mais l’option la plus simple s’effectue via l’interface utilisateur du programme d’installation de la passerelle, une fois que vous avez installé la passerelle.

1. Sur votre ordinateur, ouvrez le programme d’installation de passerelle de données locale.

1. Dans le menu de gauche, sélectionnez **Diagnostiques**.

1. Sous **Journaux d’activité de passerelle**, sélectionnez **Exporter des journaux d’activité**.

   ![Exporter des journaux d’activité à partir du programme d’installation de la passerelle](./media/logic-apps-gateway-install/export-logs.png)

Voici les emplacements où vous trouverez divers journaux d’activité :

| Type de journal | Location |
|----------|----------|
| **Journaux d’activité du programme d’installation** | %localappdata%\Temp\On-premises_data_gateway_ <*AAAAMMJJ*>.<*nombre*>.log |
| **Journaux d’activité de configuration** | C:\Utilisateurs\<*nom d’utilisateur*> \AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator<*AAAAMMJJ*>. <*nombre*>.log |
| **Journaux d’activité d’entreprise du service de passerelle** | C:\Utilisateurs\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\Gateway<*AAAAMMJJ*>.<*nombre*>.log |
|||

**Journaux d’événements**

Pour rechercher les journaux des événements de la passerelle, procédez comme suit :

1. Sur l’ordinateur doté de l’installation de passerelle, ouvrez l’**Observateur d’événements**.

1. Développez l’**Observateur d’événements (Local)**  > **Journaux des applications et des services**.

1. Sélectionnez **Service de passerelle de données locale**.

   ![Afficher les journaux d’événements pour la passerelle](./media/logic-apps-gateway-install/event-viewer.png)

### <a name="review-slow-query-performance"></a>Examinez les performances des requêtes lentes

Si vous trouvez que les requêtes s’exécutent lentement via la passerelle, vous pouvez activer la journalisation supplémentaire qui affiche les requêtes et leur durée. Ces journaux d’activité peuvent vous aider à trouver quelles requêtes s’exécutent lentement. Pour optimiser les performances des requêtes, vous devrez peut-être modifier votre source de données, par exemple en ajustant les index pour les requêtes de SQL Server.

Pour déterminer la durée d’une requête, procédez comme suit :

1. Accédez au même emplacement que pour le client de passerelle, qui se trouve généralement ici : ```C:\Program Files\On-premises data gateway```

   Sinon, pour rechercher l’emplacement du client, ouvrez la console Services sur le même ordinateur, recherchez le **Service de passerelle de données locale** et affichez la propriété **Chemin d’accès au fichier exécutable**.

1. Ouvrez et modifiez ces fichiers de configuration, comme indiqué :

   * **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

     Dans ce fichier, changez la valeur de **EmitQueryTraces** de **false** en **true**, de sorte que votre passerelle puisse enregistrer les requêtes envoyées depuis la passerelle vers une source de données :

     ```html
     <setting name="EmitQueryTraces" serializeAs="String">
        <value>true</value>
     </setting>
     ```

     > [!IMPORTANT]
     > Activer l’option de EmitQueryTraces peut augmenter considérablement la taille du journal en fonction de l’utilisation de la passerelle. Une fois que vous avez terminé d’examiner les journaux d’activité, veillez à réinitialiser EmitQueryTraces sur **false** là encore, au lieu de laisser ce paramètre actif sur le long terme.

   * **Microsoft.PowerBI.DataMovement.Pipeline.Diagnostics.dll.config**

     Pour obtenir des entrées détaillées dans votre passerelle de journaux, comprenant les entrées indiquant la durée, modifiez la valeur de **TracingVerbosity** de **4** à **5** en effectuant l’une des étapes suivantes :

     * Dans ce fichier de configuration, modifiez la valeur **TracingVerbosity** de **4** à **5**

       ```html
       <setting name="TracingVerbosity" serializeAs="String">
          <value>5</value>
       </setting>
       ```

     * Ouvrez le programme d’installation de la passerelle, sélectionnez **Diagnostiques**, activez **Journalisation supplémentaire**, puis choisissez **Appliquer** :

       ![Activer la journalisation supplémentaire](./media/logic-apps-gateway-install/turn-on-additional-logging.png)

     > [!IMPORTANT]
     > Activer l’option de TracingVerbosity peut augmenter considérablement la taille du journal en fonction de l’utilisation de la passerelle. Une fois que vous avez terminé d’examiner les journaux d’activité, vérifiez que vous désactivez bien la **Journalisation supplémentaire** dans le programme d’installation de passerelle, ou réinitialisez TracingVerbosity de nouveau sur **4** dans le fichier de configuration, au lieu de laisser ce paramètre actif sur le long terme.

1. Pour déterminer la durée d’une requête, procédez comme suit :

   1. [Exporter](#logs) et ouvrez le journal de la passerelle.

   1. Pour rechercher une requête, recherchez un type d’activité, par exemple :

      | Type d’activité | Description |
      |---------------|-------------|
      | MGEQ | Requêtes qui s’exécutent sur ADO.NET |
      | MGEO | Requêtes qui s’exécutent sur OLEDB |
      | MGEM | Requêtes qui s’exécutent à partir du moteur Mashup |
      |||

   1. Notez le second GUID, qui est l’ID de requête.

   1. Continuez la recherche du type d’activité jusqu’à ce que vous trouviez une entrée nommée « FireActivityCompletedSuccessfullyEvent » qui possède une durée en millisecondes. Vérifiez que l’entrée a le même ID de requête, par exemple :

      ```text
      DM.EnterpriseGateway Verbose: 0 : 2016-09-26T23:08:56.7940067Z DM.EnterpriseGateway    baf40f21-2eb4-4af1-9c59-0950ef11ec4a    5f99f566-106d-c8ac-c864-c0808c41a606    MGEQ    21f96cc4-7496-bfdd-748c-b4915cb4b70c    B8DFCF12 [DM.Pipeline.Common.TracingTelemetryService] Event: FireActivityCompletedSuccessfullyEvent (duration=5004)
      ```

      > [!NOTE]
      > L’entrée « FireActivityCompletedSuccessfullyEvent » est une entrée détaillée et n’est pas connectée, sauf si le paramètre « TracingVerbosity » est au niveau 5.

### <a name="trace-traffic-with-fiddler"></a>Tracez le trafic avec Fiddler

[Fiddler](https://www.telerik.com/fiddler) est un outil gratuit développé par Telerik, qui surveille le trafic HTTP. Il vous permet de visualiser le trafic entre le service Power BI et la machine client. Ce service peut également indiquer les éventuelles erreurs et autres informations connexes.

## <a name="next-steps"></a>Étapes suivantes

* [Connexion à des données locales à partir d’applications logiques](../logic-apps/logic-apps-gateway-connection.md)
* [Fonctionnalités d’intégration d'entreprise](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Connecteurs pour Azure Logic Apps](../connectors/apis-list.md)
