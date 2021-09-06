---
title: Accusés de réception et codes d’erreur X12 TA1
description: Découvrez les accusés de réception techniques et les codes d’erreur TA1 utilisés pour les messages X12 dans Azure Logic Apps.
services: logic-apps
ms.suite: integration
author: praveensri
ms.author: psrivas
ms.reviewer: estfan, divswa, azla
ms.topic: reference
ms.date: 07/15/2021
ms.openlocfilehash: be168489b6cdccd3662a174ceab34d093db41244
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481395"
---
# <a name="ta1-technical-acknowledgments-and-error-codes-for-x12-messages-in-azure-logic-apps"></a>Accusés de réception techniques et codes d’erreur TA1 pour les messages X12 dans Azure Logic Apps

Dans Azure Logic Apps, vous pouvez créer des workflows qui gèrent les messages X12 pour la communication EDI (échange de données informatisé) lorsque vous utilisez des opérations **X12**. Dans la messagerie EDI, les accusés de réception fournissent l’état d’un échange EDI. Lors de la réception d’un échange, l’[**action** Décodage X12](logic-apps-enterprise-integration-x12-decode.md) peut renvoyer un ou plusieurs types d’accusés de réception à l’expéditeur, en fonction des types d’accusés de réception activés et du niveau de validation spécifié.

Par exemple, le destinataire signale l’état de la validation de l’en-tête de contrôle de l’échange (ISA) et du code de fin de contrôle de l’échange (IEA) dans le message encodé X12 reçu, en envoyant un *accusé de réception technique TA1 (ACK)* . Si l’en-tête et le code de fin sont valides, le récepteur envoie un ACK TA1 positif, quel que soit l’état des autres contenus. Si l’en-tête et le code de fin ne sont pas valides, le récepteur envoie plutôt un **ACK TA1** avec un code d’erreur.

L’ACK TA1 X12 est conforme au schéma **X12_<*numéro de version*>_TA1.xsd**. Le récepteur envoie l’ACK TA1 dans une enveloppe ISA et IEA. Toutefois, cette enveloppe ISA et IEA n’est pas différente de celle des autres échanges.

Cette rubrique fournit un aperçu de l’ACK TA1 X12, y compris les segments ACK TA1 dans un échange et les codes d’erreur utilisés dans ces segments. Pour plus d’informations à ce sujet, consultez la documentation suivante :

* [Accusés de réception fonctionnels et codes d’erreur X12 997](logic-apps-enterprise-integration-x12-997-acknowledgment.md)
* [Échanger des messages XS2 pour l’intégration d’entreprise B2B](logic-apps-enterprise-integration-x12.md)
* [Échanger des messages EDIFACT pour l’intégration d’entreprise B2B](logic-apps-enterprise-integration-edifact.md)
* [Qu’est-ce qu’Azure Logic Apps](logic-apps-overview.md)
* [Solutions d’intégration d’entreprise B2B avec Azure Logic Apps et Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)

<a name="ta1-ack-segments"></a>

## <a name="ta1-ack-segments"></a>Segments ACK TA1

Le tableau suivant décrit les segments TA1 ACK d’un échange :

