---
title: Effectuer le suivi et les journaux d’Azure Data Box, événements lourd de zone de données Azure | Microsoft Docs
description: Décrit comment suivre et enregistrer des événements aux divers stades de votre commande d’Azure Data Box et lourdes de zone de données Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 108d17d3e0ca5f32648f9d4f6cf4b5f9a2984d0c
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495822"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Suivi et journalisation des événements pour votre Azure Data Box et lourdes de zone de données Azure

Une commande Data Box ou élevée de zone de données passe par les étapes suivantes : commander, configurer, copie des données, retourneront, chargement sur Azure et vérifier et l’effacement des données. Correspondant à chaque étape dans l’ordre, vous pouvez effectuer plusieurs actions pour contrôler l’accès à la commande, les événements d’audit, effectuer le suivi de l’ordre et interpréter les différents journaux sont générés.

Le tableau suivant récapitule les étapes de commande Data Box ou élevée de zone de données et les outils disponibles pour effectuer le suivi et d’audit de l’ordre au cours de chaque étape.

| Stade de zone des données à l’aide de la commande       | Outil de suivi et d’audit                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Créer une commande               | [Configurer le contrôle d’accès de la commande par le biais de RBAC](#set-up-access-control-on-the-order)                                                    |
| Ordre de traitement            | [Suivre l’ordre de](#track-the-order) via <ul><li> Portail Azure </li><li> Site Web du transporteur de livraison </li><li>Notifications par e-mail</ul> |
| Configurer l’appareil              | APPAREIL accès enregistrés dans les informations d’identification [journaux d’activité](#query-activity-logs-during-setup)                                              |
| Copie des données sur l’appareil        | [Vue *error.xml* fichiers](#view-error-log-during-data-copy) pour la copie des données                                                             |
| Préparer l’expédition            | [Inspecter les fichiers de nomenclature](#inspect-bom-during-prepare-to-ship) ou les fichiers manifeste sur l’appareil                                      |
| Chargement des données vers Azure       | [Révision *copylogs* ](#review-copy-log-during-upload-to-azure) pour les erreurs au cours des données à charger au centre de données Azure                         |
| Effacement des données à partir de l’appareil   | [Afficher la chaîne de responsabilité journaux](#get-chain-of-custody-logs-after-data-erasure) y compris les journaux d’audit et l’historique des commandes                                                   |

Cet article décrit en détail les différents mécanismes ou outils disponibles pour effectuer le suivi et d’audit de commande Data Box ou élevée de zone de données. Les informations contenues dans cet article s’applique à la fois, Data Box et lourdes de zone de données. Dans les sections suivantes, toutes les références à la zone de données s’appliquent également à lourd de zone de données.

## <a name="set-up-access-control-on-the-order"></a>Configurer le contrôle d’accès sur l’ordre

Vous pouvez contrôler qui peut accéder à votre commande lorsque l’ordre est créé. Configurer les rôles de contrôle d’accès en fonction du rôle (RBAC) dans différentes portées pour contrôler l’accès à la commande Data Box. Un rôle RBAC détermine le type d’accès : lecture-écriture, en lecture seule, en lecture-écriture à un sous-ensemble d’opérations.

Les deux rôles qui peuvent être définis pour le service Azure Data Box sont :

- **Lecteur de données boîte** -ont un accès en lecture seule à un ou les commandes telles que définies par l’étendue. Ils peuvent uniquement afficher les détails d’une commande. Ils ne peuvent pas accéder à tous les autres détails liés aux comptes de stockage ou modifier les détails de commande comme adresse et ainsi de suite.
- **Contributeur de zone de données** -peut créer uniquement une commande pour transférer des données vers un compte de stockage donné *s’ils ont déjà accès en écriture à un compte de stockage*. Si elles n’ont pas accès à un compte de stockage, ils ne peuvent pas même créer une commande Data Box pour copier des données au compte. Ce rôle ne définit pas de n’importe quel compte de stockage liés autorisations ni accorde l’accès aux comptes de stockage.  

Pour restreindre l’accès à une commande, vous pouvez :

- Affecter un rôle au niveau d’une commande. L’utilisateur a uniquement ces autorisations, comme défini par les rôles pour interagir avec cette commande Data Box spécifique uniquement et rien d’autre.
- Affecter un rôle au niveau du groupe de ressources, l’utilisateur a accès à toutes les commandes de zone de données au sein d’un groupe de ressources.

Pour plus d’informations sur l’utilisation RBAC suggérée, consultez [meilleures pratiques pour RBAC](../role-based-access-control/overview.md#best-practice-for-using-rbac).

## <a name="track-the-order"></a>Suivre la commande

Vous pouvez suivre votre commande via le portail Azure et via le site Web du transporteur expédition. Les mécanismes suivants sont en place pour effectuer le suivi de l’ordre de la zone de données à tout moment :

- Pour suivre l’ordre lorsque celui-ci se trouve dans le centre de données Azure ou votre site, accédez à votre **commande Data Box > vue d’ensemble** dans le portail Azure.

    ![Afficher l’état de la commande et le numéro de suivi](media/data-box-logs/overview-view-status-1.png)

- Pour suivre l’ordre, tandis que l’appareil est en transit, visitez le site Web à l’opérateur régional, par exemple, site Web de l’onduleur aux États-Unis. Fournir le numéro de suivi associé à votre commande.
- Boîte de données envoie également des notifications par courrier électronique chaque fois que les changements d’état de commande basée sur les e-mails fournis lors de la commande a été créée. Pour obtenir la liste de tous les États de la commande Data Box, consultez [afficher l’état de la commande](data-box-portal-admin.md#view-order-status). Pour modifier les paramètres de notification associés à la commande, consultez [modifier les détails de la notification](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Journaux d’activité de requête pendant l’installation

- Votre Data Box arrive sur votre serveur local dans un état verrouillé. Vous pouvez utiliser les informations d’identification de périphérique disponibles dans le portail Azure pour votre commande.  

    Quand une zone de données est configurée, vous devrez peut-être savoir qui tous accessibles les informations d’identification de l’appareil. Pour déterminer qui a accédé à la **informations d’identification de l’appareil** panneau, vous pouvez interroger les journaux d’activité.  Toute action qui implique l’accès à **détails de l’appareil > informations d’identification** panneau est enregistré dans les journaux d’activité en tant que `ListCredentials` action.

    ![Interroger les journaux d’activité](media/data-box-logs/query-activity-log-1.png)

- Chaque connexion dans la zone de données est connecté en temps réel. Toutefois, ces informations sont uniquement disponibles dans le [journaux d’Audit](#audit-logs) une fois que la commande est terminée avec succès.

## <a name="view-error-log-during-data-copy"></a>Afficher le journal d’erreur pendant la copie de données

Lors de la copie des données dans Data Box ou élevée de zone de données, un fichier d’erreur est généré s’il existe des problèmes avec les données copiées.

### <a name="errorxml-file"></a>Fichier de Error.Xml

Assurez-vous que les travaux de copie est terminée sans erreurs. S’il existe des erreurs pendant le processus de copie, télécharger les journaux à partir de la **Connect et copie** page.

- Si vous avez copié un fichier qui n’est pas de 512 octets alignés dans un dossier de disque géré dans votre boîte de données, le fichier n’est pas chargé en tant qu’objet blob de pages à votre compte de stockage intermédiaire. Vous verrez une erreur dans les journaux. Supprimez le fichier et copiez un fichier de 512 octets alignés.
- Si vous avez copié un VHDX, ou un disque dur virtuel dynamique ou un disque dur virtuel de différenciation (ces fichiers ne sont pas pris en charge), vous verrez une erreur dans les journaux.

Voici un exemple de la *error.xml* pour différentes erreurs lors de la copie vers des disques gérés.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Voici un exemple de la *error.xml* pour différentes erreurs lors de la copie vers des objets BLOB de page.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Voici un exemple de la *error.xml* pour différentes erreurs lors de la copie pour les objets BLOB de blocs.

```xml
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ni3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMy3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL">\InvalidUnicodeFiles\Ã.txt</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMi3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ny3vv70=</file>
```

Voici un exemple de la *error.xml* pour différentes erreurs lors de la copie dans Azure Files.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_LIMIT">\AzFileMorethan1TB\AzFile1.2TB</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
```

Dans chacun des cas ci-dessus, résoudre les erreurs avant de passer à l’étape suivante. Pour plus d’informations sur les erreurs reçues pendant la copie de données dans Data Box via les protocoles SMB ou NFS, accédez à [problèmes lourd de zone de données et de résoudre les problèmes de zone de données](data-box-troubleshoot.md). Pour plus d’informations sur les erreurs reçues pendant la copie de données dans la zone de données via l’API REST, accédez à [les problèmes de stockage de résoudre les problèmes de données boîte Blob](data-box-troubleshoot-rest.md).

## <a name="inspect-bom-during-prepare-to-ship"></a>Inspecter la nomenclature au cours de préparer l’expédition

Au cours de préparer l’expédition, une liste de fichiers connus comme la nomenclature (nomenclature) ou le fichier manifeste est créé.

- Utilisez ce fichier pour vérifier les noms réels et le nombre de fichiers qui ont été copiés vers la zone de données.
- Ce fichier permet de vérifier les tailles réelles des fichiers.
- Vérifiez que le *crc64* correspond à une chaîne non nulle. <!--A null value for crc64 indicates that there was a reparse point error)-->

Pour plus d’informations sur les erreurs reçues lors de la préparer pour livrés, accédez à [problèmes lourd de zone de données et de résoudre les problèmes de zone de données](data-box-troubleshoot.md).

### <a name="bom-or-manifest-file"></a>Fichier de manifeste ou BOM

La nomenclature ou le fichier manifeste contient la liste de tous les fichiers sont copiés sur l’appareil Data Box. Le fichier de nomenclature a des noms de fichiers et les tailles correspondants, ainsi que la somme de contrôle. Un fichier de nomenclature distinct est créé pour les objets BLOB de blocs, objets BLOB de pages, Azure Files, pour la copie via les API REST et de la copie vers des disques gérés sur la zone de données. Vous pouvez télécharger les fichiers de nomenclature à partir du web local de l’interface utilisateur de l’appareil pendant la préparation à expédier.

Ces fichiers se trouvent sur l’appareil Data Box également et sont chargés sur le compte de stockage associé dans le centre de données.

### <a name="bom-file-format"></a>Format de fichier de nomenclature

Fichier de nomenclature ou manifeste a le format général suivant :

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Voici un exemple de manifeste généré lorsque les données ont été copiées vers le partage de blob de bloc sur la zone de données.

```
<file size="10923" crc64="0x51c78833c90e4e3f">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer1.png</file>
<file size="15308" crc64="0x091a8b2c7a3bcf0a">\databox\media\data-box-deploy-copy-data\get-share-credentials2.png</file>
<file size="53486" crc64="0x053da912fb45675f">\databox\media\data-box-deploy-copy-data\nfs-client-access.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="7749" crc64="0xd2e346a4588e307a">\databox\data-box-deploy-ordered.md</file>
<file size="14275" crc64="0x260296e5d1b1608a">\databox\data-box-deploy-copy-data.md</file>
<file size="4077" crc64="0x2bb0a170225bceec">\databox\data-box-deploy-picked-up.md</file>
<file size="15447" crc64="0xcec0ca8527720b3c">\databox\data-box-portal-admin.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
```

La nomenclature ou les fichiers manifeste sont également copiés dans le compte de stockage Azure. Vous pouvez utiliser la marque BOM ou fichiers pour vérifier que les fichiers chargés sur Azure correspondent aux données qui a été copiées dans la zone données de manifeste.

## <a name="review-copy-log-during-upload-to-azure"></a>Passez en revue le journal de copie lors du chargement vers Azure

Lors du téléchargement de données vers Azure, un *copylog* est créé.

### <a name="copylog"></a>Copylog

Pour chaque commande est traitée, crée le service Data Box *copylog* dans le compte de stockage associé. Le *copylog* a le nombre total de fichiers qui ont été chargés et le nombre de fichiers qui erronées pendant les données copier à partir de la zone de données à votre compte de stockage Azure.

Un calcul de redondance cyclique (CRC) est effectué pendant le chargement vers Azure. Les CRC à partir de la copie des données et une fois le chargement des données sont comparées. Une incompatibilité CRC indique que les fichiers correspondants Échec du téléchargement.

Par défaut, les journaux sont écrits dans un conteneur nommé copylog. Les journaux sont stockés avec la convention d’affectation de noms suivante :

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Le chemin d’accès copylog s’affiche également sur le **vue d’ensemble** panneau pour le portail.

![Chemin d’accès à copylog dans le panneau de vue d’ensemble lorsque terminée](media/data-box-logs/copy-log-path-1.png)

L’exemple suivant décrit le format général d’un fichier copylog pour une zone de données de téléchargement qui s’est terminée correctement :

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

Chargement vers Azure soit terminée avec des erreurs.

![Chemin d’accès à copylog dans le panneau de vue d’ensemble lorsque terminée avec des erreurs](media/data-box-logs/copy-log-path-2.png)

Voici un exemple d’un copylog où le téléchargement est terminé avec des erreurs :

```xml
<ErroredEntity Path="iso\samsungssd.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="iso\iSCSI_Software_Target_33.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>72</TotalFiles_Blobs>
  <FilesErrored>2</FilesErrored>
</CopyLog>
```


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Obtenir la chaîne de journaux de responsabilité après l’effacement des données

Une fois les données sont effacées à partir des disques Data Box conformément aux instructions Revision 1 NIST SP 800-88, la chaîne de responsabilité journaux sont disponibles. Ces journaux incluent les journaux d’audit et l’historique des commandes. La nomenclature ou les fichiers manifeste sont également copiées avec les journaux d’audit.

### <a name="audit-logs"></a>Journaux d’audit

Journaux d’audit contiennent des informations sur la mise sous tension et partager l’accès sur la zone données ou lourd de zone de données lorsqu’il est en dehors du centre de données Azure. Ces journaux sont situés à : `storage-account/azuredatabox-chainofcustodylogs`

Voici un exemple du journal d’audit à partir d’une zone de données :

```
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```


## <a name="download-order-history"></a>Télécharger l’historique des commandes

L’historique des commandes sont disponible dans le portail Azure. Si la commande est terminée et que le nettoyage de l’appareil (l’effacement des données à partir des disques) est terminée, accédez à votre commande de l’appareil, puis accédez à **Order details**. ** Télécharger l’historique des commandes** option est disponible. Pour plus d’informations, consultez [télécharger l’historique des commandes](data-box-portal-admin.md#download-order-history).

Si vous faites défiler l’historique des commandes, vous voyez :

- Suivi des informations relatives à votre appareil du transporteur.
- Événements avec *SecureErase* activité. Ces événements correspondent à l’effacement des données sur le disque.
- Liaisons de journal de zone de données. Les chemins d’accès pour le *journaux d’audit*, *copylogs*, et *BOM* fichiers sont présentés.

Voici un exemple du journal d’historique de commande à partir du portail Azure :

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [résoudre les problèmes sur votre Data Box et lourdes de zone de données](data-box-troubleshoot.md).
