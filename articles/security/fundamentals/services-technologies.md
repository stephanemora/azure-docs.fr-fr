---
title: Services et technologies de sécurité Azure | Microsoft Docs
description: Cet article fournit une liste des services et technologies de sécurité Azure.
services: security
documentationcenter: na
author: terrylanfear
manager: barbkess
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/29/2019
ms.author: terrylan
ms.openlocfilehash: 83548063a796401792bd19e7ec27be36a0efa4e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80549203"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Services et technologies de sécurité disponibles sur Azure

Dans nos discussions avec les clients Azure actuels et futurs, une question revient souvent : « Avez-vous une liste de tous les services et technologies de sécurité proposés par Azure ? ».

Quand vous évaluez les options d’un fournisseur de services cloud, il est utile de disposer de ces informations. Nous avons donc confectionné cette liste pour vous aider à démarrer.

Au fil du temps, cette liste sera modifiée et développée, tout comme Azure. Veillez à consulter cette page régulièrement pour vous tenir au courant de l’évolution de nos technologies et services liés à la sécurité.

## <a name="general-azure-security"></a>Sécurité générale d’Azure
|Service|Description|
|--------|--------|
|[Azure&nbsp;Security&nbsp;Center](/azure/security-center/security-center-intro)| Solution de protection des charges de travail cloud qui fournit une gestion de la sécurité et une protection avancée contre les menaces sur l’ensemble des charges de travail cloud hybrides.|
|[Azure Key Vault](/azure/key-vault/key-vault-overview)| Magasin de secrets sécurisé pour les mots de passe, les chaînes de connexion et d’autres informations dont vous avez besoin pour que vos applications continuent de fonctionner. |
|[Journaux d’activité Azure Monitor](/azure/log-analytics/log-analytics-overview)|Service de surveillance qui collecte des données de télémétrie et d’autres données, et qui fournit un langage de requête et un moteur analytique permettant d’obtenir des insights sur le fonctionnement de vos applications et de vos ressources. Peut être utilisé seul ou avec d’autres services, comme Security Center. |
|[Dev/Test Labs Azure](/azure/lab-services/devtest-lab-overview)|Service permettant aux développeurs et aux testeurs de créer rapidement des environnements dans Azure, tout en réduisant le temps perdu et les coûts.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](/azure/azure-security-disk-encryption-overview)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Sécurité du stockage
|Service|Description|
|------|--------|
| [Azure&nbsp;Storage&nbsp;Service&nbsp;Encryption](/azure/storage/common/storage-service-encryption)|Fonctionnalité de sécurité qui chiffre automatiquement vos données dans le stockage Azure.   |
|[Stockage hybride chiffré StorSimple](/azure/storsimple/storsimple-ova-overview)| Solution de stockage intégrée qui gère les tâches de stockage entre les appareils locaux et le stockage cloud Azure.|
|[Chiffrement côté client Azure](/azure/storage/common/storage-client-side-encryption)| Solution de chiffrement côté client qui chiffre les données dans les applications clientes avant de les charger sur Stockage Azure ; elle déchiffre aussi les données lors du téléchargement. |
| [Signatures d’accès partagé Azure Storage](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)|Une signature d'accès partagé fournit un accès délégué aux ressources de votre compte de stockage.  |
|[Clés de compte de stockage Azure](/azure/storage/common/storage-create-storage-account)| Méthode de contrôle d’accès pour le stockage Azure, qui est utilisée pour l’authentification lors de l’accès au compte de stockage. |
|[Partages de fichiers Azure avec chiffrement SMB 3.0](/azure/storage/files/storage-files-introduction)|Technologie de sécurité réseau qui permet le chiffrement automatique du réseau pour le protocole de partage de fichiers Server Message Block (SMB). |
|[Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics)| Technologie de journalisation et de génération de métriques pour les données dans votre compte de stockage. |

<!------>

