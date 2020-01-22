---
title: Chiffrement des données Azure Database pour MySQL avec une clé gérée par le client
description: Le chiffrement des données Azure Database pour MySQL avec une clé managée par le client permet le scénario Bring Your Own Key (BYOK) pour la protection des données au repos et permet aux organisations d’implémenter la séparation des tâches dans la gestion des clés et des données.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 7c54b3010b42d56ffa9b701b76c7aef51095404c
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028649"
---
# <a name="azure-database-for-mysql-data-encryption-with-customer-managed-key"></a>Chiffrement des données Azure Database pour MySQL avec une clé gérée par le client

> [!NOTE]
> À ce stade, vous devez demander l’accès pour utiliser cette fonctionnalité. Pour ce faire, veuillez contacter AskAzureDBforMySQL@service.microsoft.com.

Le chiffrement des données Azure Database pour MySQL avec une clé managée par le client permet le scénario Bring Your Own Key (BYOK) pour la protection des données au repos et permet aux organisations d’implémenter la séparation des tâches dans la gestion des clés et des données. Avec le chiffrement managé par le client, vous êtes responsable du cycle de vie des clés (création, chargement, rotation, suppression des clés), des autorisations d’utilisation des clés et de l’audit des opérations sur les clés et contrôlez totalement le processus.

Pour Azure Database pour MySQL, le chiffrement des données est défini au niveau du serveur. Avec cette forme de chiffrement des données, la clé est utilisée dans le chiffrement de la clé de chiffrement de la base de données, laquelle est une clé asymétrique managée par le client, stockée dans un système de gestion des clés externes informatique [Azure Key Vault (AKV)](../key-vault/key-Vault-secure-your-key-Vault.md) appartenant au client et managé par le client. Key Vault fournit un stockage sécurisé hautement disponible et évolutif pour les clés de chiffrement RSA, éventuellement sauvegardé par les modules de sécurité matériels validés FIPS 140-2 niveau 2 (HSM). Il n’autorise pas l’accès direct à une clé stockée, mais fournit des services de chiffrement/déchiffrement à l’aide de la clé aux entités autorisées. La clé peut être générée par Key Vault, importée ou [transférée vers Key Vault à partir d’un dispositif HSM local](../key-vault/key-Vault-hsm-protected-keys.md).

> [!NOTE]
> Cette fonctionnalité est disponible dans toutes les régions Azure où Azure Database pour MySQL prend en charge les niveaux tarifaires Usage général et Mémoire optimisée.

## <a name="benefits"></a>Avantages

Le chiffrement des données pour Azure Database pour MySQL offre les avantages suivants :

* transparence, contrôle granulaire et gestion accrus pour la clé de chiffrement ;
* gestion centralisée et organisation des clés en les hébergeant dans Azure Key Vault ;
* possibilité d’implémenter la séparation des tâches dans la gestion des clés et des données au sein de l’organisation ;
* gestion des clés distincte de la gestion des données au sein d’une organisation, l’administrateur Key Vault peut ainsi révoquer les autorisations d’accès à la clé pour rendre la base de données chiffrée inaccessible ;
* confiance accrue de vos clients finaux, car Azure Key Vault est conçu de telle sorte que Microsoft ne peut pas voir ni extraire les clés de chiffrement.

## <a name="terminology-and-description"></a>Terminologie et description

**Clé de chiffrement des données** : une clé symétrique AES256 utilisée pour chiffrer une partition ou un bloc de données. Le chiffrement de chaque bloc de données avec une clé différente rend les attaques d’analyse du chiffrement plus difficiles. L’accès aux clés de chiffrement des données est nécessaire au fournisseur de ressources ou à l’instance d’application qui chiffre et déchiffre un bloc spécifique. Quand une clé de chiffrement des données est remplacée par une nouvelle clé, seules les données du bloc qui y est associé doivent être rechiffrées avec la nouvelle clé.

**Clé de chiffrement des clés** : une clé de chiffrement utilisée pour chiffrer les clés de chiffrement des données. L’utilisation d’une clé de chiffrement des clés ne quittant jamais le coffre de clés permet le chiffrement et le contrôle des clés de chiffrement des données elles-mêmes. L’entité qui a accès à la clé de chiffrement des clés peut être différente de l’entité qui a besoin de la clé de chiffrement des données. Comme la clé de chiffrement des clés est nécessaire pour déchiffrer les clés de chiffrement des données, la clé de chiffrement des clés est dès lors le point unique par lequel les clés de chiffrement des données peuvent être supprimées en supprimant la clé de chiffrement des clés.

Les clés de chiffrement des données chiffrées avec des clés de chiffrement des clés sont stockées séparément, et seule une entité ayant accès à la clé de chiffrement des clés peut déchiffrer ces clés de chiffrement des données. Pour plus d’informations, consultez [sécurité du chiffrement des données au repos](../security/fundamentals/encryption-atrest.md).

