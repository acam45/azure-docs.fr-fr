---
title: Copier des données vers ou depuis le stockage Blob Azure à l’aide d’Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser Azure Data Factory pour copier des données de banques de données sources prises en charge vers le stockage Blob Azure ou depuis le stockage Blob Azure vers des banques de données réceptrices prises en charge.
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: jingwang
ms.openlocfilehash: 3109cad0e00b6ec5af47210f2c8d094659bd4553
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2018
ms.locfileid: "51345774"
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Copier des données vers ou depuis le stockage Blob Azure à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-azure-blob-connector.md)
> * [Version actuelle](connector-azure-blob-storage.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données vers et depuis le stockage Blob Azure. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

Pour en savoir plus sur Azure Data Factory, lisez l’[article d’introduction](introduction.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données depuis n’importe quelle banque de données source prise en charge vers le stockage Blob. Vous pouvez également copier des données depuis le stockage Blob vers n’importe quelle banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md).

Plus précisément, ce connecteur de stockage Blob prend en charge ce qui suit :

- La copie d’objets blob vers et depuis des comptes de stockage Azure à usage général et un stockage d’objets blob à chaud ou à froid. 
- La copie d’objets blob à l’aide d’une clé de compte, d’une signature d’accès partagé de service ou d’identités managées pour les authentifications de ressources Azure.
- La copie d’objets blob à partir d’objets blob de blocs, d’ajout ou de page, et la copie de données uniquement vers des objets blob de blocs. Le stockage Azure Premium n’est pas pris en charge en tant que récepteur, car il repose sur des objets blob de pages.
- La copie d’objets blob en l’état ou l’analyse ou la génération d’objets blob avec les [formats de fichier et codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>Si vous activez _Allow trusted Microsoft services to access this storage account_ (Autoriser les services Microsoft autorisés à accéder à ce compte de stockage) dans les paramètres du pare-feu Azure Storage, l’utilisation d’Azure Integration Runtime pour se connecter au stockage Blob échouera avec une erreur d’interdiction, car les fichiers de définition d’application ne sont pas traités en tant que service Microsoft autorisé. Veuillez utiliser le runtime d’intégration auto-hébergé comme moyen de connexion.

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir les entités Azure Data Factory spécifiques au stockage Blob.

## <a name="linked-service-properties"></a>Propriétés du service lié

Le connecteur d’objets Blob Azure prend en charge les types d’authentification ci-dessous. Pour en savoir plus, reportez-vous à la section correspondante :

- [Authentification par clé de compte](#account-key-authentication)
- [Authentification avec une signature d’accès partagé](#shared-access-signature-authentication)
- [Authentification d’un principal du service](#service-principal-authentication)
- [Identités managées pour authentifier les ressources Azure](#managed-identity)

>[!NOTE]
>Le chargement HDInsights, Azure Machine Learning et Azure SQL Data Warehouse PolyBase prend uniquement en charge l’authentification par clé de compte de stockage Blob Azure.

### <a name="account-key-authentication"></a>Authentification par clé de compte

Pour l’authentification par clé de compte de stockage, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type doit être définie sur **AzureBlobStorage** (recommandé) ou **AzureStorage** (voir les remarques ci-dessous). |Oui |
| connectionString | Spécifiez les informations requises pour la connexion au stockage pour la propriété connectionString. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |OUI |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Vous pouvez utiliser Azure Integration Runtime ou Integration Runtime auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non  |

>[!NOTE]
>Si vous utilisiez déjà le service lié de type « AzureStorage », celui-ci est toujours pris en charge en l’état. Toutefois, vous êtes encouragé à passer au nouveau service lié de type « AzureBlobStorage ».

**Exemple :**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="shared-access-signature-authentication"></a>Authentification avec une signature d’accès partagé

Une signature d'accès partagé fournit un accès délégué aux ressources de votre compte de stockage. Vous pouvez utiliser une signature d’accès partagé pour octroyer à un client des autorisations d’accès limité à des objets de votre compte de stockage pendant une période donnée. Vous n’êtes pas obligé de partager vos clés d’accès de compte. La signature d’accès partagé est un URI qui englobe dans ses paramètres de requête toutes les informations nécessaires pour obtenir un accès authentifié à une ressource de stockage. Pour accéder aux ressources de stockage avec la signature d’accès partagé, il suffit au client de transmettre cette dernière à la méthode ou au constructeur approprié. Pour plus d’informations sur les signatures d’accès partagé, consultez [Utilisation des signatures d’accès partagé (SAP)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- Azure Data Factory prend désormais en charge les **signatures d’accès partagé de service** et les **signatures d’accès partagé de compte**. Pour plus d’informations sur ces deux types et leur construction, consultez [Types de signatures d’accès partagé](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures).
>- Dans une configuration ultérieure de jeu de données, le chemin du dossier est le chemin absolu commençant au niveau conteneur. Vous devez en configurer un qui soit aligné avec le chemin dans votre URI SAS.

> [!TIP]
> Pour générer une signature d’accès partagé de service pour votre compte de stockage, vous pouvez exécuter les commandes PowerShell suivantes. Remplacez les espaces réservés et octroyez l’autorisation nécessaire.
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Pour l’authentification par signature d’accès partagé, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type doit être définie sur **AzureBlobStorage** (recommandé) ou **AzureStorage** (voir les remarques ci-dessous). |Oui |
| sasUri | Spécifiez l’URI de signature d’accès partagé des ressources de stockage, telles qu’un objet blob, un conteneur ou une table. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |OUI |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Vous pouvez utiliser Azure Integration Runtime ou Integration Runtime auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non  |

>[!NOTE]
>Si vous utilisiez déjà le service lié de type « AzureStorage », celui-ci est toujours pris en charge en l’état. Toutefois, vous êtes encouragé à passer au nouveau service lié de type « AzureBlobStorage ».

**Exemple :**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Lorsque vous créez un URI de signature d’accès partagé, prenez en compte les points suivants :

- Définissez des autorisations de lecture/écriture appropriées sur les objets en fonction de l’utilisation du service lié (lecture, écriture, lecture/écriture) dans votre fabrique de données.
- Définissez le paramètre **Heure d’expiration** correctement. Assurez-vous que l’accès aux objets du stockage n’expire pas pendant la période active du pipeline.
- L’URI doit être créé au niveau table ou objet blob/conteneur approprié en fonction des besoins. Un URI de signature d’accès partagé à un objet blob permet à Azure Data Factory d’accéder à cet objet blob particulier. Un URI de signature d’accès partagé à un conteneur de stockage Blob permet à Azure Data Factory d’itérer via des objets blob dans ce conteneur. Pour fournir l’accès à plus ou moins d’objets ultérieurement ou mettre à jour l’URI de signature d’accès partagé, rappelez-vous de mettre à jour le service lié avec le nouvel URI.

### <a name="service-principal-authentication"></a>Authentification d’un principal du service

Pour des informations générales sur l’authentification de principal de service du stockage Azure, consultez [Authentifier l’accès au Stockage Azure à l’aide d’Azure Active Directory](../storage/common/storage-auth-aad.md).

Pour l’authentification de principal de service, effectuez les étapes suivantes :

1. Inscrivez une entité d’application dans Azure Active Directory (Azure AD) en suivant les instructions de la section [Inscrire votre application à un locataire Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Prenez note des valeurs suivantes, qui vous permettent de définir le service lié :

    - ID de l'application
    - Clé de l'application
    - ID client

2. Accordez l’autorisation appropriée au principal de service dans le Stockage Blob Azure : Pour plus d’informations sur les rôles, consultez [Gérer les droits d’accès aux données du Stockage Azure avec RBAC](../storage/common/storage-auth-aad-rbac.md).

    - **Comme source**, dans le contrôle d’accès (IAM), accordez au moins un rôle **Lecteur des données Blob du stockage** .
    - **Comme source**, dans le contrôle d’accès (IAM), accordez au moins un rôle **Contributeur aux données Blob du stockage**.

Les propriétés prises en charge pour un service lié de Stockage Blob Azure sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type doit être définie sur **AzureBlobStorage**. |Oui |
| serviceEndpoint | Spécifiez le point de terminaison du service Stockage Blob Azure à l’aide du modèle suivant : `https://<accountName>.blob.core.windows.net/`. |Oui |
| servicePrincipalId | Spécifiez l’ID client de l’application. | OUI |
| servicePrincipalKey | Spécifiez la clé de l’application. Marquez ce champ en tant que **SecureString** afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui |
| locataire | Spécifiez les informations de locataire (nom de domaine ou ID de locataire) dans lesquels se trouve votre application. Récupérez-le en pointant la souris dans le coin supérieur droit du Portail Azure. | Oui |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Vous pouvez utiliser Azure Integration Runtime ou Integration Runtime auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non  |

>[!NOTE]
>L’authentification du principal du service est uniquement prise en charge par le service lié de type « AzureBlobStorage », et non par l’ancien service lié de type « AzureStorage ».

**Exemple :**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Identités managées pour authentifier les ressources Azure

Une fabrique de données peut être associée à une [identité managée pour les ressources Azure](data-factory-service-identity.md), laquelle représente cette même fabrique de données. Vous pouvez utiliser directement cette identité de service pour l’authentification Stockage Blob, ce qui revient à utiliser votre propre principal de service. Cela permet à la fabrique désignée d’accéder aux données, et de les copier à partir de votre stockage Blob ou dans celui-ci.

Pour des informations générales sur l’authentification MSI du Stockage Azure, consultez [Authentifier l’accès au Stockage Azure à l’aide d’Azure Active Directory](../storage/common/storage-auth-aad.md).

Pour utiliser les identités managées afin d’authentifier les ressources Azure, procédez comme suit :

1. [Récupérez l’identité de service de la fabrique de données](data-factory-service-identity.md#retrieve-service-identity) en copiant la valeur « ID d’application de l’identité du service » générée en même temps que votre fabrique.

2. Accordez l’autorisation appropriée au principal de service dans le Stockage Blob Azure : Pour plus d’informations sur les rôles, consultez [Gérer les droits d’accès aux données du Stockage Azure avec RBAC](../storage/common/storage-auth-aad-rbac.md).

    - **Comme source**, dans le contrôle d’accès (IAM), accordez au moins un rôle **Lecteur des données Blob du stockage** .
    - **Comme source**, dans le contrôle d’accès (IAM), accordez au moins un rôle **Contributeur aux données Blob du stockage**.

Les propriétés prises en charge pour un service lié de Stockage Blob Azure sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type doit être définie sur **AzureBlobStorage**. |Oui |
| serviceEndpoint | Spécifiez le point de terminaison du service Stockage Blob Azure à l’aide du modèle suivant : `https://<accountName>.blob.core.windows.net/`. |Oui |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Vous pouvez utiliser Azure Integration Runtime ou Integration Runtime auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non  |

> [!NOTE]
> Les identités managées pour l’authentification des ressources Azure sont uniquement prises en charge par le service lié de type « AzureBlobStorage », et non par l’ancien service lié de type « AzureStorage ». 

**Exemple :**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données de stockage Blob.

Pour copier des données vers et depuis le stockage Blob, définissez la propriété de type du jeu de données sur **AzureBlob**. Les propriétés suivantes sont prises en charge.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type du jeu de données doit être définie sur **AzureBlob**. |OUI |
| folderPath | Chemin d'accès au conteneur et au dossier dans le stockage des objets Blobs. Le filtre de caractères génériques n'est pas pris en charge. Un exemple est myblobcontainer/myblobfolder/. |Oui pour la copie/Activité Lookup/Non pour l’activité GetMetadata |
| fileName | **Filtre de nom ou de caractères génériques** pour les objets blob sous le "folderPath" spécifié. Si vous ne spécifiez pas de valeur pour cette propriété, le jeu de données pointe vers tous les objets blob du dossier. <br/><br/>Dans le filtre, les caractères génériques autorisés sont les suivants : `*` (correspond à zéro caractère ou plus) et `?` (correspond à zéro ou un caractère).<br/>- Exemple 1 : `"fileName": "*.csv"`<br/>- Exemple 2 : `"fileName": "???20180427.txt"`<br/>Utilisez `^` comme caractère d’échappement si votre nom de fichier réel contient des caractères génériques ou ce caractère d’échappement.<br/><br/>Lorsque fileName n’est pas spécifié pour un jeu de données de sortie et que **preserveHierarchy** n’est pas spécifié dans le récepteur d’activité, l’activité de copie génère automatiquement le nom d’objet blob avec le modèle suivant : "*Data.[activity run id GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]*". Par exemple, « Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz ». |Non  |
| format | Si vous souhaitez copier des fichiers en l’état entre des magasins de fichiers (copie binaire), ignorez la section Format dans les deux définitions de jeu de données d’entrée et de sortie.<br/><br/>Si vous souhaitez analyser ou générer des fichiers d’un format spécifique, les types de formats de fichier pris en charge sont les suivants : **TextFormat**, **JsonFormat**, **AvroFormat**,  **OrcFormat** et **ParquetFormat**. Définissez la propriété **type** située sous **Format** sur l’une de ces valeurs. Pour en savoir plus, voir les sections [Format Text](supported-file-formats-and-compression-codecs.md#text-format), [Format JSON](supported-file-formats-and-compression-codecs.md#json-format), [Format Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Format Orc](supported-file-formats-and-compression-codecs.md#orc-format) et [Format Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Non (uniquement pour un scénario de copie binaire) |
| compression | Spécifiez le type et le niveau de compression pour les données. Pour plus d’informations, voir [Formats de fichier et de codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Les types pris en charge sont : **GZip**, **Deflate**, **BZip2** et **ZipDeflate**.<br/>Les niveaux pris en charge sont **Optimal** et **Fastest**. |Non  |

>[!TIP]
>Pour copier tous les objets blob d’un dossier, spécifiez **folderPath** uniquement.<br>Pour copier un seul objet blob avec un nom donné, spécifiez **folderPath** avec la partie dossier et **fileName** avec le nom du fichier.<br>Pour copier un sous-ensemble d’objets blob d’un dossier, spécifiez **folderPath** avec la partie dossier et **fileName** avec le filtre de caractères génériques. 

**Exemple :**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName": "myfile.csv.gz",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source et le récepteur du stockage Blob.

### <a name="blob-storage-as-a-source-type"></a>Stockage Blob en tant que type de source

Pour copier des données depuis le stockage Blob, définissez le type de source dans l’activité de copie sur **BlobSource**. Les propriétés suivantes sont prises en charge dans la section **source** de l’activité de copie.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type de la source d’activité de copie doit être définie sur **BlobSource**. |OUI |
| recursive | Indique si les données sont lues de manière récursive à partir des sous-dossiers ou uniquement du dossier spécifié. Notez que lorsque l’option « recursive » est définie sur true et que le récepteur est un magasin basé sur un fichier, un dossier vide ou un sous-dossier n’est pas copié ou créé sur le récepteur.<br/>Les valeurs autorisées sont **true** (par défaut) et **false**. | Non  |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="blob-storage-as-a-sink-type"></a>Stockage Blob en tant que type de récepteur

Pour copier des données vers le stockage Blob, définissez **BlobSink** comme type de récepteur dans l’activité de copie. Les propriétés suivantes sont prises en charge dans la section **récepteur**.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type du récepteur d’activité de copie doit être définie sur **BlobSink**. |OUI |
| copyBehavior | Définit le comportement de copie lorsque la source est constituée de fichiers d’une banque de données basée sur un fichier.<br/><br/>Les valeurs autorisées sont les suivantes :<br/><b>- PreserveHierarchy (par défaut)</b> : conserve la hiérarchie des fichiers dans le dossier cible. Le chemin d’accès relatif du fichier source vers le dossier source est identique au chemin d’accès relatif du fichier cible vers le dossier cible.<br/><b>- FlattenHierarchy</b> : tous les fichiers du dossier source figurent dans le premier niveau du dossier cible. Les noms des fichiers cibles sont générés automatiquement. <br/><b>- MergeFiles</b> : fusionne tous les fichiers du dossier source dans un même fichier. Si le nom d’objet blob ou de fichier est spécifié, le nom de fichier fusionné est le nom spécifié. Dans le cas contraire, il s’agit d’un nom de fichier généré automatiquement. | Non  |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="some-recursive-and-copybehavior-examples"></a>Quelques exemples de valeurs recursive et copyBehavior

Cette section décrit le comportement résultant de l’opération de copie pour différentes combinaisons de valeurs recursive et copyBehavior.

| recursive | copyBehavior | Structure du dossier source | Cible obtenue |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré de la même manière que la source :<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 |
| true |flattenHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit : <br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier5 |
| true |mergeFiles | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit : <br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Le contenu de Fichier1 + Fichier2 + Fichier3 + Fichier4 + Fichier5 est fusionné dans un fichier avec un nom de fichier généré automatiquement. |
| false |preserveHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit : <br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |
| false |flattenHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit : <br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier2<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |
| false |mergeFiles | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Le contenu de Fichier1 + Fichier2 est fusionné dans un fichier avec un nom de fichier généré automatiquement. nom généré automatiquement pour Fichier1<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).
