---
title: Nouveautés d’Azure Database pour MySQL – Serveur unique
description: Découvrez les récentes mises à jour d’Azure Database pour MySQL - Serveur unique, un service de base de données relationnelle du cloud Microsoft qui repose sur MySQL Community Edition.
author: hjtoland3
ms.service: mysql
ms.author: jtoland
ms.custom: mvc
ms.topic: conceptual
ms.date: 05/05/2021
ms.openlocfilehash: 6b77ede348073dd09c0ba44bfe282d3bf546a092
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111958549"
---
# <a name="whats-new-in-azure-database-for-mysql---single-server"></a>Nouveautés d’Azure Database pour MySQL – Serveur unique

Azure Database for MySQL est un service de base de données relationnelle dans Microsoft Cloud. Le service est basé sur le moteur de base de données [MySQL Community Edition](https://www.mysql.com/products/community/) (disponible sous licence GPLv2) et prend en charge les versions 5.6, 5.7 et 8.0. [Azure Database pour MySQL - Serveur unique](./overview.md#azure-database-for-mysql---single-server) est un mode de déploiement qui fournit un service de base de données complètement managé avec des exigences minimales pour les personnalisations de base de données. La plateforme de serveur unique est conçue pour prendre en charge la plupart des fonctions de gestion de base de données, comme les correctifs, les sauvegardes, la haute disponibilité et la sécurité, avec un minimum de configuration et de contrôle de la part de l’utilisateur.

Cet article résume les nouvelles versions et fonctionnalités d’Azure Database pour MySQL - Serveur unique à compter de janvier 2021. Les éléments s’affichent dans l’ordre chronologique inverse, avec les mises à jour les plus récentes en premier.

## <a name="february-2021"></a>Février 2021

Cette version d’Azure Database pour MySQL - Serveur unique comprend les mises à jour suivantes.

- Ajout de nouvelles procédures stockées pour prendre en charge l’identificateur de transaction global (GTID) pour les données pour les serveurs de stockage volumineux version 5.7 et 8.0.
- Mise à jour pour prendre en charge les versions MySQL 5.6.50 et 5.7.32.

## <a name="january-2021"></a>Janvier 2021

Cette version d’Azure Database pour MySQL - Serveur unique comprend les mises à jour suivantes.

- Activation de « Réinitialiser le mot de passe » pour corriger automatiquement la première autorisation administrateur.
- Exposition du paramètre de serveur `auto_increment_increment/auto_increment_offset` et de `session_track_gtids`.
- Ajout de nouvelles procédures stockées pour contrôler le vidage/la restauration du pool de tampons InnoDB.
- Exposition du paramètre de serveur de préchauffage InnoDB pour le serveur de stockage volumineux.

## <a name="contacts"></a>Contacts

Pour toute question ou suggestion au sujet de l’utilisation d’Azure Database pour MySQL, contactez l’équipe Azure Database pour MySQL ([@Ask Azure DB pour MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Cette adresse e-mail n’est pas un alias du support technique.

En outre, tenez compte des points de contact suivants le cas échéant :

- Pour contacter le support technique Azure, [émettez un ticket à partir du Portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Pour résoudre un problème relatif à votre compte, enregistrez une [demande de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) sur le portail Azure.
- Pour donner votre avis ou demander de nouvelles fonctionnalités, créez une entrée via [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [tarification d’Azure Database pour MySQL](https://azure.microsoft.com/pricing/details/mysql/server/).
- Parcourez la [documentation publique](./single-server/index.yml) d’Azure Database pour MySQL – Serveur unique.
- Passez en revue les détails sur la [résolution des erreurs courantes](./howto-troubleshoot-common-errors.md).