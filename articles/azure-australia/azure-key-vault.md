---
title: Azure Key Vault dans Azure Australie
description: Conseils sur la configuration d’Azure Key Vault pour la gestion des clés en Australie afin de répondre aux exigences particulières de la politique, des réglementations et de la législation du gouvernement australien.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 263765c777f994134befc52f0c63c7f18e590b39
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602113"
---
# <a name="azure-key-vault-in-azure-australia"></a>Azure Key Vault dans Azure Australie

Le stockage sécurisé des clés de chiffrement et la gestion de leur cycle de vie sont des éléments essentiels des systèmes de chiffrement.  Azure Key Vault est le service qui offre cette possibilité dans Azure. Key Vault a fait l’objet d’évaluation de sécurité par IRAP et a reçu la certification d’ACSC pour la classification PROTECTED.  Cet article décrit les points clés à prendre en compte lors de l’utilisation de Key Vault pour se conformer aux [contrôles du Manuel de sécurité de l’information](https://acsc.gov.au/infosec/ism/) de la Direction des transmissions australiennes (ASD).

Azure Key Vault est un service cloud qui protège les clés et les secrets de chiffrement. Comme il s’agit de données sensibles et critiques pour l’entreprise, Key Vault sécurise l’accès à vos coffres de clés en acceptant seulement les applications et les utilisateurs autorisés. Key Vault gère et contrôle trois artefacts principaux :

- clés
- secrets
- certificates

Cet article se concentrera sur la gestion des clés à l’aide de Key Vault.

![Azure Key Vault](media/azure-key-vault-overview.png)

*Diagramme 1 – Azure Key Vault*

## <a name="key-design-considerations"></a>Considérations clés en matière de conception

### <a name="deployment-options"></a>Options de déploiement

Vous pouvez créer Azure Key Vault de deux manières. Les deux options utilisent la famille de modules matériel de sécurité (HSM) nShield de Thales, certifiés conformes aux normes FIPS (Federal Information Processing Standards) et autorisés à stocker des clés dans des environnements PROTÉGÉS. Les options sont :

- **Coffres protégés par un logiciel :** Certifié conforme à la norme FIPS 140-2 de niveau 1. Clés stockées sur un HSM. Les opérations de chiffrement et de déchiffrement sont effectuées dans des ressources de calcul sur Azure.
- **Coffres protégés par HSM :** Certifié conforme à la norme FIPS 140-2 de niveau 2. Clés stockées sur un HSM. Les opérations de chiffrement et de déchiffrement sont effectuées sur le HSM.

Key Vault prend en charge les clés RSA (Rivest-Shamir-Adleman) et ECC (chiffrement à courbe elliptique). Les valeurs par défaut sont les clés RSA 2 048 bits, mais il existe une option avancée pour les clés RSA 3 072 bits, RSA 4 096 bits et ECC.  Toutes les clés sont conformes aux contrôles du service de messagerie intersite (ISM), mais les clés de courbe elliptique sont à privilégier.

### <a name="resource-operations"></a>Opérations sur les ressources

Plusieurs personnages interviennent dans Azure Key Vault :

- **Administrateur Key Vault :** Gère le cycle de vie du coffre
- **Administrateur des clés :** Gère le cycle de vie du coffre
- **Développeur/opérateur :** Intégrer des clés du coffre dans des applications et des services
- **Auditeur :** Surveiller l’utilisation des clés et leur accès
- **Applications :** Utiliser des clés pour sécuriser les informations

Azure Key Vault est sécurisée par deux interfaces distinctes :

- **Plan de gestion :** Ce plan concerne la gestion du coffre et il est sécurisé par contrôle d’accès en fonction du rôle (RBAC).
- **Plan de données :** Ce plan concerne la gestion des artefacts dans le coffre et leur accès.  Sécurisé à l’aide de la stratégie d’accès à Key Vault.

Comme l’exige ISM, une authentification et une autorisation valides sont obligatoires pour qu’un appelant (un utilisateur ou une application) puisse accéder au coffre de clés par l’un des plans.

RBAC Azure possède un rôle intégré pour Key Vault, [Contributeur Key Vault](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#key-vault-contributor), pour contrôler la gestion des coffres de clés. Nous vous recommandons de créer des rôles personnalisés alignés sur des rôles plus granulaires pour gérer votre coffre de clés.

>[!WARNING]
>Lorsque l’accès aux clés est activé grâce à la stratégie d’accès à Key Vault, l’utilisateur ou l’application a accès à toutes les clés du coffre de clés (par exemple, si un utilisateur a un accès « supprimer », il peut supprimer toutes les clés).  Par conséquent, plusieurs coffres de clés doivent être déployés pour s’aligner avec les domaines/limites de sécurité.

### <a name="networking"></a>Mise en réseau

Vous pouvez configurer les pare-feux et les réseaux virtuels Key Vault pour contrôler l’accès au plan de données.  Vous pouvez autoriser l’accès aux utilisateurs ou aux applications sur des réseaux particuliers tout en refusant l’accès aux utilisateurs ou aux applications sur tous les autres réseaux. [Les services approuvés](https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints#trusted-services) sont une exception à ce contrôle si l’option « Autoriser les services approuvés » est activée.  Le contrôle de réseau virtuel ne s’applique pas au plan de gestion.

L’accès à Key Vault doit être strictement limité à un ensemble de réseaux restreints dont les utilisateur ou les applications ont besoin d’accéder aux clés.

### <a name="bring-your-own-key-byok"></a>Bring Your Own Key (BYOK)

Key Vault prend en charge BYOK.  BYOK permet aux utilisateurs d’importer des clés à partir de leurs infrastructures de clés existantes.  Thales fournit un [ensemble d’outils australiens](https://www.microsoft.com/download/details.aspx?id=45345) permettant de prendre en charge le transfert sécurisé et l’importation de clés à partir d’un HSM externe (par exemple, les clés générées avec une station de travail hors connexion) dans Key Vault.

### <a name="key-vault-auditing-and-logging"></a>Audit et enregistrement Key Vault

ACSC exige que les entités du Commonwealth utilisent les services Azure appropriés pour effectuer une surveillance et établir des rapports en temps réel sur leurs charges de travail Azure.

La journalisation s’active à l’aide du paramètre de diagnostic **_« AuditEvent »_** sur les valeurs de la clé.  Les événements d’audit seront enregistrés dans le compte de stockage indiqué.  La période **_« RetentionInDays »_** doit être définie en fonction de la stratégie de conservation des données.  [Les opérations](https://docs.microsoft.com/azure/key-vault/key-vault-logging#interpret) effectuées sur le plan de gestion et le plan de données sont auditées et journalisées. Vous pouvez utiliser la [solution Azure Key Vault dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault) pour consulter les journaux AuditEvent Key Vault.  De nombreux autres services Azure vous permettent également de traiter et de distribuer AuditEvent Key Vault.

### <a name="key-rotation"></a>Rotation des clés

Le stockage des clés dans Key Vault offre un point unique pour gérer les clés en dehors des applications permettant de les mettre à jour sans nuire au comportement des applications concernées. Le stockage des clés dans Azure Key Vault offre plusieurs stratégies de prise en charge de la rotation des clés :

- Manuellement
- Par programme via des API
- Scripts d’automatisation (par exemple, à l’aide de PowerShell et d’Azure Automation)

Ces options permettent de faire pivoter les clés régulièrement pour répondre aux exigences de conformité ou ponctuellement en cas de soupçons de compromission des clés.

#### <a name="key-rotation-strategies"></a>Stratégies de rotation des clés

Il est important de mettre en place une stratégie de rotation appropriée pour les clés stockées dans Key Vault.  Si une clé incorrecte a été utilisée, les informations ne seront pas déchiffrées correctement. La perte d’une clé peut entraîner la perte totale de l’accès à l’information.  Voici quelques exemples de stratégies de rotation des clés pour différents scénarios :

- **Données de vol :** les informations volatiles sont transmises entre deux parties.  Lorsqu’une clé pivote, les deux parties doivent avoir un mécanisme de récupération synchrone des clés mises à jour à partir de Key Vault.
- **Données au repos :** Un tiers stocke les données chiffrées et les déchiffre par la suite pour les utiliser.  Lorsqu’il est prévu de faire pivoter une clé, les données doivent être déchiffrées avec l’ancienne clé, puis chiffrées avec la nouvelle clé pivotée.  Plusieurs approches permettent de réduire l’effet du processus de déchiffrement/chiffrement à l’aide de clés de chiffrement (voir l’exemple).  Microsoft gère la majeure partie du processus lié à la rotation des clés pour Stockage Azure (voir…)
- **Clés d’accès :** : plusieurs services Azure ont des clés d’accès qui peuvent être stockées dans Key Vault (par exemple, CosmosDB).  Les services Azure ont des clés d’accès principales et secondaires.  Les deux clés ne doivent pas pivoter en même temps.  Par conséquent, vous pouvez faire pivoter une première clé, puis, après un certain temps, et une fois que l’opération de clé a été vérifiée, vous pouvez faire pivoter la deuxième clé.

### <a name="high-availability"></a>Haute disponibilité

ISM dispose de plusieurs contrôles liés à la continuité des activités.
Azure Key Vault possède plusieurs couches de redondance avec des contenus répliqués dans la région Azure vers la région secondaire [jumelée](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

Lorsque Key Vault est dans un état de basculement, il est en lecture seule et repasse en mode lecture-écriture dès que le service principal est restauré.

ISM dispose de plusieurs contrôles liés à la sauvegarde.  Il est important de concevoir et d’exécuter des plans de sauvegarde/restauration appropriés pour les coffres et leurs clés.

## <a name="key-lifecycle"></a>Cycle de vie des clés

### <a name="key-operations"></a>Opérations sur les clés

Key Vault prend en charge les opérations sur les objets de clés suivantes :

- **créer :** permet à un client de créer une clé dans Key Vault. La valeur de la clé est générée par le coffre de clés et stockée, mais n’est pas communiquée au client. Les clés asymétriques peuvent être créées dans un coffre de clés.
- **Importer :** permet à un client d’importer une clé existante dans Key Vault. Des clés asymétriques peuvent être importées dans un coffre de clés selon diverses méthodes d’empaquetage dans un construct JWK.
- **mettre à jour :** permet à un client disposant des autorisations suffisantes de modifier les métadonnées (attributs de clé) associées à une clé précédemment stockée dans Key Vault.
- **supprimer :** permet à un client disposant des autorisations suffisantes de supprimer une clé dans Key Vault.
- **lister :** permet à un client de lister toutes les clés d’un coffre de clés donné.
- **lister des versions :** permet à un client de lister toutes les versions d’une clé donnée dans un coffre de clés donné.
- **get :** permet à un client de récupérer les parties publiques d’une clé donnée dans un coffre de clés.
- **sauvegarder :** permet d’exporter une clé sous une forme protégée.
- **restaurer :** permet d’importer une clé précédemment sauvegardée.

Un jeu d’autorisations correspondant peut être accordé aux utilisateurs, aux principaux de service ou aux applications à l’aide d’entrées de contrôle d’accès Key Vault pour leur permettre d’exécuter des opérations de clé.

La fonctionnalité de suppression réversible de Key Vault permet de récupérer des coffres et des clés qui ont été supprimés. Par défaut, **_la « suppression réversible »_** n’est pas activée. Une fois activée, les objets sont conservés pendant 90 jours (période de rétention), mais apparaissent comme étant supprimés.  Une autorisation supplémentaire nommée **_« vider »_** permet de supprimer définitivement les clés si l’option **_« Protection contre le vidage »_** est désactivée.

La création ou l’importation d’une clé existante crée une nouvelle version de la clé.

### <a name="cryptographic-operations"></a>Opérations de chiffrement

Key Vault prend également en charge les opérations de chiffrement à l’aide de clés :

- **signer et vérifier :** cette opération vise à « signer le hachage » ou à « vérifier le hachage ». Key Vault ne prend pas en charge le hachage du contenu lors de la création de la signature.
- **chiffrement/encapsulage de clés :** cette opération permet de protéger une autre clé.
- **chiffrement et déchiffrement :**  une clé stockée est utilisée pour chiffrer ou déchiffrer un bloc de données.

Un jeu d’autorisations correspondant peut être accordé aux utilisateurs, aux principaux de service ou aux applications à l’aide d’entrées de contrôle d’accès Key Vault pour leur permettre d’exécuter des opérations de chiffrement.

Trois attributs de clés peuvent être définis pour vérifier si une clé est activée et peut être utilisée pour les opérations de chiffrement :

- **activé**
- **nbf (pas avant) :** non activé avant la date indiquée
- **exp :** date d’expiration

## <a name="storage-and-keys"></a>Stockage et clés

Les clés gérées par le client sont plus souples et permettent d’évaluer et de gérer les clés à contrôler. Elles permettent également d’auditer les clés de chiffrement utilisées pour protéger les données.
Le stockage et les clés stockées dans Key Vault comprennent trois aspects :

- Clés de compte de stockage géré Key Vault
- Azure Storage Service Encryption (SSE) pour les données au repos
- Disques managés et Azure Disk Encryption

La gestion des clés du compte de stockage Azure Key Vault est une extension du service de clé de Key Vault qui prend en charge la synchronisation et la régénération (rotation) des clés du compte de stockage.  [L’intégration du stockage avec Azure Active Directory](https://docs.microsoft.com/azure/storage/common/storage-auth-aad) (préversion) est recommandée lors de sa parution car elle offre davantage de sécurité et une plus grande facilité d’utilisation.
SSE utilise deux clés pour gérer le chiffrement des données au repos :

- Clé de chiffrement principale (KEK)
- Clés de chiffrement des données (DEK)

Si Microsoft gère les clés de chiffrement des données, SSE offre la possibilité d’utiliser les clés de chiffrement principales gérées par le client qui peuvent être stockés dans Key Vault. Cela permet la rotation des clés dans Azure Key Vault selon les politiques de conformité en vigueur. Lorsque les clés pivotent, stockage Azure rechiffre la clé de chiffrement du compte pour le compte de stockage en question. Cela n’entraîne pas le rechiffrement de toutes les données et aucune autre action n’est requise.

SSE est utilisés pour les disques managés mais clés managées par le client ne sont pas prises en charge.  Le chiffrement des disques managés peut être effectué à l’aide d’Azure Disk Encryption avec des clés KEK gérées par le client dans Key Vault.

## <a name="next-steps"></a>Étapes suivantes

Consultez l’article sur la [Fédération des identités](identity-federation.md)

Consultez également la documentation et les didacticiels d’Azure Key Vault supplémentaires dans la [Bibliothèque de référence](reference-library.md)