## <a name="how-data-encryption-with-customer-managed-key-works"></a>Fonctionnement du chiffrement des données avec les clés gérées par le client

![Vue d’ensemble du Bring Your Own Key (Apportez votre propre clé)](media/concepts-data-access-and-security-data-encryption/mysqloverview.png)

Pour qu’un serveur MySQL puisse utiliser les clés gérées par le client stockées dans Azure Key Vault pour le chiffrement de la clé de chiffrement, l’administrateur du coffre de clés doit accorder les droits d’accès suivants au serveur à l’aide de son identité unique :

* **obtenir** : pour récupérer la partie publique et les propriétés de la clé dans Key Vault
* **wrapKey** : pour pouvoir chiffrer la clé de chiffrement
* **wrapKey** : pour pouvoir déchiffrer la clé de chiffrement

L’administrateur Key Vault peut également [activer la journalisation des événements d’audit Key Vault](../azure-monitor/insights/azure-key-vault.md), afin qu’ils puissent être audités ultérieurement.

Lorsque le serveur est configuré pour utiliser la clé gérée par le client qui est stockée dans le coffre de clés, le serveur envoie la clé de chiffrement au coffre de clés pour les chiffrements. Key Vault retourne la clé de chiffrement chiffrée, qui est stockée dans la base de données utilisateur. De même, le cas échéant, le serveur envoie des clés de chiffrement protégées vers Key Vault pour le déchiffrement. Les auditeurs peuvent utiliser Azure Monitor pour évaluer les journaux AuditEvent Key Vault, si la journalisation est activée.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>Exigences pour la configuration du chiffrement des données pour Azure Database pour MySQL

### <a name="requirements-for-configuring-akv"></a>Exigences pour la configuration d’AKV

* Key Vault et Azure Database pour MySQL doivent appartenir au même locataire Azure Active Directory (AAD). Les interactions entre un serveur et un coffre de clés inter-abonnés ne sont pas prises en charge. Pour déplacer des ressources par la suite, vous devez reconfigurer le chiffrement des données. En savoir plus sur le déplacement des ressources.
* La fonctionnalité suppression réversible doit être activée sur le coffre de clés pour protéger contre la suppression accidentelle d’une clé de perte de données (ou d’un coffre de clés). Les ressources supprimées de manière réversible sont conservées pendant 90 jours, sauf si elles sont récupérées ou purgées par le client entre-temps. Les actions de récupération et de vidage ont leurs propres autorisations associées dans une stratégie d’accès Key Vault. La fonctionnalité de suppression réversible est désactivée par défaut et peut être activée via PowerShell ou CLI. Elle ne peut pas être activée via le Portail Azure.
* Accordez l’accès à Azure Database pour MySQL au coffre de clés avec les autorisations **get, wrapKey, unwrapKey** à l’aide de son identité gérée unique. Quand vous utilisez Portail Azure, l’identification unique est créée automatiquement lorsque le chiffrement des données est activé sur MySQL. Consultez [Configurer le chiffrement des données pour MySQL](howto-data-encryption-portal.md) pour obtenir des instructions pas à pas détaillées lors de l’utilisation du Portail Azure.

* Lorsque vous utilisez le pare-feu avec AKV, vous devez activer l’option *Autoriser les services Microsoft approuvés pour contourner le pare-feu*.

### <a name="requirements-for-configuring-customer-key"></a>Exigences pour la configuration de la clé client

* La clé gérée par le client à utiliser pour chiffrer la clé de chiffrement peut être uniquement asymétrique, RSA 2028.
* La date d’activation de la clé (si définie) doit être une date et une heure passées. La date d’expiration (si définie) doit être une date et une heure ultérieures.
* La clé doit être dans l’état *activé*.
* Si vous importez une clé existante dans le coffre de clés, veillez à la fournir dans les formats de fichiers pris en charge (`.pfx`, `.byok`, `.backup`).

## <a name="recommendations-when-using-data-encryption-using-customer-managed-key"></a>Recommandations lors de l’utilisation du chiffrement de données à l’aide d’une clé gérée par le client

### <a name="recommendation-for-configuring-akv"></a>Suggestions pour la configuration d’Azure Key Vault

* Définissez un verrou de ressource sur le coffre de clés pour contrôler les utilisateurs pouvant supprimer cette ressource critique et pour empêcher toute suppression accidentelle ou non autorisée. En savoir plus sur les verrous de ressource.
* Activer l’audit et la création de rapports sur toutes les clés de chiffrement : Key Vault fournit des journaux d’activité faciles à injecter dans d’autres outils de gestion d’événements et d’informations de sécurité. Azure Monitor Log Analytics est un exemple de service déjà intégré.

