---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Palo Alto Networks - Captive Portal | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Palo Alto Networks - Captive Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: fa47eaea590ecb84386a6e0ce4eff0a6933be554
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444199"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---captive-portal"></a>Didacticiel : Intégration d’Azure Active Directory avec Palo Alto Networks - Captive Portal

Dans ce didacticiel, vous découvrez comment intégrer Palo Alto Networks - Captive Portal avec Azure Active Directory (Azure AD).

L’intégration de Palo Alto Networks - Captive Portal avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Palo Alto Networks - Captive Portal.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Palo Alto Networks - Captive Portal (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Palo Alto Networks - Captive Portal, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Palo Alto Networks - Captive Portal pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Palo Alto Networks - Captive Portal depuis la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-palo-alto-networks---captive-portal-from-the-gallery"></a>Ajout de Palo Alto Networks - Captive Portal depuis la galerie
Pour configurer l’intégration de Palo Alto Networks - Captive Portal avec Azure AD, vous devez ajouter Palo Alto Networks - Captive Portal à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Palo Alto Networks - Captive Portal à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, tapez **Palo Alto Networks - Captive Portal**, sélectionnez **Palo Alto Networks - Captive Portal** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Palo Alto Networks - Captive Portal dans la liste des résultats](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et vous testez l’authentification unique Azure AD avec Palo Alto Networks - Captive Portal sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Palo Alto Networks - Captive Portal équivalent à l’utilisateur dans Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et l’utilisateur Palo Alto Networks - Captive Portal associé.

Dans Palo Alto Networks - Captive Portal, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **Username** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Palo Alto Networks - Captive Portal, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créez un utilisateur de test Palo Alto Networks - Captive Portal](#create-a-palo-alto-networks---captive-portal-test-user)** pour obtenir un équivalent de Britta Simon dans Palo Alto Networks - Captive Portal lié à la représentation Azure AD associée.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et vous configurez l’authentification unique dans votre application Palo Alto Networks - Captive Portal.

**Pour configurer l’authentification unique Azure AD avec Palo Alto Networks - Captive Portal, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Palo Alto Networks - Captive Portal**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_samlbase.png)

1. Dans la section **Domaine et URL de Palo Alto Networks - Captive Portal**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL de Palo Alto Networks - Captive Portal](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<Customer Firewall Hostname>/SAML20/SP`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<Customer Firewall Hostname>/SAML20/SP/ACS`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Contactez [l’équipe de support client de Palo Alto Networks - Captive Portal](https://support.paloaltonetworks.com/support) pour obtenir ces valeurs.

1. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_400.png)

1. Ouvrez le site Palo Alto en tant qu’administrateur dans une autre fenêtre de navigateur.

1. Cliquez sur **Appareil**.

    ![Configurer l’authentification unique Palo Alto](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

1. Sélectionnez **Fournisseur d’identité SAML** dans la barre de navigation gauche et cliquez sur « Importer » pour importer le fichier de métadonnées.

    ![Configurer l’authentification unique Palo Alto](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

1. Effectuez les actions suivantes sur la fenêtre Importer

    ![Configurer l’authentification unique Palo Alto](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. Dans la zone de texte **Nom du profil**, spécifiez un nom, par exemple Azure AD Admin UI.
    
    b. Dans **Métadonnées du fournisseur d’identité**, cliquez sur **Parcourir** et sélectionnez le fichier metadata.xml que vous avez téléchargé à partir du portail Azure.
    
    c. Cliquez sur **OK**

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/paloaltonetworks-captiveportal-tutorial/create_aaduser_01.png)

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/paloaltonetworks-captiveportal-tutorial/create_aaduser_02.png)

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/paloaltonetworks-captiveportal-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/paloaltonetworks-captiveportal-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
  
### <a name="create-a-palo-alto-networks---captive-portal-test-user"></a>Créer un utilisateur de test Palo Alto Networks - Captive Portal

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Palo Alto Networks - Captive Portal. Palo Alto Networks - Captive Portal prend en charge le provisionnement juste-à-temps, qui est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. Un nouvel utilisateur est créé lors d’une tentative d’accès à Palo Alto Networks - Captive Portal, s’il n’existe pas déjà. 

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support de Palo Alto Networks - Captive Portal](https://support.paloaltonetworks.com/support).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Palo Alto Networks - Captive Portal.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Palo Alto Networks - Captive Portal, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Palo Alto Networks - Captive Portal**.

    ![Lien Palo Alto Networks - Captive Portal dans la liste des applications](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_app.png)  

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Captive Portal est configuré derrière le pare-feu sur la machine virtuelle Windows.  Pour tester l’authentification unique sur Captive Portal, ouvrez une session sur la machine virtuelle Windows avec RDP. À partir de la session RDP, ouvrez un navigateur sur n’importe quel site web : il doit ouvrir automatiquement l’URL d’authentification unique et demander l’authentification. Une fois l’authentification terminée, vous devez être en mesure d’accéder aux sites web. 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_01.png
[2]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_02.png
[3]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_03.png
[4]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_04.png

[100]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_100.png

[200]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_200.png
[201]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_201.png
[202]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_202.png
[203]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_203.png

