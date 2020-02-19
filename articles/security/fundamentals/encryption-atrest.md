---
title: Chiffrement des données au repos de Microsoft Azure | Microsoft Docs
description: Cet article fournit une vue d’ensemble du chiffrement des données au repos de Microsoft Azure, avec ses fonctionnalités globales et des considérations générales.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/07/2020
ms.author: barclayn
ms.openlocfilehash: 682f0b66f7632bce16ae134e71ea27c4df976f43
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087100"
---
# <a name="azure-data-encryption-at-rest"></a>Chiffrement des données au repos d’Azure

Microsoft Azure comprend des outils pour protéger les données en fonction des besoins de sécurité et de conformité de votre entreprise. Ce document porte sur les points suivants :

- Protection des données au repos sur Microsoft Azure
- Présentation des différents composants impliqués dans la mise en œuvre de la protection des données
- Examen des avantages et des inconvénients des principales approches de protection de la gestion

Le chiffrement au repos est une exigence de sécurité courante. Dans Azure, les organisations peuvent chiffrer les données au repos, sans assumer les risques et les coûts associés à une solution de gestion des clés personnalisée. Les organisations ont la possibilité de laisser Azure gérer complètement le chiffrement au repos. En outre, les organisations ont différentes options pour gérer étroitement le chiffrement ou les clés de chiffrement.

## <a name="what-is-encryption-at-rest"></a>Qu’est-ce que le chiffrement au repos ?

Le chiffrement au repos fait référence à l’encodage (chiffrement) des données quand elles sont stockées. La conception du chiffrement au repos dans Azure utilise le chiffrement symétrique pour chiffrer et déchiffrer rapidement de grandes quantités de données selon un modèle conceptuel simple :

- Une clé de chiffrement symétrique est utilisée pour chiffrer les données au fil de leur stockage.
- La clé de chiffrement est utilisée pour déchiffrer ces données au fil de leur préparation à une utilisation en mémoire.
- Les données peuvent être partitionnées, et des clés différentes peuvent être utilisées pour chaque partition.
- Les clés doivent être stockées dans un emplacement sécurisé doté du contrôle d’accès basé sur l’identité et de stratégies d’audit. Les clés de chiffrement de données sont souvent chiffrées avec une clé de chiffrement de clé dans Azure Key Vault pour limiter davantage l’accès.

Dans la pratique, les scénarios de gestion et de contrôle des clés, ainsi que les garanties de scalabilité et de disponibilité, nécessitent des mécanismes supplémentaires. Les concepts et les composants du chiffrement des données au repos de Microsoft Azure sont décrits ci-dessous.

## <a name="the-purpose-of-encryption-at-rest"></a>Objectif du chiffrement au repos

Le chiffrement au repos offre une protection des données pour les données stockées (au repos). Les attaques contre les données au repos sont notamment des tentatives d’obtenir un accès physique au matériel sur lequel les données sont stockées puis de compromettre les données qui y sont contenues. Dans une telle attaque, le disque dur d’un serveur peut avoir fait l’objet d’une mauvaise manipulation lors de la maintenance, permettant à un attaquant de retirer le disque dur. Plus tard, cet attaquant peut placer le disque dur dans un ordinateur qu’il contrôle pour tenter d’accéder aux données.

Le chiffrement au repos est conçu pour empêcher l’attaquant d’accéder aux données non chiffrées en garantissant que les données sont chiffrées quand elles sont sur le disque. Si un attaquant récupère un disque dur comprenant des données chiffrées, mais qu’il ne dispose pas des clés de chiffrement, il doit résoudre le chiffrement pour lire les données. Ce type d’attaque est beaucoup plus complexe et laborieux comparé aux attaques de données non chiffrées sur un disque dur. Pour cette raison, le chiffrement au repos est fortement recommandé et constitue une exigence de haute priorité pour de nombreuses organisations.

Le chiffrement au repos peut également être nécessaire pour les besoins de l’organisation en matière de gouvernance et de conformité des données. Les réglementations publiques et de l’industrie, comme HIPAA, PCI et FedRAMP définissent des protections spécifiques quant aux exigences de protection et de chiffrement des données. Le chiffrement au repos est une mesure obligatoire nécessaire à la conformité avec certaines de ces réglementations.

Non seulement le chiffrement au repos répond aux exigences de conformité et aux obligations réglementaires, mais il fournit une défense en profondeur. Microsoft Azure fournit une plateforme conforme destinée aux services, aux applications et aux données. Il fournit également une sécurité complète des équipements et des éléments physiques, un contrôle d’accès aux données et des fonctionnalités d’audit. Toutefois, il est important de mettre en œuvre des mesures de sécurité « superposées » supplémentaires en cas de défaillance de l’une des mesures de sécurité principales. Le chiffrement au repos propose une telle mesure de sécurité.

