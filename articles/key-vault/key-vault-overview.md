---
title: Présentation d’Azure Key Vault | Microsoft Docs
description: Azure Key Vault est un service cloud qui fonctionne comme un magasin des secrets sécurisé.
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 34af20ee-3fa7-4f28-9d98-6168b1759764
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 07/17/2018
ms.author: barclayn
ms.openlocfilehash: aae7836448ff27b4c80d7bb53e108034ee52db1c
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2018
ms.locfileid: "47586289"
---
# <a name="what-is-azure-key-vault"></a>Qu’est-ce qu’Azure Key Vault ?

Azure Key Vault aide à résoudre les problèmes suivants
- **Gestion des secrets** - Azure Key Vault peut être utilisé pour stocker en toute sécurité et contrôler étroitement l’accès aux jetons, mots de passe, certificats, clés API et autres secrets
- **Gestion des clés** - Azure Key Vault peut également être utilisé comme solution de gestion de clés. Azure Key Vault simplifie la création et le contrôle des clés de chiffrement utilisées pour chiffrer vos données. 
- **Gestion des certificats** - Azure Key Vault est également un service qui vous permet de configurer, gérer et déployer facilement des certificats SSL/TLS publiques et privées pour une utilisation avec Azure et vos ressources connectées internes. 
- **Stockage de secrets protégés par des modules de sécurité matériels** - Les secrets et les clés peuvent être protégés par logiciel ou par des modules de sécurité matériels valides FIPS 140-2 de niveau 2

## <a name="why-use-azure-key-vault"></a>Pourquoi utiliser Azure Key Vault ?

### <a name="centralize-application-secrets"></a>Centraliser les secrets d’application

La centralisation du stockage des secrets d’application dans Azure Key Vault vous permet de contrôler la distribution de ces secrets. Key Vault réduit considérablement les risques de fuite accidentelle des secrets. Grâce à Key Vault, les développeurs d’applications n’ont plus besoin de stocker les informations de sécurité dans leur application. Ceci vous épargne l’obligation d’intégrer ces informations dans le code. Considérons l’exemple d’une application ayant besoin de se connecter à une base de données. Dans ce cas, plutôt que d’inclure la chaîne de connexion dans les codes de l’application, stockez-la simplement en toute sécurité dans Key Vault.

Vos applications peuvent accéder de façon sécurisée aux informations qui leur sont nécessaires en utilisant des URI qui leur permettent de récupérer des versions spécifiques d’un secret après le stockage de la clé ou du secret des applications dans Azure Key Vault. Cette opération s’effectue sans nécessiter l’écriture de code personnalisé pour protéger les informations secrètes.

### <a name="securely-store-secrets-and-keys"></a>Stocker en toute sécurité les secrets et clés

Les secrets et les clés sont protégés par Azure, à l’aide d’algorithmes standards, de longueurs de clé et de modules de sécurité matériel (HSM). Les HSM utilisés sont garantis conformes aux normes FIPS (Federal Information Processing Standard) 140-2 de niveau 2.

L’accès à un coffre de clés par un appelant (utilisateur ou application) requiert une authentification et une autorisation adéquates. L’authentification établit l’identité de l’appelant, tandis que l’autorisation détermine les opérations que ce dernier est autorisé à effectuer.

L’authentification s’effectue par le biais d’Azure Active Directory. L’autorisation peut être assurée par l’intermédiaire du mécanisme de contrôle d’accès en fonction du rôle (RBQC) ou d’une stratégie d’accès à Key Vault. RBAC est utilisé dans le cadre de la gestion des coffres, et la stratégie d’accès au coffre de clés est appliquée lors d’une tentative d’accès aux données stockées dans un coffre.

Azure Key Vault peut être protégé par un logiciel ou par un HSM matériel. Dans les situations qui nécessitent un surcroît de vigilance, vous pouvez importer ou générer des clés dans des HSM dont les clés ne franchissent jamais les limites. Microsoft utilise les HSM Thales. Pour déplacer une clé de votre HSM vers Azure Key Vault, vous pouvez utiliser les outils Thales.

Enfin, Azure Key Vault a été conçu de façon que Microsoft ne puisse pas visualiser ni extraire vos données.

### <a name="monitor-access-and-use"></a>Surveiller les accès et l’utilisation

Après avoir créé plusieurs coffres de clés, vous voudrez surveiller le mode et le moment des accès à vos clés et secrets. Vous pouvez accomplir cette tâche en activant la journalisation pour Key Vault. La solution Azure Key Vault est configurable pour l’exécution des opérations suivantes :

- archivage dans un compte de stockage ;
- diffusion sur un Event Hub ;
- envoi des journaux à Log Analytics.

Vous pouvez contrôler vos journaux et les sécuriser en limitant l’accès, ainsi que supprimer les journaux dont vous n’avez plus besoin.

### <a name="simplified-administration-of-application-secrets"></a>Administration simplifiée des secrets d’application

Lorsque vous stockez vos données les plus précieuses, vous devez prendre différentes mesures. Les informations de sécurité doivent être sécurisées, suivre un cycle de vie spécifique et se révéler hautement disponibles. Azure Key Vault simplifie une grande partie de ces obligations en assurant les opérations suivantes :

- Suppression de la nécessité de connaissances en interne des modules HSM
- Montée en puissance dans un délai très court pour répondre aux pics d’utilisation de votre organisation.
- Réplication du contenu de votre coffre de clés au sein d’une région et vers une région secondaire. Cette opération garantit la haute disponibilité et élimine l’obligation pour l’administrateur de déclencher le basculement.
- Fourniture des options d’administration Azure standard par le biais du Portail, de l’interface de ligne de commande Azure et de PowerShell.
- Automatisation de certaines tâches sur les certificats que vous achetez auprès d’une autorité de certification publique, comme l’inscription et le renouvellement.

En outre, les coffres de clé Azure vous permettent de séparer les secrets d’application. Les applications sont uniquement en mesure d’accéder au coffre dont l’accès leur a été autorisé, et elles ne peuvent effectuer que des opérations spécifiques. Vous pouvez créer un coffre de clés Azure par application et restreindre les secrets stockés dans un coffre de clés à une application et une équipe de développeurs spécifiques.

### <a name="integrate-with-other-azure-services"></a>Intégrer à d’autres services Azure

En qualité de magasin sécurisé dans Azure, Key Vault est destiné à simplifier les scénarios tels que le service [Azure Disk Encryption](../security/azure-security-disk-encryption.md), la fonctionnalité [Always Encrypted]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) dans SQL Server et Azure SQL Database, ainsi que les [applications web Azure]( https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site). La solution Key Vault proprement dite peut s’intégrer aux comptes de stockage, ainsi qu’aux services Event Hubs et Log Analytics.

## <a name="next-steps"></a>Étapes suivantes

- [Démarrage rapide : créer un coffre de clés Azure Key Vault à l’aide de l’interface de ligne de commande](quick-create-cli.md)
- [Configurer une application web Azure pour lire un secret dans le coffre de clés](tutorial-web-application-keyvault.md)
