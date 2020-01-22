---
title: Chiffrement des données d’Azure Database pour PostgreSQL Serveur unique avec une clé managée par le client
description: Le chiffrement des données d’Azure Database pour PostgreSQL Serveur unique avec une clé managée par le client permet le scénario Bring Your Own Key (BYOK) pour la protection des données au repos et permet aux organisations d’implémenter la séparation des tâches dans la gestion des clés et des données.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 263fdda178752ee22997a03a11902a7bff4791dc
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028615"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-customer-managed-key"></a>Chiffrement des données d’Azure Database pour PostgreSQL Serveur unique avec une clé managée par le client

> [!NOTE]
> À ce stade, vous devez demander l’accès pour utiliser cette fonctionnalité. Pour ce faire, veuillez contacter AskAzureDBforPostgreSQL@service.microsoft.com.

Le chiffrement des données d’Azure Database pour PostgreSQL Serveur unique avec une clé managée par le client permet le scénario Bring Your Own Key (BYOK) pour la protection des données au repos et permet aux organisations d’implémenter la séparation des tâches dans la gestion des clés et des données. Avec le chiffrement managé par le client, vous êtes responsable du cycle de vie des clés (création, chargement, rotation, suppression des clés), des autorisations d’utilisation des clés et de l’audit des opérations sur les clés et contrôlez totalement le processus.

Pour Azure Database pour PostgreSQL Serveur unique, le chiffrement des données est défini au niveau du serveur. Avec cette forme de chiffrement des données, la clé est utilisée dans le chiffrement de la clé de chiffrement de la base de données, laquelle est une clé asymétrique managée par le client, stockée dans un système de gestion des clés externes basé sur le cloud [Azure Key Vault (AKV)](../key-vault/key-Vault-secure-your-key-Vault.md) appartenant au client et managé par le client. Azure Key Vault fournit un stockage sécurisé hautement disponible et évolutif pour les clés de chiffrement RSA, éventuellement sauvegardé par les modules de sécurité matériels (HSM) validés FIPS 140-2 niveau 2. Il n’autorise pas l’accès direct à une clé stockée, mais fournit des services de chiffrement/déchiffrement à l’aide de la clé aux entités autorisées. La clé peut être générée par le Key Vault, importée ou [transférée vers le Key Vault à partir d’un dispositif HSM local](../key-vault/key-Vault-hsm-protected-keys.md).

> [!NOTE]
> Cette fonctionnalité est disponible dans toutes les régions Azure où Azure Database pour PostgreSQL Serveur unique prend en charge les niveaux tarifaires Usage général et Mémoire optimisée.

## <a name="benefits"></a>Avantages

Le chiffrement des données pour Azure Database pour PostgreSQL Serveur unique offre les avantages suivants :

* Transparence, contrôle granulaire et gestion accrus pour la clé de chiffrement 
* Gestion centralisée et organisation des clés en les hébergeant dans Azure Key Vault 
* Possibilité d’implémenter la séparation des tâches dans la gestion des clés et des données au sein de l’organisation
* Gestion des clés distincte de la gestion des données au sein d’une organisation, l’administrateur Key Vault peut ainsi révoquer les autorisations d’accès à la clé pour rendre la base de données chiffrée inaccessible 
* Confiance accrue de vos clients finaux, car Azure Key Vault est conçu de telle sorte que Microsoft ne peut pas voir ni extraire les clés de chiffrement

## <a name="terminology-and-description"></a>Terminologie et description

**Clé de chiffrement des données** : une clé symétrique AES256 utilisée pour chiffrer une partition ou un bloc de données. Le chiffrement de chaque bloc de données avec une clé différente rend les attaques d’analyse du chiffrement plus difficiles. L’accès aux clés de chiffrement des données est nécessaire au fournisseur de ressources ou à l’instance d’application qui chiffre et déchiffre un bloc spécifique. Lorsqu’une clé de chiffrement des données est remplacée par une nouvelle clé, seules les données du bloc qui y est associé doivent être rechiffrées avec la nouvelle clé.

**Clé de chiffrement des clés** : clé de chiffrement utilisée pour chiffrer les clés de chiffrement des données. L’utilisation d’une clé de chiffrement des clés ne quittant jamais Key Vault permet de chiffrer et de contrôler les clés de chiffrement des données elles-mêmes. L’entité qui a accès à la clé de chiffrement des clés peut être différente de l’entité qui a besoin de la clé de chiffrement des données. Comme la clé de chiffrement des clés est nécessaire pour déchiffrer les clés de chiffrement des données, la clé de chiffrement des clés est dès lors le point unique par lequel les clés de chiffrement des données peuvent être supprimées en supprimant la clé de chiffrement des clés.

