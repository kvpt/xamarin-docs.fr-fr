---
title: Xamarin.Forms et les Services Web
description: Ce guide explique comment communiquer avec les services web différents pour fournir créer, lire, mettre à jour, fonctionnalités et de suppression (CRUD) pour une application Xamarin.Forms. Les sujets abordés incluent la communication avec les services ASMX, WCF services, services REST.
ms.prod: xamarin
ms.assetid: 8B360BDA-E4E3-4A3F-9004-0E35362F49F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2019
ms.openlocfilehash: 1cf2714191528c5619b4f877bcb43e80464c44d1
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659006"
---
# <a name="xamarinforms-and-web-services"></a>Xamarin.Forms et les Services Web

## <a name="introductionintroductionmd"></a>[Introduction](introduction.md)

Cet article fournit une description de l’exemple d’application Xamarin.Forms qui montre comment communiquer avec les services web différents. Les sujets abordés incluent l’anatomie de l’application, les pages, le modèle de données et l’appel des opérations de service web.

## <a name="consume-an-aspnet-web-service-asmxxamarin-formsdata-cloudweb-servicesasmxmd"></a>[Utilisation d’un Service Web ASP.NET (ASMX)](~/xamarin-forms/data-cloud/web-services/asmx.md)

Services Web ASP.NET (ASMX) offrent la possibilité de créer des services web qui envoient des messages via HTTP à l’aide de SOAP Simple Object Access Protocol (). SOAP est un protocole indépendant de la plateforme et indépendant du langage pour la création et l’accès aux services web. Consommateurs d’un service ASMX n’avez pas besoin de savoir quoi que ce soit sur la plateforme, le modèle d’objet ou le langage de programmation utilisé pour implémenter le service. Ils doivent uniquement comprendre comment envoyer et recevoir des messages SOAP. Cet article montre comment consommer un service web ASMX à partir d’une application Xamarin.Forms.

## <a name="consume-a-windows-communication-foundation-wcf-web-servicexamarin-formsdata-cloudweb-serviceswcfmd"></a>[Utiliser un Service Web de Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/web-services/wcf.md)

WCF est un cadre unifié de Microsoft pour la création d’applications orientées service. Il permet aux développeurs de créer des applications distribuées traitées, fiables, sécurisées et interopérables. Il existe des différences entre ASP.NET Web Services (ASMX) et WCF, mais il est important de comprendre que WCF prend en charge les mêmes fonctionnalités que fournit ASMX, les messages SOAP sur HTTP. Cet article illustre l’utilisation d’un service WCF SOAP à partir d’une application Xamarin.Forms.

## <a name="consume-a-restful-web-servicexamarin-formsdata-cloudweb-servicesrestmd"></a>[Utiliser un Service Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)

Representational State Transfer (REST) est un style d’architecture pour la création de services web. Demandes REST sont établies via HTTP en utilisant les mêmes verbes HTTP qui utilisent de navigateurs web pour récupérer les pages web et d’envoyer des données aux serveurs. Cet article montre comment utiliser un service web RESTful à partir d’une application Xamarin.Forms.
