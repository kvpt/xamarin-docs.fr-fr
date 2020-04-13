---
title: Xamarin.Forms et Services Web
description: Ce guide explique comment communiquer avec différents services Web pour fournir des fonctionnalités de création, de lecture, de mise à jour et de suppression (CRUD) à une application Xamarin.Forms. Les sujets abordés comprennent la communication avec les services ASMX, les services WCF, les services REST.
ms.prod: xamarin
ms.assetid: 8B360BDA-E4E3-4A3F-9004-0E35362F49F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2019
ms.openlocfilehash: 799a0a97f7c1a212b10dc62f8b3e7bd2cf2060c8
ms.sourcegitcommit: bf3b5925018e1bd6a00505e9f37500761b66809d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80395455"
---
# <a name="xamarinforms-and-web-services"></a>Xamarin.Forms et Services Web

## <a name="introduction"></a>[Introduction](introduction.md)

Cet article fournit une procédure pas à pas de l’application d’échantillon Xamarin.Forms qui démontre comment communiquer avec différents services Web. Les sujets abordés comprennent l’anatomie de l’application, les pages, le modèle de données et l’invocation des opérations de service Web.

## <a name="consume-an-aspnet-web-service-asmx"></a>[Consommer un service web ASP.NET (ASMX)](~/xamarin-forms/data-cloud/web-services/asmx.md)

ASP.NET Services Web (ASMX) offrent la possibilité de créer des services Web qui envoient des messages sur HTTP à l’aide du Protocole d’accès aux objets simples (SOAP). SOAP est un protocole indépendant et indépendant de la langue pour la construction et l’accès aux services Web. Les consommateurs d’un service ASMX n’ont pas besoin de savoir quoi que ce soit sur la plate-forme, le modèle d’objet ou le langage de programmation utilisé pour implémenter le service. Ils n’ont qu’à comprendre comment envoyer et recevoir des messages SOAP. Cet article montre comment consommer un service Web ASMX à partir d’une application Xamarin.Forms.

## <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>[Consommez un service Web de la Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/web-services/wcf.md)

WCF est le cadre unifié de Microsoft pour la construction d’applications axées sur le service. Il permet aux développeurs de construire des applications distribuées sécurisées, fiables, transigées et interopérables. Il existe des différences entre ASP.NET Services Web (ASMX) et WCF, mais il est important de comprendre que WCF prend en charge les mêmes capacités que ASMX fournit - messages SOAP sur HTTP. Cet article montre comment consommer un service WCF SOAP à partir d’une application Xamarin.Forms.

## <a name="consume-a-restful-web-service"></a>[Consommer un service Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)

Representational State Transfer (REST) est un style architectural pour la construction de services Web. Les demandes REST sont faites sur HTTP en utilisant les mêmes verbes HTTP que les navigateurs Web utilisent pour récupérer les pages Web et envoyer des données aux serveurs. Cet article montre comment consommer un service Web RESTful à partir d’une application Xamarin.Forms.
