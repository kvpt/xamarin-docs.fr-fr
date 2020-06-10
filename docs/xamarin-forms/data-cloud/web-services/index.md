---
title : " Xamarin.Forms et services Web" Description : "ce guide explique comment communiquer avec différents services Web pour fournir des fonctionnalités de création, de lecture, de mise à jour et de suppression (CRUD) à une Xamarin.Forms application. Les sujets abordés incluent la communication avec les services ASMX, les services WCF, les services REST.
ms. Prod : xamarin ms. AssetID : 8B360BDA-E4E3-4A3F-9004-0E35362F49F8 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 06/27/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-and-web-services"></a>Xamarin.Formset services Web

## <a name="introduction"></a>[Introduction](introduction.md)

Cet article fournit une procédure pas à pas de l' Xamarin.Forms exemple d’application qui montre comment communiquer avec différents services Web. Les rubriques couvertes incluent l’anatomie de l’application, les pages, le modèle de données et l’appel d’opérations de service Web.

## <a name="consume-an-aspnet-web-service-asmx"></a>[Consommer un service web ASP.NET (ASMX)](~/xamarin-forms/data-cloud/web-services/asmx.md)

Les services Web ASP.NET (ASMX) offrent la possibilité de créer des services Web qui envoient des messages via HTTP à l’aide du protocole SOAP (Simple Object Access Protocol). SOAP est un protocole indépendant de la plateforme et indépendant du langage pour la création et l’accès aux services Web. Les consommateurs d’un service ASMX n’ont pas besoin de savoir quoi que ce soit sur la plateforme, le modèle objet ou le langage de programmation utilisé pour implémenter le service. Ils doivent uniquement comprendre comment envoyer et recevoir des messages SOAP. Cet article explique comment utiliser un service Web ASMX à partir d’une Xamarin.Forms application.

## <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>[Utiliser un service Web Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/web-services/wcf.md)

WCF est l’infrastructure unifiée de Microsoft pour la création d’applications orientées service. Il permet aux développeurs de créer des applications distribuées sécurisées, fiables, transactionnelles et interopérables. Il existe des différences entre les services Web ASP.NET (ASMX) et WCF, mais il est important de comprendre que WCF prend en charge les mêmes fonctionnalités que celles fournies par le service ASMX : les messages SOAP sur HTTP. Cet article montre comment utiliser un service SOAP WCF à partir d’une Xamarin.Forms application.

## <a name="consume-a-restful-web-service"></a>[Utilisation d’un service Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)

REST est un style architectural pour la création de services Web. Les requêtes REST sont effectuées via HTTP à l’aide des verbes HTTP utilisés par les navigateurs Web pour récupérer des pages Web et envoyer des données aux serveurs. Cet article explique comment utiliser un service Web RESTful à partir d’une Xamarin.Forms application.