Microsoft s’engage à fournir des options de chiffrement au repos sur les services cloud et à donner aux clients le contrôle des clés de chiffrement et la journalisation de l’utilisation des clés. En outre, Microsoft s’emploie à mettre en œuvre par défaut le chiffrement au repos de toutes les données des clients.

## <a name="azure-encryption-at-rest-components"></a>Composants du chiffrement au repos d’Azure

Comme décrit précédemment, l’objectif du chiffrement au repos est que les données stockées sur disque soient chiffrées avec une clé de chiffrement secrète. Pour atteindre cet objectif de clés sécurisées, il est nécessaire de disposer d’un système permettant la création, le stockage, le contrôle d’accès et la gestion des clés de chiffrement. Bien que des détails puissent varier, les implémentations du chiffrement au repos des services Azure peuvent être décrites selon les termes illustrés dans le diagramme suivant.

![Components](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

L’emplacement de stockage des clés de chiffrement et le contrôle d’accès à ces clés sont fondamentaux pour un modèle de chiffrement au repos. Les clés doivent être hautement sécurisées, mais gérables par des utilisateurs spécifiés et disponibles pour des services spécifiques. Pour les services Azure, Azure Key Vault est la solution de stockage des clés recommandée et offre une expérience de gestion commune entre les services. Les clés sont stockées et gérées dans des coffres de clés, et l’accès à un coffre de clés peut être donné à des utilisateurs ou à des services. Azure Key Vault prend en charge la création de clés par les clients ou l’importation de clés des clients pour les utiliser dans des scénarios où les clés de chiffrement sont gérées par ces clients.

### <a name="azure-active-directory"></a>Azure Active Directory

Les autorisations d’utiliser les clés stockées dans Azure Key Vault, pour les gérer ou pour y accéder pour les chiffrer et les déchiffrer dans le cadre du chiffrement au repos, peuvent être données à des comptes Azure Active Directory.

### <a name="key-hierarchy"></a>Hiérarchie des clés

Plusieurs clés de chiffrement sont utilisées dans une implémentation du chiffrement au repos. Le stockage d’une clé de chiffrement dans Azure Key Vault garantit l’accès sécurisé aux clés et la gestion centralisée des clés. Toutefois, l’accès local du service aux clés de chiffrement est plus efficace pour le chiffrement et le déchiffrement en bloc que d’interagir avec Key Vault pour chaque opération de données, ce qui permet un chiffrement renforcé et de meilleures performances. Limiter l’utilisation d’une seule clé de chiffrement réduit le risque que la clé soit compromise et le coût du rechiffrement quand une clé doit être remplacée. Les modèles de chiffrement Azure au repos utilisent une hiérarchie de clé constituée des types de clés suivants afin de répondre à tous ces besoins :

- **Clé de chiffrement des données** : une clé symétrique AES256 utilisée pour chiffrer une partition ou un bloc de données.  Une même ressource peut avoir plusieurs partitions et de nombreuses clés de chiffrement des données. Le chiffrement de chaque bloc de données avec une clé différente rend les attaques d’analyse du chiffrement plus difficiles. L’accès aux clés de chiffrement des données est nécessaire au fournisseur de ressources ou à l’instance d’application qui chiffre et déchiffre un bloc spécifique. Quand une clé de chiffrement des données est remplacée par une nouvelle clé, seules les données du bloc qui y est associé doivent être rechiffrées avec la nouvelle clé.
- **Clé de chiffrement des clés** : une clé de chiffrement utilisée pour chiffrer les clés de chiffrement des données. L’utilisation d’une clé de chiffrement des clés ne quittant jamais le coffre de clés permet le chiffrement et le contrôle des clés de chiffrement des données elles-mêmes. L’entité qui a accès à la clé de chiffrement des clés peut être différente de l’entité qui a besoin de la clé de chiffrement des données. Une entité peut répartir l’accès à la clé de chiffrement des clés pour limiter l’accès de chaque clé de chiffrement des clés vers une partition spécifique. Comme la clé de chiffrement des clés est nécessaire pour déchiffrer les clés de chiffrement des données, la clé de chiffrement des clés est dès lors le point unique par lequel les clés de chiffrement des données peuvent être supprimées en supprimant la clé de chiffrement des clés.

Les clés de chiffrement des données chiffrées avec des clés de chiffrement des clés sont stockées séparément, et seule une entité ayant accès à la clé de chiffrement des clés peut déchiffrer ces clés de chiffrement des données. Différents modèles de stockage des clés sont pris en charge. Nous allons décrire chaque modèle plus en détail plus loin, dans la section suivante.

## <a name="data-encryption-models"></a>Modèles de chiffrement des données

Une compréhension des différents modèles de chiffrement, ainsi que de leurs avantages et inconvénients, est essentielle pour comprendre comment les différents fournisseurs de ressources dans Azure implémentent le chiffrement au repos. Ces définitions sont partagées par tous les fournisseurs de ressources dans Azure, ce qui garantit un langage et une taxonomie communs.

Il existe trois scénarios de chiffrement côté serveur :

- Chiffrement côté serveur à l’aide de clés gérées par le service
  - Les fournisseurs de ressources Azure effectuent les opérations de chiffrement et de déchiffrement
  - Microsoft gère les clés
  - Fonctionnalité cloud complète

- Chiffrement côté serveur à l’aide de clés gérées par le client dans Azure Key Vault
  - Les fournisseurs de ressources Azure effectuent les opérations de chiffrement et de déchiffrement
  - Le client contrôle les clés via Azure Key Vault
  - Fonctionnalité cloud complète

- Chiffrement côté serveur à l’aide de clés gérées par le client sur du matériel contrôlé par le client
  - Les fournisseurs de ressources Azure effectuent les opérations de chiffrement et de déchiffrement
  - Le client contrôle les clés sur du matériel contrôlé par le client
  - Fonctionnalité cloud complète

Pour le chiffrement côté client, considérez les éléments suivants :

- Les services Azure ne peuvent pas voir les données déchiffrées
- Les clients gèrent et stockent les clés localement (ou dans d’autres magasins sécurisés). Les clés ne sont pas disponibles pour les services Azure
- Fonctionnalité cloud réduite

Les modèles de chiffrement pris en charge dans Azure se divisent en deux groupes principaux : « Chiffrement client » et « Chiffrement côté serveur », comme mentionné précédemment. Indépendamment du modèle de chiffrement au repos utilisé, les services Azure recommandent toujours l’utilisation d’un transport sécurisé, comme TLS ou HTTPS. Ainsi, le chiffrement du transport doit être traité par le protocole de transport et il ne doit pas être un facteur majeur pour déterminer le modèle de chiffrement au repos à utiliser.

### <a name="client-encryption-model"></a>Modèle de chiffrement client

Le modèle de chiffrement client fait référence au chiffrement qui est effectué en dehors du fournisseur de ressources ou d’Azure par le service ou l’application appelante. Le chiffrement peut être effectué par l’application de service dans Azure, ou par une application s’exécutant dans le centre de données du client. Dans les deux cas, quand vous tirez parti de ce modèle de chiffrement, le fournisseur de ressources Azure reçoit un objet blob chiffré des données sans possibilité de déchiffrer les données en aucune façon ou d’avoir accès aux clés de chiffrement. Dans ce modèle, la gestion des clés est effectuée par le service ou l’application appelant et est opaque pour le service Azure.

![Client](./media/encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Modèle de chiffrement côté serveur

Les modèles de chiffrement côté serveur se réfèrent au chiffrement qui est effectué par le service Azure. Dans ce modèle, le fournisseur de ressources effectue les opérations de chiffrement et de déchiffrement. Par exemple, le stockage Azure peut recevoir des données dans des opérations en texte brut, et effectue le chiffrement et le déchiffrement en interne. Le fournisseur de ressources peut utiliser des clés de chiffrement qui sont gérées par Microsoft ou par le client, selon la configuration fournie.

![Serveur](./media/encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Modèles de gestion des clés de chiffrement côté serveur

Chacun des modèles de chiffrement au repos côté serveur a des particularités distinctives dans la gestion des clés. Il s’agit notamment de l’endroit et de la façon dont les clés de chiffrement sont créées et stockées, ainsi que des modèles d’accès et des procédures de rotation des clés.

#### <a name="server-side-encryption-using-service-managed-keys"></a>Chiffrement côté serveur à l’aide de clés gérées par le service

Pour de nombreux clients, l’exigence principale est de garantir que les données sont chiffrées quand elles sont au repos. Le chiffrement côté serveur à l’aide de clés gérées par le service autorise ce modèle en permettant aux clients de marquer la ressource spécifique (compte de stockage, base de données SQL, etc.) pour le chiffrement et de laisser tous les aspects de la gestion des clés, comme l’émission, la sauvegarde et la rotation des clés, à Microsoft. La plupart des services Azure prenant en charge le chiffrement au repos prennent généralement en charge ce modèle de déchargement de la gestion des clés de chiffrement sur Azure. Le fournisseur de ressources Azure crée les clés, les place dans un stockage sécurisé et les récupère quand c’est nécessaire. Cela signifie que le service a un accès complet aux clés et qu’il a un contrôle total sur la gestion du cycle de vie des informations d’identification.

![géré](./media/encryption-atrest/azure-security-encryption-atrest-fig4.png)

Le chiffrement côté serveur à l’aide de clés gérées par le service répond ainsi rapidement au besoin de chiffrement au repos avec une charge de travail peu importante au niveau du client. Quand il est disponible, un client ouvre en général le portail Azure pour l’abonnement cible et le fournisseur de ressources, et il coche une case indiquant qu’il veut que les données soient chiffrées. Dans certains gestionnaires de ressources, le chiffrement côté serveur avec des clés gérées par le service est activé par défaut.

Le chiffrement côté serveur avec des clés gérées par Microsoft implique que le service dispose d’un accès complet pour le stockage et la gestion des clés. Si certains clients peuvent souhaiter gérer les clés en pensant qu’ils peuvent assurer une meilleure sécurité, le coût et le risque associés à une solution de stockage de clés personnalisée doivent être considérés lors de l’évaluation de ce modèle. Dans de nombreux cas, une organisation peut déterminer que, dans le cadre d’une solution locale, les contraintes liées aux ressources ou les risques peuvent être supérieurs aux risques liés à la gestion des clés de chiffrement au repos dans le cloud.  Cependant, il se peut que ce modèle ne soit pas adapté aux organisations qui doivent répondre à des exigences quant au contrôle de la création ou du cycle de vie des clés de chiffrement, ou encore à celles qui souhaitent que les gestionnaires des clés de chiffrement d’un service ne soient pas les personnes qui gèrent le service (séparer la gestion des clés du modèle de gestion global du service).

##### <a name="key-access"></a>Accès aux clés

Quand le chiffrement côté serveur avec des clés gérées par le service est utilisé, la création des clés, leur stockage et l’accès du service à ces clés sont gérés par le service. En règle générale, les principaux fournisseurs de ressources Azure stockent les clés de chiffrement des données dans un magasin qui est proche des données, et qui est disponible et accessible rapidement, alors que les clés de chiffrement des clés sont stockées dans un magasin interne sécurisé.

**Avantages**

- Simplicité de la configuration
- Microsoft gère la rotation, la sauvegarde et la redondance des clés
- Le client ne doit pas faire face au coût associé à l’implémentation ni au risque d’un schéma de gestion des clés personnalisé.

**Inconvénients**

- Pas de contrôle du client sur les clés de chiffrement (spécification, cycle de vie, révocation des clés, etc.)
- Pas de possibilité de séparer la gestion de clés du modèle de gestion global pour le service

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Chiffrement côté serveur à l’aide de clés gérées par le client dans Azure Key Vault

Dans les scénarios où les données doivent être chiffrées au repos et les clés de chiffrement doivent être contrôlées, les clients peuvent utiliser le chiffrement côté serveur à l’aide de clés gérées par le client dans Azure Key Vault. Certains services peuvent stocker seulement la clé de chiffrement des clés racine dans Azure Key Vault et stocker la clé de chiffrement des données chiffrée à un emplacement interne plus proche des données. Dans ce scénario, les clients peuvent apporter leurs propres clés au coffre de clés (BYOK, Bring Your Own Key), ou en générer de nouvelles et les utiliser pour chiffrer les ressources souhaitées. Quand le fournisseur de ressources effectue les opérations de chiffrement et de déchiffrement, il utilise la clé de chiffrement de la clé configurée comme clé racine pour toutes les opérations de chiffrement.

La perte de clés de chiffrement de clé signifie la perte de données. Pour cette raison, les clés ne doivent pas être supprimées. Les clés doivent être sauvegardées chaque fois qu’elles sont créées ou pivotées. [La suppression réversible](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) doit être activée sur tout coffre stockant des clés de chiffrement de clé. Au lieu de supprimer une clé, affectez à l’activé la valeur false ou définissez la date d’expiration.

##### <a name="key-access"></a>Accès aux clés

Le modèle de chiffrement côté serveur avec des clés gérées par le client dans Azure Key Vault implique que le service accède aux clés pour chiffrer et déchiffrer lorsque nécessaire. Les clés de chiffrement au repos sont rendues accessibles à un service via une stratégie de contrôle d’accès. Cette stratégie accorde à cette identité de service un accès pour recevoir la clé. Un service Azure s’exécutant pour le compte d’un abonnement associé peut être configuré avec une identité dans cet abonnement. Le service peut effectuer l’authentification Azure Active Directory et recevoir un jeton d’authentification en s’identifiant lui-même comme étant ce service agissant pour le compte de l’abonnement. Ce jeton peut ensuite être présenté au coffre de clés pour obtenir une clé à laquelle l’accès lui a été donné.

Pour les opérations utilisant des clés de chiffrement, une identité de service peut être autorisée à accéder aux opérations suivantes : déchiffrer, chiffrer, unwrapKey, wrapKey, vérifier, signer, obtenir, répertorier, mettre à jour, créer, importer, supprimer, sauvegarder et restaurer.

Pour obtenir une clé à utiliser dans le chiffrement ou le déchiffrement des données au repos, l’identité de service pour le compte duquel l’instance du service Resource Manager s’exécutera doit avoir les autorisations UnwrapKey (pour obtenir la clé de déchiffrement) et WrapKey (pour insérer une clé dans le coffre de clés lors de la création d’une nouvelle clé).

>[!NOTE]
>Pour plus d’informations sur les autorisations de Key Vault, consultez la page consacrée à la sécurisation de votre coffre de clés dans la [documentation d’Azure Key Vault](../../key-vault/key-vault-secure-your-key-vault.md).

**Avantages**

- Contrôle total sur les clés utilisées : les clés de chiffrement sont gérées dans le coffre de clés du client, sous le contrôle du client.
- Possibilité de chiffrer de plusieurs services en un seul service maître
- Possibilité de séparer la gestion de clés du modèle de gestion global pour le service
- Possibilité de définir l’emplacement du service et des clés dans des régions différentes

**Inconvénients**

- Le client a l’entière responsabilité de la gestion de l’accès aux clés
- Le client a l’entière responsabilité de la gestion du cycle de vie des clés
- Charge de travail supplémentaire pour l’installation et la configuration

#### <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Chiffrement côté serveur à l’aide de clés gérées par le client dans du matériel contrôlé par le client

Certains services Azure activent le modèle de gestion de clés HYOK (Host Your Own Key). Ce mode de gestion est utile dans les scénarios où il est nécessaire de chiffrer les données au repos et de gérer les clés dans un référentiel propriétaire non contrôlé par Microsoft. Dans ce modèle, le service doit récupérer la clé à partir d’un site externe. Les garanties de disponibilité et de performances sont affectées et la configuration est plus complexe. En outre, comme le service n’a pas accès à la clé de chiffrement des données pendant les opérations de chiffrement et de déchiffrement, les garanties de sécurité globale de ce modèle sont similaires à celles du modèle où les clés sont gérées par le client dans Azure Key Vault.  Par conséquent, ce modèle n’est pas approprié pour la plupart des organisations, sauf si elles ont des exigences spécifiques de gestion des clés. En raison de ces limitations, la plupart des services Azure ne gèrent pas le chiffrement côté serveur à l’aide de clés gérées par le serveur dans le matériel contrôlé par le client.

##### <a name="key-access"></a>Accès aux clés

Quand le chiffrement côté serveur à l’aide de clés gérées par le service dans du matériel contrôlé par le client est utilisé, les clés sont conservées sur un système configuré par le client. Les services Azure qui prennent en charge ce modèle fournissent un moyen d’établir une connexion sécurisée avec un magasin de clés fourni par le client.

**Avantages**

- Contrôle total sur la clé racine utilisée : les clés de chiffrement sont gérées par un magasin fourni par le client
- Possibilité de chiffrer de plusieurs services en un seul service maître
- Possibilité de séparer la gestion de clés du modèle de gestion global pour le service
- Possibilité de définir l’emplacement du service et des clés dans des régions différentes

**Inconvénients**

- Entière responsabilité pour le stockage, la sécurité, les performances et la disponibilité des clés
- Entière responsabilité de la gestion de l’accès aux clés
- Entière responsabilité de la gestion du cycle de vie des clés
- Coûts considérables d’installation, de configuration et de maintenance
- Dépendance accrue de la disponibilité du réseau entre le centre de données du client et les centres de données Azure.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Chiffrement au repos dans les services cloud Microsoft

Les services Microsoft Cloud sont utilisés dans les trois modèles de cloud : IaaS, PaaS, SaaS. Voici des exemples de la façon dont ils s’adaptent sur chaque modèle :

- Services logiciels, appelés SaaS ou Software as a Server, qui ont des applications fournies par le cloud, comme Office 365.
- Services de plateforme, dont les clients tirent parti sur le cloud dans leurs applications, en utilisant le cloud pour des fonctionnalités comme le stockage, l’analytique et Service Bus.
- Services d’infrastructure, appelés IaaS ou Infrastructure as a Service, dans lesquels le client déploie des systèmes d’exploitation et des applications qui sont hébergés dans le cloud, et tirant éventuellement parti d’autres services cloud.

### <a name="encryption-at-rest-for-saas-customers"></a>Chiffrement au repos pour les clients SaaS

Les clients SaaS (Software as a Service) ont généralement le chiffrement au repos activé ou disponible dans chaque service. Office 365 propose plusieurs options permettant aux clients de vérifier ou d’activer le chiffrement au repos. Pour plus d’informations sur les services Office 365, consultez l’article dédié au [chiffrement dans Office 365](https://docs.microsoft.com/office365/securitycompliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Chiffrement au repos pour les clients PaaS

Les données des clients PaaS (Platform as a Service) résident généralement dans un service de stockage tel que le Stockage Blob, mais peuvent également être mises en cache ou stockées dans l’environnement d’exécution des applications, par exemple, une machine virtuelle. Pour connaître les options de chiffrement au repos disponibles, consultez le tableau ci-dessous pour les plateformes de stockage et d’applications que vous utilisez.

### <a name="encryption-at-rest-for-iaas-customers"></a>Chiffrement au repos pour les clients IaaS

Les clients IaaS (Infrastructure as a Service) peuvent utiliser différents services et applications. Les services IaaS peuvent activer le chiffrement au repos dans leurs machines virtuelles et leurs disques durs virtuels hébergés dans Azure en utilisant Azure Disk Encryption.

#### <a name="encrypted-storage"></a>Stockage chiffré

Comme pour PaaS, les solutions IaaS peuvent tirer parti d’autres services Azure qui stockent les données chiffrées au repos. Dans ce cas, vous pouvez activer la prise en charge du chiffrement au repos telle qu’elle est fournie par chaque service Azure utilisé. Le tableau ci-dessous énumère les principales plateformes de stockage, de services et d’applications, avec le modèle de chiffrement au repos pris en charge. 

#### <a name="encrypted-compute"></a>Calcul chiffré

Tous les disques managés, instantanés et images sont chiffrés à l’aide de Storage Service Encryption au moyen d’une clé gérée par le service. Une solution plus complète de chiffrement au repos fait en sorte que les données ne soient jamais stockées sous une forme non chiffrée. Lors de leur traitement sur une machine virtuelle, les données peuvent être conservées dans le fichier d’échange Windows ou Linux, un vidage sur plantage ou le journal des applications. Pour garantir que ces données sont chiffrées au repos, les applications IaaS peuvent utiliser Azure Disk Encryption sur une machine virtuelle (Windows ou Linux) et sur un disque virtuel IaaS Azure.

#### <a name="custom-encryption-at-rest"></a>Chiffrement au repos personnalisé

Il est recommandé que, chaque fois que c’est possible, les applications IaaS tirent parti des options d’Azure Disk Encryption et du chiffrement au repos fournies par les services Azure utilisés. Dans certains cas, comme en présence d’exigences de chiffrement irrégulières ou d’un stockage qui n’est pas basé sur Azure, le développeur d’une application IaaS peut être amené à implémenter lui-même le chiffrement au repos. Les développeurs de solutions IaaS peuvent mieux s’intégrer à la gestion d’Azure et répondre aux attentes des clients en tirant parti de certains composants Azure. En particulier, les développeurs doivent utiliser le service Azure Key Vault pour fournir un stockage des clés sécurisé, ainsi qu’offrir à leurs clients des options de gestion des clés cohérentes avec celles de la plupart des services de plateforme Azure. En outre, les solutions personnalisées doivent utiliser des identités de service gérées par Azure pour permettre aux comptes de service d’accéder aux clés de chiffrement. Pour plus d’informations sur Azure Key Vault et les identités de service gérées, les développeurs peuvent consulter leurs SDK respectifs.

## <a name="azure-resource-providers-encryption-model-support"></a>Prise en charge du modèle de chiffrement des fournisseurs de ressources Azure

Les services Microsoft Azure prennent chacun en charge un ou plusieurs modèles de chiffrement au repos. Cependant, pour certains services, un ou plusieurs des modèles de chiffrement peuvent ne pas être applicables. Pour les services qui prennent en charge les scénarios de clé gérés par le client, ils peuvent prendre en charge uniquement un sous-ensemble des types de clés pris en charge par Azure Key Vault pour les clés de chiffrement à clé. En outre, les services peuvent prendre en charge ces scénarios et les types de clé selon des planifications différentes. Cette section décrit la prise en charge du chiffrement au repos au moment de la rédaction de ce document pour chacun des principaux services de stockage de données Azure.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Tout client utilisant les fonctionnalités IaaS d’Azure peut effectuer le chiffrement au repos pour ses machines virtuelles et ses disques IaaS via Azure Disk Encryption. Pour plus d’informations sur Azure Disk Encryption, consultez la [documentation d’Azure Disk Encryption](../azure-security-disk-encryption-overview.md).

#### <a name="azure-storage"></a>Stockage Azure

Tous les services Stockage Azure (Stockage Blob, Stockage File d’attente, Stockage Table et Azure Files) prennent en charge le chiffrement au repos côté serveur ; certains services prennent également en charge les clés gérées par le client et le chiffrement côté client. 

- Côté serveur : Tous les services Stockage Azure permettent par défaut le chiffrement côté serveur avec des clés gérées par le service, une opération transparente pour l’application. Pour plus d’informations, consultez [Azure Storage Service Encryption pour les données au repos](../../storage/common/storage-service-encryption.md). Azure Stockage Blob Azure et Azure Files prennent également en charge les clés RSA 2048 bits gérées par le client dans Azure Key Vault. Pour plus d’informations, consultez [Chiffrement du service de stockage à l’aide de clés gérées par le client dans Azure Key Vault](../../storage/common/storage-encryption-keys-portal.md).
- Côté client : Les objets blob, les tables et les files d’attente Azure prennent en charge le chiffrement côté client. Lors de l’utilisation du chiffrement côté client, les clients chiffrent les données et les chargent sous la forme d’un objet blob chiffré. La gestion des clés est effectuée par le client. Pour plus d’informations, consultez [Chiffrement côté client et Azure Key Vault pour le stockage Microsoft Azure](../../storage/common/storage-client-side-encryption.md).

#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database prend actuellement en charge le chiffrement au repos pour les scénarios de chiffrement côté service géré par Microsoft et côté client.

La prise en charge du chiffrement côté serveur est actuellement fournie par la fonctionnalité SQL nommée Transparent Data Encryption. Une fois qu’un client Azure SQL Database active Transparent Data Encryption, les clés sont créées et gérées automatiquement pour lui. Le chiffrement au repos peut être activé au niveau de la base de données et au niveau du serveur. À compter de juin 2017, [Transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) est activé par défaut sur les bases de données nouvellement créées. Azure SQL Database prend également en charge les clés RSA 2048 bits gérées par le client dans Azure Key Vault. Pour plus d’informations, voir [Transparent Data Encryption avec prise en charge de BYOK pour Azure SQL Database et Data Warehouse](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current).

Le chiffrement côté client des données Azure SQL Database est pris en charge via la fonctionnalité [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx). Always Encrypted utilise une clé qui est créée et stockée par le client. Les clients peuvent stocker la clé principale dans un magasin de certificats Windows, dans Azure Key Vault ou dans un module de sécurité matériel local. Avec SQL Server Management Studio, les utilisateurs SQL choisissent quelle clé ils veulent utiliser pour quelle colonne.

#### <a name="encryption-model-and-key-management-table"></a>Modèle de chiffrement et tableau de gestion des clés

|                                  |                    | **Modèle de chiffrement et gestion des clés** |                    |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
|                                  | **Côté serveur à l’aide d’une clé gérée par le service**     | **Côté serveur à l’aide d’une clé gérée par le client**             | **Côté client à l’aide d’une clé gérée par le client**      |
| **IA et Machine Learning**      |                    |                    |                    |
| Recherche cognitive Azure           | Oui                | Oui                | -                  |
| Azure Machine Learning           | Oui                | Oui                | -                  |
| Azure Machine Learning Studio    | Oui                | Préversion, RSA 2048 bits | -               |
| Power BI                         | Oui                | Préversion, RSA 2048 bits | -                  |
| **Analyse**                    |                    |                    |                    |
| Azure Stream Analytics           | Oui                | -                  | -                  |
| Event Hubs                       | Oui                | Oui, toutes les longueurs RSA. | -                  |
| Fonctions                        | Oui                | Oui, toutes les longueurs RSA. | -                  |
| Azure Analysis Services          | Oui                | -                  | -                  |
| Azure Data Catalog               | Oui                | -                  | -                  |
| Apache Kafka sur Azure HDInsight  | Oui                | Toutes les longueurs RSA.   | -                  |
| Azure Monitor Application Insights | Oui                | Oui                | -                  |
| Azure Monitor Log Analytics | Oui                | Oui                | -                  |
| Explorateur de données Azure              | Oui                | Oui                | -                  |
| Azure Data Factory               | Oui                | Oui                | -                  |
| Azure Data Lake Store            | Oui                | Oui, RSA 2048 bits  | -                  |
| **Containers**                   |                    |                    |                    |
| Azure Kubernetes Service         | Oui                | Oui                | -                  |
| Container Instances              | Oui                | Oui                | -                  |
| Container Registry               | Oui                | Oui                | -                  |
| **Calcul**                      |                    |                    |                    |
| Virtual Machines                 | Oui                | Oui, RSA 2048 bits  | -                  |
| Groupe de machines virtuelles identiques        | Oui                | Oui, RSA 2048 bits  | -                  |
| SAP HANA                         | Oui                | Oui, RSA 2048 bits  | -                  |
| App Service                      | Oui                | Oui                | -                  |
| Automatisation                       | Oui                | Oui                | -                  |
| Portail Azure                     | Oui                | Oui                | -                  |
| Logic Apps                       | Oui                | Oui                | -                  |
| Applications managées Azure       | Oui                | Oui                | -                  |
| Service Bus                      | Oui                | Oui                | -                  |
| Site Recovery                    | Oui                | Oui                | -                  |
| **Bases de données**                    |                    |                    |                    |
| SQL Server sur machines virtuelles   | Oui                | Oui, RSA 2048 bits  | Oui                |
| Azure SQL Database               | Oui                | Oui, RSA 2048 bits  | Oui                |
| Azure SQL Database pour MariaDB   | Oui                | -                  | -                  |
| Azure SQL Database pour MySQL     | Oui                | Oui                | -                  |
| Azure SQL Database pour PostgreSQL | Oui               | Oui                | -                  |
| Azure Synapse Analytics          | Oui                | Oui, RSA 2048 bits  | Oui                |
| SQL Server Stretch Database      | Oui                | Oui, RSA 2048 bits  | Oui                |
| Stockage Table                    | Oui                | Oui                | Oui                |
| Azure Cosmos DB                  | Oui                | Oui                | -                  |
| Azure Databricks                 | Oui                | Oui                | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps                     | Oui                | -                  | Oui                |
| Azure Repos                      | Oui                | -                  | Oui                |
| **Identité**                     |                    |                    |                    |
| Azure Active Directory           | Oui                | -                  | -                  |
| Azure Active Directory Domain Services | Oui          | Oui, RSA 2048 bits  | -                  |
| **Intégration**                  |                    |                    |                    |
| Service Bus                      | Oui                | Oui                | Oui                |
| Event Grid                       | Oui                | -                  | -                  |
| Gestion des API                   | Oui                | -                  | -                  |
| **Services IoT**                 |                    |                    |                    |
| IoT Hub                          | Oui                | Oui                | Oui                |
| **Gestion et gouvernance**    |                    |                    |                    |
| Azure Site Recovery              | Oui                | -                  | -                  |
| **Média**                        |                    |                    |                    |
| Media Services                   | Oui                | -                  | Oui                |
| **Stockage**                      |                    |                    |                    |
| Stockage Blob                     | Oui                | Oui, RSA 2048 bits  | Oui                |
| Stockage sur disque                     | Oui                | Oui                | -                  |
| Stockage sur disque managé             | Oui                | Oui                | -                  |
| Stockage Fichier                     | Oui                | Oui, RSA 2048 bits  | -                  |
| Stockage File d’attente                    | Oui                | Oui                | Oui                |
| Avere vFXT                       | Oui                | -                  | -                  |
| Azure NetApp Files               | Oui                | -                  | -                  |
| Stockage archive                  | Oui                | Oui, RSA 2048 bits  | -                  |
| StorSimple                       | Oui                | Oui, RSA 2048 bits  | Oui                |
| Sauvegarde Azure                     | Oui                | Oui                | Oui                |
| Data Box                         | Oui                | -                  | Oui                |
| Data Box Edge                    | Oui                | Oui                | -                  |

## <a name="conclusion"></a>Conclusion

La protection des données des clients stockées au sein des services Azure est d’une importance capitale pour Microsoft. Tous les services hébergés par Azure doivent à terme fournir des options de chiffrement au repos. Les services fondamentaux, comme Stockage Azure, Azure SQL Database et les services clés de décisionnel et d’analytique, offrent déjà des options de chiffrement au repos. Certains de ces services prennent en charge les clés contrôlées par le client ou le chiffrement côté client, ainsi que les clés et le chiffrement gérés par le service. Les services Microsoft Azure étendent considérablement la disponibilité du chiffrement au repos, et la disponibilité en préversion puis générale de nouvelles options est planifiée dans les mois à venir.
