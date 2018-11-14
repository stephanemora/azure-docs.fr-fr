---
title: Limites de la taille des demandes adressées au pare-feu d’applications web et listes d’exclusions dans Azure Application Gateway - Portail Azure
description: Cet article fournit des informations sur la configuration des limites de la taille des demandes adressées au pare-feu d’applications web et des listes d’exclusions dans Application Gateway avec le portail Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.workload: infrastructure-services
ms.date: 11/6/2018
ms.author: victorh
ms.openlocfilehash: f89841c7712737d2d55601c6525e975274b4a103
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036715"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists-public-preview"></a>Limites de la taille des demandes adressées au pare-feu d’application web et listes d’exclusions (préversion publique)

Le pare-feu d’applications web (WAF) Azure Application Gateway fournit une protection pour les applications web. Cet article décrit la configuration des limites de la taille des demandes adressées au WAF et des listes d’exclusions.

> [!IMPORTANT]
> La configuration des limites de la taille des demandes adressées au WAF et des listes d’exclusion est disponible en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="waf-request-size-limits"></a>Limites de la taille des demandes adressées au WAF

![Limites de la taille des demandes](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Le pare-feu d’applications web permet aux utilisateurs de configurer des limites maximale et minimale de la taille des demandes. Les configurations de limites à deux tailles suivantes sont disponibles :

- Le champ de la taille maximale du corps de la demande est spécifié en Ko et contrôle la limite globale de la taille de la demande à l’exclusion de tout chargement de fichier. La valeur de ce champ peut varier de 1 Ko à 128 Ko. La valeur par défaut pour la taille du corps de la demande est de 128 Ko.
- Le champ de la limite de chargement de fichier est spécifié en Mo et régit la taille maximale autorisée pour la limite de chargement de fichier. La valeur de ce champ peut être comprise entre 1 Mo et 500 Mo. La valeur par défaut pour la limite de chargement de fichier est de 100 Mo.

Le WAF offre également un bouton configurable qui permet d’activer ou de désactiver l’inspection du corps de la demande. Par défaut, l’inspection du corps de la demande est activée. Si l’inspection du corps de la demande est désactivée, le WAF n’évalue pas le contenu du corps du message HTTP. Dans ce cas, le WAF continue d’appliquer ses règles sur les en-têtes, les cookies et les URI. Si l’inspection du corps de la demande est désactivée, le champ de la taille maximale du corps de la demande n’est pas applicable et ne peut pas être défini. La désactivation de l’inspection du corps de la demande permet l’envoi au WAF de messages dont la taille est supérieure à 128 Ko, mais le corps du message n’est pas inspecté.

## <a name="waf-exclusion-lists"></a>Listes d’exclusions du WAF

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

Les listes d’exclusion du WAF permettent aux utilisateurs d’omettre certains attributs de la demande dans une évaluation par le WAF. À titre d’exemple courant, citons les jetons Active Directory insérés qui sont utilisés pour les champs d’authentification ou de mot de passe. Ces attributs sont sujets à contenir des caractères spéciaux qui peuvent déclencher un faux positif dans les règles du pare-feu d’applications web. Une fois ajouté à la liste d’exclusions du WAF, un attribut n’est pris en considération par aucune règle du pare-feu d’applications web configurée et active. Les listes d’exclusions ont une portée globale.

Les attributs suivants peuvent être ajoutés aux listes d’exclusion :

* En-têtes de demande
* Cookies de requête
* Corps de la requête

   * Données de plusieurs parties de formulaire
   * XML
   * JSON

Vous pouvez spécifier une correspondance exacte d’une demande d’en-tête, d’un corps, d’un cookie ou d’un attribut de chaîne de requête ou spécifier des correspondances partielles.

Voici les opérateurs de critères de correspondance pris en charge :

- **Est égal à** : cet opérateur est utilisé pour une correspondance exacte. Par exemple, pour sélectionner l’en-tête **bearerToken**, utilisez l’opérateur d’égalité avec le sélecteur défini sur **bearerToken**.
- **Commence par** : cet opérateur correspond à tous les champs qui commencent par la valeur de sélecteur spécifiée.
- **Se termine par** : cet opérateur correspond à tous les champs de demande qui se terminent par la valeur de sélecteur spécifiée.
- **Contient** : cet opérateur correspond à tous les champs de demande qui contiennent la valeur de sélecteur spécifiée.

Dans tous les cas, la correspondance respecte la casse, et les expressions régulières ne sont pas autorisées en guise de sélecteurs.

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré vos paramètres du pare-feu d’applications web, vous pouvez apprendre à afficher vos journaux WAF. Pour plus d’informations, consultez [Diagnostics Application Gateway](application-gateway-diagnostics.md#diagnostic-logging).