Les clés de chiffrement des données chiffrées avec des clés de chiffrement des clés sont stockées séparément, et seule une entité ayant accès à la clé de chiffrement des clés peut déchiffrer ces clés de chiffrement des données. Pour plus d’informations, consultez [Sécurité du chiffrement des données au repos](../security/fundamentals/encryption-atrest.md).

## <a name="how-data-encryption-with-customer-managed-key-works"></a>Fonctionnement du chiffrement des données avec les clés managées par le client

![Vue d’ensemble de Bring Your Own Key (BYOK)](media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png)

Pour qu’un serveur PostgreSQL puisse utiliser les clés managées par le client stockées dans Azure Key Vault pour le chiffrement de la clé de chiffrement, l’administrateur Key Vault doit accorder les droits d’accès suivants au serveur à l’aide de son identité unique :

* **obtenir** : pour récupérer la partie publique et les propriétés de la clé dans Key Vault
* **wrapKey** : pour pouvoir chiffrer la clé de chiffrement
* **unwrapKey** : pour pouvoir déchiffrer la clé de chiffrement

L’administrateur Key Vault peut également [activer la journalisation des événements d’audit Key Vault](../azure-monitor/insights/azure-key-vault.md) afin qu’ils puissent être audités ultérieurement.

Lorsque le serveur est configuré pour utiliser la clé managée par le client qui est stockée dans le Key Vault, le serveur envoie la clé de chiffrement au Key Vault pour les chiffrements. Key Vault retourne la clé de chiffrement chiffrée, qui est stockée dans la base de données utilisateur. De même, le cas échéant, le serveur envoie des clés de chiffrement protégées au Key Vault pour le déchiffrement. Les auditeurs peuvent utiliser Azure Monitor pour évaluer les journaux AuditEvent de Key Vault, si la journalisation est activée.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>Exigences pour la configuration du chiffrement des données pour Azure Database pour PostgreSQL Serveur unique

### <a name="requirements-for-configuring-akv"></a>Exigences pour la configuration d’AKV

* Key Vault et Azure Database pour PostgreSQL Serveur unique doivent appartenir au même locataire Azure Active Directory (AAD). Les interactions entre un serveur et un Key Vault inter-abonnés ne sont pas prises en charge. Pour déplacer des ressources par la suite, vous devez reconfigurer le chiffrement des données. En savoir plus sur le déplacement des ressources.
* La fonctionnalité de suppression réversible doit être activée sur le Key Vault pour protéger contre la suppression accidentelle d’une clé de perte de données (ou d’un Key Vault). Les ressources supprimées de manière réversible sont conservées pendant 90 jours, sauf si elles sont récupérées ou purgées par le client entre-temps. Les actions de récupération et de vidage ont leurs propres autorisations associées dans une stratégie d’accès Key Vault. La fonctionnalité de suppression réversible est désactivée par défaut et peut être activée via PowerShell ou CLI. Elle ne peut pas être activée via le Portail Azure.
* Accordez l’accès à Azure Database pour PostgreSQL Serveur unique au Key Vault avec les autorisations **get, wrapKey, unwrapKey** à l’aide de son identité managée unique. Lorsque vous utilisez le portail Azure, l’identité unique est créée automatiquement lorsque le chiffrement des données est activé sur le serveur PostgreSQL unique. Consultez [Configurer le chiffrement des données pour un serveur PostgreSQL unique](howto-data-encryption-portal.md) pour obtenir des instructions pas à pas détaillées lors de l’utilisation du portail Azure.

* Lorsque vous utilisez le pare-feu avec AKV, vous devez activer l’option *Autoriser les services Microsoft approuvés pour contourner le pare-feu*.

### <a name="requirements-for-configuring-customer-managed-key"></a>Exigences pour la configuration de la clé managée par le client

* La clé managée par le client à utiliser pour chiffrer la clé de chiffrement peut être asymétrique, RSA 2028, uniquement.
* La date d’activation de la clé (si définie) doit être une date et une heure passées. La date d’expiration (si définie) doit être une date et une heure ultérieures.
* La clé doit être dans l’état *activé*.
* Si vous importez une clé existante dans le Key Vault, veillez à la fournir dans les formats de fichiers pris en charge (`.pfx`, `.byok`, `.backup`).

## <a name="recommendations-when-using-data-encryption-using-customer-managed-key"></a>Recommandations lors de l’utilisation du chiffrement de données à l’aide d’une clé managée par le client

### <a name="recommendation-for-configuring-akv"></a>Recommandations pour la configuration d’Azure Key Vault

* Définissez un verrou de ressource sur le Key Vault pour contrôler les utilisateurs pouvant supprimer cette ressource critique et pour empêcher toute suppression accidentelle ou non autorisée. En savoir plus sur les verrous de ressource.
* Activer l’audit et la création de rapports sur toutes les clés de chiffrement : Key Vault fournit des journaux d’activité faciles à injecter dans d’autres outils de gestion d’événements et d’informations de sécurité. Azure Monitor Log Analytics est un exemple de service déjà intégré.