| Champ TA1 | Nom du champ | Mappé à l’échange entrant | Valeur |
|-----------|------------|--------------------------------|-------|
| TA101 | Numéro de contrôle de l’échange | ISA13 - Numéro de contrôle de l'échange | - |
| TA102 | Date de l'échange | ISA09 : date de l’échange | - |
| TA103 | Heure de l'échange | ISA10 : heure de l’échange | - |
| TA104 | Code de l'accusé de réception de l'échange* | N/A | * Le comportement du moteur est basé sur la validation d’éléments de données, à l’exception des informations de sécurité et d’authentification, qui sont basées sur la comparaison de chaînes dans les informations de configuration. <p>La valeur du comportement du moteur (TA104) est A, E ou R, selon les définitions suivantes : <p><p>A = accepter <br>E = échange accepté avec des erreurs <br>R = échange rejeté ou interrompu. <p><p>Pour obtenir davantage d’informations, veuillez consulter les [Codes d’erreur ACK TA1](#ta1-ack-error-codes). |
| TA105 | Code de note de l'échange | N/A | Code d'erreur de résultat du traitement. Pour obtenir davantage d’informations, veuillez consulter les [Codes d’erreur ACK TA1](#ta1-ack-error-codes). |
|||||

<a name="ta1-ack-error-codes"></a>

## <a name="ta1-ack-error-codes"></a>Codes d’erreur ACK TA1

Cette section décrit les codes d’erreur utilisés dans les [Segments ACK TA1](#ta1-ack-segments). Le tableau suivant répertorie les codes d’erreur pris en charge et non pris en charge, tels que définis par la spécification X12, pour le traitement des messages X12 dans Azure Logic Apps. Dans la colonne **Comportement du moteur**, les valeurs TA104 ont les définitions suivantes :

* A = accepter
* E = échange accepté avec des erreurs
* R = échange rejeté ou interrompu

| Condition | Comportement du moteur <br>(valeur TA104) | Valeur TA105 | Pris en charge ? |
|-----------|-----------------------------------|-------------|------------|
| Succès | Un | 000 | Yes |
| Les numéros de contrôle de l'échange dans l'en-tête ISA 13 et le code de fin IEA02 ne correspondent pas | E | 001 | Yes |
| La norme n'est pas prise en charge dans ISA11 (Normes de contrôle) | E | 002 | Oui, en cas d’incompatibilité d’ID. |
| La version des contrôles n'est pas prise en charge | E | 003 | Non, le code d’erreur 017 est utilisé à la place. |
| Le terminateur de segment n’est pas valide* <p><p>* Le terminateur de segment peut avoir les combinaisons valides suivantes : <p><p>- Caractère de terminateur de segment uniquement. <br>- Caractère de terminateur de segment suivi du suffixe 1 et du suffixe 2. | R | 004 | Yes |
| Le qualificateur d'ID de l'échange n'est pas valide pour l'expéditeur | R | 005 | Oui, en cas d’incompatibilité d’ID. |
| ID de l'expéditeur de l'échange non valide | E | 006 | Oui, en cas de réception d’un échange sur un port de réception requérant l’authentification. <p><p>**Remarque** : les propriétés relatives à l’ID de l’expéditeur sont examinées. Si ces propriétés sont incohérentes, ou si les paramètres tiers sont indisponibles car ils n’ont pas été définis, l’échange est rejeté. |
| Le qualificateur d'ID de l'échange n'est pas valide pour le destinataire | R | 007 | Oui, en cas d’incompatibilité d’ID. |
| ID du récepteur de l'échange non valide | E | 008 | Non* <p><p>* Pris en charge en cas de réception d’un échange sur un port de réception requérant l’authentification. Les propriétés relatives à l’ID de l’expéditeur sont examinées. Si ces propriétés sont incohérentes, ou si les paramètres tiers sont indisponibles car ils n’ont pas été définis, l’échange est rejeté. |
| ID du récepteur de l'échange inconnu | E | 009 | Yes |
| Valeur du qualificateur d'informations d'autorisation non valide | R | 010 | Oui, en cas d’incompatibilité d’ID. |
| Valeur des informations d'autorisation non valide | R | 011 | Oui, si le tiers est configuré ou évalué. |
| Valeur du qualificateur d'informations de sécurité non valide | R | 012 | Oui, en cas d’incompatibilité d’ID. |
| Valeur des informations de sécurité non valide | R | 013 | Oui, si le tiers est configuré ou évalué. |
| Valeur de la date de l'échange non valide | R | 014 | Yes |
| Valeur de l'heure de l'échange non valide | R | 015 | Yes |
| Valeur de l'identificateur de normes de l'échange non valide | R | 016 | Yes |
| Valeur de l’ID de version de l’échange non valide | R | 017 | Oui, indiquant que la valeur enum n’est pas valide. |
| Valeur du numéro de contrôle de l'échange non valide | R | 018 | Yes |
| Valeur de l’accusé de réception demandé non valide | E | 019 | Yes |
| Valeur de l’indicateur de test non valide | E | 020 | Yes |
| Valeur du nombre de groupes inclus non valide | E | 021 | Yes |
| Structure de contrôle non valide | R | 022 | Yes |
| Fin du fichier (transmission) incorrecte (prématurée) | R | 023 | Yes |
| Contenu d’échange non valide, par exemple, segment GS non valide | R | 024 | Yes |
| Numéro de contrôle de l'échange dupliqué | R, en fonction des paramètres | 025 | Yes |
| Séparateur d'éléments de données non valide | R | 026 | Yes |
| Séparateur d'éléments composites non valide | R | 027 | Yes |
| Date de remise non valide dans la demande de remise différée | - | - | No |
| Heure de remise non valide dans la demande de remise différée | - | - | No |
| Code temporel de remise non valide dans la demande de remise différée | - | - | No |
| Catégorie de service non valide | - | - | Non |
||||

## <a name="next-steps"></a>Étapes suivantes

* [Échanger des messages XS2 pour l’intégration d’entreprise B2B](logic-apps-enterprise-integration-x12.md)