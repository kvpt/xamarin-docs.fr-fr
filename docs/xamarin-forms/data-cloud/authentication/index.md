---
title: Authentification du Service Web de Xamarin.Forms
description: Ce guide explique comment intégrer des services d’authentification dans une application Xamarin.Forms pour permettre aux utilisateurs de partager un serveur principal tout en ayant uniquement accès à leurs propres données.
ms.prod: xamarin
ms.assetid: E6FCFAE1-4F83-4F93-9190-EC5290360C54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2019
ms.openlocfilehash: a36dfba7aa07de1633ca9620674ddb4887902ba9
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650425"
---
# <a name="xamarinforms-web-service-authentication"></a>Authentification du Service Web de Xamarin.Forms

## <a name="authenticate-a-restful-web-servicerestmd"></a>[Authentifier un Service Web RESTful](rest.md)

HTTP prend en charge l’utilisation de plusieurs mécanismes d’authentification pour contrôler l’accès aux ressources. L’authentification de base fournit l’accès aux ressources aux clients qui ont les informations d’identification correctes. Cet article explique comment utiliser l’authentification de base pour protéger l’accès aux ressources du service web RESTful.

## <a name="authenticate-users-with-an-identity-provideroauthmd"></a>[Authentifier les utilisateurs avec un fournisseur d’identité](oauth.md)

Xamarin.Auth est un kit de développement logiciel inter-plateformes pour l’authentification des utilisateurs et le stockage de leurs comptes. Il inclut les authentificateurs OAuth qui prennent en charge pour l’utilisation de fournisseurs d’identité comme Google, Microsoft, Facebook et Twitter. Cet article explique comment utiliser Xamarin.Auth pour gérer le processus d’authentification dans une application Xamarin.Forms.

## <a name="authenticate-users-with-azure-active-directory-b2cazure-ad-b2cmd"></a>[Authentifier les utilisateurs avec Azure Active Directory B2C](azure-ad-b2c.md)

Azure B2C Active de répertoire est une solution de gestion des identités de cloud pour applications mobiles et web destinées aux consommateurs. Cet article explique comment utiliser Microsoft Authentication Library (MSAL) et Azure Active Directory B2C pour intégrer la gestion des identités de consommateur dans une application Xamarin.Forms.

## <a name="authenticate-users-with-an-azure-cosmos-db-document-database-and-xamarinformsazure-cosmosdb-authmd"></a>[Authentifier les utilisateurs avec une base de données de Document Azure Cosmos DB et de Xamarin.Forms](azure-cosmosdb-auth.md)

Bases de données de document Azure Cosmos DB prend en charge les collections partitionnées, ce qui peuvent s’étendre sur plusieurs serveurs et des partitions, tout en aidant le débit et stockage illimité. Cet article explique comment combiner de contrôle d’accès avec les collections partitionnées, afin qu’un utilisateur peut accéder uniquement à leurs propres documents dans une application Xamarin.Forms.