* Assurez-vous que le Key Vault et Azure Database pour PostgreSQL Serveur unique résident dans la même région pour garantir un accès plus rapide aux opérations wrap et unwrap des clés de chiffrement.

### <a name="recommendation-for-configuring-customer-managed-key"></a>Recommandations pour la configuration d’une clé managée par le client

* Conservez une copie de la clé managée par le client à un emplacement sécurisé ou déposez-la au service de dépôt.

* Si la clé est générée dans le Key Vault, créez une sauvegarde de clé avant d’utiliser la clé dans Azure Key Vault pour la première fois. La sauvegarde peut être restaurée sur un Azure Key Vault uniquement. En savoir plus sur la commande [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey).

## <a name="inaccessible-customer-managed-key-condition"></a>Condition de clé managée par le client inaccessible

Lorsque le chiffrement des données est configuré avec une clé managée par le client dans Azure Key Vault, l’accès continu à cette clé est requis pour que le serveur reste en ligne. Si le serveur perd l’accès à la clé managée par le client dans Azure Key Vault, dans les 10 minutes qui suivent, le serveur commence à refuser toutes les connexions avec le message d’erreur correspondant et passe l’état du serveur à **Inaccessible**. La seule action autorisée sur une base de données dans un état inaccessible est de la supprimer.

### <a name="accidental-key-access-revocation-from-the-azure-key-vault-akv"></a>Révocation d’accès à la clé accidentelle à partir d’Azure Key Vault (AKV)

Il peut arriver qu’une personne disposant de droits d’accès suffisants au Key Vault désactive accidentellement l’accès du serveur à la clé en :

* révoquant des autorisations get, wrapKey, unwrapKey du Key Vault à partir du serveur
* supprimant la clé
* supprimant le Key Vault
* modifiant les règles de pare-feu du Key Vault

* en supprimant l’identité managée du serveur dans Azure Active Directory

## <a name="monitoring-of-the-customer-managed-key-in-the-key-vault"></a>Surveillance de la clé managée par le client dans le Key Vault

Pour surveiller l’état de la base de données et activer l’alerte pour la perte d’accès au protecteur TDE, configurez les fonctionnalités Azure suivantes :

* [Azure Resource Health](../service-health/resource-health-overview.md) : une base de données inaccessible qui a perdu l’accès à la clé client apparaît comme « Inaccessible » après le refus de la première connexion à la base de données.
* [Journal d’activité](../service-health/alerts-activity-log-service-notifications.md) : lorsque l’accès à la clé client dans le Key Vault managé par le client échoue, des entrées sont ajoutées au journal d’activité. La création d’alertes pour ces événements vous permet de rétablir l’accès dès que possible.

* Des [groupes d’actions](../azure-monitor/platform/action-groups.md) peuvent être définis de manière à vous envoyer des notifications et des alertes en fonction de vos préférences, par exemple par e-mail/SMS/envoi (push)/notification vocale, application logique, webhook, ITSM ou Runbook Automation.

## <a name="restore-and-replica-with-customers-managed-key-in-the-key-vault"></a>Restaurer et répliquer avec la clé managée par le client dans le Key Vault

Une fois qu’Azure Database pour PostgreSQL Serveur unique est chiffré avec la clé managée du client stockée dans le Key Vault, toute copie nouvellement créée du serveur (que ce soit via une opération de restauration locale ou de géo-restauration ou via des réplicas en lecture) est également chiffrée avec la même clé managée par le client. Toutefois, elles peuvent être modifiées pour refléter la clé managée du nouveau client pour le chiffrement. Lorsque la clé managée par le client est modifiée, les anciennes sauvegardes du serveur utilisent la clé la plus récente.

Pour éviter des problèmes lors de l’établissement de la configuration du chiffrement des données managé par le client pendant la restauration ou la création d’un réplica de lecture, il est important de suivre ces étapes sur le serveur maître et sur les serveurs de restauration/réplication :

* Initiez le processus de création de réplicas en lecture ou de restauration à partir du serveur maître Azure Database pour PostgreSQL Serveur unique.
* Le serveur nouvellement créé (restauré/réplica) affiche l’état Inaccessible, car son identité unique n’a pas encore reçu les autorisations d’accès à Azure Key Vault (AKV)
* Sur le serveur restauré/réplica, revalidez la clé managée par le client dans les paramètres de chiffrement des données pour vous assurer que le serveur nouvellement créé reçoit les autorisations wrap/unwrap sur la clé stockée dans Azure Key Vault.

* Les étapes ci-dessus doivent être effectuées pour garantir que le chiffrement des données est conservé sur le serveur maître, ainsi que sur le serveur restauré/réplica.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [configurer le chiffrement des données avec une clé managée par le client pour votre Azure Database for PostgreSQL Serveur unique à l’aide du portail Azure](howto-data-encryption-portal.md).