* Assurez-vous que le coffre de clés et Azure Database pour MySQL résident dans la même région pour garantir un accès plus rapide aux opérations WRAP et UNWRAP des clés de chiffrement.

### <a name="recommendation-for-configuring-customer-managed-key"></a>Suggestions pour la configuration d’une clé gérée par le client

* Conservez une copie de la clé gérée par le client à un emplacement sécurisé ou déposez-la au service de dépôt.

* Si la clé est générée dans le coffre de clés, créez une sauvegarde de clé avant d’utiliser la clé dans AKV pour la première fois. La sauvegarde peut être restaurée sur un Azure Key Vault uniquement. En savoir plus sur la commande [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey).

## <a name="inaccessible-customer-managed-key-condition"></a>Condition de clé gérée par le client inaccessible

Lorsque le chiffrement des données est configuré avec une clé gérée par le client dans Azure Key Vault, l’accès continu à cette clé est requis pour que le serveur reste en ligne. Si le serveur perd l’accès à la clé gérée par le client dans Azure Key Vault, dans les 10 minutes, le serveur commence à refuser toutes les connexions avec le message d’erreur correspondant et passe l’état du serveur à **Inaccessible**. La seule action autorisée sur une base de données dans un état inaccessible est de la supprimer.

### <a name="accidental-key-access-revocation-from-the-azure-key-vault-akv"></a>Révocation d’accès à la clé accidentelle à partir d’Azure Key Vault

Il peut arriver qu’une personne disposant de droits d’accès suffisants au coffre de clés désactive accidentellement l’accès du serveur à la clé en :

* révoquant des autorisations get, wrapKey, unwrapKey à partir du serveur
* supprimant la clé
* supprimant le coffre de clés
* modifiant les règles de pare-feu du coffre de clés

* en supprimant l’identité managée du serveur dans Azure Active Directory

## <a name="monitoring-of-the-customer-managed-key-in-the-key-vault"></a>Surveillance de la clé gérée par le client dans le coffre de clés

Pour surveiller l’état de la base de données et activer l’alerte pour la perte d’accès au protecteur TDE, configurez les fonctionnalités Azure suivantes :

* [Azure Resource Health](../service-health/resource-health-overview.md) - Une base de données inaccessible qui a perdu l’accès à la clé client apparaît comme « Inaccessible » après le refus de la première connexion à la base de données.
* [Journal d’activité](../service-health/alerts-activity-log-service-notifications.md) - Lorsque l’accès à la clé client dans le coffre de clés géré par le client échoue, des entrées sont ajoutées au Journal d’activité. La création d’alertes pour ces événements vous permet de rétablir l’accès dès que possible.

* [Les groupes d’actions](../azure-monitor/platform/action-groups.md) peuvent être définis de manière à vous envoyer des notifications et des alertes en fonction de vos préférences, par exemple par e-mail/SMS/envoi (push)/notification vocale, application logique, webhook, ITSM ou Runbook Automation.

## <a name="restore-and-replica-with-customers-managed-key-in-the-key-vault"></a>Restaurer et répliquer avec la clé gérée par le client dans le coffre de clés

Une fois qu’Azure Database pour MySQL est chiffré avec la clé gérée du client stockée dans le coffre de clés, toute copie nouvellement créée du serveur (que ce soit via une opération de restauration locale ou de géo-restauration ou via des réplicas en lecture) est également chiffrée avec la même clé gérée par le client. Toutefois, elles peuvent être modifiées pour refléter la clé gérée du nouveau client pour le chiffrement. Lorsque la clé gérée par le client est modifiée, les anciennes sauvegardes du serveur utilisent la clé la plus récente.

Pour éviter les problèmes lors de l’établissement de la configuration du chiffrement des données géré par le client pendant la restauration ou la création d’un réplica de lecture, il est important de suivre ces étapes sur le serveur maître et sur les serveurs de restauration/réplication :

* Initiez le processus de création de réplicas en lecture ou de restauration à partir du serveur maître Azure Database pour MySQL.
* Le serveur nouvellement créé (restauré/réplica) affiche l’état inaccessible, car son identité unique n’a pas encore reçu les autorisations d’accès à Azure Key Vault.
* Sur le serveur restauré/réplica, revalidez la clé gérée par le client dans les paramètres de chiffrement des données pour vous assurer que le serveur nouvellement créé reçoit les autorisations Wrap/Unwrap sur la clé stockée dans Azure Key Vault.

* Les étapes ci-dessus doivent être effectuées pour garantir que le chiffrement des données est conservé sur le serveur maître, ainsi que sur le serveur restauré/réplica.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [configurer le chiffrement des données avec une clé gérée par le client pour votre Azure Database pour MySQL à l’aide du Portail Azure](howto-data-encryption-portal.md).
