---
title: Installation d’une passerelle de données locale - Azure Logic Apps | Microsoft Docs
description: Télécharger et installer la passerelle de données locale pour pouvoir accéder aux données locales avec Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 61a9b319b9ea44f766bc6f014b76bc48d15efc57
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598460"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Installer une passerelle de données locale pour Azure Logic Apps

Pour pouvoir vous connecter à des sources de données locales à partir d’Azure Logic Apps, téléchargez et installez la passerelle de données locale sur un ordinateur local. La passerelle fonctionne comme un pont qui effectue un transfert de données et de chiffrement entre des sources de données locales (pas situées sur le cloud) et vos applications logiques. Cet article vous explique comment télécharger, installer et configurer votre passerelle de données locale. 

Vous pouvez utiliser la même installation de passerelle pour d’autres services, tels que Power BI, Microsoft Flow, PowerApps et Azure Analysis Services. Pour en savoir plus, voir [Fonctionnement de la passerelle de données](#gateway-cloud-service).

<a name="supported-connections"></a>

La passerelle prend en charge [des connecteurs locaux](../connectors/apis-list.md#on-premises-connectors) dans Azure Logic Apps pour les sources de données suivantes :

*   BizTalk Server 2016
*   Système de fichiers
*   IBM DB2  
*   IBM Informix
*   IBM MQ
*   MySQL
*   Oracle Database
*   PostgreSQL
*   Serveur d’applications SAP 
*   Serveur de messagerie SAP
*   SharePoint Server
*   SQL Server
*   Teradata

Pour plus d’informations sur l’utilisation de la passerelle avec d’autres services, consultez les articles suivants :

* [Passerelle de données locale Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Passerelle de données locale Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Passerelle de données locale Microsoft Flow](https://flow.microsoft.com/documentation/gateway-manage/)
* [Passerelle de données locale Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

<a name="requirements"></a>

## <a name="prerequisites"></a>Prérequis

* Un [compte professionnel ou scolaire](../active-directory/fundamentals/sign-up-organization.md) avec un [abonnement Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access) 

  Pendant l’installation de la passerelle, identifiez-vous avec ce compte afin d’associer l’installation de passerelle à votre abonnement Azure. 
  Vous utiliserez par la suite le même compte lors de la création d’une ressource Azure pour installer votre passerelle dans le portail Azure. 
  Si vous n’avez pas encore d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>.

* Voici la configuration requise pour votre ordinateur local :

  **Configuration minimale requise**

  * .NET Framework 4.5.2
  * Version 64 bits de Windows 7 ou Windows Server 2008 R2 (ou version ultérieure)

  **Configuration recommandée**

  * Processeur 8 cœurs
  * 8 Go de mémoire
  * Version 64 bits de Windows Server 2012 R2 (ou version ultérieure)

* **Points importants à prendre en compte**

  * Vous pouvez uniquement installer la passerelle de données locale sur un ordinateur local, et pas sur un contrôleur de domaine. Néanmoins, il n’est pas absolument nécessaire d’installer la passerelle sur le même ordinateur que celui sur lequel se trouve votre source de données. En outre, vous n’avez besoin que d’une seule passerelle pour toutes vos sources de données, par conséquent, il est inutile d’installer une passerelle pour chaque source de données.

    > [!TIP]
    > Pour réduire la latence, vous pouvez installer la passerelle le plus près possible de votre source de données, ou sur le même ordinateur, en supposant que vous disposiez des autorisations nécessaires.

  * Installez la passerelle sur un ordinateur connecté à Internet, toujours allumé et qui ne se met *pas* en veille. Sinon, la passerelle ne peut pas s’exécuter. 
  Par ailleurs, les performances peuvent être réduites sur un réseau sans fil.

  * Pendant l’installation, vous pouvez uniquement vous connecter avec un [compte professionnel ou scolaire](../active-directory/sign-up-organization.md) géré par Azure Active Directory (Azure AD), par exemple @contoso.onmicrosoft.com, et non par un compte Azure B2B (invité) ou un compte Microsoft personnel, tel que @hotmail.com ou @outlook.com. 
  Veillez à utiliser le même compte de connexion lorsque vous inscrivez votre installation de passerelle dans le portail Azure en créant une ressource de passerelle. 
  Vous sélectionnerez ensuite cette ressource de passerelle lors de la création de la connexion entre votre application logique et la source de données locale. 
  [Pourquoi dois-je utiliser un compte professionnel ou scolaire Azure AD ?](#why-azure-work-school-account)

  > [!TIP]
  > Si vous avez souscrit à une offre Office 365 sans fournir votre véritable adresse e-mail professionnelle, il est possible que votre adresse de connexion ressemble à ceci : `username@domain.onmicrosoft.com` 
  >
  > Si vous souhaitez utiliser un compte Microsoft qui possède un abonnement standard à [Visual Studio](https://visualstudio.microsoft.com/vs/pricing/), commencez par [créer un répertoire (client) dans Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md), ou utilisez le répertoire par défaut avec votre compte Microsoft personnel. 
  > Ajoutez un utilisateur avec un mot de passe au répertoire, puis donnez-lui accès à votre abonnement. 
  > Vous pourrez alors vous connecter au cours de l’installation de la passerelle avec ce nom d’utilisateur et ce mot de passe.

  * La région Azure que vous sélectionnez pour l’installation de votre passerelle détermine l’emplacement où vous inscrirez plus tard votre passerelle dans Azure en créant une ressource Azure. 
  Lorsque vous créez cette ressource de passerelle dans Azure, vous devez sélectionner le *même* emplacement que votre installation de passerelle. La région Azure par défaut est dans le même emplacement que votre client Azure AD, qui gère votre compte Azure, mais vous pouvez modifier l’emplacement lors de l’installation de la passerelle.

  * Si vous disposez d’une passerelle existante que vous configurez avec un programme d’installation antérieur à la version 14.16.6317.4, vous ne pouvez pas modifier l’emplacement de votre passerelle en exécutant le programme d’installation le plus récent. Toutefois, vous pouvez utiliser le programme d’installation le plus récent pour configurer une nouvelle passerelle avec l’emplacement que vous désirez à la place.
  
    Si vous possédez un programme d’installation de passerelle antérieur à la version 14.16.6317.4, mais vous n’avez pas encore installé votre passerelle, vous pouvez télécharger et utiliser le programme d’installation le plus récent.

## <a name="high-availability-support"></a>Prise en charge de la haute disponibilité

La passerelle de données locale prend en charge la haute disponibilité lorsque vous avez plusieurs installations de passerelle et que vous les configurez en tant que clusters. Si vous avez une passerelle existante alors que vous êtes sur le point de créer une nouvelle passerelle, vous pouvez créer en option des clusters à haute disponibilité. Ces clusters regroupent les passerelles en groupes, ce qui peut permettre d’éviter les points de défaillance uniques. En outre, tous les connecteurs de passerelle de données locale prennent désormais en charge la haute disponibilité.

Pour utiliser la passerelle de données locale, prenez connaissance de ce qui suit :

* Vous devez déjà disposer d’au moins une passerelle d’installation dans le même abonnement Azure servant de passerelle principale et de clé de récupération pour cette installation. 

* Votre passerelle principale doit fonctionner sous la mise à jour de passerelle datant de novembre 2017 ou une version plus récente.

Une fois ces exigences satisfaites, lorsque vous créez votre nouvelle passerelle, sélectionnez **Ajouter à un cluster de passerelle existant**, sélectionnez la passerelle principale pour votre cluster, et fournissez la clé de récupération de cette passerelle principale.
Pour plus d’informations, consultez [Clusters à haute disponibilité pour la passerelle de données locale](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Installer une passerelle de données

1. [Téléchargez, enregistrez et exécutez le programme d’installation de la passerelle sur un ordinateur local](https://aka.ms/on-premises-data-gateway-installer).

2. Acceptez le chemin d’installation par défaut, ou spécifiez l’emplacement de l’installation de la passerelle sur votre ordinateur.

3. Lisez et acceptez les conditions d’utilisation et la déclaration de confidentialité, puis cliquez sur **Installer**.

   ![J’accepte les conditions d’utilisation et la déclaration de confidentialité](./media/logic-apps-gateway-install/accept-terms.png)

4. Une fois la passerelle installée avec succès, fournissez l’adresse de messagerie pour votre compte professionnel ou scolaire, puis choisissez **Se connecter**.

   ![Connectez-vous avec un compte professionnel ou scolaire](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. Choisissez **Enregistrer une nouvelle passerelle sur cet ordinateur** > **Suivant**, ce qui inscrit votre installation de passerelle auprès du [service cloud de passerelle](#gateway-cloud-service). 

   ![Inscrivez la passerelle](./media/logic-apps-gateway-install/register-new-gateway.png)

6. Fournissez les informations suivantes pour votre installation de passerelle :

   * Le nom souhaité pour votre installation 

   * La clé de récupération que vous souhaitez créer, et qui doit avoir au moins huit caractères

     > [!IMPORTANT]
     > Sauvegardez votre clé de récupération dans un endroit sûr. Vous en aurez besoin lors de la modification de l’emplacement de la passerelle, ou lorsque vous effectuez une migration, une restauration ou que vous reprenez une passerelle existante.

   * Confirmation de votre clé de récupération 

     ![Configuration de la passerelle](./media/logic-apps-gateway-install/set-up-gateway.png)

7. Vérifiez la région sélectionnée pour le service cloud de passerelle, et pour l’Azure Service Bus utilisé par votre installation de passerelle. 

   ![Vérification de la région](./media/logic-apps-gateway-install/check-region.png)

   > [!IMPORTANT]
   > Pour modifier cette région une fois l’installation de votre passerelle terminée, vous avez besoin de la clé de récupération pour cette installation de passerelle. En outre, vous devez désinstaller et réinstaller la passerelle. Pour plus d’informations, consultez [Modifier l’emplacement, migrer, restaurer ou contrôler une passerelle existante](#update-gateway-installation).

   *Pourquoi modifier la région de votre programme d’installation de passerelle ?* 

   Par exemple, pour réduire la latence, vous pouvez modifier la région de votre passerelle vers la même région que votre application logique. 
   Ou bien, vous pouvez sélectionner la région la plus proche de votre source de données locale. 
   Votre *ressource de passerelle sur Azure* et votre application logique peuvent se trouver dans des emplacements différents.

8. Pour accepter la région par défaut, choisissez **Configurer**. Ou, pour modifier la région par défaut, procédez comme suit :

   1. À côté de la région actuelle, sélectionnez **Changer la région**. 

      ![Modification de la région](./media/logic-apps-gateway-install/change-region.png)

   2. Sur la page suivante, ouvrez la liste **Sélectionner une région**, sélectionnez la région de votre choix, et cliquez sur **Terminé**.

      ![Sélection d’une autre région](./media/logic-apps-gateway-install/select-region-gateway-install.png)

9. Une fois que la page de confirmation s’affiche, choisissez **Fermer**. 

   Le programme d’installation confirme que votre passerelle est désormais en ligne et prête à être utilisée.

   ![Passerelle terminée](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

10. Enregistrez à présent votre passerelle dans Azure en [créant une ressource Azure pour votre programme d’installation de passerelle](../logic-apps/logic-apps-gateway-connection.md). 

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Modifier l’emplacement, migrer, restaurer ou contrôler une passerelle existante

Si vous devez modifier l’emplacement de votre passerelle, déplacer votre programme d’installation de passerelle vers un nouvel ordinateur, récupérer une passerelle endommagée ou contrôler une passerelle existante, vous avez besoin de la clé de récupération qui a été fournie lors de l’installation de la passerelle. Cette action déconnecte l’ancienne passerelle.

1. À partir du **Panneau de configuration** de votre ordinateur, accédez à **Programmes et fonctionnalités**. Dans la liste des programmes, sélectionnez **Passerelle de données locale**, puis cliquez sur **Désinstaller**.

2. [Réinstallez la passerelle de données locale](https://aka.ms/on-premises-data-gateway-installer).

3. Une fois le programme d’installation ouvert, connectez-vous avec le compte professionnel ou scolaire utilisé précédemment pour installer la passerelle.

4. Sélectionnez **Migrer, restaurer ou contrôler une passerelle existante**, puis cliquez sur **Suivant**.

   ![Sélectionnez « Migrer, restaurer ou contrôler une passerelle existante »](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

5. Sous **Passerelles disponibles** ou **Clusters de passerelles disponibles**, sélectionnez l’installation de passerelle que vous souhaitez modifier. Entrez la clé de récupération pour l’installation de passerelle. 

   ![Sélectionnez la passerelle principale](./media/logic-apps-gateway-install/select-existing-gateway.png)

6. Pour modifier la région, sélectionnez **Modifier la région** et la nouvelle région.

7. Lorsque vous êtes prêt, choisissez **Enregistrer**.

## <a name="configure-proxy-or-firewall"></a>Configurer le proxy ou le pare-feu

La passerelle de données locale crée une connexion sortante vers [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Si votre environnement de travail nécessite que le trafic passe par un proxy pour accéder à internet, cette restriction peut empêcher la passerelle de données de se connecter au service cloud de la passerelle. Pour déterminer si votre réseau utilise un proxy, consultez cet article sur superuser.com : 

[Comment savoir quel serveur proxy j’utilise ? (SuperUser.com)](https://superuser.com/questions/346372/how-do-i-know-what-proxy-server-im-using) 

Pour spécifier les informations proxy pour votre passerelle, consultez [Configurer les paramètres proxy](https://docs.microsoft.com/power-bi/service-gateway-proxy). Pour vérifier si votre pare-feu ou proxy risque de bloquer des connexions, vérifiez que votre ordinateur peut effectivement se connecter à Internet et à [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). À partir d’une invite PowerShell, exécutez la commande suivante :

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

## <a name="configure-ports"></a>Configuration des ports

La passerelle crée une connexion sortante vers [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) et communique sur les ports de sortie suivants : TCP 443 (par défaut), 5671, 5672 et 9350 à 9354. La passerelle ne nécessite pas de ports entrants. Pour en savoir plus, voir [Solutions Azure Service Bus et hybrides](../service-bus-messaging/service-bus-messaging-overview.md).

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

Dans certains cas, les connexions Azure Service Bus s’effectuent avec l’adresse IP plutôt qu’avec le nom de domaine complet. Nous vous recommandons de mettre sur liste approuvée les adresses IP pour votre région de données dans votre pare-feu. Si vous avez besoin de mettre sur liste approuvée des adresses IP plutôt que des domaines, vous pouvez télécharger et utiliser la [Liste des plages d’adresses IP du centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Les adresses IP dans cette liste sont en notation [Classless Inter-Domain Routing (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

### <a name="force-https-communication-with-azure-service-bus"></a>Forcer les communications HTTPS avec Azure Service Bus

Certains proxys autorisent uniquement le trafic vers les ports 80 et 443. Par défaut, la communication avec Azure Service Bus se produit sur des ports autres que 443.
Vous pouvez forcer la passerelle à communiquer avec Azure Service Bus à l’aide de HTTPS au lieu de TCP direct ; toutefois, cela peut considérablement réduire les performances. Pour accomplir cette tâche, procédez comme suit :

1. Accédez à l’emplacement pour le client de passerelle de données locale, qui se trouve généralement ici : ```C:\Program Files\On-premises data gateway\Microsoft.PowerBI.EnterpriseGateway.exe```

   Sinon, pour rechercher l’emplacement du client, ouvrez la console Services sur le même ordinateur, recherchez le **Service de passerelle de données locale** et affichez la propriété **Chemin d’accès au fichier exécutable**.

2. Ouvrez ce fichier de *configuration* : **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

3. Modifiez la valeur **ServiceBusSystemConnectivityModeString** de **AutoDetect** à **Https** :

   ```html
   <setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
      <value>Https</value>
   </setting>
   ```

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Compte de service Windows

Sur l’ordinateur où vous installez la passerelle de données locale, la passerelle s’exécute en tant que compte de service Windows nommé « Service de passerelle de données locale ». Toutefois, la passerelle utilise le nom « NT SERVICE\PBIEgwService » pour ses informations d’identification de compte « Se connecter en tant que ». Par défaut, la passerelle dispose des autorisations nécessaires pour « Se connecter en tant que service » sur l’ordinateur où vous installez la passerelle. Le compte de service Windows pour la passerelle diffère généralement du compte que vous utilisez pour la connexion aux sources de données locales, ainsi que du compte professionnel ou scolaire que vous utilisez pour vous connecter aux services cloud.

Pour créer et gérer la passerelle sur le Portail Azure, ce compte de service Windows doit disposer au moins des autorisations **Contributeur**. Pour en savoir plus sur ces autorisations, voir [Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du portail Azure](../role-based-access-control/role-assignments-portal.md). 

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Redémarrer la passerelle

La passerelle de données s’exécute en tant que service Windows et, comme avec n’importe quel autre service Windows, vous pouvez la démarrer et l’arrêter de plusieurs façons. Par exemple, vous pouvez ouvrir une invite de commandes avec des autorisations élevées sur l’ordinateur sur lequel la passerelle s’exécute, puis exécuter l’une des commandes suivantes :

* Pour arrêter le service, exécutez cette commande :
  
  `net stop PBIEgwService`

* Pour démarrer le service, exécutez cette commande :
  
  `net start PBIEgwService`

## <a name="tenant-level-administration"></a>Administration au niveau du locataire 

Il n’existe actuellement aucun emplacement unique où les administrateurs client peuvent gérer toutes les passerelles que d’autres utilisateurs ont installées et configurées. Si vous êtes un administrateur client, vous pouvez demander aux utilisateurs de votre organisation de vous ajouter en tant qu’administrateur pour chaque passerelle qu’ils installent. Cela vous permet ainsi de gérer toutes les passerelles de votre organisation via la page Paramètres des passerelles, ou via les [Commandes PowerShell](/data-integration/gateway/service-gateway-powershell-support). 

<a name="gateway-cloud-service"></a>

## <a name="how-does-the-gateway-work"></a>Comment fonctionne la passerelle ?

La passerelle de données assure une communication rapide et sécurisée entre votre application logique, le service cloud de passerelle et votre source de données locale. Le service cloud de passerelle chiffre et stocke les informations d’identification de votre source de données et les détails de la passerelle. Le service achemine également les requêtes et leurs résultats entre votre application logique, la passerelle de données locale et votre source de données locale. 

La passerelle fonctionne avec les pare-feux et utilise uniquement des connexions sortantes. Tout le trafic est initialisé en tant que trafic sortant de l’agent de passerelle sécurisé. La passerelle relaie les données des sources locales sur des canaux chiffrés via Azure Service Bus. Ce service bus crée un canal entre la passerelle et le service d’appel, mais ne stocke aucune donnée. Toutes les données qui transitent via la passerelle sont chiffrées.

![diagramme de flux de passerelle de données locale](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

Ces étapes décrivent ce qu’il se produit lorsqu’un utilisateur dans le cloud interagit avec un élément connecté à votre source de données locale :

1. Le service cloud de passerelle crée une requête, ainsi que les informations d’identification chiffrées pour la source de données, puis envoie la requête vers la file d’attente afin que la passerelle la traite.

2. Le service cloud de passerelle analyse la requête et envoie les demandes à Azure Service Bus.

3. La passerelle de données locale interroge Azure Service Bus pour connaître les requêtes en attente.

4. La passerelle reçoit la requête, déchiffre les informations d’identification et se connecte à la source de données avec ces informations d’identification.

5. La passerelle envoie la requête à la source de données pour exécution.

6. Les résultats sont envoyés de la source de données vers la passerelle, puis au service cloud de passerelle. Le service cloud de passerelle utilise ensuite les résultats.

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

* L’installation de votre passerelle est déjà enregistrée et revendiquée par une autre ressource de passerelle dans Azure. Les installations de passerelle ne s’affichent pas dans la liste des instances après la création de ressources de passerelle pour elles.
Pour vérifier vos inscriptions de passerelle dans le portail Azure, passez en revue toutes vos ressources Azure avec le type de **Passerelles de données locales** pour *tous* les abonnements Azure. 

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
2. Dans le menu de gauche, sélectionnez **Diagnostiques**.
3. Sous **Journaux d’activité de passerelle**, sélectionnez **Exporter des journaux d’activité**.

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
2. Développez l’**Observateur d’événements (Local)**  > **Journaux des applications et des services**. 
3. Sélectionnez **Service de passerelle de données locale**.

   ![Afficher les journaux d’événements pour la passerelle](./media/logic-apps-gateway-install/event-viewer.png)

### <a name="review-slow-query-performance"></a>Examinez les performances des requêtes lentes

Si vous trouvez que les requêtes s’exécutent lentement via la passerelle, vous pouvez activer la journalisation supplémentaire qui affiche les requêtes et leur durée. Ces journaux d’activité peuvent vous aider à trouver quelles requêtes s’exécutent lentement. Pour optimiser les performances des requêtes, vous devrez peut-être modifier votre source de données, par exemple en ajustant les index pour les requêtes de SQL Server.

Pour déterminer la durée d’une requête, procédez comme suit :

1. Accédez au même emplacement que pour le client de passerelle, qui se trouve généralement ici : ```C:\Program Files\On-premises data gateway```

   Sinon, pour rechercher l’emplacement du client, ouvrez la console Services sur le même ordinateur, recherchez le **Service de passerelle de données locale** et affichez la propriété **Chemin d’accès au fichier exécutable**.

2. Ouvrez et modifiez ces fichiers de configuration, comme indiqué :

   * **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

     Dans ce fichier, modifiez la valeur **EmitQueryTraces** de **false** en **true**, de sorte que votre passerelle puisse enregistrer les requêtes envoyées à partir de la passerelle vers une source de données :

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

3. Pour déterminer la durée d’une requête, procédez comme suit :

   1. [Exporter](#logs) et ouvrez le journal de la passerelle.

   2. Pour rechercher une requête, recherchez un type d’activité, par exemple : 

      | Type d’activité | Description | 
      |---------------|-------------| 
      | MGEQ | Requêtes qui s’exécutent sur ADO.NET. | 
      | MGEO | Requêtes qui s’exécutent sur OLEDB. | 
      | MGEM | Requêtes qui s’exécutent à partir du moteur Mashup. | 
      ||| 

   3. Notez le second GUID, qui est l’ID de requête.

   4. Continuez la recherche du type d’activité jusqu’à ce que vous trouviez une entrée nommée « FireActivityCompletedSuccessfullyEvent » qui possède une durée en millisecondes. 
   Vérifiez que l’entrée a le même ID de requête, par exemple :

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