## <a name="database-security"></a>Sécurité de la base de données
|Service|Description|
|------|--------|
| [Pare-feu&nbsp;SQL&nbsp;Azure](/azure/sql-database/sql-database-firewall-configure)|Fonctionnalité de contrôle d’accès réseau qui protège la base de données contre les attaques réseau. |
|[Chiffrement&nbsp;au niveau des cellules&nbsp;SQL&nbsp;Azure](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| Technologie de sécurité des bases de données qui fournit un chiffrement à un niveau granulaire.  |
| [Chiffrement de connexion&nbsp;SQL&nbsp;Azure](/azure/sql-database/sql-database-control-access)|Pour assurer la sécurité, SQL Database contrôle l’accès avec des règles de pare-feu qui limitent la connectivité par adresse IP, des mécanismes d’authentification qui obligent les utilisateurs à prouver leur identité, et des mécanismes d’autorisation qui les restreignent à certaines actions et données. |
| [Chiffrement systématique SQL Azure](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Protège les données sensibles, comme les numéros de carte bancaire ou les numéros d’identification (par exemple les numéros de sécurité sociale), stockées dans Azure SQL Database ou dans des bases de données SQL Server.  |
| [Azure&nbsp;SQL&nbsp;Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Fonctionnalité de sécurité de base de données qui chiffre le stockage de toute une base de données. |
| [Audit Azure SQL Database](/azure/sql-database/sql-database-auditing)|Fonctionnalité d’audit de base de données qui suit les événements de base de données et les consigne dans un journal d’audit, dans votre compte de stockage Azure.  |


## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès
|Service|Description|
|------|--------|
| [Contrôle d’accès&nbsp;en fonction&nbsp;du rôle&nbsp;Azure](/azure/active-directory/role-based-access-control-configure)|Fonctionnalité de contrôle d’accès conçue pour autoriser les utilisateurs à accéder seulement aux ressources auxquelles ils doivent accéder en fonction de leur rôle au sein de l’organisation.  |
| [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)|Référentiel d’authentification cloud qui prend en charge un annuaire multilocataire dans le cloud et plusieurs services de gestion des identités dans Azure.  |
| [Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview)|Service de gestion des identités qui permet de contrôler la façon dont les clients s’inscrivent, se connectent et gèrent leurs profils quand ils utilisent vos applications basées sur Azure.   |
| [Services de domaine Azure Active Directory](/azure/active-directory-domain-services/overview)| Version cloud et managée d’Active Directory Domain Services. |
| [Azure Multi-Factor Authentication](/azure/active-directory/authentication/multi-factor-authentication)| Provision de sécurité qui utilise plusieurs formulaires d’authentification et de vérification avant d’autoriser l’accès à des informations sécurisées. |

## <a name="backup-and-disaster-recovery"></a>Sauvegarde et récupération d'urgence
|Service|Description|
|------|--------|
| [Sauvegarde&nbsp;Azure](/azure/backup/backup-introduction-to-azure-backup)| Service basé sur Azure utilisé pour sauvegarder et restaurer des données dans le cloud Azure. |
| [Azure&nbsp;Site&nbsp;Recovery](/azure/site-recovery/site-recovery-overview)|Service en ligne qui réplique les charges de travail s’exécutant sur des machines virtuelles et physiques depuis un site principal vers un emplacement secondaire pour permettre la récupération des services après une défaillance. |

## <a name="networking"></a>Mise en réseau
|Service|Description|
|------|--------|
| [Groupes&nbsp;de sécurité&nbsp;réseau](/azure/virtual-network/virtual-networks-nsg)| Fonctionnalité de contrôle d’accès basée sur le réseau utilisant un tuple de 5 éléments pour prendre les décisions d’autoriser ou de refuser.  |
| [Passerelle VPN Azure](/azure/vpn-gateway/vpn-gateway-about-vpngateways)| Appareil réseau utilisé comme un point de terminaison de réseau privé virtuel pour autoriser les accès locaux à des réseaux virtuels Azure.  |
| [Application Gateway Azure](/azure/application-gateway/application-gateway-introduction)|Équilibreur de charge pour les applications web avancées qui peut router en fonction des URL et effectuer un déchargement SSL. |
|[Pare-feu d’applications web](/azure/frontdoor/waf-overview) (WAF)|Fonctionnalité d’Application Gateway qui permet de protéger de manière centralisée vos applications web contre les vulnérabilités courantes et le code malveillant exploitant.|
| [Équilibrage de charge Azure](/azure/load-balancer/load-balancer-overview)|Équilibreur de charge réseau pour les applications TCP/UDP. |
| [Azure ExpressRoute](/azure/expressroute/expressroute-introduction)| Lien WAN dédié entre des réseaux locaux et des réseaux virtuels Azure. |
| [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview)| Équilibreur de charge DNS global.|
| [Proxy d’application Azure](/azure/active-directory/active-directory-application-proxy-get-started)| Frontend d’authentification utilisé pour sécuriser l’accès à distance pour des applications web hébergées en local. |
|[Pare-feu Azure](/azure/firewall/overview)|Service de sécurité réseau cloud managé qui protège vos ressources Réseau virtuel Azure.|
|[Protection DDoS Azure](/azure/virtual-network/ddos-protection-overview)|Associé aux bonnes pratiques de conception d’application, assure une excellente protection contre les attaques DDoS.|
|[Points de terminaison de service de réseau virtuel](/azure/virtual-network/virtual-network-service-endpoints-overview)|Étend votre espace d’adressage privé de réseau virtuel et l’identité de votre réseau virtuel aux services Azure, par le biais d’une connexion directe.|